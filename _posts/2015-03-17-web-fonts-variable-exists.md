---
layout: post
title:  "Importing Web Fonts with variable-exists()"
date:   2015-03-17
author: Guil Hernandez
categories: sass tips
permalink: /web-fonts-variable-exists/
desc: "The variable-exists() introspection function in Sass is useful for importing Google Web Fonts into our style sheet."
---

Sass has [introspection functions](http://sass-lang.com/documentation/Sass/Script/Functions.html#introspection_functions) that can check for available variables, mixins, functions, and features in our code.

The introspection function I've found most useful is `variable-exists()`, which returns whether a variable exists in the current scope. With this function, we can check for a variable in our project, then instruct Sass to do something if the variable exists.

In this post, we'll see how `variable-exists()` is a useful function for importing Google Web Fonts into our style sheets.

##Web font variables
To use the function, we'll need to declare variables for each Google Web Font URL: 

{% highlight scss %}
// Web font URLs
$font-google-prim : 'http://fonts.googleapis.com/css?family=Lato:100,300,400';
$font-google-sec  : 'http://fonts.googleapis.com/css?family=PT+Serif:400,700';
{% endhighlight %}

###variable-exists()
With `variable-exists()`, we can tell Sass to import a font into our style sheet if `$font-google-prim` or `$font-google-sec` exist in our project:


{% highlight scss %}
@if variable-exists(font-google-prim) {
  @import url($font-google-prim);
}
@if variable-exists(font-google-sec) {
  @import url($font-google-sec);
}
{% endhighlight %}

If we've defined any of the font variables, Sass will import URLs for each existing font:

{% highlight scss %}
@import url("http://fonts.googleapis.com/css?family=Lato:100,300,400,700");
@import url("http://fonts.googleapis.com/css?family=PT+Serif:400,700");
{% endhighlight %}

Now you can add this function to your Sass starter template to handle all web font imports. Define the web font variables you need in a  `_config` or `_variables` partial and Sass will take care of the rest! Then it can be business as usual with your font stacks and main styles:

{% highlight scss %}
// Font Stacks
$stack-lato     : 'Lato', sans-serif;
$stack-pt-serif : 'PT Serif', serif;

// Main Styles
body {
  font-family: $stack-lato;
}
h1 {
  font-family: $stack-pt-serif;
}
{% endhighlight %}

See the demo on SassMeister:

<p class="sassmeister" data-gist-id="84c46cc0239791a0143e" data-height="380" data-theme="tomorrow"><a href="http://sassmeister.com/gist/84c46cc0239791a0143e">Play with this gist on SassMeister.</a></p><script src="http://cdn.sassmeister.com/js/embed.js" async></script>
