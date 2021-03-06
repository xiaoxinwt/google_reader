---
layout: post
title:  "关于jQuery和YUI, 还有KISSY"
date:   2010-11-06 22:43:00
author: lifesinger
categories: program
---

## 关于jQuery和YUI, 还有KISSY
### by lifesinger
### at 2010-11-06 22:43:00
### original <http://lifesinger.org/blog/2010/11/jquery-yui-kissy/>

<h3>起因</h3>
<p>John Resig: <a href="http://www.quora.com/How-could-YUI3-improve-its-image-compared-to-jQuery-MooTools-etc/">How could YUI3 improve its image compared to jQuery, MooTools, etc.?</a><br>
NCZ: <a href="http://www.nczonline.net/blog/2010/11/03/response-to-john-resigs-comments-about-yui/">Response to John Resig’s comments about YUI</a><br>
拔赤的译文：<a href="http://ued.taobao.com/blog/2010/11/06/yui3-vs-jquery/">一场关于YUI3/jQuery的精彩辩论</a></p>
<h3>感想</h3>
<p>我的感觉，NCZ 并没有很好的领悟到 John Resig 的建议。<br>
Resig 的建议里，只有一个关键点：Simplicity is key.<br>
并且很想传递出一个理念：Simplicity is far harder to get right than building complex applications.<br>
“简单比复杂更具挑战”，可惜 Resig 并没有有效传递出来。</p>
<p>NCZ 的回复，极力解释 YUI 中的 Y 是绝对必须的，没有这个 Y 就没有 YUI. 我个人不是很同意 NCZ 的反驳，John Resig 只是建议去掉 Y 的色彩，更开放开源出来，不然 YUI 将始终有太强的公司色彩，流行上必受局限。</p>
<p>就如淘宝之前的类库 TBra, 因为有 TB 两字，B2B和支付宝等兄弟公司，决定采不采用时，或多或少就会有些顾虑（当然非主因，但 TBra 中的 TB, 明显不利于推广）。</p>
<p>NCZ 从项目的角度，比如在全球 top5 的雅虎首页测试、能用于秘密项目（貌似被删除了）等，来论证 Y 的必要性，这一切都没有错。但 NCZ 忽略了用户的感受，并非所有人都对 YAHOO 充满感情和尊敬。程序员在乎开放和公平。YUI 目前的运作方式，使得 github 上的非 YAHOO 开发者并不能有效参与。感觉不到公平，对社区的激情也就会逐步淡化。<br>
<span></span><br>
一个有意思的现象：<br>
YUI3@github 的访问量，近 3 个月的访问量才 1.1 万。<br>
jQuery@github 的访问量，近 3 个月高达 36.2 万。<br>
KISSY 虽然 7 月份才放到 github, 近 3 个月流量也比 YUI3 多，已经有 2.2 万。<br>
这并不能说明 KISSY 比 YUI3 流行（还差了一大截），但或多或少能说明，开发者并不怎么喜欢去浏览 YUI3 在 github 上的源码。</p>
<p>YUI 的开发团队，太强烈的 YAHOO 色彩，真有可能是成也萧何，败也萧何。</p>
<p>NCZ 还反复强调了 jQuery 和 YUI 的方向不同，不存在竞争关系。<br>
原则上说没错，的确如此。<br>
但 NCZ 再次忽略了用户的感受。</p>
<p>jQuery 用户不会在构建复杂项目中，想起去用 YUI.<br>
YUI 用户也不会在构建简单站点时，切换到 jQuery.</p>
<p>用户的惰性决定了 jQuery 和 YUI 必然是竞争关系。<br>
竞争关系不是由 John Resig 和 NCZ 决定的。<br>
竞争关系是由用户和市场决定的。<br>
NCZ 的回复回避了用户只会选择其中一种导致的真实竞争。</p>
<p>除了上面这些，我强烈感觉到，YUI3 堪忧。<br>
如果真的哪一天 YAHOO 彻底没落，YUI 可能真的会很快成为历史。</p>
<p>有公司在背后支持是没问题的，但不能太放到明面上说，更不能产生强依赖。<br>
比如提起 jQuery，我们并不会立刻想到 Mozilla. Mozilla 只是默默的支持者。<br>
Prototype 和 MooTools 等也是，我们不 google 一下，甚至不知道 Prototype 和 MooTools 背后的公司是哪些。</p>
<p>YUI 的 Y, 使得 YUI 很难彻底开放开源。<br>
想起 code.taobao.org 上的 TFS 等开源代码，这些，比 YUI 则更不如了。<br>
code.taobao.org, 现阶段，更多的仅是一种姿态，表明淘宝的开放态度。<br>
但要指望非阿里的某个公司，直接采纳 TFS 来构建文件系统，我深深的持怀疑态度（但愿我的感觉是错的）</p>
<h3>说说 KISSY</h3>
<p>KISSY 我一直想吸引百度和腾讯的前端也参与进来（好难），尽量去淘宝化。<br>
淘宝可以用 KISSY, 证明 KISSY 的健壮和价值，但不能让 KISSY 成为淘宝的 KISSY.<br>
当然，KISSY 究竟会成为什么，也不是我们能 YY 出来的，会受各种因素的影响。<br>
KISSY 团队目前能做的，就是尽量吸纳各种经验教训，从实用和微创新中，逐步去走出自己的路来。</p>
<p>KISSY 目前，core 倾向 jQuery, 会极力追求简洁简单。<br>
YUI3 的颗粒化和定制化，看起来很美，但作为内部机制即可，不必大力做为特性推广给普通用户。<br>
当 SimpleYUI 出来的时候，我的感觉和 John Resig 一致。<br>
SimpleYUI 才应该是 YUI 的默认使用方式。<br>
YUI3 目前的默认使用方式，应该改名叫 AdvancedYUI, 只暴露给高级用户使用。<br>
优雅的复杂，能让程序员很有成就感；但绝大部分用户要的，只是尽可能傻瓜式的简单。</p>
<p>KISSY Core 之外的组件，倾向 YUI 化。YUI 在组件基础架构和开发组织上，包括 gallery 等社区建设上，我觉得比 jQuery 做得更好。jQuery 除了 jquery.js 的流行，在 jQuery UI 项目上，并不如意。目前有 jQuery EasyUI 等类库，还有各种“丰富”的插件，但质量也良莠不齐。这方面，感觉 jQuery 还很嫩，甚至存在先天不足。</p>
<p>真的要好好使用 jQuery, 可能还真得像克军他们学习，在 jQuery 基础上，弄一个 Do 框架，这可能是基于 jQuery 的最佳实践之一。</p>
<h3>请勿转载</h3>
<p>jQuery 是我接触的第一个 JavaScript 类库，创始人 John Resig 一直是我的偶像。<br>
jQuery 看似简单，但着实不简单。（打住，怕继续说下去，此文完结不了……）</p>
<p>YUI 是我工作以来使用最频繁的类库。NCZ, Eric, Dav Glass 等开发者，也都是很令人钦佩的大牛。<br>
淘宝前端对 YUI 的情感，绝对比西湖的湖水还深。</p>
<p>最后，特别强调：本文感想，仅代表个人观点，请勿歪解放大，禁止任何形式的转载。</p>