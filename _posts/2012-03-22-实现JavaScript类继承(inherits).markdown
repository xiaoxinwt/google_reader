---
layout: post
title:  "实现JavaScript类继承(inherits)"
date:   2012-03-22 23:45:57
author: 枯藤昏鸦
categories: program
---

## 实现JavaScript类继承(inherits)
### by 枯藤昏鸦
### at 2012-03-22 23:45:57
### original <http://ioio.name/javascript-inherits.html>

<p>简单实现了个JavaScript类继承（比下午在公司实现的要简单得多），继承prototype方法和静态方法，实际使用过程中mix函数还需要丰满一下。这东西水很深，自己也一知半解的，不多说直接放代码。</p>

<div><div><pre style="font-family:monospace"><span style="color:#003366;font-weight:bold">Function</span>.<span style="color:#660066">prototype</span>.<span style="color:#660066">method</span> <span style="color:#339933">=</span> <span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#000066">name</span><span style="color:#339933">,</span>fn<span style="color:#009900">)</span><span style="color:#009900">{</span>
	<span style="color:#000066;font-weight:bold">this</span>.<span style="color:#660066">prototype</span><span style="color:#009900">[</span><span style="color:#000066">name</span><span style="color:#009900">]</span> <span style="color:#339933">=</span> fn<span style="color:#339933">;</span>
	<span style="color:#000066;font-weight:bold">return</span> <span style="color:#000066;font-weight:bold">this</span><span style="color:#339933">;</span>
<span style="color:#009900">}</span>
<span style="color:#003366;font-weight:bold">Function</span>.<span style="color:#660066">method</span><span style="color:#009900">(</span><span style="color:#3366cc">'inherits'</span><span style="color:#339933">,</span><span style="color:#009900">(</span><span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#009900">{</span>
	<span style="color:#003366;font-weight:bold">function</span> mix<span style="color:#009900">(</span>a<span style="color:#339933">,</span>b<span style="color:#009900">)</span><span style="color:#009900">{</span>
		<span style="color:#000066;font-weight:bold">for</span><span style="color:#009900">(</span><span style="color:#003366;font-weight:bold">var</span> i <span style="color:#000066;font-weight:bold">in</span> b<span style="color:#009900">)</span><span style="color:#009900">{</span>
			<span style="color:#006600;font-style:italic">//replace</span>
			<span style="color:#000066;font-weight:bold">if</span><span style="color:#009900">(</span><span style="color:#000066;font-weight:bold">typeof</span> a<span style="color:#009900">[</span>i<span style="color:#009900">]</span> <span style="color:#339933">==</span> <span style="color:#3366cc">'undefined'</span><span style="color:#009900">)</span><span style="color:#009900">{</span>
				a<span style="color:#009900">[</span>i<span style="color:#009900">]</span> <span style="color:#339933">=</span> b<span style="color:#009900">[</span>i<span style="color:#009900">]</span><span style="color:#339933">;</span>
			<span style="color:#009900">}</span> 
		<span style="color:#009900">}</span>
	<span style="color:#009900">}</span>
	<span style="color:#000066;font-weight:bold">return</span> <span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span>Parent<span style="color:#009900">)</span><span style="color:#009900">{</span>
		<span style="color:#003366;font-weight:bold">var</span> fn <span style="color:#339933">=</span> <span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#009900">{</span><span style="color:#009900">}</span><span style="color:#339933">;</span>
		mix<span style="color:#009900">(</span><span style="color:#000066;font-weight:bold">this</span>.<span style="color:#660066">prototype</span><span style="color:#339933">,</span>Parent.<span style="color:#660066">prototype</span><span style="color:#009900">)</span><span style="color:#339933">;</span>
		mix<span style="color:#009900">(</span><span style="color:#000066;font-weight:bold">this</span><span style="color:#339933">,</span>Parent<span style="color:#009900">)</span><span style="color:#339933">;</span>
		<span style="color:#000066;font-weight:bold">this</span>.__super__ <span style="color:#339933">=</span> Parent<span style="color:#339933">;</span>
		<span style="color:#006600;font-style:italic">//static method</span>
		<span style="color:#006600;font-style:italic">//object.__super__.demo</span>
 
		<span style="color:#006600;font-style:italic">//prototype method</span>
		<span style="color:#006600;font-style:italic">//object.__super__.prototype.hidden</span>
	  <span style="color:#000066;font-weight:bold">return</span> <span style="color:#000066;font-weight:bold">this</span><span style="color:#339933">;</span>
	<span style="color:#009900">}</span><span style="color:#339933">;</span>
<span style="color:#009900">}</span><span style="color:#009900">)</span><span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#009900">)</span><span style="color:#339933">;</span>
 
 
<span style="color:#003366;font-weight:bold">var</span> a <span style="color:#339933">=</span> <span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#009900">{</span><span style="color:#009900">}</span><span style="color:#339933">;</span>
<span style="color:#003366;font-weight:bold">var</span> b <span style="color:#339933">=</span> <span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#009900">{</span><span style="color:#009900">}</span><span style="color:#339933">;</span>
<span style="color:#003366;font-weight:bold">var</span> c <span style="color:#339933">=</span> <span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#009900">{</span><span style="color:#009900">}</span><span style="color:#339933">;</span>
<span style="color:#003366;font-weight:bold">var</span> d <span style="color:#339933">=</span> <span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#009900">{</span><span style="color:#009900">}</span><span style="color:#339933">;</span>
a.<span style="color:#660066">prototype</span>.<span style="color:#660066">show</span> <span style="color:#339933">=</span> <span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#009900">{</span>
	console.<span style="color:#660066">log</span><span style="color:#009900">(</span><span style="color:#3366cc">'a show'</span><span style="color:#009900">)</span><span style="color:#339933">;</span>
<span style="color:#009900">}</span><span style="color:#339933">;</span>
b.<span style="color:#660066">prototype</span>.<span style="color:#660066">hidden</span> <span style="color:#339933">=</span> <span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#009900">{</span>
	console.<span style="color:#660066">log</span><span style="color:#009900">(</span><span style="color:#3366cc">'b hidden'</span><span style="color:#009900">)</span><span style="color:#339933">;</span>
<span style="color:#009900">}</span><span style="color:#339933">;</span>
a.<span style="color:#660066">test</span> <span style="color:#339933">=</span> <span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#009900">{</span>
	console.<span style="color:#660066">log</span><span style="color:#009900">(</span><span style="color:#3366cc">'a test'</span><span style="color:#009900">)</span><span style="color:#339933">;</span>
<span style="color:#009900">}</span><span style="color:#339933">;</span>
b.<span style="color:#660066">demo</span> <span style="color:#339933">=</span> <span style="color:#003366;font-weight:bold">function</span><span style="color:#009900">(</span><span style="color:#009900">)</span><span style="color:#009900">{</span>
	console.<span style="color:#660066">log</span><span style="color:#009900">(</span><span style="color:#3366cc">'b demo'</span><span style="color:#009900">)</span><span style="color:#339933">;</span>
<span style="color:#009900">}</span><span style="color:#339933">;</span>
 
<span style="color:#006600;font-style:italic">//usage</span>
b.<span style="color:#660066">inherits</span><span style="color:#009900">(</span>a<span style="color:#009900">)</span><span style="color:#339933">;</span>
c.<span style="color:#660066">inherits</span><span style="color:#009900">(</span>a<span style="color:#009900">)</span><span style="color:#339933">;</span>
d.<span style="color:#660066">inherits</span><span style="color:#009900">(</span>b<span style="color:#009900">)</span><span style="color:#339933">;</span></pre></div></div>

<p>-EOF-</p>
<hr><h2>Related posts:</h2><ul><li><a href="http://ioio.name/object-oriented-javascript-note-2.html" rel="bookmark" title="Permanent Link: Object-Oriented JavaScript笔记(二)">Object-Oriented JavaScript笔记(二)</a></li><li><a href="http://ioio.name/object-oriented-javascript-note-4.html" rel="bookmark" title="Permanent Link: Object-Oriented JavaScript笔记(四)">Object-Oriented JavaScript笔记(四)</a></li><li><a href="http://ioio.name/javascript-get-up-guide.html" rel="bookmark" title="Permanent Link: JavaScript 入门指南">JavaScript 入门指南</a></li><li><a href="http://ioio.name/object-oriented-javascript-note-7.html" rel="bookmark" title="Permanent Link: Object-Oriented JavaScript笔记(七)">Object-Oriented JavaScript笔记(七)</a></li><li><a href="http://ioio.name/object-oriented-javascript-note-5.html" rel="bookmark" title="Permanent Link: Object-Oriented JavaScript笔记(五)">Object-Oriented JavaScript笔记(五)</a></li></ul><hr><small>Copyright © 2005~2011 | <a href="http://ioio.name/javascript-inherits.html" title="Permalink">Permalink</a> | <a href="http://ioio.name/javascript-inherits.html#comments">0 Comments</a> | <a href="http://closetou.com" title="Close To U">Close To U</a> <br>
<a href="http://feeds.feedburner.com/miss">订阅</a> <a href="https://twitter.com/tearnon">Twitter</a> <a href="http://ioio.name/godaddy">域名优惠码</a> <a href="http://ioio.name/mt">Media Temple空间</a>
<a href="http://mdiatemple.com/" title="Domain Sale! $6.89 .com at GoDaddy">主机/域名优惠码</a>
</small> )