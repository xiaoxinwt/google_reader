---
layout: post
title:  "设备像素比devicePixelRatio简单介绍"
date:   2012-08-23 15:56:39
author: 张 鑫旭
categories: program
---

## 设备像素比devicePixelRatio简单介绍
### by 张 鑫旭
### at 2012-08-23 15:56:39
### original <http://www.zhangxinxu.com/wordpress/2012/08/window-devicepixelratio/>

<p>by <a href="http://www.zhangxinxu.com/">zhangxinxu</a> from <a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a><br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2568">http://www.zhangxinxu.com/wordpress/?p=2568</a></p>
<p>本文所说<code>devicePixelRatio</code>其实指的是<code>window.devicePixelRatio</code>, 被所有WebKit浏览器以及Opera所支持，随着显示器的发展，这个属性也慢慢登上了前端技术的舞台。</p>
<p>本文内容大部分属于翻译性质内容，因此，会不那么通俗易懂。不过，你是做手机开发的，或是有意向的，本文的内容如果细细读来，还是有些收获的。</p>
<h3>一、定义</h3>
<p>定义如下：</p>
<blockquote><p>window.devicePixelRatio是设备上物理像素和设备独立像素(device-independent pixels (dips))的比例。<br>
公式表示就是：window.devicePixelRatio = 物理像素 / dips</p></blockquote>
<p>dip或dp,（device independent pixels，设备独立像素）与屏幕密度有关。dip可以用来辅助区分视网膜设备还是非视网膜设备。</p>
<p>所有非视网膜屏幕的iphone在垂直的时候，宽度为320物理像素。当你使用<code>&lt;meta name=&quot;viewport&quot; content=&quot;width=device-width&quot;&gt;</code>的时候，会设置视窗布局宽度（不同于视觉区域宽度，不放大显示情况下，两者大小一致，见下图）为320px, 于是，页面很自然地覆盖在屏幕上。</p>
<p><img alt="布局视图大小 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201208/mobile_viewportzoomedout.jpg" title="布局视图大小 张鑫旭-鑫空间-鑫生活" width="229" height="402"></p>
<p>这样，非视网膜屏幕的iphone上，屏幕物理像素320像素，独立像素也是320像素，因此，<code>window.devicePixelRatio</code>等于<code>1</code>.</p>
<p>而对于视网膜屏幕的iphone，如iphone4s, 纵向显示的时候，屏幕物理像素640像素。同样，当用户设置<code>&lt;meta name=&quot;viewport&quot; content=&quot;width=device-width&quot;&gt;</code>的时候，其视区宽度并不是640像素，而是320像素，这是为了有更好的阅读体验 – 更合适的文字大小。</p>
<p>这样，在视网膜屏幕的iphone上，屏幕物理像素640像素，独立像素还是320像素，因此，<code>window.devicePixelRatio</code>等于<code>2</code>.</p>
<h3>二、浏览器支持</h3>
<ul>
<li>IE以及FireFox压根不支持。可能接下来的版本会支持。</li>
<li>Opera桌面浏览器时，即使是视网膜设备，返回的值也是1而不是2. 不过，这个bug在后续的版本中会修复的。</li>
<li>Opera Mobile 10不支持，不过Opera Mobile 12正确支持。</li>
<li>UC总是显示1，不过其viewport属性有些让人费解。</li>
<li>只有最近的Chrome浏览器才能正确实现该属性。Chrome19返回不准确的1, Chrome22可以正确返回2.</li>
<li>MeeGo WebKit (Nokia N9/N950)就吓人了：当你应用了meta viewport时候（类似<code>&lt;meta name=&quot;viewport&quot; content=&quot;width=device-width&quot;&gt;</code>），值会从1变成1.5!</li>
</ul>
<p>真是喜忧参半。好的是Safari, Android WebKit, Chrome 22+(Android), Opera Mobile, BlackBerry WebKit, QQ, Palm WebKit, 及Dolfin都能正确实现该属性。</p>
<p>当然，大部分这些浏览器仍然运行在<code>devicePixelRatio</code>值应该为<code>1</code>的系统上，当它们移动到视网膜类似设备时候，可能就会遇到问题。</p>
<p>两个注意事项：<br>
MeeGo WebKit <code>meta viewport</code>应用时改变值的做法是大错特错的。设备像素比应该是不变的，不仅物理像素值，设备独立像素也是如此。</p>
<p>二是，一些浏览器习惯在<code>meta viewport</code>应用时改变各种东西（三星的Dolfin就是代表），这完全就是在瞎搞。唯一的变化应该是布局视图的尺寸。如果浏览器变了其他什么都是，那都是很挫的。</p>
<p><strong>实际测试</strong><br>
您可以狠狠地点击这里：<a href="http://www.zhangxinxu.com/study/201208/window-device-pixel-ratio.html">window.devicePixelRatio值支持与否测试demo</a></p>
<p>例如，我现在的FireFox桌面版(14.0.1)弹出的就是<code>undefined</code>, 如下图：<br>
<img alt="FireFox不支持devicePixelRatio结果截图 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201208/2012-08-23_140354.png" title="FireFox不支持devicePixelRatio结果截图" width="280" height="186"></p>
<p>Chrome下是认识这个属性的，在我机子上弹出的是<code>1</code>, 如下图：<br>
<img alt="Chrome浏览器下devicePixelRatio弹出结果截图 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201208/2012-08-23_140751.png" title="Chrome浏览器下devicePixelRatio弹出结果截图 张鑫旭-鑫空间-鑫生活" width="396" height="171"></p>
<h3>其他一些系统、设备</h3>
<p><strong>1. iOS</strong><br>
类似的，无视网膜设备<code>devicePixelRatio</code>值为<code>1</code>，视网膜设备为<code>2</code>. 因为实际的像素个数是双倍。不过，iphone似乎不愿意改变大家都熟知习惯的320像素宽度布局，没有把设备宽度一下子变成640像素，因此，dips宽度依然是320, 于是<code>devicePixelRatio</code>就是<code>640/320 = 2</code>.</p>
<p>iOS上的情况要相对简单些，除了<code>1</code>就是<code>2</code>. 在其他平台也基本上很简单，因为一般分辨率都比较挫，<code>devicePixelRatio</code>都是<code>1</code>.</p>
<p><strong>2. Android</strong><br>
据我所知，谷歌的Nexus One是第一个使用dips的，比iphone还早。同时Galaxy Nexus以及Galaxy Note都是类运动视网膜显示器。近距离探究这三个设备应该会有所收获。</p>
<p>Nexus One分辨率是480*800, 为了最优的页面浏览，Android WebKit团队决定纵向手持时候的宽度依然是320像素，因此，<code>devicePixelRatio</code>值为<code>480/320 = 1.5</code>.<br>
在同一手机上，Opera Mobile有相同的结论，dips为320宽，<code>devicePixelRatio</code>也是<code>1.5</code> .</p>
<p>顺便提一下，BlackBerry Torch 9810(OS7)物理像素同样480像素，BlackBerry WebKit团队决定坚持<code>devicePixelRatio</code>为<code>1</code>. 这可能是更不错的做法，在Torch显示器上480px宽度站点或多或少有些难以阅读。</p>
<p>Galaxy Nexus有像素的提升，为720×1200. Android团队决定提高dips层的宽度到360像素。从而使<code>devicePixelRatio</code>为<code>720/360 = 2</code>. Chrome团队决定跟进，就如腾讯QQ浏览器所做的那样。</p>
<p>然而，Opera，坚持自我，dips宽度为320px, 于是<code>devicePixelRatio</code>为<code>720/320 = 2.25</code>. 不过似乎还与zoom缩放层级有关。</p>
<p>Galaxy Note物理像素为800×1200. 这里所有浏览器都决定使用与Galaxy Nexus一样的比率：Android WebKit, Chrome, 以及QQ都是<code>2</code>，也就意味着其dips宽度为400px. 然而，Opera依然一意孤行<code>2.25</code>, 于是其dips宽度值有些怪怪的: 356px.</p>
<p>Android标准似乎不严格，于是自家人玩自家人的游戏，对于开发者而言，可能又会面临苦逼~~</p>
<p><strong>3. 视网膜MacBook</strong><br>
新的MacBook采用视网膜显示屏，其<code>devicePixelRatio</code>是（如果不出意外）<code>2</code>. 视网膜MacBook的物理像素是2800×1800，而显示出分辨率为1400×900，如果把分辨率作为dips层，则<code>devicePixelRatio</code>为<code>2</code>应该是无误的。</p>
<p>需要指出的是，如果你把分辨率改成1920×1200，<code>devicePixelRatio</code>依然是<code>2</code>. 严格来讲，这是不准确的，应该是<code>1.5</code>, 然而你也可以说MacBook的分辨率不同于dips层，这种情况下<code>devicePixelRatio</code>在台式机/笔记本下的定义就不一样（哪一个？不知道。）。</p>
<p>在任何情况下，根据苹果的规范做法，<code>devicePixelRatio</code>值只可能是<code>1</code>或者<code>2</code>. 如果你看到<code>2</code>，你要提供视网膜优化显示图片，如果是<code>1</code>，使用正常的图片——（这里内容其实属于视网膜站点的开发内容）。</p>
<h3>四、其他相关属性</h3>
<p>当页面设置了<code>&lt;meta name=&quot;viewport&quot; content=&quot;width=device-width&quot;&gt;</code>时候，<code>document.documentElement.clientWidth</code>在大部分浏览器下，得到的是布局视区的宽度，等同于dips的宽度。</p>
<p>对于<code>screen.width</code>的值：</p>
<ul>
<li>在iOS视网膜设备上，<code>screen.width</code>返回dips宽。因此，在竖着显示的时候，视网膜显示屏的ipad和非视网膜显示屏的ipad返回的都是768.</li>
<li>在上面提到的三个Android设备上，<code>screen.width</code>返回的是物理像素宽度，分别480, 720, 和800. 该设备上的所有浏览器都是该值。</li>
</ul>
<p><a href="http://vasilis.nl/">Vasilis</a>有一个很好的理论：苹果像素，因为它想使显示更清晰，更流畅，而Android厂商增加的像素在屏幕上塞进更多的东西。它解释了为什么苹果强调非视网膜视网膜的连续性，而Android集中在原始像素数。</p>
<p>Nokia Lumia Windows Phone上的IE9 <code>screen.width</code>的值与Android设备一样，返回的是物理像素。而且其不支持<code>devicePixelRatio</code>. 因此，我们无法从中看出其对待像素的态度是如何的。</p>
<p><strong>小小结论</strong></p>
<ol>
<li><code>devicePixelRatio</code>在大多数浏览器是值得信赖的。</li>
<li>在iOS设备，<code>screen.width</code>乘以<code>devicePixelRatio</code>得到的是物理像素值。</li>
<li>在Android以及Windows Phone设备，<code>screen.width</code>除以<code>devicePixelRatio</code>得到的是设备独立像素(dips)值。</li>
</ol>
<p>注：本文的DIPs切勿和DPI搞混了！DPI指每英寸点的个数，本文的DIPs指设备独立像素。</p>
<p>参考文章：<br>
<a href="http://www.quirksmode.org/blog/archives/2012/06/devicepixelrati.html">devicePixelRatio</a><br>
<a href="http://www.quirksmode.org/blog/archives/2012/07/more_about_devi.html">More about devicePixelRatio</a></p>
<p>原创文章，转载请注明来自<a href="http://www.zhangxinxu.com/">张鑫旭-鑫空间-鑫生活</a>[<a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a>]<br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2568">http://www.zhangxinxu.com/wordpress/?p=2568</a></p>
<p>（本篇完）</p>
<div>有话要说，点击<a href="http://www.zhangxinxu.com/wordpress/2012/08/window-devicepixelratio/#response">这里</a>发表评论。</div>