---
 layout: post
 title:  "Take care when using `render` in Rails"
 date:   2017-09-05 14:34:09 +0000
 categories: ruby rails controllers
 comments: true
---

{: style="text-align:center"}
![Double](http://i.imgur.com/RHhzGMv.jpg)

The more I experiment with other technologies, the more I realize what a joy it is to work with Rails and Ruby every single day. Take for example the following:

### Implicit rendering

I take for granted the baked-in convention-over-configuration ethos, which allows developers to maximize their efforts with genuine building and creativity. One of these that we enjoy from day one is implicit rendering from ActionController. When spinning up a controller action, you do not need to specify what template to render. As long as the namespaces match and are found in `app/views`, your application will do the work for you. In fact if you stay RESTful on a CRUD app you may rarely if ever find yourself manually rendering templates.

{% gist e4cc7c3bb19a9c0d412f5cf306957c31 %}

See? It knows to spit out the associated view. This is the kind of joy you experience from day one with the framework.

### Explicit rendering

However, there are times where we stray as we continue building larger applications. That's okay though! Rails is convention __over__ configuration, not the banishment of configuration altogether. In that case we can send the template explicitly down....

{% gist 2c93aabc5a94dfc57b5e3a41f012b91e %}

... and Rails knows what we're about.

### Extra curricular activity

If you made your way to this post, you more than likely are familiar with the last two pieces. I think it's important we cover the __easily-missed__ case where we can be executing code that we do not expect. Take the following code:

{% gist fb15697bc0d44d60b37d665deb649951 %}

It may be obvious from such a small controller action, but this can easily be missed as controller get more fat. All code after a `redirect` or `render` __is still executed__. That means in this case we see this page:

{: style="text-align:center"}
![Double](http://i.imgur.com/ji4y7TT.png)

But if we refresh we notice that our title indeed *was* updated behind the scenes.

{: style="text-align:center"}
![Double](http://i.imgur.com/2d7hB1Q.png)

While building out controllers and especially as they get larger, be sure to take care to consider code that may be executed after.

### Handling DoubleRenderError

Another issue that we can see is if `render` or `redirect` code is executed more than once. See below:

{% gist 5c8d5c6bda0e3008c23d9cb6c62f2045 %}

I almost guarantee that you've encountered this a fair few times yourself. Most likely as controllers are growing. If you find yourself unable to remove the render or redirect (which I recommend trying first) you can resolve this by calling `and return` after the render is hit.

{% gist a279b21a319d8c8f4035029922e53b4f %}

You may notice that this also precludes all later code from executing, which makes this a handy tool in the event that you not only want to prevent a DoubleRenderError, but stop later code from executing. When we visit `'/books/blocks_double_render_error'` we can clearly see that the code has not executed on the title attribute.

{: style="text-align:center"}
![Double](https://i.imgur.com/aAa8NtL.png)

### Conclusion

If you want to have a play with this yourself, I fired up a repo [with the example code](https://github.com/Schwad/be_careful_how_you_render/blob/master/app/controllers/books_controller.rb) that you can access.

For a deeper dive on this, I heartily recommend [The Rails 5 Way](https://leanpub.com/tr5w) by [Obie Fernandez](https://twitter.com/obie). It is my absolute favorite Rails 5 reference to date.
