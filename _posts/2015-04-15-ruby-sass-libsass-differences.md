---
layout: post
title:  "Ruby Sass, LibSass – What's the Difference?"
date:   2015-04-15
author: Guil Hernandez
categories: sass tips
permalink: /ruby-sass-libsass-differences/
desc: "Learn the differences between Ruby Sass and LibSass, along with some of the advantages of using each one."
---

Just when you're getting used to working with [Sass](http://sass-lang.com/), you hear about a new type of Sass called [LibSass](http://libsass.org/). Then you immediately start asking yourself: *"What the heck is LibSass? Should I drop Ruby Sass and get on board with LibSass? Oh no &mdash; I'm behind on the new hotness!"*

Don't worry, you're not behind... As you'll learn, Ruby Sass and LibSass are similar. The main differences are in the implementation, compiling speed, and feature support. In this post, I'll cover the differences between Ruby Sass and LibSass, along with some of their advantages and disadvantages.

##Ruby Sass
The original Sass you've come to love is written in Ruby. If you just started writing Sass, chances are you're using the Ruby-based Sass. With Ruby Sass, as long as you're running Ruby, you install Sass, run the compiler and that's it&mdash;off you go using all Sass' awesome features.

But Ruby Sass will *not* work if used in a development environment outside of Ruby. And depending on the size of a project, the Ruby Sass engine can take a long time to compile to CSS. This is more noticeable on large projects; the bigger the project, the slower the compilation.

So, what do you do if you're not running Ruby in your dev environment, or working on large projects with slow compilation times? 

##Enter LibSass 
[LibSass](http://libsass.org/) is a C/C++ port of the original Sass engine. It doesn't rely on Ruby to generate CSS, so it can be implemented in other languages. 

By itself, LibSass doesn't do anything &ndash; it's just library. For LibSass to work, you'll need a wrapper (or implementor) around LibSass to run the library and compile your style sheets. Some of the most common LibSass wrappers are [SassC](https://github.com/sass/sassc), the first wrapper developed; [node-sass](https://github.com/sass/node-sass), [grunt-sass](https://github.com/sindresorhus/grunt-sass)&mdash;there's even a [Ruby wrapper](https://github.com/sass/ruby-libsass).

For instance, if a project is running node-sass, LibSass is the core library while node-sass is the wrapper that allows compiling inside of Node.js.

##Why LibSass?
With LibSass you're no longer bound to a Ruby dependency and, with the many wrappers available, it's now easy to integrate Sass with [almost any language](http://sass-lang.com/libsass).

The best part about using LibSass is compiling speed. LibSass runs and compiles style sheets significantly faster than Ruby Sass&mdash;up to [4000% faster](http://www.moovweb.com/blog/libsass/)! Take a look at this [compile time comparison](http://www.solitr.com/blog/2014/01/css-preprocessor-benchmark/) between Sass, LibSass, and other CSS preprocessors.

##Compatibility with Ruby Sass
LibSass isn't perfect, though. Developers are still hesitant about moving their codebase over to LibSass because it's behind Ruby Sass when it comes to certain features.

For example, `@at-root`, `@error`, and many of the [new features of Sass 3.4](http://www.phase2technology.com/blog/everything-you-need-to-know-about-sass-3-4/) are not supported in LibSass. For an extensive list of the inconsistencies between Sass, LibSass, and older Sass engines, take a look at [sass-compatibility.github.io](http://sass-compatibility.github.io).

###The future of LibSass looks promising! 
The good news is that LibSass will soon catch up to Ruby Sass. The upcoming release of LibSass 3.2 will have support for `@at-root`, `@error`, `@media` and `@keyframes` directive bubbling, and [lots more](https://github.com/sass/libsass/releases/tag/3.2.0-beta.1). 

In fact, Ruby Sass will not release any new features until LibSass catches up. Once it does, there will be feature parity between the two moving forward. So soon, we'll have the blazing speed of LibSass with all the features of Ruby Sass!

For a quick way of testing Ruby Sass features against LibSass, check out [SassMeister](http://sassmeister.com/).

##Final thoughts
Once you're all set up with LibSass, you'll realize that it's as easy as using Ruby Sass, just faster. And if you're only using the core Sass features, you should be fine if you decide to make the move to LibSass.

If you, your team, and your projects are happy using Ruby Sass, stick with it. But depending on your project, the benefit of LibSass’ speed alone may be worth temporarily missing out on a few Sass features. 


