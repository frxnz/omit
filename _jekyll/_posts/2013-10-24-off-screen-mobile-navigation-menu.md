---
layout: post
title:  "Off-Screen Mobile Navigation Menu"
subtitle: "A simple pattern for full-height off-canvas mobile nav."
date:   2013-10-24 21:38:21
---

## This thing is so much easier than it looks

<p>Let&rsquo;s just start off with the demo, because I couldn&rsquo;t think of a good way to describe this in a post title, so you might not have any idea what I&rsquo;m talking about. If you don&rsquo;t like reading just <a href="http://codepen.io/frxnz/pen/ptoeu">skip to the Pen</a>.</p>

<p data-height="600" data-theme-id="0" data-slug-hash="ptoeu" data-user="frxnz" data-default-tab="result" class='codepen'>See the Pen <a href='http://codepen.io/frxnz/pen/ptoeu'>ptoeu</a> by Derek Wheelden (<a href='http://codepen.io/frxnz'>@frxnz</a>) on <a href='http://codepen.io'>CodePen</a></p>


<script async src="//codepen.io/assets/embed/ei.js"></script>


<p>So the blue box represents the edges of your mobile viewport. Everything <em>inside</em> the box you see, everything else you don&rsquo;t.</p>

<p>When you click the menu button (the guy with the three horizontal lines), the menu slides into the box, and some of the content slides out. Same deal, everything inside the box is visible, everything outside is not.</p>

<h2>Let&rsquo;s dig into the code</h2>

<h3>HTML</h3>

<p>The HTML is easy. You just use your standard nav markup. This is extremely flexible, basically the only requirements are the outer-wrapper and a nav container. The outer wrapper <em>must</em> wrap all of your body markup.</p>

<pre title="HTML"><code class="language-markup">
&lt;body>
    &lt;div class="#outer-wrapper">
        &lt;!-- Header Code, Whatever -->
        &lt;nav role='navigation'>
            &lt;ul>
                &lt;li>&lt;a href="#">Home&lt;/a>&lt;/li>
                &lt;li>&lt;a href="#">About&lt;/a>&lt;/li>
                &lt;li>&lt;a href="#">Clients&lt;/a>&lt;/li>
                &lt;li>&lt;a href="#">Contact Us&lt;/a>&lt;/li>
            &lt;/ul>
            &lt;a href="#" id="nav-toggle">&lt;/a>
        &lt;/nav>
        &lt;!-- Any other markup -->
    &lt;/div> &lt;!-- // #outer-wrapper -->
&lt;/body>
</code></pre>


<h3>JavaScript</h3>

<p>The JavaScript is also very easy. Basically all you&rsquo;re doing here is toggling a class on your outer wrapper, which will make your CSS transition fire.</p>

<pre title="JavaScript"><code class="language-javascript">
$('#nav-toggle').on('click', function(e) {
    $('#outer-wrapper').toggleClass('nav-open');

    // If you used an anchor tag for your menu button
    e.preventDefault();
});
</code></pre>


<h3>CSS</h3>

<p>This is where the magic happens. There are also a few more picky details here, but it&rsquo;s still fairly simple. Below are the minimum styles you need to make this work, you can certainly add to this.</p>

<pre title="SCSS"><code class="language-scss">
$nav-width: 20em; // Can be em, px, %, rem

#outer-wrapper {
    position: relative;
    min-height: 100%;
    transition: transform 0.25s;
    -webkit-backface-visibility: hidden; // Chrome/OSX bug fix

    &.nav-open {
        transform: translate3d($nav-width, 0, 0);
    }
}

nav {
    position: absolute;
    top: 0;
    left: -$nav-width;
    width: $nav-width;
    min-height: 100%;
}
</code></pre>


<p>This should be pretty straight-forward, which just a couple important pieces&hellip;</p>

<h4>$nav-width</h4>

<p>This is really the pickiest part of the whole deal. We use this in three places: <code>transform</code>, <code>left</code>, and <code>width</code>. The value itself isn&rsquo;t super important (it should be less than the container width, is all), but it&rsquo;s very important that all three values be the same (<code>left</code> being negative). <code>width</code> defines the width, <code>left</code> takes the nav off-screen, and <code>transform</code> moves the outer-wrapper enough to show the whole nav on screen.</p>

<h4>translate3d</h4>

<p>The magic of <code>transform</code> is that it moves things without affecting the layout of the page. So when we translate the entire page to the right, we don&rsquo;t get scrollbars or anything weird. It just repaints everything <code>$nav-width</code> to the right.</p>

<p>You might be thinking &ldquo;wouldn&rsquo;t <code>transform: translateX($nav-width)</code> make more sense?&rdquo; The answer is&hellip;sort of. <code>translateX</code> is less verbose and more straight-forward than <code>translate3d</code>, but there&rsquo;s a very good reason for doing it this way: <strong>hardware acceleration</strong>. Mobile browsers aren&rsquo;t the most high-performance things in the world, and 2D transforms can often look pretty choppy. But 3D transforms like <code>translate3d</code> get the GPU involved, and the hardware accelartion makes things oh so smooth.</p>

<h2>And that&rsquo;s it</h2>

<p>Hopefully this makes sense, and if you want to dig into the code more <a href="http://codepen.io/frxnz/pen/ptoeu">go over to the Pen</a> and play around with it. The code is surprisingly flexible.</p>
