---
layout: post
title:  "近期工作总结：关于对Flash player的逆向工程进展"
date:   2012-05-19 11:04:25
author: changming
categories: program
---

## 近期工作总结：关于对Flash player的逆向工程进展
### by changming
### at 2012-05-19 11:04:25
### original <http://www.udpwork.com/item/7323.html>

<p>从上个月的最后一周（4月21日）到现在，我一直在做对flash的逆向工程，主要是使用IDA做静态代码分析。这方面我完全是外行，上次干这个还是在大3的时候，给宿舍用的电信的802.1x客户端做开源FreeBSD版。下面介绍一下中途遇到的困难以及现在的进展。</p>
<p>首先是用PEiD做壳检测，什么都没查到，我估计是因为PEiD很久没更新了。从PE Header中能看到linker version 是9.0，所以，如果它没有加壳的话，那么PEiD应该能检测到vc++ 2008的runtime才对。就我后续的分析来看，没有发现它带壳。所以这次的工作跟我上次做802.1x客户端相比，简单了很多。上次的重心在于找到OEP，脱掉asprotect2的壳，然后分析出发送密码的那个包采用的什么方式对密码进行hash就行了。这次呢，虽然没有壳，但是flash player的代码量何其庞大，其中我要分析的部分，大约包含至少1万3千行C++代码。所以这次的重心就是要有耐心，挨个挨个标函数。</p>
<p>然后是要找一个合适的IDA的版本。我现在用的是IDA 6.1 with hex-rays decompiler 1.5，从verycd上下载的（<a href="http://www.verycd.com/topics/2900356/" title="http://www.verycd.com/topics/2900356/">http://www.verycd.com/topics/2900356/</a>）。其实我没看出来IDA 6.1比5.5有哪些特别的改进，可能是因为我之前用的也不深吧。IDA这种神器我还是很愿意花钱买的，少请朋友吃2次饭就够了。可是国内没有销售渠道。IDA对产品销售控制的很严格。</p>
<p>然后就是选择flash的版本。我试了好几个，包括standalone的和ActiveX的，debug的和非debug的，11.2、11.1、10.x的。最后选择的是从<a href="http://www.adobe.com/support/flashplayer/downloads.html" title="http://www.adobe.com/support/flashplayer/downloads.html">http://www.adobe.com/support/flashplayer/downloads.html</a>下载的“Windows Flash Player 11.2 Projector content debugger ”。这是一个独立的exe，无需安装，双击即可运行，调试起来比ActiveX的方便很多。</p>
<p>刚开始的工作比较简单，我还没有开始用OllyDbg，就直接在IDA中调试。遇到的第一个问题就是关于Address Space Load Randomization（ALSR）的。有很大的概率，在我启动调试后，IDA会卡住很久，说“Rebasing program to 0x01F51000”（这个地址频繁变）。因为这个对话框是IDA弹的，所以我刚开始以为这是IDA的特性，就在IDA的设置里面改来改去，发现始终不能解决问题。后来才发现自己走了弯路，这明明是操作系统干的，它为了防止缓存区溢出漏洞被利用，于是就在载入PE镜像的时候把基址随机化。然后这个操作被IDA检测到了，所以IDA需要在新的基址上，把以前的分析重做一遍，所以就会卡住很久。而且，由于地址一直在变，所以导致我对vtable的标注没法进行。解决办法很简单，修改PE Header，删掉关于ASLR的flags即可。详情请见<a href="http://www.sunchangming.com/blog/?p=4148" title="http://www.sunchangming.com/blog/?p=4148">http://www.sunchangming.com/blog/?p=4148</a></p>
<p>然后遇见的第二个问题，就是如何更有效的识别C++对象？IDA比较智能，能自动帮我把全局函数和对象的成员函数分开（虽然他的判断经常有误）。但是然后呢？如果一个成员函数中大量的使用到了对象指针，而我不能获知对象类型的话，简直就是盲人抓瞎啊。于是我在网上找了很多资料，如何从对象指针获得类名。主要有两种手段，一个是通过C++的RTTI，很不幸，flash貌似没有开RTTI。另一个是通过MFC/QT的RTTI，很不幸，flash没有用MFC/QT。可能ActiveX版本的Flash会用到一些MFC，但是我所关注的这部分代码因为和界面无关，所以没用。但是比较幸运的是，我发现这些代码大量的使用了继承。就像MFC的所有Object都从CObject继承那样，这边的每个C++类，也都至少有一个vtable。于是，利用vtable可以粗略的把类型分开。虽然这种判断方式常常会出错，因为vtable可以在不同类之间复用。但是就目前来看，这种方式似乎工作的还不错。</p>
<p>然后遇见的第三个问题，就是msvcrt中很多很基本的函数，IDA没有给我识别出来，比如malloc，比如memcpy，而是显示成unknown_libname_115这样的形式。这个问题至今没有解决。但是当我在跟踪对象的动态创建的时候，有了一个很大的意外收获。就是我通过在Google中搜索某一个错误提示字符串，找到了MMgc的源代码。MMgc来自于Mozilla的一个名为Tamarin开源项目。Tamarin的主要代码来自于Adobe贡献，最初的目的是做一个开源的ECMAScript引擎，给Adobe flash以及Firefox的js用。Tamarin的源代码仓库现在主要有3个分支，分别是tamarin-central（最后更新2010年3月）、tamarin-redux（最近一直在更新）、tamarin-tracing（最后更新2008年10月）。我把3份代码都下载下来，最后发现最新的那个分支（tamarin-redux）与Adobe Flash 11.2基本完全一致。从这点来说，Adobe确实是把Flash的核心代码都贡献出来了，而且一直在持续向Mozilla提交。虽然这个项目已经处于被Mozilla遗弃的状态，因为Firefox嫌这个引擎太慢，没用它。于是我前面的疑惑也有了答案，初步看来，大部分类都是从GCObject直接或间接继承而来，所以几乎每个对象都有一个vtable，而且后面还通常紧跟着一个32位的int，看起来像是reference counting。</p>
<p>我一直有个想法，把tamarin的代码编译一遍，然后用IDA把特征文件sig做出来，然后apply到我当前的项目中去，应该是比我之前那样挨个手工标注要好很多。另外就是把vs2008的msvcrt也这么搞一下，会不会识别率能高一些？</p>
<p>我现在主要的工作是在标记类型。一个对象，如果是在栈上，那么直接就可以通过栈的布局看到大小，如果是在堆上，那么可以通过传递给MMgc的内存分配函数看到大小。然后根据构造函数，挨个挨个的把布局猜出来。不过此时类成员变量只有一个大致的类型名(int、bool)，没有名字，而且无法把指针从int中区分出来。然后就是根据函数中怎么用它，把类成员变量的名字给慢慢补上。</p>
<p>我在做这件事情的时候，一直遭到很多质疑，关于这么做是否必要。我的目的是为了把某协议的spec弄清楚，有人认为抓包分析就够了。我不赞同这点。首先，这个协议的所有报文都是使用AES加密的，要不是别人已经通过逆向工程把密钥给你提供出来了，就凭自己抓包想猜出AES密钥来，那完全没可能，因为你没那么大的计算能力。其次，通过抓包会发现某些位置，收到的总是同样的字节。这些字节若不是0xFF、0xFF这样的重复的padding，就肯定都是有特殊含义的，通过抓包很难把每一位代表什么意思搞清楚。最后，我已经找到了收到包根据包类型进行处理的那个swith…case函数，通过这种方式，我可以保证没有漏任何包类型。只要我把每个包处理函数都跟踪完，那么协议就完全明了了。而通过抓包分析，可能会漏掉某些类型，以及某些特殊的flag。</p>
<p>目前进展比较慢。我也很急。</p>
<img src="http://www.sunchangming.com/blog/wp-content/plugins/google-reader-stats/google-reader-view.php?id=4165">
			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/7323.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/7323.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">IT 牛人博客聚合网站(udpwork.com)</a> 聚合
					|
					<a href="http://www.udpwork.com/item/7323.html#reviews">评论: 0</a>
					|
					<a href="http://book.benegg.com/tag/%E7%BC%96%E7%A8%8B?from=udpwork-feed">10000+ 本编程/Linux PDF/CHM 电子书下载</a>
				</p>
			</div>