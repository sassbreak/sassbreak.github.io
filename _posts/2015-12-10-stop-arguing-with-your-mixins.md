---
author     : Dale Sande
title      : "Stop Arguing So Much with Your Mixins"
date       : 2016-01-16

categories: sass tips
permalink  : /stop-arguing-with-your-mixins
desc       : "The Sass community in South Florida started a new meetup to talk about all things Sass."
layout     : post
---

# Stop arguing so much with your mixins

Mixins are arguably one of the more widely used tools in the Sass language next to the variable. Coming with that, mixins are more apt to be the most abused tool in the language. Mixins are extremely powerful and capable of doing a great number of things, but as we are creating these massive bodies of complex code, it's easy to find ourselves in a place where we have too many arguments in order to use our mixin. It's at this point we have started to lose comprehension of what's happening. The complexity of the tool starts to outweigh it's value.

Recently in refactoring a mixin with too many arguments, I began to think of how I could use list-maps to solve this issue.

## The classic way

Say you are creating a mixin that is relatively complex in scope and will consume a large number of arguments due to the number of alternative outcomes. We have all done it, I know I sure have. In the following example I am using a `button` mixin, the second most overly-engineered solution next to Grids, so the following code shouldn't seem that crazy to you:

{% highlight scss %}
@mixin core-button($color, $background-color, $border-color, $background-hover, $border-color-hover, $background-active, $border-color-active) {
  color: $color;
  background-color: $background-color;
  border: 1px solid $border-color;
  border-radius: 3px;
  cursor: pointer;
  font-family: $type-family-title;

  &:hover,
  &:focus {
    background-color: $background-color-hover;
  border-color: $border-color-hover;
  }
  &:active,
  &[aria-selected="true"] {
    background-color: $background-color-active;
    border-color: $border-color-active;
  }
}
{% endhighlight %}

Given the relative simplicity of the mixin, it still takes 7 arguments (yes you can have defaults, but that's not the point). From here things can only get more complex. then the number of times you need to compare back and forth between the mixin and where you are including it to make sure that you are putting the right value in the right place. So, you end up with this:

{% highlight scss %}
.foo {
    @include core-button(#ffffff, #ededed, #b2005c, #dbdbdb, #ba0060, #c7c7c7, #a60056);
}
{% endhighlight %}

Great, a list of hex values. In the moment, it makes perfect sense, you know the order of things. But next time you come back to this, will you remember what `#ba0060` is referencing? Was it `$background-hover` or `$border-color-hover`?

Then, this is a long list of values that I would assume that you would make a variable of so that you can use this with other similar buttons, right? So the process goes as follows ... see selector > see mixin > see variable. Search for variable > search for mixin > compare argument list > update and hope you got it right. What usually happens was, "Oh crap, I wanted to update the 4th value, not the 5th!"

Now sure about you, but this is annoyed me for years.

## The "options" way

While refactoring a lot of old code from older versions of Sass up to the latest tools available in libsass. And one tool that I am trying to find 1001 uses for is __list-maps__. If you are not familiar with list-maps, here is an [article I wrote](http://anotheruiguy.roughdraft.io/10302472-so-you-want-to-play-with-list-maps) over a year ago that can help you get started. The best part is, peering into the future with [sass-maps-plus](https://github.com/lunelson/sass-maps-plus) which is [planned to be an integrated feature](https://github.com/sass/sass/issues/1739) of the official Sass library.

List-maps bring to the table a great way to really manage a variable as a series of key-value pairs. Just like a real programming language! ＼(＾O＾)／

Looking back at the mixin above, wouldn't it be great if we could only have only one argument, a list of `options` perhaps, and then pass in a list-map of variables within? Good news, we can.

As illustrated in the following example, I have removed the dependency on an ordered list of arguments within the mixin. This `options` method allows me to use list-maps to have a complex array of named arguments in the form of key/value pairs and not have to deal with the complexities and ambiguity of an ordered list of arguments:

{% highlight scss %}
@mixin core-button ($options) {
  ...
}
{% endhighlight %}

### I've seen this before ...

Functions and mixins in Sass work much like functions on JavaScript. You can have either a list of ordered arguments where you must follow that specific order to address the needs of the function, or you can use named arguments as well. As shown in the following example, using the `.` syntax, you can basically make a single argument into an object and parse out the individual values for later use.

{% highlight js %}
// Define function to take one "argument", which is in fact an object:
function fnParseInt( oArg ){
    return parseInt( oArg.number, oArg.radix );
}

// Passing in the object literal you can then call like this:
fnParseInt( { number : 'afy', radix : 36 } );
{% endhighlight %}

This JavaScript method and similar methods in Sass have served us really well. But expanding on these ideas is what lead me to consider a single `option` in a mixin versus passing in a list or individually named arguments. Interestingly enough, and this was pointed out to me by a colleague, this very same model is heavily used in much of Backbone. The concept of `options` in Backbone is basically a javascript object of __key/value__ pairs that provide data to a method call.

{% highlight js %}
var makeVehicle = function(make, options) {
  var vehicle = {};
  vehicle.make  = make;
  vehicle.model = options.model;
  vehicle.year  = options.year;
  vehicle.value = options.value;
  return vehicle;
};

var car = makeVehicle(
  'Lamborghini', {
    model:'Aventador',
    value:'$397,500',
    year:2015,
  });

var minivan = makeVehicle(
  'Toyota', {
    model:'Sienna',
    value:'$28,700',
    year:2015,
  });
{% endhighlight %}

### Huston, we have a problem

With the new `options` style mixin and the desire to use a a single argument, we can't simply pass in a list of arguments from the previous mixin? Understanding the JavaScript models and understanding on how we can use list-maps, this is easy enough to address. Pretty sure you saw that coming, right?

In the following example I am creating a standard list-map variable and am using key/value pairs to address variable values.

{% highlight scss %}
$core-buttons: (
  color:               #ffffff,
  background-color:    #ededed,
  border-color:        #b2005c,
  background-hover:    #dbdbdb,
  border-color-hover:  #ba0060,
  background-active:   #c7c7c7,
  border-color-active: #a60056
);
{% endhighlight %}

I already love how this looks. Now instead of a list like we had before, `#ffffff, #ededed, #b2005c ...`, or a series of overly complicated variables with a naming convention, we have something that makes sense. It reads much like an object in any other language. We understand the role of each value in that list and that can mean a whole lot when you are managing thousands of lines of code.

### Update the signal, improve the receiver

Now that we have a __list-map__ with __key/value pairs__, it's required that we update all the variables within the mixin so that we can parse though the list-map. In the following example you will see that I replaced the more traditional *ordered* argument/variable pairs with the `map-get` function and the *named* variables. IMHO, this is WAY more human readable. It's clear that we are targeting a single variable which is inherited from the `$options` argument. Then from that `map-get` function we are asking for simple values, `color` or `border-color`. The mixin itself should remain pretty stable post this set up configuration.

{% highlight scss %}
@mixin core-button($options) {
  color: map-get($options, color);
  background-color: map-get($options, background-color);
  border: 1px solid map-get($options, border-color);
  border-radius: 3px;
  cursor: pointer;
  font-family: $type-family-title;

  &:hover,
  &:focus {
    background-color: map-get($options, background-hover);
  border-color: map-get($options, border-color-hover);
  }
  &:active,
  &[aria-selected="true"] {
    background-color: map-get($options, background-active);
    border-color: map-get($options, border-color-active);
  }
}
{% endhighlight %}

There is something that I really just love about looking at this code. It's expressive, it's clear, it's direct to the point. What I love even more is how it's actually used. In the following example you will see a mixin with a single argument, or `option`, that is able to address multiple concerns:

{% highlight scss %}
block {
  @include core-button($core-buttons);
}
{% endhighlight %}

### One variable to rule them all!

Once all the values are in an easy-to-read list-map variable, we are not beholden to one use case. The variable of `$core-buttons` can be global so these values are not trapped inside the scope of the mixin as they were before. Say I had a simpler case, like making a quick button? In the following example you will see that I can reference that same list-map and just pull out a few key values that I want to make use of without any extraneous naming conventions like `core-button--button-color`, or use the mixin and find ways to ignore rules I don't want, or worse yet ... just write in regular CSS!

{% highlight scss %}
button {
  color: map-get($core-buttons, color);
  background-color: map-get($core-buttons, background-color);
  border: 1px solid map-get($core-buttons, border-color);
}
{% endhighlight %}

Same variable. No copy/paste. No long naming conventions to find the original values from variables. All the values I ever need all scoped correctly within the same concept, or dare I say ... object.

## Conclusion

Coming to this conclusion and having worked on many Sass projects in my career, I can only say, "*If you are not using list-maps, what the hell are you waiting for?*"

The more I use list-maps, the more I find myself greatly reducing the complexity of my code. I too was victim of variable naming conventions. For me, finding new and creative ways of using list-maps, like reducing the number of arguments within a super-mixin, really just helps to dial in the sanity of my code.
