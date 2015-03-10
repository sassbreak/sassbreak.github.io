---
layout: post
title:  "The Double Ampersand Mixin"
date:   2015-03-09
author: Guil Hernandez
categories: sass tips
permalink: /double-ampersand-mixin/
desc: "With the Sass double ampersand mixin, we can generate helpful layout styles."
---

A while back, [I wrote about a useful selector](http://blog.teamtreehouse.com/sass-tip-double-ampersand-selector) for applying margins to columns and buttons. The selector used a double ampersand with an adjacent sibling combinator (`&+&`).

Here's the gist of the article: The `+` combinator targets an element’s immediate sibling, so if we reference adjacent parent selectors with Sass, we can generate helpful layout styles. For example, to create a row where all but the first column have a left margin, we can write the following rule:

{% highlight scss %}
.col {
  width: 25%;
  float: left;
  & + & {
    margin-left: 20px;
  }
}
{% endhighlight %}

This will output:

{% highlight scss %}
.col {
  width: 25%;
  float: left;
}
.col + .col {
  margin-left: 20px;
}
{% endhighlight %}

This keeps the first column flush with the left side of the page, while the others get a `20px` left margin.

##The Mixin

I found myself repeating this "double ampersand selector" several times on projects, so I wrote a simple mixin for it:

{% highlight scss %}
@mixin doubly($margin) {
  & + & {
    margin-left: $margin;
    @content;
  }
}
{% endhighlight %}

A basic `@include` looks something like this:

{% highlight scss %}
.col {
  @include doubly(20px);
  float: left;
  width: 25%;
}
{% endhighlight %}

The mixin also works well with links and button groups. For instance, if a sibling button needs a different background color and a left margin&mdash;for separation, we can write the following:


{% highlight scss %}
.btn {
  @include doubly(10px) {
    background: firebrick;
  }
  background: dodgerblue;
  ...
}
{% endhighlight %}

The adjacent button gets the `firebrick` background color and `10px` left margin:

{% highlight scss %}
.btn {
  background: dodgerblue;
  ...
}
.btn + .btn {
  margin-left: 10px;
  background: firebrick;
}
{% endhighlight %}

##What if we need it for other *things*?

Sure, we could create further customization by adding other properties, passing more values, control directives, etc. Let's keeps things simple here by setting the default `$margin` value to `null`:

{% highlight scss %}
@mixin doubly($margin: null) {
  & + & {
    margin-left: $margin;
    @content;
  }
}
{% endhighlight %}

Now we can do other neat things like pass declarations for creating hierarchy in typographic  layouts. This rule uses `text-indent` to differentiate the paragraph above from paragraph below: 

{% highlight scss %}
p {
  @include doubly() {
    text-indent: 1.5em; 
  }
}
{% endhighlight %}

Or, we can zero out the top margins on all but the first paragraph:

{% highlight scss %}
p {
  @include doubly() {
    margin-top: 0; 
  }
}
{% endhighlight %}

I've noticed this selector *(the CSS version)* used on many typography websites like [jessicahische.is](http://jessicahische.is) and [trentwalton.com](http://trentwalton.com). Kudos goes out to [Mattox](https://dribbble.com/mattoxshuler) [Shuler](https://twitter.com/mattoxshuler) for showing me this clever typography use case.

Check out an example on CodePen:  

<p data-height="300" data-theme-id="0" data-slug-hash="jEKpae" data-default-tab="result" data-user="Guilh" class='codepen'>See the Pen <a href='http://codepen.io/Guilh/pen/jEKpae/'>jEKpae</a> by Guil H (<a href='http://codepen.io/Guilh'>@Guilh</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>

