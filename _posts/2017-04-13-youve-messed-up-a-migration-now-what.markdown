---
layout: post
title:  "You've messed up a migration. Now what?"
date:   2017-04-13 14:34:09 +0000
categories: ruby rails migrations
comments: true
---

![Facepalm](http://img11.deviantart.net/fab9/i/2012/150/c/c/facepalm_2_by_wolkenfels_stock-d51mpuf.jpg)

You've written a migration in your Ruby on Rails application, and _like any normal human_, you've made a mistake!

In your development environment, you don't want to drop and recreate the database you're working in, you just want to 're-run' the migration that you've now fixed.

__No need to fear - I've got your back.__

In your console, run:

`rake db:migrate:status`

Which will give you the following output:

```
database: my_database

 Status   Migration ID    Migration Name
--------------------------------------------------
   up     20170104131525  Devise create users
   up     20170104174145  Create roles
   up     20170704182740  Create fish
   up     20170704192840  Create octopus
   up     20170704195845  Create new attributes for fish and migrate from octopus
```

Nice. The latest one is the migration you want to 'redo', but this will work on any migration. Grab the migration id and run:

`rake db:migrate:redo VERSION=20170704195845`

That's it! You're ready to move on and didn't need to spend a chunk of an hour resetting everything.

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

<form action="https://www.getdrip.com/forms/275494850/submissions" method="post" data-drip-embedded-form="275494850">
  <h3 data-drip-attribute="headline">Stay in Touch</h3>
  <div data-drip-attribute="description">I like to write about Ruby and building things, typically once every month or so. Get an email when I have written something new.</div>
    <div>
        <label for="drip-email">Email Address</label><br />
        <input type="email" id="drip-email" name="fields[email]" value="" />
    </div>
  <div>
    <input type="submit" value="I Love Ruby too! 💎" data-drip-attribute="sign-up-button" />
  </div>
</form>
