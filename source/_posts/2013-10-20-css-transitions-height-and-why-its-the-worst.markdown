---
layout: post
title: "CSS Transitions, Height, and Why It&rsquo;s the Worst"
date: 2013-10-20 20:07
comments: true
categories:
subtitle: "The pitfalls of transitioning to height: auto, and how do a better job."
---

# So you want to transition height...

Our old friend jQuery has  `.slideUp()` and `.slideDown()`, which mostly works without any trouble. But you're a modern man/lady, and you want to use CSS for display. "This is a job for CSS transitions!" you might say. So you try something like this...

{% codeblock Some CSS That Won't Work lang:css %}
.el {
    height: auto;
    transition: height 0.5s;
}

.el.active {
    height: 0;
}
{% endcodeblock %}

That totally seems like it should work, right?

## Sad Trombone

Unfortunately CSS transitions aren't that smart, and they can't transition to `height: auto`. You'll get your `height: 0` and your `height: auto` but it will happen immediately, without any transition.

And if you're me, you'll spend *way* too long trying to figure out where the typo in your code is.

# max-height to the rescue!

[Several](http://jsfiddle.net/leaverou/zwvNY/) [people](https://coderwall.com/p/psqk4g) have [written](https://coderwall.com/p/mn2a2g) about this. And, generally, they get it right. `max-height` and `max-width` are animatable, and we can use them to sort of fake the effect we're going for.

{% codeblock Some CSS That Will Work lang:css %}
.el {
    max-height: 999px;
    transition: max-height 0.5s;
}

.el.active {
    max-height: 0;
}
{% endcodeblock %}

This is the classic example. As long as the content in your box is less than your `max-height`, the height will fit to the content. If your content is only 200px, your box will be 200px tall. So set `max-height` to some absurdly large value, and your transition will always work the way you want.

## But wait!

There is -- of course -- a caveat. When you transition `max-height` the browser transitions the **whole thing**. This means if you set `max-height: 1000px;` and your box is only 500px tall, that 0.5s transition is going to look more like a 0.25s transition. This probably isn't a big deal, and you just crank your transition time up until you're happy.

The real trouble comes when you have multiple elements of different heights being transitioned at the same time. The demo below uses our max-height trick, setting very values. As you can see it gives the illusion of an animation queue, but really it's just the delay while the browser transitions the part of your max-height that doesn't have content.

<p data-height="452" data-theme-id="0" data-slug-hash="orvHd" data-user="frxnz" data-default-tab="result" class='codepen'>See the Pen <a href='http://codepen.io/frxnz/pen/orvHd'>WTF, Transitions?</a> by Derek Wheelden (<a href='http://codepen.io/frxnz'>@frxnz</a>) on <a href='http://codepen.io'>CodePen</a></p>
<script async src="http://codepen.io/assets/embed/ei.js"></script>

# How do we deal with this?

The easiest way is to set more reasonable max-heights. Allowing for fluid content is the ultimate goal here, but in a most cases your content height isn't going to vary wildly. Using 500px instead of 1000px will improved things significantly.

The other solution is to just avoid relying on height or max-height. If one of the elements you're transitioning has fixed aspect ratio, you can use the [ol' padded box](http://daverupert.com/2012/04/uncle-daves-ol-padded-box/). In the demo above, I know the cat photo will always be square. So I can transition padding like so:

{% codeblock The Ol' Padded Box lang:css %}
event.active {
    .image-wrapper { padding-bottom: 0%; }
    .description { max-height: 25em; }
}
  
.image-wrapper {
    position: relative;
    padding-bottom: 100%;
    overflow: hidden;
    transition: padding 0.5s;
}

img {
    position: absolute;
    width: 100%;
}
{% endcodeblock %}

This is a step in the right direction, and combined with a more reasonable `max-height` on my `.description` element things are looking pretty smooth.