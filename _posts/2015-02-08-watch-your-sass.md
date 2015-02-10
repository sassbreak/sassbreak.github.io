---
layout: post
title:  "--watch Yo' Sass"
date:   2015-02-09
author: Guil Hernandez
categories: sass tips
permalink: /watch-your-sass/
image: sass-watch.jpg
---

I know the feeling: You just installed Sass, eager to dive into all the awesome features and finally convert that stale CSS project over to Sass. You create your first .scss file, crack open Terminal, type a `--watch` command&mdash;everything comes to a hault. You get errors like &nbsp;`No such file or directory`, or you notice your Sass compiling to the wrong directory, but can’t figure out why.

After Googling and attempting a few `--watch` commands, you check your .css file. Nothing. This goes on for almost an hour. Yes, this is frustrating, but don’t let it discourage you from crossing over to the wonderful world of Sass. 

In this short post, we’ll go over a few simple `--watch` commands, what they mean, and how to overcome possible errors.

##What does `--watch` do?

The `--watch` command literally tells Sass to watch your project for changes. It's what converts your Sass files into CSS and auto-compiles your Sass every time it changes.

For example, this command tells Sass to watch and compile to a specific file – it's basically saying *"watch style.scss and compile to style.css"*:

{% highlight ruby %}
sass --watch style.scss:style.css
{% endhighlight %}

Following is a more general command that looks for your Sass files and watches for changes inside the directory containing your Sass files:

{% highlight ruby %}
sass --watch .
{% endhighlight %}

So if your Sass file is in the root directory, this will auto-compile to css within the root directory. This can get messy if, say, you only want Sass files in a "scss" directory, because the `sass --watch .` command will also watch and compile to CSS within that "scss" directory. 

##The commands I prefer

Usually, to keep our project nice and tidy, we'll want our Sass and CSS files in separate directories. For instance, if our Sass file is in a directory named "scss," this command will compile our Sass and update the style sheet inside the "css" directory:

{% highlight ruby %}
sass --watch scss/style.scss:css/style.css
{% endhighlight %}

If you’re working on a large project with many Sass files, you can tell Sass to just watch your Sass directory and update your CSS directory. Once my project is all set up, I like to use the command:

{% highlight ruby %}
sass --watch scss:css
{% endhighlight %}

This tells Sass *"watch the scss directory for changes, then update the style sheet inside the css directory."*

If the `--watch` command succeeds, you should a notification like this in the command line output: `Sass is watching for changes. Press Ctrl-C to stop.`

You also may see something like:

{% highlight ruby %}
directory css
    write css/style.css
    write css/style.css.map
{% endhighlight %}

This simply means that it generated a "css" directory containing a style sheet and [sourcemap](http://sass-lang.com/documentation/file.SASS_CHANGELOG.html#source_maps). **Remember**: you don't even have to create the css directory, style sheet, or sourcemap &ndash; the `--watch` command will generate them for you!

##Tried it. Still no dice.

OK, now you need to make sure that you’re in the right working directory. Let’s say you have a folder on your desktop named "project". Open up Terminal and type the command `cd desktop/project`. This will bring you to the correct working directory on your desktop.

Once you're in the project directory, write the command `sass --watch scss:css` to begin auto-compiling your Sass to CSS&mdash;provided you’ve already created the “scss” directory.

##That's it!

Now that you’re a `sass --watch` pro, next time you're up late working on a Sass project and frustrated because nothing’s happening in the browser, don't *spend minutes, hours, days, weeks, or even months over-analyzing the situation*. Just `--watch` yo’ Sass.

Check out our [Sass Resources](/resources/) page to learn more about getting up and running with Sass.



