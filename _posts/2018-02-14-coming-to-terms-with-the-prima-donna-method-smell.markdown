---
 layout: post
 title:  "Coming to Terms with the 'Prima Donna Method' Smell"
 date:   2018-02-14 14:34:09 +0000
 categories: ruby rails code-quality code-smells
 comments: true
---

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

{: style="text-align:center"}
![Facepalm](http://i.imgur.com/iWKad22.jpg)

Oh man. I wrote this post originally as 'In Defense of the Prima Donna Method Smell'. By the time I was done, the powerful words of David A. Black had brought me back to the light. Here's the tale of my journey:

I have been a staunch advocate of enforcing code smell/quality standards in my work. Nothing makes me happier than requiring [rubocop](https://github.com/bbatsov/rubocop), [reek](https://github.com/troessner/reek) and [fasterer](https://github.com/DamirSvrtan/fasterer) to pass every PR. Heck I even occasionally like to unwind with a bottle of wine and [Ruby Critic](https://github.com/whitesmith/rubycritic) to highlight all the bad decisions I've ever made.

But what happens when I disagree with a valid code smell?

The 'Prima Donna Method' code smell has been a settled Ruby convention [for at least a decade](http://davidablack.net/dablog.html#2007/8/15/bang-methods-or-danger-will-rubyist). It is a generally recognized component of [most major Ruby code quality tooling](http://www.rubydoc.info/github/troessner/reek/Reek/Smells/PrimaDonnaMethod).

Simply put, a 'Prima Donna Method' is one that has a ! suffix but with no bang-less partner. (Rails devs - think `#save` and `#save!`)

David A. Black of ['The Well Grounded Rubyist'](https://www.amazon.com/Well-Grounded-Rubyist-David-Black/dp/1617291692) fame eloquently articulates why this is a smell:


> The ! in method names that end with ! means, “This method is dangerous”—or, more precisely, this method is the “dangerous” version of an otherwise equivalent method, with the same name minus the !. “Danger” is relative; the ! doesn’t mean anything at all unless the method name it’s in corresponds to a similar but bang-less method name.
>
> So, for example, gsub! is the dangerous version of gsub. exit! is the dangerous version of exit. flatten! is the dangerous version of flatten. And so forth.

For those keeping score at home, an example violating this principle would be:

{% highlight ruby %}

class Foo

  def bar!
    puts 'Bar!'
  end

end

{% endhighlight %}

And a passing equivalent would be:

{% highlight ruby %}

class Foo

  def bar
    puts 'Bar!'
  end

  def bar!
    puts 'Bar!'
  end

end

{% endhighlight %}

Also note that this smell does not care about method content. It simply cares about the **signal** the method name sends with the bang. Developers require a common language for better programming experiences, and we expect certain things with how our methods are named.

However, during one of my wine-laden Ruby Critic sessions on a codebase, I discovered some violations on a few methods that would require one of the following resolutions:

1. Create a companion method that I would never use

2. Ignore the smell linter via yaml configuration

3. Rename the method, without the bang

Here's the problem though: I felt *none of those solutions improve my codebase*. Ignoring smells is a 'smell' to me, adding pointless methods equally so, and renaming the method would take away from the articulation of the method for me.

The current dialect around bangs, I thought, had become more and more often a signal of 'transformative' dangerous types of behavior. I felt the following method did not deserve splitting out:

```
class Purchase < ApplicationRecord
  def void!
    #whether or not you should use #update_column is a discussion for another post...
    update_column(:void, true)
  end
end
```

I thought that would be articulate enough as-is, and the bang would communicate to future developers on the fly that this is an action which changes the database row. So I went around penning my revelations and opinions to the community. However, the following further passages from Black really rung true with me:

> Don’t add ! to your destructive (receiver-changing) methods’ names, unless you consider the changing to be “dangerous” and you have a “non-dangerous” equivalent method without the !. If some arbitrary subset of destructive methods end with !, then the whole point of ! gets distorted and diluted, and ! ceases to convey any information whatsoever.
>
> If you want to write a destructive method and you don’t think the name conveys destruction, you might be tempted to add a ! to make it clear. That’s not a good idea. If the name of a destructive method, without a !, does not connote destruction, then the name is wrong and cannot be repaired by slapping a ! on it.

It's easy for us to view criticism or code-smell failures from an egotistical vantage - 'how could *I* be in the wrong here!? I am a *professional*!'. However sometimes it's worth coming down off of your high horse and reconsidering your passionate position on a snippet of smelly code.

I got my original 'signalling' ideas crosswired. *Any method can be destructive*. The ! is solely to convey information for __dangerous versions of existing methods__.

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
