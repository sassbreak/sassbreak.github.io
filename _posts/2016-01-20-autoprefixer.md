---

author     : Dale Sande
title      : "Autoprefixer"
date       : 2016-01-20

categories: sass tips
permalink  : /recipe-for-a-build-processor
desc       : "If you are new to Sass, or even an experienced user, getting up to speed with the latest tools and configurations can be a real challenge ..."
layout     : post

---

# Sass, Gulp, Sourcemaps and Autoprefixer

If you are new to Sass, or even an experienced user, getting up to speed with the latest tools and configurations can be a real challenge. Clearly one of the biggest differences between the original Ruby version of Sass and Libsass is setting up the processor. Ruby Sass came with this built in, and used with Rails the Rails Asset Pipeline also addressed most of those needs. In today's JavaScript asset build systems, there are many more choices, between Gulp, Grunt, Broccoli and others, what are you to focus on? How do you configure them? How do you simply get started?

While there is a lot to learn, you don't need to learn the whole API in order to get up and running with most of the common power tools. Setting up Libsass with Gulp and integrating Sourcemaps with Autoprefixer for a real powerhouse of utilities is actually a pretty simple process. 

## What is Autoprefixer?

Vendor prefixing, UGH! Am I right? At first this seemed like such a good idea since the W3C was really late to the game and updates to CSS were coming so slow that the concept was just stale. With the ushering in of the iPhone, Apple was  dead-set on evolving App UX in the browser, so `-webkit-` was born! Such an advancement of thought, no one really considered the consequences. It wasn't long until we were starting to feel the backside of this decision. Remember gradients, anyone?

In the Sass community there were many really cleaver pre-processing ideas out there. Compass had a HUGE and an extremely well thought out solution to the `experimental` problem. This was pretty good, but in the end we were left with a lot of really abstracted code. In the end, wouldn't it just make more sense to write regular CSS and then process that post fact? Enter the minds behind postCSS. While much of their toolset I regard as a re-hash of Sass, some tools like Autoprefixer is a very welcome addition to my CSS processing pipeline.

Autoprefixer, unlike Sass, is a POST PROCESSOR. That means, it will take the output CSS from Sass and then process it again for a final output. Kind of crazy, but pretty cool when you understand the concept. Autoprefixer specifically will scrub your CSS and look for matches of rules that require vendor prefixing based on data from caniuse.com.

So, going in with ...

```
@keyframes foo {
  0%   { opacity: 0; }
  100% { opacity: 1; }
}
```

will result with a final output of ...

```
@-webkit-keyframes foo {
  0% {
    opacity: 0; }

  100% {
    opacity: 1; } }

@keyframes foo {
  0% {
    opacity: 0; }

  100% {
    opacity: 1; } }
```

For many of you this is old news ... but for many of you this is going to blow your mind! Using this with Sass and Gulp is pretty simple, it uses a function called pipes that will take the data from Sass, then run it through Autoprefixer and then output the final CSS. As this tutorial will explain, this can all be put into a single command so that all you do is work and the machine worries about the details.

## What are Sourcemaps?

Now Sourcemaps on the other hand, I am assuming that anyone reading this article is up to speed on Sourcempas in Sass. It's been baked into Ruby Sass for years now and the concept is not new to the JavaScript community. But if you are not aware of this, please simply Google `sass sourcemaps` and there are tons of articles out there about what this is and how this works. 

## The Setup

Getting into the setup there are some assumptions.

1. Basic understanding of Gulp
1. Basic understanding of npm
1. You have Node installed
1. oh ... and you know how to use computers o_O

This article contains a number of code examples, that if you are new to Gulp and NPM, may be difficult to follow. No worries, [see the accompanying github repo](https://github.com/blackfalcon/recipe-for-a-sass-build-processor) that has a complete working example based off of this article.  


## The Install

For the sake of argument ... we will start with a clean directory. No other codes, greenfield, etc ...

### package.json

The easiest way to get things kicked off is to create a new directory anywhere on  your computer and inside that directory run `npm init`. Node will ask you a series of questions that you don't need to be too concerned about in this exercise. Just keep hitting the `return` key and you will be golden.

In the end, look back in that directory and you should have a new `package.json` file in there. It should look something like this ..

```
{
  "name": "article",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

With that, we are off to a great start!!!

### All Sass things

In the end, to get this all working, we need to have Sass in there, right? Well, you don't, but if you are not using Sass WHAT ARE YOU DOING?!?! It's not that bad and I would assume that most of you know how do to this, but let's sure.

#### First, let's get Gulp in there:

```
$ npm install gulp --save-dev
```

In case you are really following along ... your `package.json` file should look like the following:

```
{
  "name": "article",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "devDependencies": {
    "gulp": "^3.9.0"
  }
}
```

The only update will be with the addition of the `devDependencies` object in the `.json` file. This is where Node keeps track of the Node Packages installed in your new app.

There will also be a new directory in your project called `node_modules`. This is where Node will store all the requested libraries.

#### The Gulp-Sass

Gulp-Sass is the Node Package that wraps Node-Sass, that in turn wraps Libsass, the C+ version of Sass. There are multiple ways to run Sass, but if you are interested in using Libsass, using Node-Sass is pretty much the most preferred solution.

To get this in there ...

```
$ npm install gulp-sass --save-dev
```

After that, you should have everything installed that you need to get started.

### Create some directories and some Sass

At the root level of your app lets make a `source` and a `target` directory.

Within the `source` directory, create a new Sass file. Once created, add some basic code that we can play with going forward.

```
$color: orange;

@mixin the-test($flex, $rotate, $hyphens) {
  display: $flex;
  animation: spin 800ms steps(6) 0s infinite both;
  transform: rotate($rotate);
  user-select: none !important;
  cursor: grab;
  hyphens: $hyphens;
  @content;
  space: nowrap;
}

.foobar {
  @include the-test(flex, -60deg, auto) {
    background-color: $color;
  }
}
```

### Create the Gulp file

In order for Gulp to run tasks, be it Sass or Autoprefixer, you need a `Gulpfile.js` file in your app:

```
$ touch Gulpfile.js
```

Once created, open it in your editor and we will start with a simple Gulp task. There is a lot of goodness in there to get started. In the following example you will see the required packages; the `build:css` gulp task itself; an optional watcher `sass:watch`; and last we have the default task definition.

```
'use strict';

var gulp = require('gulp'),
    sass = require('gulp-sass');

// Gulp Sass Task
gulp.task('build:css', function() {
  gulp.src('./source/{,*/}*.{scss,sass}')
    .pipe(sass({
      errLogToConsole: true,
      outputStyle: 'expanded' //alt options: nested, compact, compressed
    }))
    .pipe(gulp.dest('./target'));
});

gulp.task('default', ['build:css']);
```

Once you have this completed, returning back to your command line, you will have two new options to process your Sass files. First, notice that `build:css` is listed as the `default` gulp task, so simply running `$ gulp` will run the Sass Gulp task. If you do not want to run the Sass task as a default, you can remove that reference and simply run `$ gulp build:css` to call the specific task.

At this point, retuning to your command line you can run either of those tasks and you should see something like the following in your output log:

```
[10:22:51] Starting 'build:css'...
[10:22:51] Finished 'build:css' after 12 ms
[10:22:51] Starting 'default'...
[10:22:51] Finished 'default' after 17 μs
```

Now, check your `/taget` directory for `style.css`. Open the file and you should see output CSS like:

```
.foobar {
  display: flex;
  animation: spin 800ms steps(6) 0s infinite both;
  transform: rotate(-60deg);
  user-select: none !important;
  cursor: grab;
  hyphens: auto;
  background-color: orange;
  space: nowrap;
}
```

#### Does Gulp-Sass have a watcher?

Anyone with past Ruby Sass experience will be wondering where the Sass watcher is. This functionality is still available, but we just need to build this into our Gulp file. 

To build a watcher, we simply need to build another Gulp task. Just like the other task, we need to reference the `gulp.task` function and pass in a name, something like `sass:watch`. 

The only difference is that we are going to use the `gulp.watch` function, versus  `gulp.src`, and pass in the path that we want the task to '*watch*'.

```
gulp.task('sass:watch', function() {
  gulp.watch('./scss/{,*/}*.{scss,sass}', ['build:css'])
});
```

#### REFACTOR ALERT!!!

You may have noticed that we are using the same path in the Gulpfile __TWICE__! The very bane of the DRY'sayers of the world. 

Gulp has a very simple solution to this, or should I say JavaScript for that matter. We can make that path into a variable and then pass that into the function. In the head of the Gulpfile, we can add another variable, like the following: 

```
sassPath = './source/{,*/}*.{scss,sass}'
```

Then, where that path is being used, simply replace with `sassPath`, like:

```
gulp.src(sassPath)

gulp.watch(sassPath, ['build:css'])
```

#### Running the watcher

Running the watcher is as simple as `$ gulp sass:watch` and edit the Sass files as desired. The only thing that you need to be aware of is that a watcher is just that, a watcher. Meaning that it will run the `build:css` task once the watch is triggered. 

This is important to know because, let's say that you have a clean project and there is no processed CSS, running `$ gulp sass:watch` will not produce the first person of the CSS file. So, it's a good habit to always run your default Gulp tasks before running any watchers. 

## Save your sanity, use Autoprefixer

This is where we discover that setting up Autoprefixer is so simple that we question why we are not already using this. The infrastructure to support it's use is already in place, we just need to add another library, some configuration to the Gulp tasks we already created and we are ready to go. 

#### Install the Node Package:

```
$ npm install gulp-autoprefixer --save-dev
```

### Update Gulpfile.js

At the top of the document, you need to add the following variable reference.

```
autoprefixer = require('gulp-autoprefixer')
```

The resulting code should be like the following:

```
var gulp = require('gulp'),
    sass = require('gulp-sass'),
    autoprefixer = require('gulp-autoprefixer'),
    sassPath = './source/{,*/}*.{scss,sass}';
```

#### Update the gulp.task

In the `Gulpfile.js` you need to add the following configuration to the `build:css` Gulp task:

```
.pipe(autoprefixer({
  browsers: ['last 2 versions'], 
  	cascade: false
}))
```

The entire task would then look something like the following:

```
// Gulp Sass Task
gulp.task('build:css', function() {
  gulp.src('./source/{,*/}*.{scss,sass}')
    .pipe(sass({
      errLogToConsole: true,
      outputStyle: 'expanded' //alt options: nested, compact, compressed
    }))
    .pipe(autoprefixer({
        browsers: ['last 2 versions'],
          cascade: false
        }))
    .pipe(gulp.dest('./target'));
});
```

It's interesting to take note of what is happening here. Gulp is taking the pipe of processed Sass and running it through another pipe for Autoprefixer. It's this ability to store tasks in memory and continue processing them that has made Gulp the new hotness as apposed to Grunt. 

In this example the tolerance is set to `last 2 versions`. In reading [Autoprefixer's documentation](https://github.com/postcss/autoprefixer/blob/master/README.md#options), you will see that there are multiple configurable options. If you wanted to get more complicated, say further versions back and address some specific IE support, a popular configuration is as follows:

```
['last 4 versions', 'ie > 8']
```

#### Re-run the Gulp task

At this point, all the things are in place to processes Sass into CSS and a seamless integration of Autoprefixer. Once set up, literally ignore and it should keep doing what's it's expected to do. After all, that's what computers are for, right?

When you now run `gulp build:css` the output CSS, unless you changed things, should look like the following, notice all those horrible vendor prefixes:

```
.foobar {
  display: -webkit-flex;
  display: -ms-flexbox;
  display: flex;
  -webkit-animation: spin 800ms steps(6) 0s infinite both;
  animation: spin 800ms steps(6) 0s infinite both;
  -webkit-transform: rotate(-60deg);
  -ms-transform: rotate(-60deg);
  transform: rotate(-60deg);
  -webkit-user-select: none !important;
  -moz-user-select: none !important;
  -ms-user-select: none !important;
  user-select: none !important;
  cursor: -webkit-grab;
  cursor: grab;
  -webkit-hyphens: auto;
  -moz-hyphens: auto;
  -ms-hyphens: auto;
  hyphens: auto;
  background-color: orange;
  space: nowrap;
}
```

We started out with something so simple and clean and the output is this mess of vendor prefixes matched up to your support grid. You can literally sit back, write the codes and let the machine worry about all the details in between. That's pretty close to nirvana for me. Well done postCSS, well done.

#### Preserve existing codes

Be warned. Autoprefixer is pretty harsh with how it updates files. For example, if you state that you want `last 2 versions` of support, that is EXACTLY what you will get, __2 versions__.

That means, even if you hard-coded vendor prefixing support for something that is outside that range, say some outdated `-moz-` thing for that old special case, Autoprefixer will __delete it__. This can be a good thing if you have code that is full of crud and you want to have a cleaner build version, but if need to preserve that code, simple add on the `remove: false` flag. Like so:

```
.pipe(autoprefixer({
  browsers: ['last 4 versions', 'ie > 8'], 
  	cascade: false,
  	remove: false
}))
```

Again, be sure to read the documentation, that's why it's there.

### Your support changes, it's easy, change the configs

One of the largest issues with web development is stale code and the changing browser market. If are using vanilla CSS, you are pretty much screwed, although you could pass regular old CSS through Autoprefixer to clean things up I guess? But we know you are using Sass, so that makes it that much more awesome. Make some global updates, re-process, profit.

Adding Autoprefixer to this just adds steroids to that solution. Now you don't even need to update any Sass, simply update your browser support model in the Gulpfile. Remember, `browsers: ['last 2 versions']` boom ... that's it.

## You promised Sourcemaps?

Here we are again, with another feature that seems daunting at first, but is really very simple to incorporate. The one confusing part here again is, with Ruby Sass, source map support in included. And again, with the ever configurable and customizable world of JavaScript, this is an independent part of the process. No worries, just a few simple steps and we will have this addressed. First, let's install the supporting library: 

#### Install the package

```
$ npm install gulp-sourcemaps --save-dev
```

#### Update the variables

Now that we have the supporting library installed, it's just a matter of adding this to our Gulpfile. With the other variables in the header, add the following: 

```
sourcemaps = require('gulp-sourcemaps')
```

Your variable header may look like the following: 

```
var gulp = require('gulp'),
    sass = require('gulp-sass'),
    autoprefixer = require('gulp-autoprefixer'),
    sourcemaps = require('gulp-sourcemaps'),
    sassPath = './source/{,*/}*.{scss,sass}';
```

#### Update the gulp.task

This part requires two updates. One to add the `.pipe(sourcemaps.init())` within the `gulp.src` function, and then the other pipe to config where the output sourcemap file will go. Some may prefer to have the source map integrated into the output CSS file itself or to have it as an external `.css.map` file that the CSS will reference. 

My personal preference is to have the sourcemap as an external file, because these maps can get pretty large and there is no issue with performance as sourcemaps is typically something you only use in development mode. 

According to the examples from the sourcemaps Node Package, `pipe(sourcemaps.init())` is placed as the first pipe in the set. Then `sourcemaps.write` is placed just before the `gulp.dest` function. 

the following example illustrates the opening and closing of the Gulp task: 

```
gulp.task('build:css', function() {
  gulp.src(sassPath)
    .pipe(sourcemaps.init())

		...

    .pipe(sourcemaps.write('.'))
    .pipe(gulp.dest('./target'));
});
```

#### Profit

Now that your task has been updated, run your `$ gulp` task again and look inside the `/target` directory. The CSS file will have an update at the foot of the document, something like: 

```
/*# sourceMappingURL=style.css.map */
```

Then you will notice a new file, `style.css.map`. This is the key to all the data in regards to how the CSS was generated from the Sass files. 

To take advantage of this new data, simply have the CSS file referenced in a HTML document, load, and open the Inspector. Pretty much every major browser is supporting this by default, including Internet Explorer. 

Loading our example, inspecting on the `<p>` element, you would see the reference to `style.scss:14` versus a reference to a `style.css` line number. 


```
.foobar {										style.scss:14
    display: -webkit-flex;
    display: -ms-flexbox;
    display: flex;
    -webkit-animation: spin 800ms steps(6) 0s infinite both;
	
	...

}
```

## Conclusion

There you have it, the basic makeup of a Gulp build process for your new Libsass based project. As seen, there are a large number of additional setup steps needed for this versus how Ruby Sass worked, but you may also notice that there are a vast number of configurable options available as well. 











