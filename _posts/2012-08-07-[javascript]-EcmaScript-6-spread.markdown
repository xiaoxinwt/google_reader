---
layout: post
title:  "[javascript] EcmaScript 6 spread"
date:   2012-08-07 01:30:00
author: 小玉西瓜
categories: program
---

## [javascript] EcmaScript 6 spread
### by 小玉西瓜
### at 2012-08-07 01:30:00
### original <http://www.cnblogs.com/enix/archive/2012/08/07/2626009.html>

<p>最近小弟在实现一个Class方法时，为了保证在不用new关键字的情况下也能产生实力，又要传递参数。所以费了很大力气，但是代码还是不够优美。今晚终于找到答案，解决方案就是es6的 spread</p><br><p> </p><br><p>具体案例请见<a href="https://github.com/Neverland/Class/blob/master/script/class.js">https://github.com/Neverland/Class/blob/master/script/class.js</a></p><br><p> </p><br><p>es6 官方对spred的解释是</p><br><div><br><ul><br><li><br><div>Replace an array or array-like actual parameter value with its elements as positional parameters</div><br></li><br><li><br><div>Let <code>new</code> compose with a <code>this</code>-free form of <code>apply</code></div><br></li><br><li><br><div>Remove (some day, and in similar future methods) automagic (and half-the-time wrong) array flattening, e.g. in <code>Array.prototype.concat</code></div><br><div><code> </code></div><br></li><br></ul><br></div><br><p><a name="sketch"></a></p><br><p>按照在下理解就是</p><br><p>1.把类数组或者数组这类实际参数值替换成多个位置参数</p><br><p>2.在使用new时不必考虑使用apply的情况</p><br><p>3.移除自动化数组扁平化</p><br><p> </p><br><p>语法：</p><br><p> ...非常简单 三个点</p><br><p> </p><br><p>具体案例：</p><br><p>1.如果同时使用new 和apply会导致语法错误 // new Elements.apply(document.getElementsByTagName('div'));</p><br><p> </p><br><div style="background-color:#f5f5f5;border:1px solid #cccccc;padding:10px"><br><pre><span style="color:#0000ff">new</span> Elements(...document.getElementsByTagName('div'));</pre><br></div><br><p> </p><br><p>2. 结构赋值，可以区段拆解</p><br><div style="background-color:#f5f5f5;border:1px solid #cccccc;padding:10px"><br><pre>var [a,...b]=[1,2,3,4];<br>a //1;<br>b //[2,3,4]</pre><br></div><br><p> </p><br><p>3.调用函数时，直接拆解类数组或数组参数</p><br><p>var a=[1,2,3,4];</p><br><p>如果调用某个函数时，把数组a拆解成4个值传入，以前要借助apply</p><br><div style="background-color:#f5f5f5;border:1px solid #cccccc;padding:10px"><br><pre>add.apply(this,a);<br></pre><br></div><br><p>现在</p><br><div style="background-color:#f5f5f5;border:1px solid #cccccc;padding:10px"><br><pre>add(...a);<br></pre><br></div><br><p>　　</p><img src="http://www.cnblogs.com/enix/aggbug/2626009.html?type=1" width="1" height="1" alt=""><p><a href="http://www.cnblogs.com/enix/archive/2012/08/07/2626009.html">本文链接</a></p>