---
 layout: post
 title:  "Overhauling the PorkCast database in Rails"
 date:   2017-10-13 14:34:09 +0000
 categories: ruby rails database
 comments: true
---

{: style="text-align:center"}
![Data](https://static.pexels.com/photos/577585/pexels-photo-577585.jpeg)

PorkCasts, [one of my hallmark Rails applications,](https://www.bozemandailychronicle.com/news/politics/porkcast-lawmaker-hopes-new-website-improves-government-transparency/article_f6f49d3a-eea8-5fde-a7f7-fa4b321a419d.html) turned two this year. It was the first application of its kind in Montana, relying on the State's Socrata-based API for checkbook and credit card payments to expose state spending. It also allows users to set up 'notifications' to receive emails when a specific query receives public moneys.

As with many personal projects, 2015 saw the vast majority of hacking on this project and then it was left to run on its own. I finally have turned my eye back to it in 2017 and am making several significant improvements for 'PorkCast 2.0'. For this post I want to focus on the dataset.

Before starting I want to evangelize the glory of `ProgressBar`. [This gem](https://github.com/paul/progress_bar) is a must-have when running rake tasks or console commands that may take up a lot of work and time.

{% highlight ruby %}

require 'progress_bar'

bar = ProgressBar.new(MyObject.count)

MyObject.each do |obj|
  obj.some_mutative_task!
  bar.increment!
end

{% endhighlight %}

This will give you a live terminal-based progress bar inclusive of time estimates for the task at hand. Don't leave home without it.

### Expanding the in-house database

The old system relied on the State of Montana API far too much. Whenever a user would search for a query, PorkCast would ping a request to MT and build a set of check and credit card payments from there.

Here's what I didn't like about that:

1. The request cycle was incredibly slow (by developer terms) for the end user.
2. The data remained on the state side primarily, limiting our ability to run in-house analytics.
3. We had no ability to 'audit' state data, checking if payments were deleted or changed.

I wanted to take all of the state data in my application and only ping the state for update purposes.

My Heroku DB would not support this (free tier is 10,000 rows). I knew I would need support for up to 10,000,000 rows. It was nerve-wracking, but by [following the comprehensive heroku docs](https://devcenter.heroku.com/articles/upgrading-heroku-postgres-databases) I was able to upgrade without any issues. Now to bring the data in.

### Importing hundreds of thousands of rows of new data

PorkCast had not taken on new data in the better part of a year- and the State of Montana recently terminated their Socrata API license. I had no easy way to update and read in data. I went to [the state transparency website](https://transparency.mt.gov/) and downloaded their files for payments in checks and credit cards for FY 2017 and FY 2018.

Fun fact, just because a file says `.csv` does *not* mean that it will play nice with the Ruby `CSV` library. After a few confused hours of debugging I broke down and opened the files in Excel. Turns out they were in `UTF-16` format- all I had to do was save them to vanilla CSV each and they worked fine.... Besides the fact that each file had a different approach to how they rendered payment dates (European and American date formats).

If you are able to edit your CSV rows down to just what you need, I heartily recommend [using the activerecord-import gem.](https://github.com/zdennis/activerecord-import) Taking from examples from their wiki, this is how you can handle massive imports much, much faster:

{% highlight ruby %}

columns = [ :title, :author ]
values = [ ['Book1', 'FooManChu'], ['Book2', 'Bob Jones'] ]

Book.import columns, values
Book.import columns, values, :batch_size => 1000

{% endhighlight %}

### Changing the internal architecture

The DB schema used to go:

`Query belongs_to User`
`User has_many Queries`

This created a problem, I only want one source of truth for queries instead of risking duplication if everyone searches 'NICHOLAS R SCHWADERER'. To do so I created a 'UserQuery' object and set up queries and users to utilize it [with a has_many: through association](http://guides.rubyonrails.org/association_basics.html#the-has-many-through-association).

To get this up to speed takes three migrations:

1. Generate the first batch of UserQuery objects to mimic the original associations.
2. Delete the duplicate queries.
3. Iterate over the UserQuery objects and point to the only remaining query.

The same deduping would be needed for checks.

### The saga of deduping and killing orphan queries

The major problem I had now as mentioned earlier was deduping, reassigning and cleanup up the Check, CreditCard and Query tables in the database. This data was the core truth of the system so before I committed any of these changes I backed up my db on Heroku.

First, to alter my production database I utilized [herou pg:push and pg:pull](https://devcenter.heroku.com/articles/heroku-postgresql#pg-push-and-pg-pull) for a slick interface to push and pull my db.

Second, I silenced outputs. I just wanted to see the progress bar, so any code I ran from here I did within the block:

{% highlight ruby %}
ActiveRecord::Base.logger.silence do
  # My Code
end
{% endhighlight %}

Then- to be safe- I added a 'name' column to UserQuery objects and migrated their query's name over. Consider it a safeguard.

{% highlight ruby %}
UserQuery.all.each do |uq|
  puts 'Now assigning user query names.'
  uq.name = uq.query.content
  uq.save!
end
{% endhighlight %}

Next I killed all old 'empty' queries. These were typically old searches by users that didn't match anything. From now on all our queries will point to actual real entities. This killed about 12,500 queries.

{% highlight ruby %}
Query.all.includes(:checks, :credit_cards).where(checks: { id: nil }).where(credit_cards: { id: nil }).destroy_all
{% endhighlight %}

Then I ran a dedupe against queries based on their name. Pretty straightforward. Afterwards I reassigned all outstanding UserQuery objects to the remaining queries.

{% highlight ruby %}
bar = ProgressBar.new(UserQuery.count)
UserQuery.all.each do |uq|
  begin
    if Query.where(id: uq.query_id).count == 0
      uq.query_id = Query.find_by_content(uq.name)
      uq.save
    end
  rescue => e
    puts "had a slight oops on UQ #{uq.id} of #{e.message}"
  end
end
{% endhighlight %}

And I did the same for checks and credit cards.

{% highlight ruby %}
bar = ProgressBar.new(Check.count)
Check.all.each do |c|
  begin
    if Query.where(id: c.query_id).count == 0
      c.query_id = Query.find_by_content(c.payee)
      c.save
    end
  rescue => e
    puts "had a slight oops on UQ #{uq.id} of #{e.message}"
  end
  bar.increment!
end
{% endhighlight %}

Here's the real fun. After everything was appropriately reassigned, I still had an issue of duplicate checks and credit cards from a couple raw imports over the last year that have some level of overlap. Deduping records that aren't 100% identical when you have millions of rows is __no easy task__. I approached this with an approach garnered in a [great SO post I had uncovered](https://stackoverflow.com/questions/14124212/remove-duplicate-records-based-on-multiple-columns). All I had to do was select the columns I wanted to check against the dedupe.

{% highlight ruby %}
ary = []
grouped = Check.all.group_by{|check| [check.department,check.payee,check.payment_category,check.amount,check.payment_date] }

bar = ProgressBar.new(grouped.values.count)

grouped.values.each do |repeats|
  original = repeats.shift
  repeats.each{|double| ary << double.id}
  bar.increment!
end

puts "To delete #{ary.length} checks"
Check.delete(ary)

{% endhighlight %}

Once all of this was wrapped up, I finally had the db how I wanted and could `pg:push` the db live.

### Including Elasticsearch in the stack

On the original version of PorkCast, I home-rolled all my searching and auto-complete logic. That's fine for small stuff, but for a dataset of this size and larger, I really wanted to give an elasticsearch integration a try.

Elasticsearch is no small beast to play with. If you look at `remoteok.io` a lot of job postings specifically mention this toolset. I was intimidated. Luckily, I found a fantastic ruby gem with great documentation on hooking up elasticsearch functionality and deploying to heroku: [Searchkick](https://github.com/ankane/searchkick).

Once I followed their docs all I had to do was run `Query.reindex` in the console and I was good to go. It was shocking to me how lightning fast it is- I even allow autocomplete suggestions on every keystroke with little to no pain on the server end. Thank goodness for [Ruby Weekly](https://www.rubyweekly.com) for pointing me to searchkick.

I reckon in the next month or so I'll have a shorter post highlighting all the new components of PorkCast 2.0 once it's live.

<!-- Drip -->
<script type="text/javascript">
  var _dcq = _dcq || [];
  var _dcs = _dcs || {};
  _dcs.account = '2671646';

  (function() {
    var dc = document.createElement('script');
    dc.type = 'text/javascript'; dc.async = true;
    dc.src = '//tag.getdrip.com/2671646.js';
    var s = document.getElementsByTagName('script')[0];
    s.parentNode.insertBefore(dc, s);
  })();
</script>
<!-- end Drip -->
