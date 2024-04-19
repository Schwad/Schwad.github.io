---
 layout: post
 title:  "Using Rubyfmt with Atom"
 date:   2019-12-02 07:34:09 +0000
 categories: ruby rails tutorials
 comments: true
---

{: style="text-align:center"}
![Favorite](https://i.imgur.com/cdkKQBu.png)

Rubyfmt, the exciting project from [@penelope_zone](https://twitter.com/penelope_zone) continues to grow by the day. After being a fan of this work for quite a while, I set out to hack together a way to get it running slick on Atom. I'm writing today to share those few steps so that you, too, can enjoy the magic of Rubyfmt on Atom!

## What is Rubyfmt?

I reckon 90% of people reading this article will already be intimately familiar with this project. If not, here's the quick hits.

[Rubyfmt](https://github.com/penelopezone/rubyfmt) is inspired by [gofmt](https://golang.org/cmd/gofmt/). It formats existing Ruby code, and you can set this up as you like (today we will do it after save). It is mature enough to be able to run against all of rspec-core without breaking. You can read more details on the Github README.

I first heard about this when Penelope discussed it with [@sgrif](https://twitter.com/sgrif?lang=en) on the [Yak Shave](http://yakshave.fm/) podcast. The subject came up in multiple episodes, and if I'm honest the technical details of creating this awesome tool likely went over my head. I still recommend taking a listen!

{: style="text-align:center"}
![Favorite](https://i.imgur.com/DdzSG3u.png)

## Okay, let's get this running on Atom

### 1. [Follow initial README instructions](https://github.com/penelopezone/rubyfmt)

At time of writing these are:
  - `git init`
  - `git clone https://github.com/penelopezone/rubyfmt.git`
  - `make`
  - `make install`

### 2. Set up Atom 'after-save' listener.
  - [Install Atom package save-commands](https://atom.io/packages/save-commands)
  - add the following to your save-commands.json in your directory:
  - `"**/*.rb": "ruby --disable=gems path/to/bin/rubyfmt.rb -i {absPath}{filename}"`

Full example:

```
# save-commands.json
{
	"commands": [
		"**/*.rb : ruby --disable=gems /path/to/bin/rubyfmt.rb -i {absPath}{filename}"
  ]
}
```

And voila! You should be in business. As a test you can add something unRuby-ish to a Ruby file (or just excess whitespace), save, and it should autoformat!

### 3. Gotchas:
  - Atom didn't like the recommended command on RubyFmt of `ruby --disable=gems ~/bin/rubyfmt.rb -i ${file}`
  - To get around this I manually passed in the actual path, obtained with `realpath rubyfmt.rb`
  - Happy to improve this if a reader has a more elegant solution


### 4. Moving forward:
  - I will try to keep this post updated, but following [@penelope_zone](https://twitter.com/penelope_zone) on Twitter and checking out the [README](https://github.com/penelopezone/rubyfmt) are the current best sources of truth
  - It would be nice to wrap this up automatically in an atom package in future, so these steps aren't necessary. I've started looking into this but it may be more straightforward with someone more familiar with writing Atom package plugin code
  - If you have any "this doesn't format correctly" feedback, hold fire for the moment as there's [a lot of work happening on this right now](https://twitter.com/penelope_zone/status/1199674689578295301?s=20)

### Thanks, Penelope! ❤️
