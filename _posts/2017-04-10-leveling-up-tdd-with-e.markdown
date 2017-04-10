---
layout: post
title:  "Leveling up TDD with -e"
date:   2017-04-10 14:34:09 +0000
categories: ruby rails rspec tdd
comments: true
---

There are many tools out there to help you with the [Red-Green-Refactor](https://thoughtbot.com/upcase/fundamentals-of-tdd) process of Test-Driven Development. We pick up new ones, drop old ones, and probably have several that we know we should be using more often. For example, in my early days of Rails development, I was trained to use [Guard](https://github.com/guard/guard-rspec) pretty heavily to keep an eye on my [RSpec](https://github.com/rspec/rspec) tests while in the flow of code.

Then reality sets in. Deadlines, client needs, feature requests and limited time constraints put pressure on how we test our applications.

The one thing I have learned, however, is that if you don't prioritize test coverage of your pull requests today you will pay for it tomorrow.

So normally, my flow would be to write a few feature tests in say,

`spec/features/posts/post_management_spec.rb`

then run

`rspec spec/features/posts/post_management_spec.rb`

see them fail, and move on. If there were a lot of tests in the file, I would comment out the ones I didn't want temporarily and run the spec with only the wanted tests enabled until it was time to merge.

This became quickly impractical. What if I have a plethora of model and feature tests across multiple files? What if I only want one test per file across 10 files?

So even if I just ran against the files with the changed tests, I could potentially be running `n` console commands (n= new spec count) and actually be executing `n*5` or `n*10` tests! Of course, if your full suite takes 30+ minutes, this is a definite issue.

__Inefficiencies in the development process afford you time and pain to reflect on your bottlenecks.__

Thankfully, we can turn this from a `O(n*5)` problem to an `O(1)` problem with relative ease thanks to a simple trick I recently picked up from [Avdi Grimm's Ruby Tapas](https://www.rubytapas.com/) _(unfortunately I couldn't track down the exact link at time of writing)_.

It's all in the `-e`.

`-e` allows you to match part of a test's name and run only those tests. So I took this to the next level.

### Workflow

Let's go through this in its simplest iteration. Say you have the following model tests.

{% highlight ruby %}
require 'rails_helper'

RSpec.describe Post, type: :model do
  let(:post){ create(:post) }

  context 'Scope' do
    context '#old' do
      it 'should only show old if post is greater than a week old' do
        post.update_column(:created_at, Date.today)
        expect(Post.old).not_to include(post)
        post.update_column(:created_at, Date.today - 8)
        expect(Post.old).to include(post)
      end
    end
  end

  context 'Methods' do
    context '#upcase_author' do
      it 'should render the author name in upcase' do
        expect(post.upcase_author).to eq("STANLEY SMITH")
      end
    end
  end
end
 {% endhighlight %}

 You will be adding new tests here, and in several other files. You really just want to run the new tests.

 You are working in a Github/Gitlab workflow, your branch is married to an issue by a number, something like `#123-allow-downcase-author`.

 When you write your new tests, at the end of the test name __add in `#123_spec`__, like so:

 {% highlight ruby %}
 require 'rails_helper'

 RSpec.describe Post, type: :model do
   let(:post){ create(:post) }

   context 'Scope' do
     context '#old' do
       it 'should only show old if post is greater than a week old' do
         post.update_column(:created_at, Date.today)
         expect(Post.old).not_to include(post)
         post.update_column(:created_at, Date.today - 8)
         expect(Post.old).to include(post)
       end
     end

     context '#without_author' do
        it 'should only render if the post does not have an author __#123_spec__*' do
          expect.(Post.without_author).not_to include(post)
          post.update_column(:author_id, nil)
          expect.(Post.without_author).to include(post)
        end
     end
   end

   context 'Methods' do
     context '#upcase_author' do
       it 'should render the author name in upcase' do
         expect(post.upcase_author).to eq("STANLEY SMITH")
       end

       it 'should render the author name in downcase __#123_spec__*' do
         expect(post.downcase_author).to eq("stanley smith")
       end
     end
   end
 end
  {% endhighlight %}
  #_*emphasis added_
Pretty straightforward, huh?

Whenever you check your tests, simply run:

`rspec -e '#123_spec'`

And your application knows only to run those tests. When you're done, you're simply one `CMD+Shift+F` away from clearing out the tags and pushing up your working code along with its new code and tests (assuming you aren't coding on a rock)!

This simple technique combined with a little customization for my use case has decreased the pain of TDD and been a boon to efficiency in my work day. Thanks, Avdi!
