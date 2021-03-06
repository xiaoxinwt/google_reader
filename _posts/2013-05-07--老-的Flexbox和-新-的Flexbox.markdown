---
layout: post
title:  "“老”的Flexbox和“新”的Flexbox"
date:   2013-05-07 16:21:16
author: Airen
categories: program
---

## “老”的Flexbox和“新”的Flexbox
### by Airen
### at 2013-05-07 16:21:16
### original <http://www.w3cplus.com/css3/old-flexbox-and-new-flexbox.html>

<div><div><div><blockquote>
	<p>本文由<a href="http://www.w3cplus.com">大漠</a>根据<a href="http://chriscoyier.net" rel="external nofollow">Chris Coyier</a>的《<a href="http://css-tricks.com/old-flexbox-and-new-flexbox">“Old” Flexbox and “New” Flexbox</a>》所译，整个译文带有我们自己的理解与思想，如果译得不好或不对之处还请同行朋友指点。如需转载此译文，需注明英文出处：<a href="http://css-tricks.com/old-flexbox-and-new-flexbox">http://css-tricks.com/old-flexbox-and-new-flexbox</a>，以及作者相关信息</p>
	<p style="text-align:right">——作者：<a href="http://chriscoyier.net" rel="external nofollow">Chris Coyier</a></p>
	<p style="text-align:right">——译者：<a href="http://www.w3cplus.com">大漠</a></p>
</blockquote>
<p>大家都清楚的知道:“Flexbox”（全称：CSS Flexible Box Layout Module）在过去的三年中经历了许多变化。变化都达到了<a href="http://dev.w3.org/csswg/css-flexbox/">规范</a>以及什么样的<a href="http://caniuse.com/#feat=flexbox">浏览器支持Flexbox</a>。</p>
<h2>
	如何辨别</h2>
<p>如果你使用<a href="http://www.google.com">google</a>搜索<a href="http://www.w3cplus.com/">Flexbox</a>，你会发现很多过时的信息。这里将告诉你如何迅速的辨别你需要的信息。</p>
<p>如果你正在查找有关于Flexbox的博客资料，你看到了“display:box;”或者“box-{*}”属性，那么你看的正是2009年版本的Flexbox。</p>
<p>如果你正在查找有关于Flexbox的博客资料，你看到了“display:flexbox;”或者“flex()”函数，那么你看的正是2011年版本的Flexbox。</p>
<p>如果你正在查找有关于Flexbox的博客资料，你看到了“display:flex;”和“flex-{*}”属性，那么你查看的是当前（在写此文时）的规范。</p>
<h2>
	过时的教程与例子</h2>
<p>这些东西在创建的时候都非常的棒，但对于现在而言有些过时。</p>
<p><a href="http://flexiejs.com/">Flexie</a>——一个javascript脚本，使用的是2009年的旧版本语法。</p>
<p>2011年<a href="http://coding.smashingmagazine.com/author/richard-shepherd/?rel=author" rel="author" title="Posts by Richard Shepherd">Richard Shepherd</a>在<a href="http://www.smashingmagazine.com/">Smashingmagazine.com</a>写了篇<a href="http://coding.smashingmagazine.com/2011/09/19/css3-flexible-box-layout-explained/">文章</a>。文章附带的提到了2011年版本的语法，但更侧重于2009年旧版本的语法。</p>
<p><a href="http://www.the-haystack.com/">Stephen Hay</a>早前就写了一篇有关于Flexbox的教程，他的<a href="http://www.the-haystack.com/2010/01/23/css3-flexbox-part-1">这篇教程</a>介绍的也是2009年老版本语法，随后他对<a href="http://www.the-haystack.com/2012/01/04/learn-you-a-flexbox/">2011版本Flexbox</a>也做了一个跟踪，并且分享了出来。</p>
<p>我第一次接触Flexbox是来自于<a href="http://paulirish.com/about/">Paul Irish</a>分享的<a href="http://www.html5rocks.com/en/tutorials/flexbox/quick/">教程</a>，他使用的是2009年语法版本。顶部介绍了Flexbox的特性，并且链接到<a href="http://www.the-haystack.com/">Stephen Hay</a>介绍2011年语法版本的<a href="http://www.the-haystack.com/2012/01/04/learn-you-a-flexbox">文章</a>中。</p>
<h2>
	支持</h2>
<p><a href="http://caniuse.com/#feat=flexbox">浏览器支持条款</a>变得有点复杂。</p>
<p>2009年旧版本语法得到浏览器较好的支持：Chrome、Firefox2+、Safari3.1+ ...除IE9和Opera几乎所有浏览器都支持。我所说的“支持”，实际的实现与支持会有点不同（因此，有些需要重写）。</p>
<p>尽管旧的语法得到很好的支持，但使用旧的语法并不是一个聪明的做法。旧的规范始终要淘汰。浏览器在未来有可能还会支持旧的语法。至少，新的语法更容易理解和更深入、更一致的实现效果。浏览器不支持Flexbox新的规范，主要是因为他在CR状态，当他成为规范时，会得到浏览器完美支持。</p>
<p>新版本语法支持的浏览器：Chrome21+、Opera（Opera Mobile12.1+和Blackberry10+）。</p>
<p>在IE10中将运行Flexbox的中间版本（2011年版本）：display: flexbox;</p>
<h2>
	DEMO</h2>
<p>我最近正帮助别人解决一个创建Fluid-Fixed-Fluid的布局。两个语法都可以轻松的做到这一点。他们都充分的对浏览器做了测试。</p>
<p><a href="http://codepen.io/chriscoyier/pen/DLikE">旧语法案例</a> <a href="http://codepen.io/chriscoyier/pen/qazmI">新语法案例</a></p>
<p><strong>译者手语：</strong>整个翻译依照原文线路进行，并在翻译过程略加了个人对技术的理解。如果翻译有不对之处，还烦请同行朋友指点。谢谢！</p>
<p>如需转载烦请注明出处：</p>
<p>英文原文：<a href="http://css-tricks.com/old-flexbox-and-new-flexbox">http://css-tricks.com/old-flexbox-and-new-flexbox</a></p>
<p>中文译文：<a href="http://www.w3cplus.com/css3/old-flexbox-and-new-flexbox.html">http://www.w3cplus.com/css3/old-flexbox-and-new-flexbox.html</a></p>
</div></div></div><div><ul><li><a href="http://www.w3cplus.com/blog/tags/308.html">译文</a></li></ul></div><div><ul><li><a href="http://www.w3cplus.com/blog/tags/157.html">flexbox</a></li><li><a href="http://www.w3cplus.com/blog/tags/69.html">CSS3</a></li></ul></div><div><div><div><div>
      <div>14</div>
                  <a href="http://www.w3cplus.com/vote/node/751/1/vote/alternate/3RiRFwYV3n_O5S15AqeKKLmHAx79aCnBZx9Zwj8Jm4c/nojs" rel="nofollow">
                <div title="Vote up!"></div>
          <div>Vote up!</div>
              </a>
                </div>
</div></div></div><img src="http://www1.feedsky.com/t1/735580178/W3CPlus/feedsky/s.gif?r=http://www.w3cplus.com/css3/old-flexbox-and-new-flexbox.html" border="0" height="0" width="0">