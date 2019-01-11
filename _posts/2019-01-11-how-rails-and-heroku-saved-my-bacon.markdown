---
 layout: post
 title:  "How Rails and Heroku Saved My Bacon"
 date:   2019-01-11 14:34:09 +0000
 categories: ruby rails heroku
 comments: true
---

{: style="text-align:center"}
![Favorite](https://i.imgur.com/I8as7eZ.png)

I love Rails, Ruby and Heroku. If I need to get something that __works__ live and on the web __right now__, I know I can trust these tools to the ends of the earth.

The other day I had 30 minutes to do just that.

{: style="text-align:center"}
## The Schwad Awards

{: style="text-align:center"}
![Dundies](https://i.giphy.com/media/kq42EnMcsHuBG/giphy.webp)

For either 9 or 10 years now (no one is quite sure), every holiday season I have been doing the Schwad Awards. For the first few years they were public on twitter, then I moved them to the more private-and-taggable world of Facebook.

What are the Schwad Awards? They started off as an opportunity to make up jokes and have ended up being a 'sort-of-dundies' where a great deal of my friends get nods of affection via an award. Could be an inside joke, a general compliment. (i.e. "The Award for Person Most Likely To Aspirate a Jalapeño goes to Bob Smith! 🙊")

Funnily enough, I've started doing so many of them that they take up a good chunk of time. Once someone has gotten a 'Schwad Award' three years in a row, it's quite a slight to leave them out. It always grows, never shrinks.

This year was particularly interesting for the awards.

## Schwad gets married

Let me back up. __WAY__ up. Over four years after our first date, I got to be the luckiest man on the planet and marry my sweetheart last December. Biggest day of our lives by a mile. We spent nearly two years saving and the amount of planning was off the charts.

I promised that on New Years Eve this year, as I'll have just been married, I would not be spending half the night slowly writing out Facebook schwad award posts.

Around that time I made a post (jokingly) saying that the Schwad Awards would happen 'during' the wedding day and only be awarded to those in attendance.

I forgot about this until the week before the wedding, when I found out a fair few guests actually __were__ expecting this to happen! I didn't worry - Facebook has the tooling to allow me to write these out in advance, my plan was to have them drip out on social networking at a steady pace during the time between the ceremony and the dinner. I figured it would be a nice treat while my wife and I were away taking all of our photos.

## WRONG

As I discovered during the __30 minutes I had allocated myself to write the awards before leaving for the venue__ Facebook does NOT allow post scheduling. This was only a feature I had gotten used to on my official facebook pages. Yep. We had a problem.

{: style="text-align:center"}
## WHAT DO I DO NOW 🚨 😵

{: style="text-align:center"}
![Shock](https://i.giphy.com/media/umMYB9u0rpJyE/giphy.webp)

Don't panic, I told myself. In the scheme of things the Schwad Awards __really do not matter at all__, they can be left behind. But I still had 29.5 minutes. With the pressure off, I thought simply: __'what do I *need*'__ to make this work?

I needed text online.

I needed it to become available at only a prespecified time.

I needed only my facebook friends to be able to read it.

## Rails and Heroku to the Rescue

Instead of using the limited social networking tooling I whipped out the sharp sword of Ruby on Rails to solve my problem, and fast. I had my app to work with in seconds.

`rails new save_my_bacon --database=postgresql`

Under a minute later I had my routes, controller and linked view created. To test timing in development I checked:

```
  - if Time.now < end_of_wedding_ceremony
    %h2= "The Schwad Awards will be released at #{end_of_wedding_ceremony}"
  - else
    ....
```

Perfect. All I needed was:

`git init`

And then:

`heroku create please-save-my-bacon`

And finally:

`git push heroku master`

And boom! I was live in under five minutes! I had the rest of the 25 minutes to build out a very rudimentary HTML based table with the data I wanted. Thanks [Heroku](https://heroku.com)! ❤️

## Let's make sure only my friends see this

One more important thing was to protect these awards from the 'wide open world' of the web.

Normally my authentication is done with [Devise](https://github.com/plataformatec/devise), but for a time like this I just needed a simple username and password blocker that I would share on my facebook page. Luckily Rails has slick support for this with [HTTP Basic authentication](https://guides.rubyonrails.org/action_controller_overview.html#http-basic-authentication).

Taken straight from the guides here, you can implement this in any Rails controller in seconds like so:

{% highlight ruby %}
  class AdminsController < ApplicationController
    http_basic_authenticate_with name: "humbaba", password: "5baa61e4"
  end
{% endhighlight %}

Sweet! And I was off to the races!

{: style="text-align:center"}
![Dundies](https://i.imgur.com/oBsZpMQ.png)

## All's well....

Thanks to these wonderful tools we have at our disposal, I was able to solve a surprise problem in a fraction of an hour and move on with my life (and my wedding!). I completely forgot about it until some attendees were commenting on their awards over drinks during the reception.

❤️ Rails + Heroku ❤️

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
