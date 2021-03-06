---
layout: post
title:  "jQuery Roundup: CraftMap, Scrollorama, Plugins Archive"
date:   2012-01-03 08:00:00
author: 
categories: program
---

## jQuery Roundup: CraftMap, Scrollorama, Plugins Archive
### by 
### at 2012-01-03 08:00:00
### original <http://feedproxy.google.com/~r/dailyjs/~3/D5OCdUID28k/jquery-roundup>

<div>
Note: You can send your plugins and articles in for review through our <a href="http://dailyjs.com/contact.html">contact form</a> or <a href="http://twitter.com/dailyjs">@dailyjs</a>.
</div>
<h3>CraftMap</h3>

<p><img src="http://dailyjs.com/images/posts/craftmap.png" alt="CraftMap"></p>

<p><a href="http://www.jscraft.net/plugins/craftmap.html">CraftMap</a> (License: <em>free for non-commercial use</em>, Price: $49, Size: 13KB, Minified: 6.5KB) by Marcin Dziewulski is a lightweight plugin that turns an image into a map, complete with overlays and markers.</p>

<p>The plugin comes with lots of options, including saving position to cookies, map positioning, and controls.</p>

<p>Marcin has also written a whole slew of other plugins:</p>

<ul>
<li><a href="http://www.jscraft.net/plugins/scroller.html">Scroller</a> (License: <em>free for non-commercial use</em>, Price: $24, Size: 5KB, Minified: 2.7KB)</li>

<li><a href="http://www.jscraft.net/experiments/css-3d-image-gallery.html">CSS 3D Gallery</a></li>

<li><a href="http://www.jscraft.net/experiments/custom-preloader-effect.html">Custom Preloader Effect</a></li>

<li><a href="http://www.jscraft.net/experiments/horizontal-bar-graph-with-css3-and-jquery.html">Horizontal Bar Graph with CSS3</a></li>
</ul>

<h3>Scrollorama</h3>

<p><img src="http://dailyjs.com/images/posts/scrollorama.png" alt="Scrollorama"></p>

<p><a href="http://johnpolacek.github.com/scrollorama/">Scrollorama</a> (GitHub: <a href="https://github.com/johnpolacek/scrollorama">johnpolacek / scrollorama</a>, License: <em>MIT and GPL</em>, Size: 9.63KB) by John Polacek is a plugin for controlling animations as a page is scrolled. Once a page is split into blocks, events can be triggered as blocks become visible:</p>
<div><pre><code><span>var</span> <span>scrollorama</span> <span>=</span> <span>$</span><span>.</span><span>scrollorama</span><span>({</span>
  <span>blocks</span><span>:</span> <span>&#39;.scrollblock&#39;</span>
<span>});</span>

<span>scrollorama</span><span>.</span><span>onBlockChange</span><span>(</span><span>function</span><span>()</span> <span>{</span>
  <span>alert</span><span>(</span><span>&#39;You just scrolled to block#&#39;</span> <span>+</span> <span>scrollorama</span><span>.</span><span>blockIndex</span><span>);</span>
<span>});</span>
</code></pre>
</div>
<p>Elements can be animated, like the examples on Scrollorama’s site:</p>
<div><pre><code><span>scrollorama</span><span>.</span><span>animate</span><span>(</span><span>&#39;#example1&#39;</span><span>,</span> <span>{</span>
  <span>duration</span><span>:</span> <span>400</span><span>,</span> <span>property</span><span>:</span> <span>&#39;opacity&#39;</span>
<span>});</span>
</code></pre>
</div>
<h3>Plugins Archive</h3>

<p>After the official jQuery plugin site was shelved, the old content has now been reinstated at <a href="http://archive.plugins.jquery.com/">archive.plugins.jquery.com</a>.</p>

<p>The new site is open source and can be found at <a href="https://github.com/jquery/plugins.jquery.com">GitHub: jquery / plugins.jquery.com</a>. It can be installed locally, and requires a web server, PHP, MySQL, WordPress, Node, and Git.</p><img src="http://feeds.feedburner.com/~r/dailyjs/~4/D5OCdUID28k" height="1" width="1">