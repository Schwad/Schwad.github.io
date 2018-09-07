---
 layout: post
 title:  "How I got my first pull request merged on Rails-core"
 date:   2018-09-06 14:34:09 +0000
 categories: ruby rails community
 comments: true
---

{: style="text-align:center"}
![Favorite](https://i.imgur.com/XIOBAIg.png)

[DHH recently reviewed and merged my first ever pull request to Rails core](https://github.com/rails/rails/pull/33523). Short of getting my first Ruby job years ago, this is my most proud Ruby-achievement to date.

Open source contributions are terrifying to many - even me.

I want to tell you how I got here. This post will be equal parts personal experiences and concrete steps to meaningfully level up your open-source participation __while still being a hard-working developer at your day job__.

## Why contribute?

It is important to approach contributions with __healthy__ motivations. This will help the process come more naturally and enjoyably.

You should contribute in order to:

* Give back to the community that you rely on
* Interact with other developers who are just as passionate about these projects as you
* Provide your voice, perspective and feedback to diversify the OSS community
* Level up your understanding of core tooling

You should not contribute in order to:

* Be seen by potential employers
* Make your github commit chart dark green

These are all nice-to-haves that may actually occur, but are unlikely to be sustainable motivations for you as a developer.

## Okay, but where do I start? 🤔

To get started, do not troll around on github reading endless lists of pull requests and issues to try and find a place for yourself. You need a constant guided feed of things to review, and the perfect tool for that ([very kindly created by Schneems](https://github.com/schneems)) is CodeTriage.

{: style="text-align:center"}
![Favorite](https://i.imgur.com/DAbjF4H.png)

Go to [codetriage.com](https://www.codetriage.com) and sign up. Right now. Go ahead, open a new tab and I will still be here when you come back. If you take absolutely nothing from this piece except for joining codetriage, I will feel justified.

CodeTriage is a tool that allows you to 'subscribe' to github repositories that you may be interested in contributing to. Then, once a week, it feeds you a sample of pull requests and issues from those repositories that have not been recently reviewed (all via email). You can configure how often you get these emails and how many issues are included with them.

Do this, and just get in the habit of reading what comes in at first. Then, jump in. I recommend the following contributions at least at first, and if you never graduate beyond this you will still be a massive OSS help:

### Be the human advocate of the issue or pull request 🙋

{: style="text-align:center"}
![Favorite](https://i.imgur.com/77Zc2i0.png)

This is 80-85% of what I do (and I do not do much, but more on that later). You will see issues and PR's that have never been responded to, have unanswered questions, or have unfulfilled commitments from many months or years. You are completely in your right to pop your head around the corner and ask: `"@user, do you feel that your question has been fully answered, so we can close this?"` or `"Hello @user_2! Any chance you've had an opportunity to get that commit together?"`. People have tried to do this with bots, but there is nothing like the urgency of a real, empathetic, caring human behind the ping.

This is 100% of how I use code triage. I actually think I have never used it to make PRs myself, although you certainly could. Even if you ignore your emails and only look at 4-6 issues for a half hour a month, you are giving back in a big way.

### Drive your gems/dependencies forward ➡️

{: style="text-align:center"}
![Favorite](https://i.imgur.com/x8s8JVG.png)

Your applications have dependencies. There's no way around it.

Those who have taken larger applications through major Rails upgrades know all-too-well the pain of gem version conflicts holding the upgrade back. The next time this happens with you, instead of forking and bumping the gem or filing an issue with the maintainers, file a PR updating the `gemspec` to support your latest Ruby or Rails version. (`Note: do your due diligence to ensure that the gem still functions with the new bump through tests and manual usage. I have found that 4 out of 5 times the upgrade has no problem`)

This habit actually will help you get involved in larger projects that benefit the community.

Recently, this approach led myself and a group of developers to actually move forward with [a new `trix` gem after the current one lapsed in maintenance](https://schwad.github.io/ruby/rails/community/2018/08/21/new-community-maintained-trix-gem.html).

Also during a Rails 5 major upgrade, I came across a non-Rails-5-complaint and [unmaintained](https://github.com/activerecord-hackery/squeel/pull/428) gem. Participating in the discussion helped myself and other developers to move to [another maintained gem](https://github.com/rzane/baby_squeel) where I was afforded the chance to help build out the 'migration' part of the wiki. I now have more confidence in the future of my dependencies by participating in the community.

### Flag up the problem, clearly 🚩

Occasionally you will come across a genuine problem or issue with open-sourced code that you rely on. Often times we find 'hacky' ways around the problem or simply stop using that element of functionality.

Here's the thing: __if you have struggled with a problem, odds are someone else has too__.

This is the one element you are most likely to do already, but I really want to underline the importance of sharing issues with the community. That leads to the next issue:

### File a PR for what you want solved ✅

{: style="text-align:center"}
![Favorite](https://i.imgur.com/lQola4S.png)

When you encounter an issue - if you possibly can - try to file a PR over an issue. This honors maintainers by showing your personal investment in the issue. It also feeds, big time, the above-mentioned benefit of getting you familiar with the core technologies you rely on. Just remember that __closures are OKAY.__

When I used [active storage](https://edgeguides.rubyonrails.org/active_storage_overview.html) for the first time, the process was as slick as you could imagine. A handful of commands, rake tasks, minimal code required- and I was up and rolling. __EXCEPT IN ONE PLACE__

ActiveStorage only works with activerecord objects using integer primary keys. It does not support other types like UUID out of the box. Further, there was no clear 'way' to implement this. I ended up having to go through a [step-by-step guide](https://www.wrburgess.com/posts/2018-02-03-1.html) built out by another developer. That did not feel right.

Instead of simply firing up an issue complaining about this, I [filed my own Rails pull request to have a crack at implementing what I wanted.](https://github.com/rails/rails/pull/32466). It was a difficult slog. I worked through an area of Rails-core that I was not familiar with.

After a back-and-forth and several commits, I actually ended up closing the PR. The need was not strong enough and my skills in that area were not sufficient.

### ⚠️ It is okay to flakily make small contributions ⚠️

Only a small segment of our community will be able to dedicate full-time or even 5-10H/week to open source participation and contributions. Those of us working full time will often feel 'guilt' about neglecting our OSS-time. Don't. If you end up looking at CodeTriage twice a year for half an hour, filing half a dozen issues a year, and filing one meaningful PR every 18 months, you are still lapping the developer who is doing nothing.

{: style="text-align:center"}
![Favorite](https://i.imgur.com/B0JHLJz.png)

### Take ownership 🔥

All of the above revolves around one idea - 'take ownership'. You are a part of the community of the code you use. Participate. Speak up. Be heard. Your advancement will happen naturally.

Rails 5.1 [introduced form_with, effectively 'soft-replacing' form_tag and form_for](https://m.patrikonrails.com/rails-5-1s-form-with-vs-old-form-helpers-3a5f72a8c78a). I thoroughly enjoyed jumping on this approach with all new forms on upgraded apps. I used the [core docs whenever I had a question](https://api.rubyonrails.org/classes/ActionView/Helpers/FormHelper.html#method-i-form_with). One day I found my way on [Rails Guides](https://edgeguides.rubyonrails.org/form_helpers.html) with a question about `form_with`, and noticed that it was not mentioned anywhere in the form helper guide. In its place were still mentions solely of `form_for` and `form_tag`. That did not seem quite right. __(NOTE: The code for Rails Guides is a part of Rails-core)__

I found my way to the original `form_with` issue and [asked about this, offering to help in any way I could](https://github.com/rails/rails/issues/25197#issuecomment-408386800). [The original author](https://github.com/rails/rails/issues/25197#issuecomment-408600838) and [DHH himself](https://github.com/rails/rails/issues/25197#issuecomment-408448753) agreed with my observation, and told me to 'take a stab at it'! 😱

__No Pressure__

{: style="text-align:center"}
![Favorite](https://i.imgur.com/s0jWege.png)

I nervously added a discussion about form_with to the guides, and after a code review reworked the whole guide to only mention `form_with` with a `form_for/form_tag` footnote. [It was ultimately reviewed and merged!](https://github.com/rails/rails/pull/33523) I will be doing a further overhaul PR in the new future as well.

At no point was I __trying__ to get the 'Rails Contributor' feather in my cap. I just wanted to 'take ownership' of what mattered to me. So should you.
