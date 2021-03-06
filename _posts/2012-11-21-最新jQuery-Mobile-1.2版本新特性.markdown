---
layout: post
title:  "最新jQuery Mobile 1.2版本新特性"
date:   2012-11-21 18:27:12
author: terry
categories: program
---

## 最新jQuery Mobile 1.2版本新特性
### by terry
### at 2012-11-21 18:27:12
### original <http://www.qianduan.net/new-features-of-the-latest-jquery-mobile-1-2-version.html>

<p> </p>
<p><a title="最新jQuery Mobile 1.2版本新特性" href="http://223.4.150.152/technology/jquerymobile/20121121-jquery-mobile-latest-updates/index.html"><img src="http://223.4.150.152/technology/jquerymobile/20121121-jquery-mobile-latest-updates/title.jpg" alt="最新jQuery Mobile 1.2版本新特性"></a></p>
<p>大家还记得在jQuery 1.0 RC版本发布的时候我们曾经发布过一个<a title="jquerymobile" href="http://www.gbin1.com/technology/jquerymobile/20111214jquerymobile10/">jQuery Mobile RC版本介绍文章介绍主要的特性</a>。就在前不久，jQuery Mobile团队又发布了jQuery Mobile 1.2。新版本中带来了一些非常不错的特性。在今天的这篇文章中我们将继续介绍1.2版本的一些新的widget，及其一些针对老版本widget的功能加强。希望大家喜欢！<span></span></p>
<h2>Widgets</h2>
<p>jQuery Mobile最核心的地方就在于widgets。提供了与用户交互的界面。在最新的版本中，加入了一个全新的widget：popup modal。</p>
<h3>Popups （弹出层）</h3>
<p>弹出层是一个覆盖于页面其它内容的小的区域。可以用来设计提示栏，显示照片，地图或者其它内容。在jQuery mobile 1.2中，实现了这个超棒的widgets。</p>
<p>在本篇文章中，我们将使用如下代码框架来演示代码：</p>

<div><table><tr><td><pre>1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
</pre></td><td><pre style="font-family:monospace"><span style="color:#00bbdd">&lt;!DOCTYPE html&gt;</span>
<span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">html</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">head</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">meta</span> <span style="color:#000066">name</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;viewport&quot;</span> <span style="color:#000066">content</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;width=device-width, initial-scale=1&quot;</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">title</span>&gt;</span>jQuery Mobile 1.2<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">title</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">link</span> <span style="color:#000066">rel</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;stylesheet&quot;</span>  <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;http://code.jquery.com/mobile/1.2.0/jquery.mobile-1.2.0.min.css&quot;</span> <span style="color:#66cc66">/</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">script</span> <span style="color:#000066">src</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;http://code.jquery.com/jquery-1.8.0.min.js&quot;</span>&gt;&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">script</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">script</span> <span style="color:#000066">src</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;http://code.jquery.com/mobile/1.2.0/jquery.mobile-1.2.0.min.js&quot;</span>&gt;&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">script</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">head</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">body</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">div</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;content&quot;</span>&gt;</span>
        <span style="color:#808080;font-style:italic">&lt;!-- 请将以下的文章代码粘贴到此处 --&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">div</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">body</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">html</span>&gt;</span></pre></td></tr></table></div>

<p>这里我们使用CDN来加载javascript。</p>
<blockquote><p>友情提示：请大家使用Chrome来访问以下的“在线调试”地址，谢谢！</p></blockquote>
<p>为了添加popup弹出层，我们需要添加如下属性来定义所参考类型是popup：</p>

<div><table><tr><td><pre>1
</pre></td><td><pre style="font-family:monospace">data-rel=&quot;popup&quot;</pre></td></tr></table></div>

<p>然后定义具体的插件类型，如下：</p>

<div><table><tr><td><pre>1
</pre></td><td><pre style="font-family:monospace">data-role=&quot;popup&quot;</pre></td></tr></table></div>

<p>展示的触发层内容，可以是表单，菜单或者图片，完整代码如下：</p>

<div><table><tr><td><pre>1
2
3
4
</pre></td><td><pre style="font-family:monospace"><span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;#simplepopup&quot;</span> data-<span style="color:#000066">rel</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;popup&quot;</span>&gt;</span>Open Popup<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">div</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;popup&quot;</span> <span style="color:#000066">id</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;simplepopup&quot;</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">p</span>&gt;</span>This is a completely basic popup, no options set.<span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">p</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">div</span>&gt;</span></pre></td></tr></table></div>

<p><a title="在线调试" href="http://www.gbin1.com/gb/debug/c909477c-afb4-4e62-b161-6afa9cb417a7.htm">在线调试</a></p>
<p><img src="http://d2o0t5hpnwv4c1.cloudfront.net/2150_NewInjQueryMobile/images/popup-simple.png" alt="popup" width="390" height="79"></p>
<h3>Tooltips（工具提示条）</h3>
<p>使用popup我们还可以创建工具提示条，如下：</p>

<div><table><tr><td><pre>1
2
3
4
</pre></td><td><pre style="font-family:monospace"><span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;#tooltip&quot;</span> data-<span style="color:#000066">rel</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;popup&quot;</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;button&quot;</span>&gt;</span>Find out more<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">div</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;popup&quot;</span> <span style="color:#000066">id</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;tooltip&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;e&quot;</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">p</span>&gt;</span>You found out more!.<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">p</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">div</span>&gt;</span></pre></td></tr></table></div>

<p><a title="在线调试" href="http://www.gbin1.com/gb/debug/3e696fdb-3eac-4e9e-a6fe-4fdb927a4f0f.htm">在线调试</a></p>
<h3>Menus（菜单）</h3>
<p>下面我们生成一个菜单，如下：</p>

<div><table><tr><td><pre>1
2
3
4
5
6
7
8
9
</pre></td><td><pre style="font-family:monospace"><span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;#menu&quot;</span> data-<span style="color:#000066">rel</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;popup&quot;</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;button&quot;</span>&gt;</span>Menu<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">div</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;popup&quot;</span> <span style="color:#000066">id</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;menu&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;a&quot;</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">ul</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;listview&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;c&quot;</span> data-inset<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;true&quot;</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">li</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;divider&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;a&quot;</span>&gt;</span>My Menu<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">li</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">li</span>&gt;</span>Unlinked<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">li</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">li</span>&gt;&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;methods.html&quot;</span>&gt;</span>Linked<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">li</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">li</span>&gt;&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;methods.html&quot;</span>&gt;</span>With count<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;&lt;<span style="color:#000000;font-weight:bold">span</span>&gt;</span>42<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">span</span>&gt;&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">li</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">ul</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">div</span>&gt;</span></pre></td></tr></table></div>

<p><a title="在线调试" href="http://www.gbin1.com/gb/debug/06de3ec5-5905-41fb-983a-18540454235e.htm">在线调试</a></p>
<p><img src="http://d2o0t5hpnwv4c1.cloudfront.net/2150_NewInjQueryMobile/images/popups-menu.png" alt="menu"></p>
<p>当然，你也可以生成可缩放list，如下：</p>

<div><table><tr><td><pre>1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
16
17
18
19
</pre></td><td><pre style="font-family:monospace"><span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;#nestedmenu&quot;</span> data-<span style="color:#000066">rel</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;popup&quot;</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;button&quot;</span>&gt;</span>Nested Menu<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">div</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;popup&quot;</span> <span style="color:#000066">id</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;nestedmenu&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;none&quot;</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">div</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;collapsible-set&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;b&quot;</span> data-content-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;c&quot;</span> data-collapsed-icon<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;arrow-r&quot;</span> data-expanded-icon<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;arrow-d&quot;</span> <span style="color:#000066">style</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;margin:0; width:250px;&quot;</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">div</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;collapsible&quot;</span> data-inset<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;false&quot;</span>&gt;</span>
            <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">h2</span>&gt;</span>Colors<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">h2</span>&gt;</span>
            <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">ul</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;listview&quot;</span>&gt;</span>
                <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">li</span>&gt;&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;#&quot;</span>&gt;</span>Red<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">li</span>&gt;</span>
                <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">li</span>&gt;&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;#&quot;</span>&gt;</span>Blue<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">li</span>&gt;</span>
            <span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">ul</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">div</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">div</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;collapsible&quot;</span> data-inset<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;false&quot;</span>&gt;</span>
            <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">h2</span>&gt;</span>Shapes<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">h2</span>&gt;</span>
            <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">ul</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;listview&quot;</span>&gt;</span>
                <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">li</span>&gt;&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;#&quot;</span>&gt;</span>Circle<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">li</span>&gt;</span>
                <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">li</span>&gt;&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;#&quot;</span>&gt;</span>Square<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">li</span>&gt;</span>
            <span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">ul</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">div</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">div</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">div</span>&gt;</span></pre></td></tr></table></div>

<p><a title="在线调试" href="http://www.gbin1.com/gb/debug/f30bca93-287d-4303-8cdb-ba0d5c1c26af.htm">在线调试</a></p>
<p><img src="http://d2o0t5hpnwv4c1.cloudfront.net/2150_NewInjQueryMobile/images/popups-nested-menu.png" alt="collapsible  menu"></p>
<h3>Form（表单）</h3>
<p>我们也可以生成弹出式样的登录表单，如下：</p>

<div><table><tr><td><pre>1
2
3
4
5
6
7
8
9
10
11
</pre></td><td><pre style="font-family:monospace"><span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;#login&quot;</span> data-<span style="color:#000066">rel</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;popup&quot;</span> data-position-to<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;window&quot;</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;button&quot;</span>&gt;</span>Login<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">div</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;popup&quot;</span> <span style="color:#000066">id</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;login&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;a&quot;</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">form</span> <span style="color:#000066">style</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;padding:10px 20px;&quot;</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">h3</span>&gt;</span>Please sign in<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">h3</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">label</span> <span style="color:#000066">for</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;un&quot;</span>&gt;</span>Username:<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">label</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">input</span> <span style="color:#000066">type</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;text&quot;</span> <span style="color:#000066">name</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;user&quot;</span> <span style="color:#000066">id</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;un&quot;</span> placeholder<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;username&quot;</span> <span style="color:#66cc66">/</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">label</span> <span style="color:#000066">for</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;pw&quot;</span>&gt;</span>Password:<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">label</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">input</span> <span style="color:#000066">type</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;password&quot;</span> <span style="color:#000066">name</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;pass&quot;</span> <span style="color:#000066">id</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;pw&quot;</span> placeholder<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;password&quot;</span> <span style="color:#66cc66">/</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">button</span> <span style="color:#000066">type</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;submit&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;b&quot;</span>&gt;</span>Sign in<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">button</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">form</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">div</span>&gt;</span></pre></td></tr></table></div>

<p><a title="在线调试" href="http://www.gbin1.com/gb/debug/cd955dad-28ab-444d-b264-d88f06efc202.htm">在线调试</a></p>
<p><img src="http://d2o0t5hpnwv4c1.cloudfront.net/2150_NewInjQueryMobile/images/popup-form.png" alt="form"></p>
<h3>Dialogs（对话框）</h3>
<p>对话框也是我们常用的，如下：</p>

<div><table><tr><td><pre>1
2
3
4
5
6
7
8
9
10
11
12
</pre></td><td><pre style="font-family:monospace"><span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;#dialog&quot;</span> data-<span style="color:#000066">rel</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;popup&quot;</span> data-position-to<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;window&quot;</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;button&quot;</span> data-transition<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;pop&quot;</span>&gt;</span>Dialog<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">div</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;popup&quot;</span> <span style="color:#000066">id</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;dialog&quot;</span> data-overlay-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;a&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;c&quot;</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">div</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;header&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;a&quot;</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">h1</span>&gt;</span>Delete Page?<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">h1</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">div</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">div</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;content&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;d&quot;</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">h3</span>&gt;</span>Are you sure you want to delete this page?<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">h3</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">p</span>&gt;</span>This action cannot be undone.<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">p</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;#&quot;</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;button&quot;</span> data-inline<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;true&quot;</span> data-<span style="color:#000066">rel</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;back&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;c&quot;</span>&gt;</span>No<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;#&quot;</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;button&quot;</span> data-inline<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;true&quot;</span> data-<span style="color:#000066">rel</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;back&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;b&quot;</span>&gt;</span>Yes, Delete it<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">div</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">div</span>&gt;</span></pre></td></tr></table></div>

<p><a title="在线调试" href="http://www.gbin1.com/gb/debug/d257cf0c-0a07-46c1-9044-c377f3750d58.htm">在线调试</a></p>
<p><img src="http://d2o0t5hpnwv4c1.cloudfront.net/2150_NewInjQueryMobile/images/popup-dialog.png" alt="dialog"></p>
<h3>Photos（图片展示）</h3>
<p>使用悬浮方式展示图片，如下：</p>

<div><table><tr><td><pre>1
2
3
4
</pre></td><td><pre style="font-family:monospace"><span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;#photo&quot;</span> data-<span style="color:#000066">rel</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;popup&quot;</span> data-position-to<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;window&quot;</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;button&quot;</span> data-transition<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;fade&quot;</span>&gt;</span>Photo<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">div</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;popup&quot;</span> <span style="color:#000066">id</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;photo&quot;</span> data-overlay-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;a&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;d&quot;</span> data-corners<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;false&quot;</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;#&quot;</span> data-<span style="color:#000066">rel</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;back&quot;</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;button&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;a&quot;</span> data-icon<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;delete&quot;</span> data-iconpos<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;nojs&quot;</span>&gt;</span>Close<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;&lt;<span style="color:#000000;font-weight:bold">img</span> <span style="color:#000066">src</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;http://www.gbin1.com//gbin1/themes/gbin1_2column_page/images/logo.png&quot;</span> <span style="color:#66cc66">/</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">div</span>&gt;</span></pre></td></tr></table></div>

<p><a title="在线调试" href="http://www.gbin1.com/gb/debug/88495a66-49a4-4991-9845-308b85954d7b.htm">在线调试</a></p>
<p><img src="http://d2o0t5hpnwv4c1.cloudfront.net/2150_NewInjQueryMobile/images/popup-photo.png" alt="photo" width="482" height="339"></p>
<h3>List Views （可缩放列表）</h3>
<p>这个效果肯定你也会需要：</p>

<div><table><tr><td><pre>1
2
3
4
5
6
7
8
9
10
</pre></td><td><pre style="font-family:monospace"><span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">div</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;collapsible&quot;</span> data-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;b&quot;</span> data-content-theme<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;c&quot;</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">h2</span>&gt;</span>Favorite Spice Girl?<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">h2</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">ul</span> data-role<span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;listview&quot;</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">li</span>&gt;&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;index.html&quot;</span>&gt;</span>Posh<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">li</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">li</span>&gt;&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;index.html&quot;</span>&gt;</span>Scary<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">li</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">li</span>&gt;&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;index.html&quot;</span>&gt;</span>Sporty<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">li</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">li</span>&gt;&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;index.html&quot;</span>&gt;</span>Baby<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">li</span>&gt;</span>
        <span style="color:#009900">&lt;<span style="color:#000000;font-weight:bold">li</span>&gt;&lt;<span style="color:#000000;font-weight:bold">a</span> <span style="color:#000066">href</span><span style="color:#66cc66">=</span><span style="color:#ff0000">&quot;index.html&quot;</span>&gt;</span>Ginger<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">a</span>&gt;&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">li</span>&gt;</span>
    <span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">ul</span>&gt;</span>
<span style="color:#009900">&lt;<span style="color:#66cc66">/</span><span style="color:#000000;font-weight:bold">div</span>&gt;</span></pre></td></tr></table></div>

<p><a title="在线调试" href="http://www.gbin1.com/gb/debug/88495a66-49a4-4991-9845-308b85954d7b.htm">在线调试</a></p>
<h3><img src="http://d2o0t5hpnwv4c1.cloudfront.net/2150_NewInjQueryMobile/images/listview-collapsible.png" alt="Collapsible "></h3>
<h2>功能加强</h2>
<p>其它的更新包括了一些功能上的加强。</p>
<h3>jQuery支持更新</h3>
<p>开始支持jQuery1.8。sizzle.js的重写带来了性能的提升及其其它。并且开始考虑放弃支持1.6，如果你使用旧版本的话，可能会不舒服。</p>
<h3>列表视图的自动分隔</h3>
<p>原来：</p>
<p><img src="http://d2o0t5hpnwv4c1.cloudfront.net/2150_NewInjQueryMobile/images/listview-dividers-before.png" alt="List View Autodividers" width="380" height="283"></p>
<p>现在：</p>
<p><img src="http://d2o0t5hpnwv4c1.cloudfront.net/2150_NewInjQueryMobile/images/listview-dividers-after.png" alt="List View Autodividers"></p>
<p>注意这里不包括管理排序，分组和过滤。如果你需要类似功能，考虑这个插件：<a title="jqm-tinysort" href="http://andymatthews.net/code/jqm-tinysort/">jQuery Mobile Tinysort plugin.</a></p>
<h3>只读列表</h3>
<p>提供了只读列表，但是对于能不能点击不是很明显。</p>
<h3>提供了更好的表单元素宽度调整</h3>
<p>jQuery mobile version1.2解决了表单元素的问题，有时候不能完全的占有父元素宽度</p>
<h3>其它的设备的支持</h3>
<p>添加了另外一些平台：iOS6；Android1.4(Jellybean)，Tizen，firefox for Android和Kindle Fire HD。</p>
<h3>完整的更新列表</h3>
<p>你可以在官方博客来查看完整的更新，希望我们的介绍能够给你带来一部分的新版本体验，如果你有任何问题，请给我们留言！via <a title="tutsplus" href="http://net.tutsplus.com/">tutsplus</a></p>
<p>来源：<a href="http://223.4.150.152/technology/jquerymobile/20121121-jquery-mobile-latest-updates/">最新jQuery Mobile 1.2版本新特性</a></p>
<img src="http://feeds.feedburner.com/~r/qianduannet/~4/7bkIIZyCYdA" height="1" width="1">