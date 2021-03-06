---
layout: post
title:  "Javascript异步编程的4种方法"
date:   2012-12-21 16:27:34
author: 阮一峰
categories: program
---

## Javascript异步编程的4种方法
### by 阮一峰
### at 2012-12-21 16:27:34
### original <http://www.udpwork.com/item/8899.html>

<p>你可能知道，Javascript语言的执行环境是&quot;单线程&quot;（single thread）。</p>
<p>所谓&quot;单线程&quot;，就是指一次只能完成一件任务。如果有多个任务，就必须排队，前面一个任务完成，再执行后面一个任务，以此类推。</p>
<p><img src="http://image.beekka.com/blog/201212/bg2012122101.jpg"></p>
<p>这种模式的好处是实现起来比较简单，执行环境相对单纯；坏处是只要有一个任务耗时很长，后面的任务都必须排队等着，会拖延整个程序的执行。常见的浏览器无响应（假死），往往就是因为某一段Javascript代码长时间运行（比如死循环），导致整个页面卡在这个地方，其他任务无法执行。</p>
<p>为了解决这个问题，Javascript语言将任务的执行模式分成两种：同步（Synchronous）和异步（Asynchronous）。</p>
<p>&quot;同步模式&quot;就是上一段的模式，后一个任务等待前一个任务结束，然后再执行，程序的执行顺序与任务的排列顺序是一致的、同步的；&quot;异步模式&quot;则完全不同，每一个任务有一个或多个回调函数（callback），前一个任务结束后，不是执行后一个任务，而是执行回调函数，后一个任务则是不等前一个任务结束就执行，所以程序的执行顺序与任务的排列顺序是不一致的、异步的。</p>
<p><img src="http://image.beekka.com/blog/201212/bg2012122102.png"></p>
<p>&quot;异步模式&quot;非常重要。在浏览器端，耗时很长的操作都应该异步执行，避免浏览器失去响应，最好的例子就是Ajax操作。在服务器端，&quot;异步模式&quot;甚至是唯一的模式，因为执行环境是单线程的，如果允许同步执行所有http请求，服务器性能会急剧下降，很快就会失去响应。</p>
<p>本文总结了&quot;异步模式&quot;编程的4种方法，理解它们可以让你写出结构更合理、性能更出色、维护更方便的Javascript程序。</p>
<p><strong>一、回调函数</strong>
</p>
<p>这是异步编程最基本的方法。</p>
<p>假定有两个函数f1和f2，后者等待前者的执行结果。</p>
<p>　　f1();</p>
<p>　　f2();</p>
<p>如果f1是一个很耗时的任务，可以考虑改写f1，把f2写成f1的回调函数。</p>
<p>　　function f1(callback){</p>
<p>　　　　setTimeout(function () {</p>
<p>　　　　　　// f1的任务代码</p>
<p>　　　　　　callback();</p>
<p>　　　　}, 1000);</p>
<p>　　}</p>
<p>执行代码就变成下面这样：</p>
<p>　　f1(f2);</p>
<p>采用这种方式，我们把同步操作变成了异步操作，f1不会堵塞程序运行，相当于先执行程序的主要逻辑，将耗时的操作推迟执行。</p>
<p>回调函数的优点是简单、容易理解和部署，缺点是不利于代码的阅读和维护，各个部分之间高度<a href="http://en.wikipedia.org/wiki/Coupling_(computer_programming)">耦合</a>（Coupling），流程会很混乱，而且每个任务只能指定一个回调函数。</p>
<p><strong>二、事件监听</strong>
</p>
<p>另一种思路是采用事件驱动模式。任务的执行不取决于代码的顺序，而取决于某个事件是否发生。</p>
<p>还是以f1和f2为例。首先，为f1绑定一个事件（这里采用的jQuery的<a href="http://api.jquery.com/on/">写法</a>）。</p>
<p>　　f1.on('done', f2);</p>
<p>上面这行代码的意思是，当f1发生done事件，就执行f2。然后，对f1进行改写：</p>
<p>　　function f1(){</p>
<p>　　　　setTimeout(function () {</p>
<p>　　　　　　// f1的任务代码</p>
<p>　　　　　　<strong>f1.trigger('done');</strong>
</p>
<p>　　　　}, 1000);</p>
<p>　　}</p>
<p>f1.trigger('done')表示，执行完成后，立即触发done事件，从而开始执行f2。</p>
<p>这种方法的优点是比较容易理解，可以绑定多个事件，每个事件可以指定多个回调函数，而且可以<a href="http://en.wikipedia.org/wiki/Decoupling">&quot;去耦合&quot;</a>（Decoupling），有利于实现<a href="http://www.ruanyifeng.com/blog/2012/10/javascript_module.html">模块化</a>。缺点是整个程序都要变成事件驱动型，运行流程会变得很不清晰。</p>
<p><strong>三、发布/订阅</strong>
</p>
<p>上一节的&quot;事件&quot;，完全可以理解成&quot;信号&quot;。</p>
<p>我们假定，存在一个&quot;信号中心&quot;，某个任务执行完成，就向信号中心&quot;发布&quot;（publish）一个信号，其他任务可以向信号中心&quot;订阅&quot;（subscribe）这个信号，从而知道什么时候自己可以开始执行。这就叫做<a href="http://en.wikipedia.org/wiki/Publish-subscribe_pattern">&quot;发布/订阅模式&quot;</a>（publish-subscribe pattern），又称<a href="http://en.wikipedia.org/wiki/Observer_pattern">&quot;观察者模式&quot;</a>（observer pattern）。</p>
<p>这个模式有多种<a href="http://msdn.microsoft.com/en-us/magazine/hh201955.aspx">实现</a>，下面采用的是Ben Alman的<a href="https://gist.github.com/661855">Tiny Pub/Sub</a>，这是jQuery的一个插件。</p>
<p>首先，f2向&quot;信号中心&quot;jQuery订阅&quot;done&quot;信号。</p>
<p>　　jQuery.subscribe(&quot;done&quot;, f2);</p>
<p>然后，f1进行如下改写：</p>
<p>　　function f1(){</p>
<p>　　　　setTimeout(function () {</p>
<p>　　　　　　// f1的任务代码</p>
<p>　　　　　　<strong>jQuery.publish(&quot;done&quot;);</strong>
</p>
<p>　　　　}, 1000);</p>
<p>　　}</p>
<p>jQuery.publish(&quot;done&quot;)的意思是，f1执行完成后，向&quot;信号中心&quot;jQuery发布&quot;done&quot;信号，从而引发f2的执行。</p>
<p>此外，f2完成执行后，也可以取消订阅（unsubscribe）。</p>
<p>　　jQuery.unsubscribe(&quot;done&quot;, f2);</p>
<p>这种方法的性质与&quot;事件监听&quot;类似，但是明显优于后者。因为我们可以通过查看&quot;消息中心&quot;，了解存在多少信号、每个信号有多少订阅者，从而监控程序的运行。</p>
<p><strong>四、Promises对象</strong>
</p>
<p>Promises对象是CommonJS工作组提出的一种规范，目的是为异步编程提供<a href="http://wiki.commonjs.org/wiki/Promises/A">统一接口</a>。</p>
<p>简单说，它的思想是，每一个异步任务返回一个Promise对象，该对象有一个then方法，允许指定回调函数。比如，f1的回调函数f2,可以写成：</p>
<p>　　f1().then(f2);</p>
<p>f1要进行如下改写（这里使用的是jQuery的<a href="http://www.ruanyifeng.com/blog/2011/08/a_detailed_explanation_of_jquery_deferred_object.html">实现</a>）：</p>
<p>　　function f1(){</p>
<p>　　　　var dfd = $.Deferred();</p>
<p>　　　　setTimeout(function () {</p>
<p>　　　　　　// f1的任务代码</p>
<p>　　　　　　dfd.resolve();</p>
<p>　　　　}, 500);</p>
<p>　　　　<strong>return dfd.promise;</strong>
</p>
<p>　　}</p>
<p>这样写的优点在于，回调函数变成了链式写法，程序的流程可以看得很清楚，而且有一整套的<a href="http://api.jquery.com/category/deferred-object/">配套方法</a>，可以实现许多强大的功能。</p>
<p>比如，指定多个回调函数：</p>
<p>　　f1().then(f2).then(f3);</p>
<p>再比如，指定发生错误时的回调函数：</p>
<p>　　f1().then(f2).fail(f3);</p>
<p>而且，它还有一个前面三种方法都没有的好处：如果一个任务已经完成，再添加回调函数，该回调函数会立即执行。所以，你不用担心是否错过了某个事件或信号。这种方法的缺点就是编写和理解，都相对比较难。</p>
<p><strong>五、参考链接</strong>
</p>
<p>　　*<a href="http://sporto.github.com/blog/2012/12/09/callbacks-listeners-promises/">Asynchronous JS: Callbacks, Listeners, Control Flow Libs and Promises</a></p>
<p>（完）</p>
<div><h3>文档信息</h3>
<ul><li>版权声明：自由转载-非商用-非衍生-保持署名 |<a href="http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh">Creative Commons BY-NC-ND 3.0</a></li>
<li>原文网址：<a href="http://www.ruanyifeng.com/blog/2012/12/asynchronous%EF%BC%BFjavascript.html">http://www.ruanyifeng.com/blog/2012/12/asynchronous＿javascript.html</a></li>
<li>最后修改时间：2012年12月25日 19:17</li>
<li>付费支持：<a href="https://me.alipay.com/ruanyf"><img src="http://www.ruanyifeng.com/blog/images/rmb_32.png" alt="人民币 - 支付宝"></a>|<a href="https://www.paypal.com/cgi-bin/webscr?cmd=_xclick&amp;business=yifeng.ruan@gmail.com&amp;currency_code=USD&amp;amount=0.99&amp;return=http://www.ruanyifeng.com/thank.html&amp;item_name=Ruan%20YiFeng&#39;s%20Blog&amp;undefined_quantity=1&amp;no_note=0"><img src="http://www.ruanyifeng.com/blog/images/dollar_32.png" alt="美元 - paypal"></a></li>
</ul>
</div>
<div><p><strong>[广告]</strong>
　<a href="http://ushan.cn?utm_source=ruanyifeng.com">优衫（Ushan）是国内顶尖的定制西服店，常年为众多政商名流、影视明星、跨国高管定制衬衫与西服。以工艺精良、用料考究、版型出色、性价比高等特点广受各界好评。</a></p>
</div>

			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/8899.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/8899.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">IT 牛人博客聚合网站(udpwork.com)</a> 聚合
					|
					<a href="http://www.udpwork.com/item/8899.html#reviews">评论: 0</a>
					|
					<a href="http://book.benegg.com/tag/%E7%BC%96%E7%A8%8B?from=udpwork-feed">10000+ 本编程/Linux PDF/CHM 电子书下载</a>
				</p>
			</div>