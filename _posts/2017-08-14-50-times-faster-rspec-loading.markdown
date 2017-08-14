---
 layout: post
 title:  "How I got RSpec to boot 50 times faster"
 date:   2017-07-31 14:34:09 +0000
 categories: ruby rails testing
 comments: true
---

{: style="text-align:center"}
![Speed](http://i.imgur.com/A5N2uos.jpg)

TDD was getting painful. Switching back and forth from my terminal to text editor running my latest RSpec tests saw me spending way too much time waiting not for my tests to run, but RSpec to load.

Here is an example running RSpec with one new test packaged with a feature:

```
Finished in 48.19 seconds (files took 33.15 seconds to load)
1 example, 0 failures
```

Good *lord*! I understand the 15 seconds as this was quite a hefty feature test, but waiting 30 extra seconds every time I want to check the spec, it's been adding up. And I want to see ways if I can improve this. My first port of call was the content of what was getting loaded - obviously that must correlate to load times.

I thought it might be my gemset bloating. So, I checked.

` bundle | wc -l`
`#=> 210`
`gem list -q | wc -l`
`#=> 420`

Yep, I'm relying on almost double the number of gems in my local gemset compared to bundle. Let's fix that. There's a great guide to setting up a new gemset with the rvm docs here https://rvm.io/gemsets/using

Following that, I went around to setting up a new gemset:

```
rvm gemset create schwad
#=> ruby-2.3.3 - #gemset created /Users/nickschwaderer/.rvm/gems/ruby-2.3.3@schwad
#=> ruby-2.3.3 - #generating schwad wrappers........
rvm gemset use schwad
#=> Using ruby-2.3.3 with gemset schwad
rvm use 2.3.3@schwad
#=> Using /Users/nickschwaderer/.rvm/gems/ruby-2.3.3 with gemset schwad
rvm use 2.3.3@schwad --default
#=> Using /Users/nickschwaderer/.rvm/gems/ruby-2.3.3 with gemset schwad
gem list -q | wc -l
#=> 42
```

Shwing! Lowered number of gems. Now to rebundle:

`gem install bundler`
`bundle install`
`gem list -q | wc -l`
`#=> 233`

Great! even after bundling I've chopped down unused dependencies for this gemset. Now to test... Does anything speed up here?

```
Finished in 39.19 seconds (files took 38.15 seconds to load)
1 example, 0 failures
```

dang.

nope.

The only speed improvement was against the actual test when I switched from a full feature test to a `true` assertion while focusing on load times.

Despite running this spec and switching gemsets multiple times, even opening a new terminal and doing so, no meaningful change. So my boot time must be from something other than my local gems.

After digging around, I realized that reducing the burden on the load wasn't the only way to achieve speed. If I'm running the same few tests over and over while working in a TDD fashion, `preloading` them may be the way forward like I do with the Rails console.


In my development/test group, I added the spring-commands-rspec gem found here: https://github.com/jonleighton/spring-commands-rspec . Then I ran `bundle exec spring binstub rspec` to generate `bin/rspec`. I knew spring worked well for my rails server, would it work well for loading rspec?

`Finished in 1.39 seconds (files took 0.52567 seconds to load)`

__HOLY SWEET SACCHARINE JEHOZAFAT!!!!__

This had to be a mistake. Ran it again, and again, and got the speed boost that `spring` provides by keeping things loaded for me.

This small tweak is saving me countless hours over the work cycle and seriously helped lower the pain points of proper TDD workflow.
