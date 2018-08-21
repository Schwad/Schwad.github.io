---
 layout: post
 title:  "Migrate your apps to the new community-maintained trix gem: 'trix-rails'"
 date:   2018-08-21 14:34:09 +0000
 categories: ruby rails community
 comments: true
---

{: style="text-align:center"}
![New Horizons](https://i.imgur.com/vwLzQIy.png)

[Trix](https://github.com/basecamp/trix) is a fantastic open-source WYSIWYG tool from the lovely folks at Basecamp. Thanks to their hard work, we can easily punch in rich text editing for our users in a text field or text area. It has also become a critical piece of technology used by companies like [Podia](https://www.podia.com/) and [OceansHQ](https://www.oceanshq.com/).

{: style="text-align:center"}
![WYSIWYG](https://i.imgur.com/8eIg8Wy.png)

If you wanted to integrate `trix` seamlessly into your Rails app, there was a very handy [Ruby Gem](https://github.com/maclover7/trix) that could get you going in no time flat. We are incredibly grateful to the maintainers who got this gem going that we rely on so much.

It is time to give back. [For reasons that I will not go into here, the gem was in need of community-driven support to continue](https://github.com/maclover7/trix/issues/69). After a [public discussion with users in the trix Ruby community](https://github.com/maclover7/trix/issues/69), `trix-rails` has been announced as the fully-maintained RubyGem for using trix in Rails.

Please feel free to [stop on by and pitch in](https://github.com/kylefox/trix) or direct any issues/PRs here for future work on the trix gem.

Just want to migrate your app to the latest supported gem? Simply swap out trix in your gemfile for:

`gem 'trix-rails', require: 'trix'`

And you're ready to go! Thanks again to the prior maintainers and basecamp for all your amazing work on trix!
