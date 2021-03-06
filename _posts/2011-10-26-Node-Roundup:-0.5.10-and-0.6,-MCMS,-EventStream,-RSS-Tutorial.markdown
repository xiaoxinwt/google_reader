---
layout: post
title:  "Node Roundup: 0.5.10 and 0.6, MCMS, EventStream, RSS Tutorial"
date:   2011-10-26 07:00:00
author: 
categories: program
---

## Node Roundup: 0.5.10 and 0.6, MCMS, EventStream, RSS Tutorial
### by 
### at 2011-10-26 07:00:00
### original <http://feedproxy.google.com/~r/dailyjs/~3/h987OlfmwzM/node-roundup>

<div>
<p>You can send your node modules and articles in for review through our <a href="http://dailyjs.com/contact.html">contact form</a> or <a href="http://twitter.com/dailyjs">@dailyjs</a>.</p>
</div>
<h3>0.5.10 and 0.6</h3>
<p><a href="http://blog.nodejs.org/2011/10/21/node-v0-5-10/">Node 0.5.10</a> has been released.  This version changes the build system, adds performance improvements, and it looks like there’s been a lot of documentation improvements.</p>
<p><a href="http://blog.nodejs.org/2011/10/25/version-0-6/">Node 0.6</a> was also formally announced on the official Node blog:</p>
<blockquote>
<p>The <span>API</span> changes between v0.4.12 and v0.5.10 are 99% cosmetic, minor, and easy to fix. Most people are able to migrate their code in 10 minutes. Don’t fear.</p>
</blockquote>
<p>Sounds good to me!</p>
<h3><span>MCMS</span></h3>
<p><a href="https://github.com/olegp/mcms"><span>MCMS</span></a> (npm: <em>mcms</em>, License: <em><span>MIT</span></em>) by Oleg Podsechin is a content management system built with <a href="http://olegp.github.com/common-node/">Common Node</a>.  Actually, Common Node is just part of the story:</p>
<blockquote>
<p>By being built on top of the CommonJS Filesystem/A and <span>JSGI</span> 0.3 specs, it runs on multiple server side JavaScript platforms, such as Node.js (via Common Node) and RingoJS.</p>
</blockquote>
<p>Oleg has provided installation instructions for both Node and RingoJS.</p>
<p><span>MCMS</span> is more like a static site generator than a traditional <span>CMS</span>, and serves as an interesting example of how to build something using Common Node while still being able to deploy to other CommonJS-compatible environments.</p>
<h3>EventStream</h3>
<blockquote>
<p>Streams are like Arrays, but laid out in time, rather than in memory.</p>
</blockquote>
<p><a href="https://github.com/dominictarr/event-stream">EventStream</a> (npm: <em>event-stream</em>) by Dominic Tarr is a library for manipulating “through streams”, which is the author’s terms for streams that are both readable and writable:</p>
<div><pre><code><span>// pretty.js</span>

<span>if</span> <span>(</span><span>!</span><span>module</span><span>.</span><span>parent</span><span>)</span> <span>{</span>
  <span>var</span> <span>es</span> <span>=</span> <span>require</span><span>(</span><span>&#39;event-stream&#39;</span><span>);</span>

  <span>es</span><span>.</span><span>connect</span><span>(</span>                          <span>// connect streams together with `pipe`</span>
    <span>process</span><span>.</span><span>openStdin</span><span>(),</span>               <span>// open stdin</span>
    <span>es</span><span>.</span><span>split</span><span>(),</span>                        <span>// split stream to break on newlines</span>
    <span>es</span><span>.</span><span>map</span><span>(</span><span>function</span><span>(</span><span>data</span><span>,</span> <span>callback</span><span>)</span> <span>{</span> <span>// turn this async function into a stream</span>
      <span>callback</span><span>(</span><span>null</span>
        <span>,</span> <span>inspect</span><span>(</span><span>JSON</span><span>.</span><span>parse</span><span>(</span><span>data</span><span>)))</span>   <span>// render it nicely</span>
    <span>}),</span>
    <span>process</span><span>.</span><span>stdout</span>                     <span>// pipe it to stdout!</span>
  <span>);</span>
<span>}</span>

<span>// On the terminal:</span>
<span>// curl -sS registry.npmjs.org/event-stream | node pretty.js</span>
</code></pre>
</div><p>The library provides many interesting ways to enhance native objects and functions with streams.  The author has even created a list of compatible Node modules, which includes popular modules like <a href="https://github.com/isaacs/sax-js">sax-js</a> and <a href="https://github.com/mikeal/request">request</a>.</p>
<h3><span>RSS</span> with NodeJS and Socket.IO</h3>
<p>French-speaking readers may appreciate <a href="http://www.it-wars.com/article290/rss-avec-nodejs-socket-io-et-express"><span>RSS</span> with NodeJS Socket.IO and Express</a> which is a full tutorial on how to build an <span>RSS</span> reader with Express and Socket.IO.  I had a go at reading it with Google Translate, and I was able to follow it (for the most part).</p><img src="http://feeds.feedburner.com/~r/dailyjs/~4/h987OlfmwzM" height="1" width="1">