---
layout: post
title:  "初识Silverlight 4及其架构"
date:   2010-09-19 12:06:00
author: mikelij
categories: program
---

## 初识Silverlight 4及其架构
### by mikelij
### at 2010-09-19 12:06:00
### original <http://www.cnblogs.com/mikelij/archive/2010/09/19/1824589.html>

<p><a href="http://www.cnblogs.com/mikelij/"><img src="http://pic.cnblogs.com/face/u12720.jpg" alt="" border="0"></a><br>作者: <a href="http://www.cnblogs.com/mikelij/">mikelij</a> 发表于 2010-09-19 12:06 <a href="http://www.cnblogs.com/mikelij/archive/2010/09/19/1824589.html">原文链接</a> 阅读: 1365 评论: 9</p><p><strong>简介</strong> </p>
<p>  才开始学Silverlight 4。本文将谈到对Silverlight 4最初步的认识，同时将从多个角度谈一些与Silverlight 4架构有关的认识。<br>  Silverlight 4是今年早些时候发布的。这项技术为我们带来了许多革新。它不仅是为用户带来了丰富的交互式的互联网和多媒体内容，还为我们提供了强大的轻量级的平台。我们可以在此平台上开发可移植的，跨平台的，基于网络的应用程序，并且可以从不同的源头集成服务和数据。相比传统Web应用的界面，Silverlight应用的界面让人耳目一新。让人感觉像是Windows Form应用程序搬到了Web上。</p>
<p> </p>
<p><strong>为什么需要Silverlight 4</strong></p>
<p> </p>
<p>  前面我们在开发Web应用的时候，碰到了许多技术上的难题，如平台兼容，不同的文件格式和通讯协议，同样的页面在不同浏览器上的表现，还有不同浏览器对Javascript的解释，经常为这些问题加班熬夜，我们没有少费脑筋来试图解决这些令人头痛的问题。有了Silverlight技术：</p>
<ul>
<li>我们就能克服现有技术平台的的不兼容性，不一致性。</li>
<li>我们就有了能力在多平台多种浏览器上创建一致的用户体验，不管在哪里，应用程序在外观上和行为上都是统一的。</li>
<li>我们就能利用熟悉的.net框架的类和功能将不同网络位置的数据和服务集成到一个应用程序中。</li>
<li>我们就能为用户提供一个多媒体的，表现丰富的，操作性强的用户界面。</li>
<li>Silverlight 4为我们提供了更多的控件，更强大的功能，让我们可以开发出更强大的应用，更好地应对商务上的挑战。 </li>
</ul>
<p><strong>Silverlight 4有哪些新东西？</strong> </p>
<ul>
<li>  几个实用的控件，如RichTextBox, ViewBox, WebBrowser, WebBrowserBrush等。</li>
<li>  浏览器外运行支持，浏览器外运行能象普通窗口的特性，如改变大小，置顶等。</li>
<li>  提升了信任的应用程序，允许信任的Silverlight程序可以自由读写用户文件和目录，全屏幕，调用Windows API等。</li>
<li>  更丰富的媒体，可以捕捉视屏信号和话筒语音。</li>
<li>  更强的打印功能</li>
<li>  更强更方便的UI界面，支持拖拽，支持剪贴板等等。</li>
<li>  更好的数据绑定和WCF数据服务</li>
<li>  WCF RIA 服务对多层应用支持更强。</li>
<li>  DLR的支持 </li>
</ul>
<p><strong>Silverlight 4有哪些系统和浏览器支持？</strong></p>
<p><br><img src="http://pic002.cnblogs.com/img/mikelij/201009/2010091909560779.jpg"></p>
<p> </p>
<p>  要构建有效的Silverlight 4应用，开发者有必要了解Silverlight 4的架构。</p>
<p> </p>
<p><strong>Silverlight平台的部件</strong></p>
<p> </p>
<p>  Silverlight作为一个整体主要包括三个部分，如下表：</p>
<table cellpadding="0" cellspacing="0" border="1">
<tbody>
<tr>
<td>
<p><span style="font-size:11pt"><strong>部件</strong></span></p>
</td>
<td>
<p><span style="font-size:11pt"><strong>说明</strong></span></p>
</td>
</tr>
<tr>
<td>
<p><span style="font-size:11pt">核心展现部件</span></p>
</td>
<td>
<p><span style="font-size:11pt">负责呈现XAML所表现的UI元素，包括常用的控件，多媒体控件等，同时负责处理用户输入，事件等。</span></p>
</td>
</tr>
<tr>
<td>
<p><span style="font-size:11pt">Silverlight中的.net框架</span></p>
</td>
<td>
<p><span style="font-size:11pt">.net框架的子集。包括部件和库，其中有数据集成，可扩展的Windows控件，网络，基础类库，垃圾收集和CLR等。<br>有一部分Silverlight .net框架与应用程序一起部署。这些"Silverlight库"是一些程序集，不随Silverlight运行时一起发布，在Silverlight SDK中发布。开发者用到了这些Silverlight库，这些程序集就必须同应用程序一起打包。用户浏览的时候就下载到浏览器中。 这些与应用程序一起部署的的库包括新的一些UI控件，XLINQ, RSS/Atom, XML 序列化等, 和动态语言运行时(DLR).</span></p>
</td>
</tr>
<tr>
<td>
<p><span style="font-size:11pt">客户端安装器和更新器</span></p>
</td>
<td>
<p><span style="font-size:11pt">第一次浏览Silverlight应用程序，客户端的浏览器需要安装上浏览器插件，这个安装器兼更新器负责为客户端浏览器安装插件并始终保持客户端的浏览器插件最新。</span></p>
</td>
</tr>
</tbody>
</table>
<p> </p>
<p>下图是MSDN官方的图，展示了Silverlight平台的部件，及一些相关部件和服务。<br><img src="http://pic002.cnblogs.com/img/mikelij/201009/2010091223475440.jpg"></p>
<p>  核心展现部件Core presentation framework包括了XAML parser, UI Core, Inputs, DRM(digital rights management), Media, Deep Zoom等，多数是需直接与具体的操作系统API打交道的功能，所以这里没有CLR Execution Engine.</p>
<ul>
<li>XAML parser负责解析XAML，能兼容不同规格的XAML</li>
<li>UI Core负责向量，bitmap，动画，文字的渲染</li>
<li>Inputs负责处理键盘，鼠标等设备的输入</li>
<li>DRM负责处理数字媒体的数字版权管理</li>
<li>Media负责处理多媒体的处理，支持不同的媒体格式和编码器</li>
<li>Deep zoom是用于高分辨率图片的放大和缩小</li>
<li>Layout负责UI元素的定位</li>
</ul>
<p> .net framework for Silverlight 是供应用程序使用的商业&quot;库&quot;。包括Data, WCF, WPF, BCL等。</p>
<ul>
<li>BCL提供了.net平台上的基本类型, 集合collections,泛型Generics，加密等</li>
<li>WCF提供了应用程序处理WCF相关功能的一些类型, 用于处理SOAP, REST, RSS/ATOM等</li>
<li>Data提供了LINQ, LINQ to xml等</li>
<li>WPF提供了一些UI控件，将数据绑定到这些控件，布局，处理用户输入的一些功能。</li>
</ul>
<p>  .net framework for Silverlight分布在若干个.net程序集中。有一部分的程序集(比如BCL)是随Silverlight 安装器在用户浏览器中安装，有一部分是在Silverlight SDK中，应用程序引用到了相关的程序集，才会随应用程序一起发布到服务器上，当有用户访问应用程序时，这些.net framework程序集才随应用程序的程序集一起下载到用户的浏览器中被CLR执行引擎(CLR Execution Engine)执行。</p>
<p>  CLR执行引擎(CLR Execution Engine)是这里面的核心，负责执行应用程序的.net 代码和.net framework的.net代码。CLR总是基于一个宿主。通常的宿主是一个浏览器，在浏览器外的场景时的宿主是一个操作系统进程。Silverlight 4对其CLR做了改进，Silverlight 4的CLR与桌面应用程序的CLR的差别进一步缩小。</p>
<p>  DLR提供了对动态语言的支持。目前支持JScript, Iron Python, Iron Ruby。这是Silverlight 4新提供的特性。</p>
<p>  Silverlight 4还有一些编程相关的特性：如独立存储(Isolated storage)，后台线程，安全的文件管理，序列化，XAP打包，XML处理，HTML与托管代码的交互。</p>
<ul>
<li>隔离的存储提供一种安全的方式让Silverlight程序去访问浏览器本地的文件系统。</li>
<li>后台工作者线程使异步，多线程在Silverlight上成为了可能。</li>
<li>安全的文件管理提供一个安全的文件打开对话框，允许文件上传等。</li>
<li>序列化使对象的序列化存储和恢复成为了可能。</li>
<li>XAP打包提供了Application类和工具来打XAP的包。.xap的包有应用程序和入口点，以便让用户浏览器插件来运行这个.xap包。应用程序的发布都是需要一个.xap包。</li>
<li>XML处理包含了处理Xml的功能。XmlReader类和XmlWriter类提供了最基本的XML处理功能，同时XLinq让开发者可以用c#或者VB.net就可以查询Xml中的数据。</li>
<li>HTML与托管代码的交互让开发者可以在托管代码中直接用HTML DOM就可以管理HTML页面里的UI元素，同时也可以写Javascript代码调用托管代码的功能，访问其中的方法，属性，激发其事件等。这个功能就为上图中的Javascript Engine和MS AJAX Library提供了可能。</li>
</ul>
<p>  上图中的浏览器宿主(Browser Host)是用户浏览器插件提供的一个环境。它提供了运行一个Silverlight应用程序的基础环境，如Application服务，HTML DOM的集成，网络通信的基础，还有安装器。使用托管API的Silverlight应用程序都有一个继承Application类的类，有一个程序入口点，浏览器宿主(Browser Host)为应用程序建好了运行环境，然后才调用应用程序的入口。</p>
<p> </p>
<p>结束语</p>
<p>  以上是关于Silverlight 4的一些粗浅的认识。是任何一个Silverlight开发者想知道的内容。如果更深入些，就从多个方面来了解：它的原理，它的处理周期，它的部署，它的安全性，它的伸缩性，它的分布式计算能力，它的容错能力，等等等等。本人也是刚开始学，所知有限。有待进一步学习。等有了一点东西了再写。</p>
<p>  题外话：最重要的还是快速学习和分析能力。这个能力让你面对新技术时，有潜力迅速学习并掌握该技术。</p><img src="http://www.cnblogs.com/mikelij/aggbug/1824589.html?type=1" width="1" height="1" alt=""><p>评论: 9　<a href="http://www.cnblogs.com/mikelij/archive/2010/09/19/1824589.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/mikelij/archive/2010/09/19/1824589.html#commentform">发表评论</a></p><p><a href="http://job.cnblogs.com/">程序员找工作，就在博客园</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/74860/">甲骨文CEO亟需回答十大问题：赫德新角色</a><span style="color:gray">(2010-09-19 23:17)</span><br>· <a href="http://news.cnblogs.com/n/74859/">世界首台图像可触摸3D电视在日本问世</a><span style="color:gray">(2010-09-19 23:03)</span><br>· <a href="http://news.cnblogs.com/n/74858/">实用HTML，CSS和JavaScript速查表</a><span style="color:gray">(2010-09-19 23:00)</span><br>· <a href="http://news.cnblogs.com/n/74843/">亚马逊拟以3.1亿美元收购DVD租赁公司Lovefilm</a><span style="color:gray">(2010-09-19 20:49)</span><br>· <a href="http://news.cnblogs.com/n/74842/">张朝阳：给我两年时间 再造搜狐</a><span style="color:gray">(2010-09-19 20:27)</span><br></p><p>编辑推荐：<a href="http://www.cnblogs.com/zc22/archive/2010/09/19/1830793.html">对ASP.NET的最新安全漏洞进一步跟进说明</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">个人主页</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://space.cnblogs.com">社区</a>  <a href="http://kb.cnblogs.com">知识库</a></p>