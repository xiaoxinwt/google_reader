---
layout: post
title:  "WebGL Examples, Backbone Forms, Third-Party JavaScript"
date:   2011-09-23 07:00:00
author: 
categories: program
---

## WebGL Examples, Backbone Forms, Third-Party JavaScript
### by 
### at 2011-09-23 07:00:00
### original <http://feedproxy.google.com/~r/dailyjs/~3/9HyvM4eC_k4/webgl-backboneforms-thirdpartyjs>

<h3>WebGL Examples</h3>
<p><img src="http://dailyjs.com/images/posts/cycleblob.png" alt=""></p>
<p>I found this collection of <a href="http://www.ibiblio.org/e-notes/webgl/webgl.htm">WebGL examples</a> at <a href="http://www.ibiblio.org">ibiblio.org</a> that includes a few things I haven’t seen before, particularly <a href="http://cycleblob.com/">Cycleblob</a> which is an interesting take on the Tron light cycles concept.</p>
<h3>Backbone Forms</h3>
<p><a href="https://github.com/powmedia/backbone-forms">Backbone Forms</a> by Charles Davison is a form framework for <a href="http://documentcloud.github.com/backbone/">Backbone.js</a> with some advanced features like nested forms, and it even comes with <span>CSS</span> for styling fields.</p>
<p>This library makes it possible to automatically generate forms based on model instances:</p>
<div><pre><code><span>var</span> <span>form</span> <span>=</span> <span>new</span> <span>Backbone</span><span>.</span><span>Form</span><span>({</span>
  <span>model</span><span>:</span> <span>users</span><span>.</span><span>get</span><span>(</span><span>userId</span><span>)</span>
<span>}).</span><span>render</span><span>();</span>
</code></pre>
</div><p>If a model isn’t available, a <code>Backbone.Form</code> can be instantiated using <code>data</code> and <code>schema</code> properties:</p>
<div><pre><code><span>new</span> <span>Backbone</span><span>.</span><span>Form</span><span>({</span>
  <span>data</span><span>:</span> <span>{</span> <span>id</span><span>:</span> <span>123</span><span>,</span> <span>name</span><span>:</span> <span>&#39;Rod Kimble&#39;</span><span>,</span> <span>password</span><span>:</span> <span>&#39;cool beans&#39;</span> <span>},</span>
  <span>schema</span><span>:</span> <span>{</span>
    <span>id</span><span>:</span>       <span>{</span> <span>type</span><span>:</span> <span>&#39;Number&#39;</span> <span>},</span>
    <span>name</span><span>:</span>     <span>{},</span>
    <span>password</span><span>:</span> <span>{</span> <span>type</span><span>:</span> <span>&#39;Password&#39;</span> <span>}</span>
  <span>}</span>
<span>}).</span><span>render</span><span>();</span>
</code></pre>
</div><p>The source is documented and the <span>README</span> has enough examples to get started.</p>
<h3>Third-Party JavaScript</h3>
<p><a href="http://thirdpartyjs.com/">Third-Party JavaScript</a> by Ben Vinegar and Anton Kovalyov (both from Disqus) is a book about the art of third-party scripting — writing JavaScript intended to be executed on a remote site.  It’s aimed at intermediate developers, and covers distributing and loading applications, server communication, cross-domain iframe messaging, rendering <span>HTML</span> and <span>CSS</span>, authentication, security, and performance.  Considering the authors work at Disqus it seems like a potentially essential book on the subject.</p>
<p>The book will be published by Manning, and an “early access” edition can be purchased from $35.99.  Three chapters are available so far, and the example source code is on GitHub at <a href="https://github.com/benvinegar/thirdpartyjs-code">benvinegar / thirdpartyjs-code</a>.  The first chapter is available for free: <a href="http://manning.com/vinegar/TPJS_meap_ch01.pdf">Introduction to Third-Party JavaScript</a> (<span>PDF</span>).</p>
<p>To pre-order the book, visit <a href="http://manning.com/vinegar/">Third-Party JavaScript at Manning’s site</a>.</p><img src="http://feeds.feedburner.com/~r/dailyjs/~4/9HyvM4eC_k4" height="1" width="1">