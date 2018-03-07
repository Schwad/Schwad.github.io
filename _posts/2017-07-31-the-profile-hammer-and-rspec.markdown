---
layout: post
title:  "The --profile hammer and RSpec"
date:   2017-07-31 14:34:09 +0000
categories: ruby rails testing
comments: true
---
{: style="text-align:center"}
![Hammer](http://i.imgur.com/FmD3O29.png)

Whenever we need a solution as programmers, it's easy to fire up a dozen tabs in the browser and read through hours of debate on dozens of third party libraries. As we narrow down our possible solutions we are then faced with integrating these into full-scale production web or mobile applications. Even if everything moves slick right out of the gate, the best case scenario is that we've added to our dependency bloat.

__The thing is, the basic tools we use have more in-house firepower than we could possibly hope to cover or learn.__

I think of this principle as `getting to know the tools you own > buying more tools`.

To spread this evangelism, I'm going to make sure I highlight these moments of discovery with the tools in my toolbox. Today: [RSpec](https://relishapp.com/rspec/rspec-core/docs/configuration/profile-examples)

With some of the rails apps I've been maintaining lately, test times have been running longer and longer. I find myself further and further to the right on the "increase test coverage" v "make the tests run faster" pendulum. I started my every few month peek at the latest tools to profile my RSpec tests-- seeing which bad actors were top of the list to kill.

I was pleasantly surprised to find the native flag for this, simply `--profile`. See your slowest specs in all their gory glory.

This flag provided me __exactly__ what I was looking for and I will definitely be using it heavily in the future. Example output:

```
Top 4 slowest examples (1.55 seconds, 88.8% of total time):
  User creates todo successfully
    1.49 seconds ./spec/features/user_creates_todo_spec.rb:5
  User sees own todos doesn't see others' todos
    0.04213 seconds ./spec/features/user_sees_own_todos_spec.rb:4
  user visits homepage successfully
    0.01424 seconds ./spec/features/user_visits_homepage_spec.rb:4
  Todo add some examples to (or delete) path/to/repo/spec/models/todo_spec.rb
    0.00001 seconds ./spec/models/todo_spec.rb:4

Top 4 slowest example groups:
  User creates todo
    1.49 seconds average (1.49 seconds / 1 example) ./spec/features/user_creates_todo_spec.rb:4
  User sees own todos
    0.04263 seconds average (0.04263 seconds / 1 example) ./spec/features/user_sees_own_todos_spec.rb:3
  user visits homepage
    0.01462 seconds average (0.01462 seconds / 1 example) ./spec/features/user_visits_homepage_spec.rb:3
  Todo
    0.0003 seconds average (0.0003 seconds / 1 example) ./spec/models/todo_spec.rb:3

Finished in 1.74 seconds (files took 13.61 seconds to load)
4 examples, 0 failures, 1 pending
```

This was on a small, simple starter application but you can imagine what this looks like on a 2,000-3,000 test app with large feature tests that can run 20-80 seconds a pop.

## Important Consideration

- This flag defaults to a small sample of tests, you can pass an integer after the flag if you want to increase or decrease the number of your slowest tests that you wnat to see on the report.

- `--profile` will always run through a full pass of `rspec`, even if tests are failing. If you want your specs to stop and only run the profile on a passing suite, add the `--fail-fast` spec as well.

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
