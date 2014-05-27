---
layout: post
title:  "Improving Your Gulp Failures"
subtitle: "Or, helping Gulp to help you. Or, making bad code look good."
date:   2014-05-26 20:19:21
---

## Or, we all make mistakes.

Gulp is great for watching files, and then doing stuff when those files change.
Gulp is, out of the box, not even a little great at handling errors when things
inevitably go wrong. If you're just using the basic Gulp methods and plugins,
you probably spend a lot of time restarting your tasks. If you're me, this is
especially relevant when writing Sass. I'm a sloppy typer.

## First, an example

Let's say our Gulpfile looks something like this:

<pre title="Hark, A Gulpfile"><code class="language-javascript">
var gulp    = require('gulp'),
    sass    = require('gulp-ruby-sass'),
    prefix  = require('gulp-autoprefixer');

gulp.task('sass-dev', function () {

    return gulp.src('scss/main.scss')
        .pipe(sass({
            sourcemap : true,
            style     : 'compact',
            precision : 4,
        }))
        .pipe(prefix())
        .pipe(gulp.dest('css'));

});
</code></pre>

Pretty reasonable Gulpfile there, right? Compiles Sass, runs Autoprefixer, and
then pipes the stream on over to a CSS folder. Unfortunately, if an error occurs
in Sass or Autoprefixer, your whole task will just up and quit. You'll have to
go back over to Terminal and start the task again. Gross.

### Yea but can't I just&mdash;

It stands to reason you could just catch the error with `on('error')`, maybe log
it with `gutil`, and move on. Surprise! This doesn't really work. Your error
will get logged, but Gulp will unpipe the stream and just generally kind of
overreact.

## Hey guys it's plumber!

There is a Gulp plugin called `gulp-plumber` that can solve all of our problems.
It replaces the the standard `onerror` and handles the error more gracefully. If
we add just a couple lines to our Gulp task, we can greatly improve our workflow:

<pre title="Hark, A Gulpfile"><code class="language-javascript">
var gulp    = require('gulp'),
    sass    = require('gulp-ruby-sass'),
    prefix  = require('gulp-autoprefixer'),
    plumber = require('gulp-plumber');

gulp.task('sass-dev', function () {

    return gulp.src('scss/main.scss')
        .pipe(plumber(function () {
            console.log('There was an issue compiling Sass');
            this.emit('end');
        }))
        .pipe(sass({
            sourcemap : true,
            style     : 'compact',
            precision : 4,
        }))
        .pipe(prefix())
        .pipe(gulp.dest('css'));

});
</code></pre>

`gulp-plumber` catches any errors, logs it, and then...crashes? Yes. That
`this.emit('end')` is what really makes this great. `gulp-plumber` does any
error handling you want it to do, and then `this.emit('end')` actually
gracefully ends the task, leaving `watch` to continue doing its thing. You can
use plumber anywhere you would normally use `on('error')`, which should be
anywhere a task could possibly fail.

## Hey guys it's beepbeep and colors!

Now that we're handling our errors a little better, let's make the output work a
little harder for us. There are two plugins I really like for this: `beepbeep`
and `colors`. These are actually just regular Node modules, not Gulp plugins,
but they provide some nice feedback improvements.

`beepbeep` will fire your system's alert sound wherever you call it. This is
nice for error handling where you don't necessarily have your console visible.
You might not realize your task is erroring if you can't see it, so having some
auditory feedback is nice.

`colors` just changes the color of your `console.log`. Again a small thing, but
if you color-code certain messages it can help you find errors and other things
much faster.

Using these two modules, this is what my Sass task actually looks like:

<pre title="Hark, A Gulpfile"><code class="language-javascript">
var gulp    = require('gulp'),
    beep    = require('beepbeep'),
    plumber = require('gulp-plumber'),
    sass    = require('gulp-ruby-sass'),
    prefix  = require('gulp-autoprefixer'),
    colors  = require('colors');

// Compile Sass with Source Maps
gulp.task('sass-dev', function () {

    console.log('[sass]'.bold.magenta + ' Compiling Sass');

    return gulp.src('scss/main.scss')
        .pipe(plumber(function () {
            beep();
            console.log('[sass]'.bold.magenta + ' There was an issue compiling Sass\n'.bold.red);
            this.emit('end');
        }))
        .pipe(sass({
            sourcemap : true,
            style     : 'compact',
            precision : 4,
        }))
        .pipe(prefix())
        .pipe(gulp.dest('css'));

});
</code></pre>

I like to label task-specific logs with the task name in magenta, so I can
easily tell which task is yelling at me. Errors get logged in red so I can
quickly find the actual Sass error output. Gulp is all about improving your
workflow, and although these are small touches, they really make it easier to
quickly scan your console output.
