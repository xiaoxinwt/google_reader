---
layout: post
title:  "HTML5之Canvas标签简要学习"
date:   2010-10-16 22:04:00
author: 独钓寒江
categories: program
---

## HTML5之Canvas标签简要学习
### by 独钓寒江
### at 2010-10-16 22:04:00
### original <http://www.cnblogs.com/youring2/archive/2010/10/16/1853260.html>

<p><p>    HTML5 提供了画布（canvas）标签，通过与Javascript结合使用，可以在网页上绘制图像。Canvas是一个矩形区域，使用Javascript可以控制其每一个像素。本文将对canvas标签进行简要的学习。</p>
<p><strong>1、canvas标签说明</strong></p>
<p>    canvas标签是一个矩形区域，它包含两个属性width和height，分别表示矩形区域的宽和高，这两个属性都是可选的，并且都可以通过css来设定，他们的默认值是300px和150px。canvas在网页中的形式如下：</p>
<blockquote><pre>&lt;canvas id=&quot;myCanvas&quot; width=”300” height=”200” style=&quot;border:1px solid #c3c3c3;&quot;&gt;
Your browser does not support the canvas element.
&lt;/canvas&gt;</pre></blockquote><pre> 解析：</pre>
<blockquote><pre>为canvas设置id属性是便于Javascript调用；</pre><pre>第二行是当浏览器不支持canvas标签时，将显示这行文字。</pre></blockquote>
<p> </p>
<p><strong>2、检测浏览器支持</strong></p>
<p>Canvas标签并没有得到目前所有浏览器的支持，因此在使用canvas绘图的时候，要先检测客户端浏览器是否支持。下面的示例将通过Javascript判断浏览器是否支持： </p>
<blockquote><pre>&lt;canvas id=&quot;myCanvas&quot; width=”300” height=”200” style=&quot;border:1px solid #c3c3c3;&quot;&gt;
Your browser does not support the canvas element.
&lt;/canvas&gt;

&lt;script type=&quot;text/javascript&quot;&gt;
    var myCanvas = document.getElementById(&quot;myCanvas&quot;);
    if (!myCanvas.getContext)
    {
        alert(&quot;Your browser does not support the canvas element.&quot;);
    }
    else
    {
        // do something here
    }
&lt;/script&gt;</pre></blockquote>
<p>解析：</p>
<blockquote>
<p>上面的Javascript代码通过判断getContext方法是否为空来判断浏览器是否支持canvas标签。</p></blockquote>
<p> </p>
<p><strong>3、绘制线条和图案</strong></p>
<p>    在没有canvas之前，想要在网页上面绘制线条或图案，是非常麻烦且不兼容的。我曾在《<a href="http://www.cnblogs.com/youring2/archive/2009/07/25/1530862.html"><font color="#000000">js画直线</font></a>》一文中转载了网络上常用的在IE和FF中绘制线条的方法，但如果想要这些方法通用，则是不可能的。但现在有了canvas标签，一切就变得简单了。</p>
<p>    现在，你只需要在页面上添加canvas标签，并通过Javascript调用它的相应方法，就可以轻松的在页面上绘制线条和图案。</p>
<blockquote>
<p>//绘制线条</p><pre>&lt;canvas id=&quot;myCanvas&quot;  style=&quot;border:1px solid #c3c3c3;&quot;&gt;
Your browser does not support the canvas element.
&lt;/canvas&gt;

&lt;script type=&quot;text/javascript&quot;&gt;
    var myCanvas = document.getElementById(&quot;myCanvas&quot;);
    if (!myCanvas.getContext)
    {
        alert(&quot;Your browser does not support the canvas element.&quot;);
    }
    else
    {
        var myContext = myCanvas.getContext(&quot;2d&quot;);
        myContext.moveTo(200, 150);
        myContext.lineTo(100, 100);
        myContext.lineTo(200, 50);
        myContext.strokeStyle = &quot;#FF0000&quot;;
        myContext.lineWidth = 4;
        myContext.stroke();

    }   
&lt;/script&gt;</pre></blockquote>
<p>解析：</p>
<blockquote>
<p>显示在页面中放置了一个canvas标签，设定id、width、height等属性，并为这个标签设定边框。</p>
<p>在Javascript中检测浏览器是否支持</p>
<p>当浏览器支持的时候开始调用getContext方法获得绘图的上下文（这地方很像vc中的GDI绘图~~），目前只提供了2D上下文，将来还有可能支持OpenGL ES的3D上下文。</p>
<p>moveTo方法是将当前位置移动到指定的坐标</p>
<p>lineTo方法是向指定的左边绘制直线</p>
<p>strokeStyle 属性是指定线条的颜色，在本例子中指定为红色</p>
<p>lineWidth 属性设置线条的粗细，这里设置为4px</p>
<p>stroke方法则是将直线显示出来。貌似是之前只是规定了哪里有线条，并没有显示，当调用这个方法后，线条才可以显示。</p></blockquote>
<p> </p>
<blockquote>
<p>// 绘制图案</p>
<p> </p><pre>&lt;canvas id=&quot;myCanvas&quot; width=&quot;300&quot; height=&quot;200&quot;  style=&quot;border:1px solid #c3c3c3;&quot;&gt;
Your browser does not support the canvas element.
&lt;/canvas&gt;

&lt;script type=&quot;text/javascript&quot;&gt;
    var myCanvas = document.getElementById(&quot;myCanvas&quot;);
    if (!myCanvas.getContext)
    {
        alert(&quot;Your browser does not support the canvas element.&quot;);
    }
    else
    {
        var myContext = myCanvas.getContext(&quot;2d&quot;);

        myContext.fillStyle = &quot;rgb(200,0,0)&quot;;
        myContext.fillRect(10, 10, 55, 50);

        myContext.fillStyle = &quot;rgba(0, 0, 200, 0.5)&quot;;
        myContext.fillRect(30, 30, 55, 50);

        myContext.fillRect(100, 0, 150, 50);
        myContext.strokeRect(100, 60, 150, 50);
        myContext.clearRect(130, 10, 90, 30);
    }
&lt;/script&gt;</pre></blockquote>
<p>解析：</p>
<blockquote>
<p>直接到fillStyle属性，之前的代码是相同的，不再赘言。</p>
<p>fillStyle属性设定了填充的颜色、透明度等，如果设置为"rgb(200,0,0)"，则表示一个颜色，不透明；如果设置为"rgba(0,0,200,0.5)"，则表示颜色为rgb(0,0,200)，透明度为50%，</p>
<p>fillRect方法是绘制一个矩形，这个矩形区域没有边框，只有填充色。这个方法有四个参数，前两个表示左上角的坐标位置，第三个参数为长度，第四个参数为高度。</p>
<p>strokeRect方法绘制一个带边框的矩形。该方法的四个参数的解释同上。</p>
<p>clearRect方法是清除一个矩形区域，被清除的区域将没有任何线条。该方法的四个参数的解释同上。</p></blockquote>
<p> </p>
<p><strong>4、绘制路径</strong></p>
<p>    路径是绘制自定义图形的好方法，在canvas中通过beginPath()方法开始绘制路径，这个时侯你就可以绘制直线、曲线等，绘制完成后调用fill()和stroke()完成填充和设置边框，通过closePath()方法结束路径的绘制。下面的例子讲演时如何绘制路径：</p>
<blockquote><pre>&lt;canvas id=&quot;myCanvas&quot; width=&quot;300&quot; height=&quot;200&quot;  style=&quot;border:1px solid #c3c3c3;&quot;&gt;
Your browser does not support the canvas element.
&lt;/canvas&gt;

&lt;script type=&quot;text/javascript&quot;&gt;
    var myCanvas = document.getElementById(&quot;myCanvas&quot;);
    if (!myCanvas.getContext)
    {
        alert(&quot;Your browser does not support the canvas element.&quot;);
    }
    else
    {
        var myContext = myCanvas.getContext(&quot;2d&quot;);
        
        myContext.fillStyle = &#39;#0000ff&#39;;
        myContext.strokeStyle = &#39;#ff0000&#39;;
        myContext.lineWidth = 2;
        
        myContext.beginPath();
        
        myContext.moveTo(30, 30);
        myContext.lineTo(150, 30);
        myContext.lineTo(150, 120);
        myContext.lineTo(90, 100);
        myContext.lineTo(120, 60);
        myContext.lineTo(30, 30);
        
        myContext.fill();
        myContext.stroke();
        
        myContext.closePath();
    }</pre></blockquote>
<p>解析：</p>
<blockquote>
<p>在获得渲染上下文后，设置了填充色、边框色和边框宽度。</p>
<p>调用beginPath()方法开始绘制路径</p>
<p>通过lineTo()方法绘制一个闭合的自定义图形</p>
<p>调用fill()方法进行填充</p>
<p>调用stroke()方法设置边框</p>
<p>调用closePath()方法结束路径的绘制</p></blockquote><img src="http://www.cnblogs.com/youring2/aggbug/1853260.html?type=1" width="1" height="1" alt=""><p>作者: <a href="http://www.cnblogs.com/youring2/">独钓寒江</a> 发表于 2010-10-16 22:04 <a href="http://www.cnblogs.com/youring2/archive/2010/10/16/1853260.html">原文链接</a></p><p>评论: 1　<a href="http://www.cnblogs.com/youring2/archive/2010/10/16/1853260.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/youring2/archive/2010/10/16/1853260.html#commentform">发表评论</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/77609/">10月17日IT微博TOP10：苹果的霸王条款(10/17 18:02)</a><span style="color:gray">(2010-10-17 18:50)</span><br>· <a href="http://news.cnblogs.com/n/77607/">开心网高调宣布盈利遭质疑：存在三点问题</a><span style="color:gray">(2010-10-17 18:37)</span><br>· <a href="http://news.cnblogs.com/n/77596/">微软首席软件架构师 Ray Ozzie（再次）开始写博客</a><span style="color:gray">(2010-10-17 17:08)</span><br>· <a href="http://news.cnblogs.com/n/77593/">如何跟程序员沟通？</a><span style="color:gray">(2010-10-17 15:45)</span><br>· <a href="http://news.cnblogs.com/n/77592/">Oracle要求LibreOffice成员离开OOo社区委员会</a><span style="color:gray">(2010-10-17 15:38)</span><br></p><p>编辑推荐：<a href="http://www.cnblogs.com/ClassroomStudio/archive/2010/10/16/1852704.html">连载（一）参观 Google 北京办公室</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">我的园子</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://kb.cnblogs.com">知识库</a></p></p>