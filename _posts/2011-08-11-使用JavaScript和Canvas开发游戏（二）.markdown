---
layout: post
title:  "使用JavaScript和Canvas开发游戏（二）"
date:   2011-08-11 19:41:56
author: 为之漫笔
categories: program
---

## 使用JavaScript和Canvas开发游戏（二）
### by 为之漫笔
### at 2011-08-11 19:41:56
### original <http://www.cn-cuckoo.com/2011/08/11/game-development-with-javascript-and-the-canvas-element-2-2585.html>

<p>原文作者：Matthew Casperson • 编辑：Michele McDonough<br>
原文链接: <a href="http://www.brighthub.com/hubfolio/matthew-casperson/blog/archive/2009/06/29/game-development-with-javascript-and-the-canvas-element.aspx">Game Development with JavaScript and the Canvas element</a></p>
<p>1、认识一下Canvas<br>
2、在Canvas上绘图<br>
3、通过Canvas元素实现高级图像操作<br>
4、通过Canvas实现视差滚动<br>
5、写一个游戏框架（一）<br>
6、写一个游戏框架（二）<br>
7、动画<br>
8、JavaScript键盘输入<br>
9、综合运用<br>
10、定义级别<br>
11、跳跃与坠落<br>
12、添加道具<br>
13、加载资源<br>
14、添加主菜单</p>
<h1>3、通过Canvas元素实现高级图像操作</h1>
<p><a href="http://www.brighthub.com/internet/web-development/articles/39509.aspx">http://www.brighthub.com/internet/web-development/articles/39509.aspx</a></p>
<p>这篇文章将带领大家学习使用JavaScript和Canvas元素操作图像了几种不同的方式，这些方式在Canvas元素出现之前是不可能的事儿。</p>
<p>上一篇文章演示了如何利用Canvas实现一个基本的图像动画。那个例子很简单，同样的效果通过修改IMG或DIV等标准HTML元素的一些属性，照样也可以轻易实现。下面我们就来演示一下画布元素的高级应用，展示一下它的真正威力。</p>
<p>首先，还是准备一个HTML页面。</p>
<pre>

  &lt;!DOCTYPE HTML PUBLIC &quot;-//W3C//DTD HTML 4.01//EN&quot;&gt;
  &lt;html lang=&quot;en&quot;&gt;
     &lt;head&gt;
        &lt;title&gt;JavaScript Platformer 2&lt;/title&gt;
        &lt;script type=&quot;text/javascript&quot; src=&quot;jsplatformer2.js&quot;&gt;&lt;/script&gt;
        &lt;style type=&quot;text/css&quot;&gt;
           body { font-family: Arial,Helvetica,sans-serif;}
        &lt;/style&gt;
     &lt;/head&gt;
    &lt;body&gt;
       &lt;p&gt;
          &lt;a href=&quot;http://www.brighthub.com/internet/web-development/articles/38364.aspx&quot;&gt;
             Game Development with Javascript and the canvas element
          &lt;/a&gt;
       &lt;/p&gt;
       &lt;canvas id=&quot;canvas&quot; width=&quot;600&quot; height=&quot;400&quot;&gt;
          &lt;p&gt;Your browser does not support the canvas element.&lt;/p&gt;
       &lt;/canvas&gt;
       &lt;br /&gt;
       &lt;button onclick=&quot;currentFunction=alpha;&quot;&gt;Change Alpha&lt;/button&gt;
       &lt;button onclick=&quot;currentFunction=shear;&quot;&gt;Shear&lt;/button&gt;
       &lt;button onclick=&quot;currentFunction=scale;&quot;&gt;Scale&lt;/button&gt;
       &lt;button onclick=&quot;currentFunction=rotate;&quot;&gt;Rotate&lt;/button&gt;
    &lt;/body&gt;
 &lt;/html&gt;
</pre>
<p>与上个一例子的HTML页面相比，唯一的区别就是添加了一些按钮。单击这些按钮，就会设置currentFunction变量（稍后介绍）的值，用以改变在渲染循环中运行的函数。<br>
<span></span><br>
以下是 jsplatformer2.js 的代码。</p>
<pre>

 // 每秒多少帧
 const FPS = 30;
 const SECONDSBETWEENFRAMES = 1 / FPS;
 const HALFIMAGEDIMENSION = 75;
 const HALFCANVASWIDTH = 300;
 const HALFCANVASHEIGHT = 200;
 var image = new Image();
 image.src = &quot;jsplatformer2-smiley.jpg&quot;; //还是第一个例子中的图像
 var canvas = null;
 var context2D = null;
 var currentFunction = null;
 var currentTime = 0;
 var sineWave = 0;

 window.onload = init;

 function init()
 {
    canvas = document.getElementById(&#39;canvas&#39;);
    context2D = canvas.getContext(&#39;2d&#39;);
    setInterval(draw, SECONDSBETWEENFRAMES * 1000);
    currentFunction = scale;
 }

 function draw()
 {
     currentTime += SECONDSBETWEENFRAMES;
     sineWave = (Math.sin(currentTime) + 1) / 2;

     context2D.clearRect(0, 0, canvas.width, canvas.height);

     context2D.save();

     context2D.translate(HALFCANVASWIDTH - HALFIMAGEDIMENSION, HALFCANVASHEIGHT - HALFIMAGEDIMENSION);

     currentFunction();

     context2D.drawImage(image, 0, 0);

     context2D.restore();
 }

 function alpha()
 {
     context2D.globalAlpha = sineWave;
 }

 function shear()
 {
     context2D.transform(1, 0, (sineWave - 0.5), 1, 0, 0);
 }

 function scale()
 {
     context2D.translate(HALFIMAGEDIMENSION * (1 - sineWave), HALFIMAGEDIMENSION * (1 - sineWave));
     context2D.scale(sineWave, sineWave);
 }

 function rotate()
 {
     context2D.translate(HALFIMAGEDIMENSION, HALFIMAGEDIMENSION);
     context2D.rotate(sineWave * Math.PI * 2);
     context2D.translate(-HALFIMAGEDIMENSION, -HALFIMAGEDIMENSION);
 }
</pre>
<p>跟前面一样，这个JavaScript文件先定义了一些全局变量。</p>
<ul>
<li>
FPS：每秒多少帧
</li>
<li>
SECONDSBETWEENFRAMES：两帧之间间隔的秒数（FPS的倒数）
</li>
<li>
HALFIMAGEDIMENSION：要绘制图像的宽度/高度的一半，用于把图像定位到画布的中心点
</li>
<li>
HALFCANVASWIDTH：画布宽度的一半，用于配合HALFIMAGEDIMENSION使用，以便在画布上居中图像
</li>
<li>
HALFCANVASHEIGHT：画布高度的一半，用于配合HALFIMAGEDIMENSION使用，以便在画布上居中图像
</li>
<li>
currentFunction：渲染循环（参见上一篇文章）中运行的函数
</li>
<li>
currentTime：应用已经运行了多少秒
</li>
<li>
sineWave：0到1之间的一个值，用于控制图像的运动
</li>
<li>
image：要在画布上绘制的图像
</li>
<li>
canvas：画布元素的引用
</li>
<li>
context2D：画布元素的2D上下文的引用
</li>
</ul>
<p>然后，跟前面一样，要设置在window的onload事件发生时立即调用init函数（关于init函数的介绍，请参见上一篇文章）。</p>
<h2>draw函数</h2>
<p>下面来看一看draw函数：</p>
<pre>

function draw()
{
    currentTime += SECONDSBETWEENFRAMES;
    sineWave = (Math.sin(currentTime) + 1) / 2;

    context2D.clearRect(0, 0, canvas.width, canvas.height);

    context2D.save();
    context2D.translate(HALFCANVASWIDTH - HALFIMAGEDIMENSION, HALFCANVASHEIGHT - HALFIMAGEDIMENSION);
    currentFunction();
    context2D.drawImage(image, 0, 0);
    context2D.restore();
}
</pre>
<p>这个例子要演示4种效果：修改alpha值（透明度），以及缩放、旋转和切变图像。为了展示这些效果，需要基于某一范围内的值来应用变化。变量sineWave就用来定义这个范围值的基准。</p>
<p>标准的正弦函数能够在-1到1之间产生非常完美的波形图。首先，我们通过递增currentTime变量来反映动画已经运行了多长时间，然后再利用这个值在正弦曲线上找到一个点。给正弦函数返回的值（从-1到1）先加1再除以2，就可以把它们转换成0到1这个范围内的值。</p>
<pre>

    currentTime += SECONDSBETWEENFRAMES;
    sineWave = (Math.sin(currentTime) + 1) / 2;
</pre>
<p>然后，调用clearRect方法清空画布，以便为后面的绘图准备一个干净的版面。</p>
<pre>

    context2D.clearRect(0, 0, canvas.width, canvas.height);
</pre>
<p>应用到画布上面的效果是可以累积的，因而就可以利用几个简单的函数来“组合”出效果来。例如，在向屏幕上绘制之前，可能会有一艘飞船需要旋转、变换和缩放。因为所有效果都对画布起作用，所以这些效果会应用到将被绘制在屏幕上的所有对象，而不仅仅是某一幅图像或某一个形状（比如一艘飞船）。</p>
<p>其中，save和restore函数为应用这些累积的效果提供了一种简单的机制，可以将应用了这些效果的图像或图形绘制到画布上，然后“撤销”这些改变。后台的操作是什么呢？save函数把当前的绘制状态推进栈里，而restore函数则把最后一个状态弹出栈。还拿前面提到的飞船为例，需要执行下列操作：</p>
<ul>
<li>调用save函数（保存当前的绘制状态）</li>
<li>旋转、变换和缩放上下文</li>
<li>绘制飞船</li>
<li>调用restore函数，移除自上一次调用save方法以来所添加的任何效果，也就是撤销之前的变化</li>
</ul>
<p>在这里，我们就是要组合起来使用这两个方法。首先，在把任何效果应用到画布之前，先保存绘制状态。</p>
<pre>

    context2D.save();
</pre>
<p>保存了绘制状态之后，就该应用目标效果了。为此，首先调用translate函数，从而将随后要绘制的图像在画布上居中。</p>
<pre>

    context2D.translate(HALFCANVASWIDTH - HALFIMAGEDIMENSION, HALFCANVASHEIGHT - HALFIMAGEDIMENSION);
</pre>
<p>接下来，调用由变量currentFunction引用的函数。正是这些被引用的函数，是让图像发生alpha（透明度）变化以及缩放、旋转和切变的关键。这些函数我们稍后再介绍。</p>
<pre>

    currentFunction();
</pre>
<p>为图像应用完效果之后，就可以把它绘制到画布上面了。所以，接下来就是调用drawImage来绘图。</p>
<pre>

    context2D.drawImage(image, 0, 0);
</pre>
<p>最后，再调用restore函数，把自调用save函数以来应用的所有效果从画布上移除。</p>
<pre>

    context2D.restore();
</pre>
<h2>alpha函数</h2>
<pre>

 function alpha()
 {
     context2D.globalAlpha = sineWave;
 }
</pre>
<p>通过修改上下文对象的globalAlpha属性，所有后续绘制操作的透明度都会被修改。将globalAlpha设置为0，意味着被绘制的任何对象都将完全透明，而将这个属性设置为1，则意味着任何绘制操作都会保持原有的透明度级别。在此，我们通过修改这个globalAlpha属性，可以实现笑脸的淡入和淡出效果。</p>
<h2>shear函数</h2>
<pre>

 function shear()
 {
     context2D.transform(1, 0, (sineWave - 0.5), 1, 0, 0);
 }
</pre>
<p>切变操作是通过transform函数向画布应用一个矩阵来实现的。变换矩阵本身就是一个值得研究的主题，但对我们来说，如果不想理解背后的数学原理，可以在网上找到很多标准的2D变换矩阵（<a href="http://en.wikipedia.org/wiki/Transformation_matrix#Examples_in_2D_graphics">http://en.wikipedia.org/wiki/Transformation_matrix#Examples_in_2D_graphics</a>），直接使用transform函数来应用它们即可。所谓切变，其实就是把图像的顶部或底部推到一边。</p>
<h2>scale函数</h2>
<pre>

 function scale()
 {
     context2D.translate(HALFIMAGEDIMENSION * (1 - sineWave), HALFIMAGEDIMENSION * (1 - sineWave));
　   context2D.scale(sineWave, sineWave);
 }
</pre>
<p>顾名思义，scale（缩放）函数修改的是图像的大小。但在此之前，我们还调用了一次transalte函数。这是为了让缩放后的图像在画布上居中。如果你把这行代码注释掉，就会发现图像会从左上角向右下角膨胀。调用translate函数就是为抵消其圆心的位移，让图像始终居中。</p>
<h2>rotate函数</h2>
<pre>

 function rotate()
 {
     context2D.translate(HALFIMAGEDIMENSION, HALFIMAGEDIMENSION);
     context2D.rotate(sineWave * Math.PI * 2);
     context2D.translate(-HALFIMAGEDIMENSION, -HALFIMAGEDIMENSION);
 }
</pre>
<p>与scale函数类似，rotate（旋转）函数的作用也正如其名：旋转图像。与scale函数同样类似的是，这里也额外调用了translate函数以确保图像围绕中心点而不是左上角旋转。建议大家把对translate函数的调用注释掉，自己看一看结果有什么不同。</p>
<p>刚刚我们看到了使用画布元素实现的4种也还算简单的效果，这些效果使用标准的HTML元素几乎是不可能做到的。其中，有的效果可以使用scale和rotate等内置函数来实现，而使用transform函数则可以完成大量的图像操作（切变只是其中之一）。</p>
<p>看看Demo吧。<a href="http://webdemos.sourceforge.net/jsplatformer2/jsplatformer2.html">http://webdemos.sourceforge.net/jsplatformer2/jsplatformer2.html</a></p>
<p>为之漫笔 最后编辑于：<br>
2011/08/12 @ 06:29</p>