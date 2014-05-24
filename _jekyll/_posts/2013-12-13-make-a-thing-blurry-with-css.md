---
layout: post
title:  "Make a Thing Blurry With CSS"
subtitle: "But really you probably shouldn’t for now."
date:   2013-12-13 21:38:21
---

## Disclaimer: This Isn&rsquo;t Going to be Great

<p>Making things blurry with CSS is a thing we can do now. That&rsquo;s pretty awesome. But there are some pitfalls that make it kind of iffy. Browser support for straight-up CSS blurring (<code>filter: blur(10px)</code>) is pretty terrible. Like webkit-only terrible. And when it does work, it doesn&rsquo;t look great. Transitions are also a little janky. Personally, I don&rsquo;t think this guy is ready for primetime.</p>

<p>That said, it doesn&rsquo;t look terrible on text, and there are ways to improve browser support. If you need to blur some stuff <em>today</em>, here&rsquo;s what you can do.</p>

<h2>First, a demo!</h2>

<p data-height="300" data-theme-id="0" data-slug-hash="bmAqI" data-user="frxnz" data-default-tab="result" class='codepen'>See the Pen <a href='http://codepen.io/frxnz/pen/bmAqI'>bmAqI</a> by Derek Wheelden (<a href='http://codepen.io/frxnz'>@frxnz</a>) on <a href='http://codepen.io'>CodePen</a></p>


<script async src="//codepen.io/assets/embed/ei.js"></script>


<p>So this is kind of the cleanest implementation of <code>blur</code>, and it also highlights all of the weaknesses.</p>

<ol>
<li>It only works in webkit.</li>
<li>The transitions aren&rsquo;t smooth at all.</li>
<li>If you watch edges of the photos, you&rsquo;ll notice they look pretty bad.</li>
</ol>


<h2>A little background on filters</h2>

<p>So <code>filter: blur()</code> actually applies the SVG filter feGaussianBlur to whatever element you apply it to. This is important to note because it will actually help us deal with browser support.</p>

<p>Newer versions of Firefox and IE(10+) support both the <code>filter</code> property and SVG. They just don&rsquo;t really have an implementation that combines them the way webkit does. So let&rsquo;s look at how we can use this to improve browser support for the blur filter.</p>

<h2>SVG in markup, kinda gross</h2>

<p>You&rsquo;ve probably seen SVG inside an HTML document. It&rsquo;s a great way to add scalable vector graphics to your page. It just so happens that you can also define SVG filters in your markup, and then apply them to another element using CSS. Confused? Let&rsquo;s look at some code.</p>

<pre title="SVG Markup"><code class="language-markup">
&lt;svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">
    &lt;filter id="svgBlur">
        &lt;feGaussianBlur stdDeviation="5" />
    &lt;/filter>
&lt;/svg>
</code></pre>


<p>There are 2 things you want to pay attention to here: <code>id="svgBlur"</code> and <code>stdDeviation="5"</code>. The <code>ID</code> is how you&rsquo;re going to reference the SVG filter in your CSS later. The <code>stdDeviation</code> is the radius of your blur, in pixels.</p>

<pre title="CSS"><code class="language-css">
img {
    filter: url(#svgBlur);
}
</code></pre>


<p>Pretty simple here. You&rsquo;re just referencing the <code>ID</code> of the SVG filter you put in your markup.</p>

<h3>Things to note&hellip;</h3>

<p>It&rsquo;s kind of gross to put display code in your markup. That&rsquo;s what CSS for, and it&rsquo;s just not great for code maintenance. It also makes it a little more difficult to make adjustments. If you decide you want a 10px blur instead of 5px, you have to crack open your markup file and change it in there. You also have to include that SVG markup on every page you want to use the filter.</p>

<h2>But hey what about Data URIs</h2>

<p>You can improve on the SVG markup approach using Data URIs. This improves maintenance a little, but it still feels out of place. Anyway, here&rsquo;s what that would look like:</p>

<pre title="SVG Data URI"><code class="language-css">
img {
    filter: url(data:image/svg+xml,&lt;svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink">&lt;filter id="svgBlur">&lt;feGaussianBlur stdDeviation="5" />&lt;/filter>&lt;/svg>);
}
</code></pre>


<p>It&rsquo;s long, but it&rsquo;s all in one place. If you&rsquo;re using blur all over the place your CSS file will start to get pretty large, but you could do worse. You could even clean it up quite a bit with a little Sass or LESS.</p>

<h2>There&rsquo;s also one other way&hellip;</h2>

<p>There is another way to include SVG filters in CSS, but I&rsquo;m not going to go too far into it. Essentially you would create a file &mdash; say <code>filters.svg</code> &mdash; that would contain all of the filters you want to use, with IDs on each of them. You would then reference that filter in your CSS like <code>filter: url(filters.svg#blur)</code>. Honestly I think Data URIs are probably the way to go if you need to support more than webkit.</p>

<h1>So We&rsquo;re Good Then Right?</h1>

<p>While we&rsquo;ve improved our browser support pretty significantly, we haven&rsquo;t changed the way the filter itself behaves at all. All of the quirks still exist, they just exist in more browsers.</p>

<p>Personally I would wait until <code>filter: blur()</code> gets better support, and I would wait until the filter itself works a little better, especially if you need transitions. But if you have to have CSS blur <em>right now</em>, it can be done.</p>
