---
layout: post
title:  "jQuery Roundup: 1.6.3, lccache, Storagify"
date:   2011-09-06 07:00:00
author: 
categories: program
---

## jQuery Roundup: 1.6.3, lccache, Storagify
### by 
### at 2011-09-06 07:00:00
### original <http://feedproxy.google.com/~r/dailyjs/~3/88SIwQaD40U/jquery-roundup>

<div>
<p>Note: You can send your plugins and articles in for review through our <a href="http://dailyjs.com/contact.html">contact form</a> or <a href="http://twitter.com/dailyjs">@dailyjs</a>.</p>
</div>
<h3>jQuery 1.6.3</h3>
<p><a href="http://blog.jquery.com/2011/09/01/jquery-1-6-3-released/">jQuery 1.6.3</a> has been released, not long after RC1.  This version fixes a much discussed <span>XSS</span> attack, animation browser tab switchiing issues, and better handling of HTML5 attribute names.</p>
<h3>lccache</h3>
<p><a href="https://github.com/JohnMunsch/lccache">lccache</a> (License: <em>Apache 2.0</em>) by Pamela Fox and John Munsch emulates memcache for client-side caching.  John Munsch’s port uses <a href="http://westcoastlogic.com/lawnchair/">Lawnchair</a> instead of HTML5 Local Storage, which means it should work in older browsers.</p>
<p>The library has three methods: <code>set</code>, <code>get</code> and <code>remove</code>:</p>
<div><pre><code><span>//          key,        value,          time/expiration</span>
<span>lccache</span><span>.</span><span>set</span><span>(</span><span>&#39;greeting&#39;</span><span>,</span> <span>&#39;Hello World!&#39;</span><span>,</span> <span>2</span><span>);</span>

<span>lccache</span><span>.</span><span>get</span><span>(</span><span>&#39;greeting&#39;</span><span>);</span>
<span>// Returns &#39;Hello World!&#39;</span>

<span>// Objects can also be stored</span>
<span>lccache</span><span>.</span><span>set</span><span>(</span><span>&#39;data&#39;</span><span>,</span> <span>{</span> <span>&#39;greeting&#39;</span><span>:</span> <span>&#39;Hello World!&#39;</span> <span>},</span> <span>2</span><span>);</span>
<span>lccache</span><span>.</span><span>get</span><span>(</span><span>&#39;data&#39;</span><span>).</span><span>greeting</span><span>;</span>
</code></pre>
</div><p>Pamela’s real world examples included developing against <span>JSON</span> APIs on unreliable networks.  The most obvious example is a mobile web app.  I’d also consider it for single page apps, because it makes knowing when to refresh data that might change on the server a lot easier.</p>
<h3>Storagify</h3>
<p><a href="http://ek.alphaschildren.org/resources/jquery-plugins/storagify/">Storagify</a> (GitHub: <a href="https://github.com/ekdevdes/Storagify">ekdevdes / Storagify</a>, License: <em><span>MIT</span>/<span>GPL</span></em>) by Ethan Kramer combines HTML5 <code>contenteditable</code> with Local Storage to make potentially any element editable.  Calling <code>$('selector').storagify('storageKey');</code> makes the element editable, and changes will be visible after a page refresh.</p>
<p>Now imagine combining this with a simple <span>JSON</span> <span>API</span> and you’ve got yourself a <span>CMS</span> in minutes!</p><img src="http://feeds.feedburner.com/~r/dailyjs/~4/88SIwQaD40U" height="1" width="1">