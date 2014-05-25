---
layout: post
title:  "Sass, Gulp, Source Maps, and You"
subtitle: "Making the most out of an imperfect workflow."
date:   2014-05-24 19:52:21
---

I've been using Gulp for my projects lately, and one of the most important
features for me is the ability to compile Sass. I use a lot of the newer
features of Sass, and I've run into a few issues.

If you just want to see how I use Sass 3.3 with Gulp, here is a gist of the
[gulpfile I use for this site](https://gist.github.com/frxnz/ab278e1d7d6088fdb64c).

## So you want the latest features of Sass

Sass 3.3 was released in March, and along with it a ton of great new features.
You can read about many of them on the
[Sass blog](http://blog.sass-lang.com/posts/184094-sass-33-is-released) or you
can see all of them in the
[changelog](http://sass-lang.com/documentation/file.SASS_CHANGELOG.html#330_7_March_2014).
What you're probably interested in, though, is how you can get the most out of
these new features in your current workflow, or you're getting started and you
want to make sure you can use these new features.

Build tools like Gulp allow us to create tasks to automate a lot of
the things we've had to do manually, or with a slew of third-party apps and
command lines, or things that we just couldn't do. If you're using a build tool
already, you're probably even using it to compile Sass. What you may not realize
is you're probably missing out on some things.

## Libsass to the rescue?

With Gulp there are two ways to compile Sass. You can use the Ruby compiler, or
you can use the Node implementation of Libsass. If you're not familiar with
Libsass, it is a port of Sass written in C/C++. The benefit of using Libsass in
Node is that it runs faster, and conforms to Node standard practices. This is
great for things like Gulp, because it's very easy to implement.

Libsass is under active development, and is backed by Moovweb, but it's still
playing catch-up. There are actually features from Sass 3.2 that haven't been
implemented yet, and as of now 3.3 are
[not a priority](http://www.solitr.com/blog/2014/01/state-of-libsass/). So if
you want to take advantage of the newest features of Sass, Libsass is probably
not for you.

## Ok so Ruby then, great?

Gulp has two Sass plugins: `gulp-sass` and `gulp-ruby-sass`. For those of us
wanting Sass 3.3 features, `gulp-ruby-sass` is where it's at. For the most
part it really gets the job done, but there are a couple caveats. First it might
help to understand how `gulp-ruby-sass` works under the hood.

The plugin actually executes the Ruby command-line command using Node's `spawn`.
This is great because it means you're using exactly the compiler that Sass was
built to use. Since Gulp is stream based&mdash;meaning it doesn't work on files
directly&mdash;the plugin has to do a little bit of trickery. Essentially what
happens is the plugin moves the Sass file in question to the system's temp
directory, runs the Ruby command on that, grabs the contents of the output, and
pipes that back to Gulp as a stream.

This has 2 major implications. First, it's kind of slow, especially when you get
into larger projects. I have a project now that takes about 6s to compile.
Second, source maps get a little weird.

## Let's talk about source maps

Source maps are great, and as far as I'm concerned they're the best feature of
Sass 3.3. In recent version of Chrome and Firefox dev tools, you actually see
the partial that contains the code for the styles on the element you're
inspecting. This makes it a lot easier to find exactly where some code you're
debugging lives. If your source maps are built correctly, you can even click
on the partial name in Chrome and see the code right there. *If your source maps
are built correctly.*

Remember when I said `gulp-ruby-sass` was working on files copied over to the
system temp directory? Yea, that causes some issues. Source maps normally
contain relative paths to the files being concatenated. So you'd normally see
something like `../scss/modules/_widget.scss`. When you start moving things into
those temp folders, the paths start to look more like
`../../../../project/scss/modules/_widget.scss`. Not good.

There is [an open issue](https://github.com/sindresorhus/gulp-ruby-sass/issues/17)
on GitHub, but it might be a while before the problem is resolved. In the
meantime, this isn't really a dealbreaker. You'll still get your partial names
and line numbers, you just won't be able to see the code in Chrome. Certainly
better than nothing.

## Speed vs. Features

`gulp-ruby-sass` is not perfect, but even with it's flaws, it is much more
feature-rich than it's libsass brother. What it really comes down to is whether
you need fast compilation, or the latest features of Sass. For me, even it's it
slightly-broken state, source maps are worth a couple extra seconds of
compilation time.
