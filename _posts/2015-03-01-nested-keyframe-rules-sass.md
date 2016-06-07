---
layout: post
title:  "Nested @keyframes Rules"
date:   2015-03-01
author: Guil Hernandez
categories: sass tips
permalink: /nested-keyframe-rules-sass/
desc: "We can nest @keyframes rules in Sass without an @at-root directive."
---

A handy feature in Sass 3.3 was the [`@at-root` directive](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#at-root), which outputs nested Sass rules at the root of the CSS.

It was Stu Robson who came up with [an awesome use case and example](http://www.alwaystwisted.com/articles/2014-03-08-using-sass-33s-at-root-for-piece-of-mind) for `@at-root` when dealing with animations and `@keyframes` rules. Instead of creating keyframe rules at the root of the style sheet, we could nest them inside their modules with `@at-root`. Like this:

{% highlight scss %}
.bunny {
  animation: hop 2s ease-in-out infinite, 
             move 6s ease-out forwards;

  @at-root {
    @keyframes hop { 
      50%  { transform: translateY(40px); }
    }
    @keyframes move { 
      100% { left: 400px; }
    }
  }
}
{% endhighlight %}

I was excited about this feature because it made `@keyframes` rules easier to find, read and maintain.

## No more @at-root
I recently discovered that [Sass 3.4](http://sass-lang.com/documentation/file.SASS_CHANGELOG.html) automatically "@at-roots" nested keyframe rules. So now, instead of wrapping our `@keyframes` rules in an `@at-root`, we can nest them like regular Sass rules:

{% highlight scss %}
.bunny {
  animation: hop 2s ease-in-out infinite, 
             move 6s ease-out forwards;

  @keyframes hop { 
    50%  { transform: translateY(40px); }
  }
  @keyframes move { 
    100% { left: 400px; }
  }
}
{% endhighlight %}

... and Sass outputs them at the root level:

{% highlight css %}
.bunny {
  animation: hop 2s ease-in-out infinite,  
             move 6s ease-out forwards;
}
@keyframes hop {
  50% {
    transform: translateY(40px);
  }
}
@keyframes move {
  100% {
    left: 400px;
  }
}
{% endhighlight %}

Sweet!
