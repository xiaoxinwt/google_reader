---
layout: post
title:  "Unobtrusive Ajax 的两种境界"
date:   2010-10-12 22:56:00
author: 鹤冲天
categories: program
---

## Unobtrusive Ajax 的两种境界
### by 鹤冲天
### at 2010-10-12 22:56:00
### original <http://www.cnblogs.com/ldp615/archive/2010/10/12/unobtrusive-ajax.html>

<p><a href="http://www.cnblogs.com/ldp615/"><img src="http://pic.cnblogs.com/face/u80686.jpg" alt="" border="0"></a><br>作者: <a href="http://www.cnblogs.com/ldp615/">鹤冲天</a> 发表于 2010-10-12 22:56 <a href="http://www.cnblogs.com/ldp615/archive/2010/10/12/unobtrusive-ajax.html">原文链接</a> 阅读: 4 评论: 0</p><p>王国维在《人间词话》说：古今之成大事业、大学问者，必经过三种之境界：</p>  <ol>   <li>昨夜西风凋碧树。独上高楼，望尽天涯路。 </li>    <li>衣带渐宽终不悔，为伊消得人憔悴。 </li>    <li>众里寻他千百度，蓦然回首，那人却在，灯火阑珊处。 </li> </ol>  <p>类似，在 Web 中使用 Ajax 也有境界之分，这里境界不指技能的高低，而是思想的成熟。</p>  <h1>Unobtrusive Ajax</h1>  <p>有一片关于 Unobtrusive Ajax 的著名文章，是 <a href="http://www.thefutureoftheweb.com/">Jesse Skinner</a> （80后，3~4岁就玩电脑）在06年10月发表的：<a href="http://www.thefutureoftheweb.com/talks/2006-10-ajax-experience/slides/">http://www.thefutureoftheweb.com/talks/2006-10-ajax-experience/slides/</a> 。</p>  <p>文中指出应当将 JavaScript （或其它脚本）从 Html 中分离出来，就像最初将样式从 Html 中分离出来一样道理。简单来说就是将嵌入在 Html 中的 JavaScript 全部取出来，放在单独的 js 文件中，页面中不要出现任何 onclick、onload 等。</p>  <p>使用 Unobtrusive Ajax 我们最大的收益就是简单易于维护的代码（Code is cleaner and easier to maintain）。</p>  <p><a href="http://www.thefutureoftheweb.com/">Jesse Skinner</a> 将脚本分离（Separation）分成了两种：Physical Separation 和 Conceptual Separation 两种，也就是标题中所提的两种境界。如果没有进行脚本分离，只能算是个初学者，技术再高也谈不上境界。</p>  <h1>Physical Separation</h1>  <p>代码中没有任何 onclick、onload，就算达到这一境界了。</p>  <p>技术上比较容易实现，通过 jQuery 或其它 JavaScript Library 我们很容易进行做到：</p>  <pre>$('#abc').click(function(){
    <span style="color:#008000">//...</span>
});</pre>

<p>这种分离，简化了页面，提高了页面代码的可读性。能做到这一步，是思想上的一种进步，也是 Web 开发开始走向成熟的第一步。</p>

<p>但这种分离只是初步的，只是将 JavaScript 移了下位置，并没有从根本上改变 HTML 和 JavaScript 之间的关系。</p>

<h1>Conceptual Separation</h1>

<p>这个境界可不容易了，要满足以下要求（源自 <a href="http://www.thefutureoftheweb.com/">Jesse Skinner</a> 的文章，翻译的可能不太准确）：</p>

<ol>
  <li>网页内容和表单使用纯 HTML； </li>

  <li>不借助 JavaScript，表单和超级连接也能正常使用； </li>

  <li>页面外观完全由 CSS 控制，而不是 HTML（不要用 table 来布局） 或 JavaScript； </li>

  <li>任何人都能通过任何设备（考虑不支持JavaScript的设备）访问； </li>
</ol>

<p>说白了，就是没有 JavaScript，网站也能正常运行。这种分离其实是重新定义了 HTML 和 JavaScript 的分工：</p>

<ol>
  <li>HTML 为主，JavaScript 为辅；</li>

  <li>HTML 不依赖于 JavaScript，可独立运行; </li>

  <li>JavaScript 仅用来增加页面效果。</li>
</ol>

<p>要达到这种境界，要求开发时先不使用任何脚本，这样保证了 HTML 独立运行。然后在不影响 HTML 的前提下，逐步加入脚本，以增强页面效果。</p>

<p>之所以作为一个境界，是因为做到这一步是非常困难的，有时是不可能的（如：Google 地图），如果能做到，你也将受益匪浅：</p>

<ol>
  <li>没有脚本的干扰，网站调试更加简单; </li>

  <li>更换网站动态效果像换肤一样简单，只需要引用另外一个脚本; </li>

  <li>开发人员分工更加明确，前端开发与后台开发彻底分开，互不影响。 </li>
</ol>

<h1>Unobtrusive Ajax 面临的挑战</h1>

<p>现在是 2010 年 10 月了，离 <a href="http://www.thefutureoftheweb.com/">Jesse Skinner</a> 发表文章已经有四年时间了，这四年间 web 发生了巨大变化：</p>

<ol>
  <li>Chorme 为代表的新一代浏览器的出现，JavaScript 运行效率越来越高，对新的 Web 标准（HTML5 和 CSS3 ）支持也不断完善； </li>

  <li>各种 JavaScript 类库的涌现和成熟，有和 Unobtrusive Ajax 同一战线的（jQuery、Prototype），也有站在对立面的如：ExtJS； </li>

  <li>杂乱的 Web 控件很多，很多都是具有很强侵入性的； </li>

  <li>WebOS、在线 Office 等逐渐兴起，且对 JavaScript 依赖较强。 </li>
</ol>

<h1>后计</h1>

<p>从一开始接触到 Unobtrusive Ajax，我就感觉这是一种伟大的想法，并积极向 Conceptual Separation 的境界努力。当然 Unobtrusive Ajax 也有一定的适用范围，并不适用于所有的 Web 开发。</p>

<p>希望听听大家对 Unobtrusive Ajax 的看法，支持的也好，反对的也好，交流就是一种进步！</p><img src="http://www.cnblogs.com/ldp615/aggbug/1849269.html?type=1" width="1" height="1" alt=""><p>评论: 0　<a href="http://www.cnblogs.com/ldp615/archive/2010/10/12/unobtrusive-ajax.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/ldp615/archive/2010/10/12/unobtrusive-ajax.html#commentform">发表评论</a></p><p><a href="http://job.cnblogs.com/">程序员找工作，就在博客园</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/77067/">Membase发布1.0版本</a><span style="color:gray">(2010-10-12 22:22)</span><br>· <a href="http://news.cnblogs.com/n/77066/">Groupon探路中国 秘会5家中国团购网站</a><span style="color:gray">(2010-10-12 22:11)</span><br>· <a href="http://news.cnblogs.com/n/77065/">微软Translator翻译引擎新增3种语言支持</a><span style="color:gray">(2010-10-12 21:48)</span><br>· <a href="http://news.cnblogs.com/n/77064/">高科技公司产品名称背后故事：Twitter抓阄决定</a><span style="color:gray">(2010-10-12 20:59)</span><br>· <a href="http://news.cnblogs.com/n/77062/">DST创始人13亿美元豪赌美国SNS网站</a><span style="color:gray">(2010-10-12 20:49)</span><br></p><p>编辑推荐：<a href="http://www.cnblogs.com/cmt/archive/2010/10/12/1848613.html">程序员节，10月24日！</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">个人主页</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://space.cnblogs.com">社区</a>  <a href="http://kb.cnblogs.com">知识库</a></p>