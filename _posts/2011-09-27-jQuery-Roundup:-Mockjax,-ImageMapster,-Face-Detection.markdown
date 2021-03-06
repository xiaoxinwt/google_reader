---
layout: post
title:  "jQuery Roundup: Mockjax, ImageMapster, Face Detection"
date:   2011-09-27 07:00:00
author: 
categories: program
---

## jQuery Roundup: Mockjax, ImageMapster, Face Detection
### by 
### at 2011-09-27 07:00:00
### original <http://feedproxy.google.com/~r/dailyjs/~3/HXZHfo0PD7Y/jquery-roundup>

<div>
<p>Note: You can send your plugins and articles in for review through our <a href="http://dailyjs.com/contact.html">contact form</a> or <a href="http://twitter.com/dailyjs">@dailyjs</a>.</p>
</div>
<h3>jQuery Mockjax</h3>
<p><a href="http://enterprisejquery.com/2010/07/mock-your-ajax-requests-with-mockjax-for-rapid-development/">jQuery Mockjax</a> (GitHub: <a href="https://github.com/appendto/jquery-mockjax">appendto / jquery-mockjax</a>, License: <em><span>MIT</span> or <span>GPL</span></em>) by Jonathan Sharp is a library for mocking jQuery’s network requests.  This can be useful when writing tests.  It can mock the same data types that jQuery supports: text, <span>HTML</span>, <span>JSON</span>, <span>JSONP</span>, Script, and <span>XML</span>.</p>
<p>Mocking a request looks a lot like the ajax <span>API</span>:</p>
<div><pre><code><span>$</span><span>.</span><span>mockjax</span><span>({</span>
  <span>url</span><span>:</span> <span>&#39;/restful/fortune&#39;</span>
<span>,</span> <span>responseTime</span><span>:</span> <span>750</span>
<span>,</span> <span>responseText</span><span>:</span> <span>{</span> <span>status</span><span>:</span> <span>&#39;success&#39;</span> <span>}</span>
<span>});</span>
</code></pre>
</div><p>Mockjax modifies jQuery using <code>$.extend({ ajax: function() { // ... } });</code> — by passing one argument to <code>$.extend</code> the jQuery object itself is modified.  The original ajax module is kept around internally and used to simulate network requests by changing jQuery’s <code>xhr</code> function.</p>
<p>As shown above, request latency can be simulated using a parameter.  There are other advanced configuration options as well, including <code>contentType</code> and <code>isTimeout</code>.</p>
<h3>ImageMapster</h3>
<p>ImageMapster (GitHub: <a href="https://github.com/jamietre/imagemapster">jamietre / ImageMapster</a>, License: <em><span>MIT</span></em>) by James Treworgy is a plugin that makes it easier to manipulate <span>HTML</span> image maps.  That means interesting effects can be created using image maps and more modern <span>HTML</span>/<span>CSS</span>/JavaScript techniques, while still falling over to the basic functionality in older browsers.  The <a href="http://www.outsharked.com/imagemapster/default.aspx?demos.html">ImageMapster demos page</a> has a lot of examples — the “Vegetable Tray” example shows off some of the basic features.</p>
<p>By calling <code>$('img').mapster(options)</code>, ImageMapster will attempt to bind to each image that has an associated map.  An area within the image map can be selected with <code>$('area').mapster('select')</code>.  The project has lots of options and features, but fortunately the <a href="http://www.outsharked.com/imagemapster/default.aspx?docs.html">ImageMapster documentation</a> is thorough.</p>
<h3>JavaScript Face Detection</h3>
<p>This isn’t a jQuery project, but some extremely interesting client-side coding: <a href="http://wesbos.com/html5-video-face-detection-canvas-javascript/">JavaScript Face Detection + Canvas + Video = HTML5 Glasses!</a>  The author, Wes Bos, has written a tutorial and the provided source (<a href="https://github.com/wesbos/HTML5-Face-Detection">wesbos / HTML5-Face-Detection</a>) for some interesting HTML5 Canvas and JavaScript that uses the <a href="https://github.com/liuliu/ccv">Core Computer Vision Library</a>.</p>
<p>The <span>CCV</span> JavaScript library uses Web Worker to parallelise computation, but Wes said:</p>
<blockquote>
<p>In the <span>CCV</span> examples, they provide a web worker example so we could do this asynchronously, but in my tests it was significantly slower.</p>
</blockquote>
<p>The <a href="http://wesbos.com/demos/html5-face-detection/">HTML5 Glasses</a> demo that comes with this tutorial doesn’t run in real time (on my computer it updates every 380-400ms), but the tracking library seems to work very well.</p><img src="http://feeds.feedburner.com/~r/dailyjs/~4/HXZHfo0PD7Y" height="1" width="1">