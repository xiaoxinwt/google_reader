---
layout: post
title:  "小tip:我是如何在实际项目中使用before/after伪类的"
date:   2012-11-16 12:02:11
author: 张 鑫旭
categories: program
---

## 小tip:我是如何在实际项目中使用before/after伪类的
### by 张 鑫旭
### at 2012-11-16 12:02:11
### original <http://www.zhangxinxu.com/wordpress/2012/11/before-after-use-web/>

<p>by <a href="http://www.zhangxinxu.com/">zhangxinxu</a> from <a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a><br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2781">http://www.zhangxinxu.com/wordpress/?p=2781</a></p>
<h3>一、诉说前言</h3>
<p>before/after伪类相当于在元素内部插入两个额外的标签，其最适合也是最推荐的应用就是<code>图形生成</code>。在一些精致的UI实现上，可以简化HTML代码，提高可读性和可维护性。国外这两个伪类应用相当的普及以及兴盛，不过貌似我们这边前端er们普遍缺乏使用这两个伪类的意识，要使用顶多也就是跟风的“清除浮动”应用。</p>
<p>我想，这可能受兼容性思维影响——IE6/7浏览器并不支持，还是等什么时候IE6/IE7换届下台的时候再说吧~~</p>
<p>虽然八尾九尾都是残体，并不影响十尾的成型。一粒老鼠屎掉进锅里，你就真让这一锅红烧肉毁了——把有翔的这部分给阿黄（中华田园犬）吃就可以了。</p>
<p>本文简单展示我在实际项目中，应用before/after伪类时候，如何CSS命名的，HTML有哪些处理，如何对付IE6, IE7浏览器的。</p>
<h3>二、大致处理流程</h3>
<ol>
<li><strong>CSS部分</strong><br>
类似这样子的命名：
<div>
<pre>.example:before, .example before {}
.example:after, .example after {}</pre>
</div>
<p>一个有冒号，一个是空格分隔。前者IE8+及其他现代浏览器；后者为IE6-7准备的。</p></li>
<li><strong>HTML部分</strong><br>
如果before/after伪类元素含有<code>content</code>内容（不是空字符），则content内容在HTML标签上呈现（方便IE6/7下获取），例如：
<div>
<pre>&lt;div class=&quot;example&quot; data-content=&quot;◀&quot;&gt;&lt;/div&gt;</pre>
</div>
<p>CSS部分的<code>content</code>属性值应该如下：
<div>
<pre>.example:before, .example before { <span style="color:#cd0000">content: attr(data-content);</span> ... }
.example:after, .example after { <span style="color:#cd0000">content: attr(data-content);</span> ... }</pre>
</div>
</p></li>
<li><strong>JS部分</strong><br>
JS是为IE6/7准备的，所做的事情很简单，网元素内部插入两个元素，标签名为”<code>before</code>“或”<code>after</code>“或同时。方法代码如下：
<div>
<pre>var $beforeAfter = function(dom) {
    if (document.querySelector || !dom &amp;&amp; dom.nodeType !== 1) return;

    var content = dom.getAttribute(&quot;data-content&quot;) || &#39;&#39;;
    var before = document.createElement(&quot;before&quot;)
        , after = document.createElement(&quot;after&quot;);

    <span style="color:green">// 内部content</span>
    before.innerHTML = content;
    after.innerHTML = content;
    <span style="color:green">// 前后分别插入节点</span>
    dom.insertBefore(before, dom.firstChild);
    dom.appendChild(after);
};</pre>
</div>
</li>
</ol>
<p>于是，您想要的效果就可以实现了，例如下面的例子。</p>
<h3>三、实际应用之含尖角的tip类提示效果</h3>
<p>下面是效果图，IE6下以及FireFox16下的效果截图：<br>
<img alt="IE6下tip类提示效果截图 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201211/2012-11-16_114728.png" title="IE6下tip类提示效果截图" width="292" height="117"> <img alt="FireFox16下tip提示效果after/before下效果 张鑫旭-鑫空间-鑫生活" src="http://image.zhangxinxu.com/image/blog/201211/2012-11-16_114759.png" title="FireFox16下tip提示效果after/before下效果" width="291" height="119"></p>
<p>您可以狠狠地点击这里：<a href="http://www.zhangxinxu.com/study/201211/before-after-tip-effect.html">before/after生成的tip类提示效果demo</a></p>
<p>IE6/7的CSS与IE8+基本上都是公用的，唯一不同就是选择器一个空格间开，一个冒号伪类，如下：</p>
<div>
<pre>.tip:before, .tip before,
.tip:after, .tip after{...}</pre>
</div>
<p>然后，JS如下一调用即可：</p>
<div>
<pre>$beforeAfter(document.getElementById("tip"));</pre>
</div>
<p>OK，全部兼容，完事OK。</p>
<p>一般而言，after以及before生成的元素只能是一些不影响功能或是重要视觉的效果。因此，IE6/7下JS驱动不关紧要效果的生成是相当OK的。</p>
<p>赶着帮老婆带饭，抛砖引玉，就这些！</p>
<p>原创文章，转载请注明来自<a href="http://www.zhangxinxu.com/">张鑫旭-鑫空间-鑫生活</a>[<a href="http://www.zhangxinxu.com/">http://www.zhangxinxu.com</a>]<br>
本文地址：<a href="http://www.zhangxinxu.com/wordpress/?p=2781">http://www.zhangxinxu.com/wordpress/?p=2781</a></p>
<p>（本篇完）</p>
<div>有话要说，点击<a href="http://www.zhangxinxu.com/wordpress/2012/11/before-after-use-web/#response">这里</a>发表评论。</div>