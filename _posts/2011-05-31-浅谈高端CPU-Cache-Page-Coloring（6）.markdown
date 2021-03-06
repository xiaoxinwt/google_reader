---
layout: post
title:  "浅谈高端CPU Cache Page-Coloring（6）"
date:   2011-05-31 13:58:45
author: 陈怀临
categories: program
---

## 浅谈高端CPU Cache Page-Coloring（6）
### by 陈怀临
### at 2011-05-31 13:58:45
### original <http://www.tektalk.org/2011/05/30/%e6%b5%85%e8%b0%88%e9%ab%98%e7%ab%afcpu-cache-page-coloring%ef%bc%886%ef%bc%89/>

<div><h3>系列目录 高端CPU Cache Page Coloring</h3><ol><li><a href="http://www.tektalk.org/2011/04/11/%e5%be%97%e5%85%b6%e6%84%8f%ef%bc%9b%e5%bf%98%e5%85%b6%e5%bd%a2-%e6%b5%85%e8%b0%88%e9%ab%98%e7%ab%afcpu-cache-page-coloring%ef%bc%881%ef%bc%89/" title="浅谈高端CPU Cache Page-Coloring（1）">浅谈高端CPU Cache Page-Coloring（1）</a></li><li><a href="http://www.tektalk.org/2011/04/17/%e6%b5%85%e8%b0%88%e9%ab%98%e7%ab%afcpu-cache-page-coloring%ef%bc%882%ef%bc%89/" title="浅谈高端CPU Cache Page-Coloring（2）">浅谈高端CPU Cache Page-Coloring（2）</a></li><li><a href="http://www.tektalk.org/2011/04/24/%e6%b5%85%e8%b0%88%e9%ab%98%e7%ab%afcpu-cache-page-coloring%ef%bc%883%ef%bc%89/" title="浅谈高端CPU Cache Page-Coloring（3）">浅谈高端CPU Cache Page-Coloring（3）</a></li><li><a href="http://www.tektalk.org/2011/04/30/%e6%b5%85%e8%b0%88%e9%ab%98%e7%ab%afcpu-cache-page-coloring%ef%bc%884%ef%bc%89/" title="浅谈高端CPU Cache Page-Coloring（4）">浅谈高端CPU Cache Page-Coloring（4）</a></li><li><a href="http://www.tektalk.org/2011/05/08/%e6%b5%85%e8%b0%88%e9%ab%98%e7%ab%afcpu-cache-page-coloring%ef%bc%885%ef%bc%89/" title="浅谈高端CPU Cache Page-Coloring（5）">浅谈高端CPU Cache Page-Coloring（5）</a></li><li>浅谈高端CPU Cache Page-Coloring（6）</li></ol></div> <p>前面5节阐述了大CPU大Cache的Cache Page-Coloring的一些基本概念。主要是通过妈咪的包厢运作制度来作为各位弟兄们比较熟悉的场景，从而达到融会贯通的。 </p>
<p>在Cache Page-Coloring方面，一个前提是“里面放了N个长凳子的包厢制度”–Set Associative的Cache。否则，一切无从谈起。</p>
<p>这一节谈一下目前大CPU中的L3 Cache的一些问题。通常我们说LLC–Last Layer Cache。</p>
<p>目前市场上不少芯片都有了On-Die的L3 cache，例如Intel的Nehalem-EX, Westmere，IBM的Power7，RMI的XLP，Tilera等等。</p>
<p>与L1和L2的Cache相比，L3 cache的设计和管理方式有相同的地方，也有不一样的地方。</p>
<p>这里比较容易犯迷惑。</p>
<p>1。L3也是Set／Associative的。换言之，学术界和工业界没有，也没有傻到整一个新的Cache机制。从而，Page Coloring的各种思想和算法是而且当然是可以apply到L3 cache中去的。<br>
2。 与L1，L2 cache controller相比，L3的实现是各个厂商差别很大。各有千秋。但总体而言，都是通过一个Distributed L3 Cache的机制，在系统层面提供一个完整的Set／Associative的L3 Cache。其中用Ring结构的还是比较多，例如Intel，RMI等等。Tilera用是其声称的Mesh结构。</p>
<p>3。对L3 cache理解里最容易出错误的就是这个通常不是Local bus的Interconnect结构，例如Ring。从而对其寻址方式和其结构方式造成混淆。请记住：Local Bus的直接拿Index bit来寻址和通过Pa做Hash来寻址，这是一个微结构的事情（Micro－Arch），而非结构（Arch）的事情。在结构上，L3仍然是一个Set／Associate的Cache。例如Nehalem－EX（Beckton）的24M的24Way的L3 Cache。这就说明，这个L3 Cache有(24＊2^20)/(24 * 2^6) = 2^14=64K Sets.<br>
[Nehalem的Cache Line是64Bytes]。</p>
<p>显然，通过我们上述几节的分析，在Intel CPU上，一个44位物理地址的0－5是cache offset；6－19是Set的Index bit。我们会在后续文章中对Intel的L3 Cache的微结构做一些探讨，例如其hash机制。</p>
<p>下面是一些相关CPU（Nehalem－EX；XLP－832<br>
，Tilera和Power7）的L3 Cache结构示意图：<br>
<a href="http://www.tektalk.org/wp-content/uploads/2011/05/Nehalemllc.jpg"><img src="http://www.tektalk.org/wp-content/uploads/2011/05/Nehalemllc.jpg" alt="" title="Nehalemllc" width="580" height="387"></a></p>
<p><a href="http://www.tektalk.org/wp-content/uploads/2011/05/rmi_xlp832.jpg"><img src="http://www.tektalk.org/wp-content/uploads/2011/05/rmi_xlp832.jpg" alt="" title="rmi_xlp832" width="580" height="393"></a><br>
<a href="http://www.tektalk.org/wp-content/uploads/2011/05/tilera.gif"><img src="http://www.tektalk.org/wp-content/uploads/2011/05/tilera.gif" alt="" title="tilera" width="580" height="307"></a><br>
<a href="http://www.tektalk.org/wp-content/uploads/2011/05/Power7.png"><img src="http://www.tektalk.org/wp-content/uploads/2011/05/Power7.png" alt="" title="Power7" width="580" height="281"></a></p>

<div>
	<a href="http://www.jiathis.com/share/">分享到：</a>
	<a title="分享到新浪微博">新浪微博</a>
	<a title="分享到QQ空间">QQ空间</a>
	<a title="分享到开心网">开心</a>
	<a title="分享到人人网">人人</a>
	<a title="分享到微软Live">Live</a>
	<a title="分享到Digg">Digg</a>
	<a title="分享到Facebook">FB</a>
	<a title="分享到Twitter">Twitter</a>


</div>

<br> <div><a href="http://www.tektalk.org/2011/05/08/%e6%b5%85%e8%b0%88%e9%ab%98%e7%ab%afcpu-cache-page-coloring%ef%bc%885%ef%bc%89/" title="浅谈高端CPU Cache Page-Coloring（5）">系列前一篇</a> </div><img src="http://www1.feedsky.com/t1/519097148/tektalk/feedsky/s.gif?r=http://www.tektalk.org/2011/05/30/%e6%b5%85%e8%b0%88%e9%ab%98%e7%ab%afcpu-cache-page-coloring%ef%bc%886%ef%bc%89/" border="0" height="0" width="0">