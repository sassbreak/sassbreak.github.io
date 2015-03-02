---
layout: post
title:  "Getting Back To our @at-roots"
date:   2015-03-02
author: David Conner
categories: sass tips
permalink: /getting-back-to-our-roots/
desc: "The `@at-root` directive, introduced with Sass 3.3, emits a collection of nested rules at the top-level root of the document. "
image: the-roots.jpg
---

Nesting is a feature in Sass that allows you to write your CSS with the same visual hierarchy that we see in HTML. It can provide many organizational and time saving benefits but its misuse is one of the main criticisms of Sass and the CSS it outputs. There have been many articles espousing the three levels deep nesting rule. When observed, this rule can take care of the majority of nesting issues but there are still cases where pushing a nested selector up to the root would be useful. That is where the `@at-root` directive comes in.

##@at-root
The `@at-root` directive, introduced with Sass 3.3, emits a collection of nested rules at the top-level root of the document. You can use it in-line with a single selector or as a block with multiple selectors.

{% highlight scss %}
//inline
.parent {
  ...
  @at-root .child { ... }
}

//as a block
.parent {
  ...
  @at-root {
    .child1 { ... }
    .child2 { ... }
  }
}

{% endhighlight %}

By default `@at-root` will move nested styles out from within a parent selector but it’s also possible to use it to move outside of nested directives such as `@media` as well. You can pass an argument of `(without: media)` to keep the nesting but jump out of the directive or you can use `(with: media)` to keep the directive but lose the nesting.

{% highlight scss %}
@media print {
  .page {
    width: 8in;
    @at-root (without: media) {
      color: red;
    }
  }
}

{% endhighlight %}

<a href="http://sass-lang.com/documentation/file.SASS_REFERENCE.html#at-root">Source</a>

##Use Cases
I admit, the obvious uses for `@at-root` are pretty limited but the control that this directive provides is beneficial in specific edge cases. Here are three instances where you may find the `@at-root` to be helpful.

###1. Modify an element's use of a class
For certain situations you may need to tweak a specific element's use of a class. For instance, removing the text decoration from an anchor tag that will be used as a button.

{% highlight scss %}
.btn {
    background: #ededed;
    border: 1px solid #555;  
    color: #555;
    padding: 1em 2em;  
    font: { 
      family: 'Open Sans Condensed', sans-serif;
      size: 1rem;
    }
    
    @at-root a#{&} {
        text-decoration: none;
    }
}

//compiles to
.btn {
  background: #ededed;
  border: 1px solid #555;
  color: #555;
  padding: 1em 2em;
  font-family: "Open Sans Condensed", sans-serif;
  font-size: 1rem; 
}

a.btn {
  text-decoration: none;
}
{% endhighlight %}

###2. Escaping @supports for fallback code 
The styles are all kept together but the fallback is emitted outside of the supports along with any styles that will be needed regardless of support.

{% highlight scss %}
@supports  (appearance: none) or (-webkit-appearance: none) or (-moz-appearance: none){
    .select {
        background: tomato;
        z-index: 100;
        &:before { 
            border-top: 0.625em solid rgb(255, 255, 255);
            border-left: .5em solid rgba(0, 0, 0, 0);
            border-right: .5em solid rgba(0, 0, 0, 0);
            content: "";
            position: absolute;
            top: 50%;
            right: 1.2em;
             transform: translate(0, -50%);
            z-index: 100;
         }
    
        select {
            -webkit-appearance:none;
            -moz-appearance:none;
            appearance:none;
            background: none;
            background-image: none;
            color: #fff;
            z-index: 200;
        }
       
        @at-root (without: supports) {
            margin-top: 1em;
            max-width: 16em;
            position: relative;
            width: 100%;
            
            select {
                border: .1em solid darken(tomato, 6%);
                outline: none;
                width: 100%;
            }
        } 
    }
}

//compiles to
@supports (appearance: none) or (-webkit-appearance: none) or (-moz-appearance: none) {
  .select {
    background: tomato;
    z-index: 100;
  }
  .select:before {
    border-top: 0.625em solid white;
    border-left: 0.5em solid transparent;
    border-right: 0.5em solid transparent;
    content: "";
    position: absolute;
    top: 50%;
    right: 1.2em;
    transform: translate(0, -50%);
    z-index: 100;
  }
  .select select {
    -webkit-appearance: none;
    -moz-appearance: none;
    appearance: none;
    background: none;
    background-image: none;
    color: #fff;
    position: relative;
    z-index: 200;
  }
}
.select {
  margin-top: 1em;
  max-width: 16em;
  position: relative;
  width: 100%;
}
.select select {
  border: 0.1em solid #ff4928;
  outline: none;
  padding: 1em;
  width: 100%;
}

{% endhighlight %}

###3. Removing specificity for items that will likely get modified while still keeping the organization of nesting. 
I love the visual organization that nesting allows but sometimes you know you are going to have to modify a class that exist both inside and out of a nested parent. Compound selectors add to the specificity so kicking classes that you plan on altering out to the root can be a good idea.


{% highlight scss %}
.parent {
    font-size: 1rem;
    padding: 1em 2em;
    
    @at-root .likely-modified {
        font-size: 1.5em;
    }
}
//compiles to
.parent {
  font-size: 1rem;
  padding: 1em 2em;
}
.likely-modified {
  font-size: 1.5em;
}
{% endhighlight %}


##Final Thoughts
Admittedly the use cases for `@at-root` were difficult to come by. I started writing the article with an amazing one that would save multiple lines of code only to have it fall apart on me once I started writing. The examples that were left are decent but far from a game changer. If you have a good use for `@at-root` please let us know in the comments. 

Check out the examples on CodePen:

<p data-height="266" data-theme-id="6879" data-slug-hash="raKNwX" data-default-tab="result" data-user="davidicus" class='codepen'>See the Pen <a href='http://codepen.io/davidicus/pen/raKNwX/'>@at-root use case examples</a> by David Conner (<a href='http://codepen.io/davidicus'>@davidicus</a>) on <a href='http://codepen.io'>CodePen</a>.</p>
<script async src="//assets.codepen.io/assets/embed/ei.js"></script>


















