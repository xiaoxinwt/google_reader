---
layout: post
title:  "深入理解JavaScript系列（24）：JavaScript与DOM（下）"
date:   2012-02-17 09:58:00
author: 汤姆大叔
categories: program
---

## 深入理解JavaScript系列（24）：JavaScript与DOM（下）
### by 汤姆大叔
### at 2012-02-17 09:58:00
### original <http://www.cnblogs.com/TomXu/archive/2012/02/17/2351938.html>

<p><strong>介绍</strong></p>
<p>上一章我们介绍了JavaScript的基本内容和DOM对象的各个方面，包括如何访问node节点。本章我们将讲解如何通过DOM操作元素并且讨论浏览器事件模型。</p>
<p>本文参考：http://net.tutsplus.com/tutorials/javascript-ajax/javascript-and-the-dom-lesson-2/</p>
<p><strong>操作元素</strong></p>
<p>上一章节我们提到了DOM节点集合或单个节点的访问步骤，每个DOM节点都包括一个属性集合，大多数的属性都提供为相应的功能提供了抽象。例如，如果有一个带有ID属性intro的文本元素，你可以很容易地通过DOM API来改变该元素的颜色：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre>document.getElementById('intro').style.color = '#FF0000';</pre>
</div>
<p>为了理解这个API的功能，我们一步一步分开来看就非常容易理解了：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">var</span> myDocument = document;  <br><span style="color:#0000ff">var</span> myIntro = myDocument.getElementById('intro');  <br><span style="color:#0000ff">var</span> myIntroStyles = myIntro.style;  <br>  <br><span style="color:#008000">//</span><span style="color:#008000"> 现在，我们可以设置颜色了:  </span><span style="color:#008000"><br></span>myIntroStyles.color = '#FF0000';</pre>
</div>
<p>现在，我们有了该文本的style对象的引用了，所以我们可以添加其它的CSS样式：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre>myIntroStyles.padding = '2px 3px 0 3px';  <br>myIntroStyles.backgroundColor = '#FFF';  <br>myIntroStyles.marginTop = '20px'; </pre>
</div>
<p>这里我们只是要了基本的CSS属性名称，唯一区别是CSS属性的名称如果带有-的话，就需要去除，比如用marginTop代替margin-top。例如，下面的代码是不工作的，并且会抛出语法错误：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre>myIntroStyles.padding-top = '10em';  <br>      <br><span style="color:#008000">//</span><span style="color:#008000"> 产生语法错误：</span><span style="color:#008000"><br>//</span><span style="color:#008000"> 在JavaScript里横线-是减法操作符</span><span style="color:#008000"><br>//</span><span style="color:#008000"> 而且也没有这样的属性名称</span></pre>
</div>
<p>属性可以像数组一样访问，所以利用这个知识我们可以创建一个函数来改变任何给定元素的样式：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">function</span> changeStyle(elem, property, val) {<br>    elem.style[property] = val; <span style="color:#008000">//</span><span style="color:#008000"> 使用[]来访问属性</span><span style="color:#008000"><br></span>}<br><br><span style="color:#008000">//</span><span style="color:#008000"> 使用上述的函数：  </span><span style="color:#008000"><br></span><span style="color:#0000ff">var</span> myIntro = document.getElementById('intro'); <span style="color:#008000">//</span><span style="color:#008000"> 获取intro文本对象</span><span style="color:#008000"><br></span>changeStyle(myIntro, 'color', 'red');  </pre>
</div>
<p>这仅仅是个例子，所以该函数也许没什么用，语法上来说，直接用还是会快点，例如（elem.style.color = ‘red’）。除了style属性以外，一个节点（或元素）也还有其他很多属性可以操作，如果你使用Firebug，点击DOM选项卡可以看到所有该节点（或元素）的所有属性：</p>
<p><img src="http://pic002.cnblogs.com/images/2012/349491/2012021422301835.png" alt=""></p>
<p>所有的属性都可以通过点标示符来访问（例如：Element.tabIndex）。不是所有的属性都是原始数据类型（strings, numbers, Booleans等等），sytle属性也是一个包含自己属性的对象，很多元素的属性都是只读的，也就是说不能修改他们的值。例如，你不能直接修改一个节点的parentNode属性，如果你修改只读属性的时候浏览器会抛出错误：例如，抛出错误“setting a property that has only a getter”，只是我们需要注意的。</p>
<p>通常DOM操作都是改变原始的内容，这里有几种方式来实现这个，最简单的是使用innerHTML属性，例如：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">var</span> myIntro = document.getElementById('intro');  <br>  <br><span style="color:#008000">//</span><span style="color:#008000"> 替换当前的内容</span><span style="color:#008000"><br></span>myIntro.innerHTML = &#39;New content for the &lt;strong&gt;amazing&lt;/strong&gt; paragraph!&#39;;  <br>  <br><span style="color:#008000">//</span><span style="color:#008000"> 添加内容到当前的内容里 </span><span style="color:#008000"><br></span>myIntro.innerHTML += '... some more content...';</pre>
</div>
<p>唯一的问题是该方法没在规范里定义，而且在DOM规范里也没有定义，如果你不反感的话请继续使用，因为它比我们下面要讨论其它的方法快多了。</p>
<p><strong><strong>Node节点</strong></strong></p>
<p>通过DOM API创建内容的时候需要注意node节点的2种类型，一种是元素节点，一种是text节点，上一章节已经列出了所有的节点类型，这两种需要我们现在特别注意。创建元素可以通过createElement方法，而创建text节点可以使用createTextNode，相应代码如下：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">var</span> myIntro = document.getElementById('intro');  <br>  <br><span style="color:#008000">//</span><span style="color:#008000"> 添加内容</span><span style="color:#008000"><br></span><span style="color:#0000ff">var</span> someText = 'This is the text I want to add';  <br><span style="color:#0000ff">var</span> textNode = document.createTextNode(someText);  <br>myIntro.appendChild(textNode);</pre>
</div>
<p>这里我们使用了appendChild方法将新text节点附件到文本字段，这样做比非标准的innerHTML方法显得有点长，但了解这些原理依然很重要，这里有一个使用DOM方法的更详细例子：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">var</span> myIntro = document.getElementById('intro');  <br>  <br><span style="color:#008000">//</span><span style="color:#008000"> 添加新连接到文本节点</span><span style="color:#008000"><br>//</span><span style="color:#008000"> 首先，创建新连接元素</span><span style="color:#008000"><br></span><span style="color:#0000ff">var</span> myNewLink = document.createElement('a'); <span style="color:#008000">//</span><span style="color:#008000"> &lt;a/&gt;  </span><span style="color:#008000"><br></span>myNewLink.href = 'http://google.com'; <span style="color:#008000">//</span><span style="color:#008000"> &lt;a href=&quot;http://google.com&quot;/&gt;  </span><span style="color:#008000"><br></span>myNewLink.appendChild(document.createTextNode('Visit Google')); <br><span style="color:#008000">//</span><span style="color:#008000"> &lt;a href=&quot;http://google.com&quot;&gt;Visit Google&lt;/a&gt;  </span><span style="color:#008000"><br></span>  <br><span style="color:#008000">//</span><span style="color:#008000"> 将内容附件到文本节点</span><span style="color:#008000"><br></span>myIntro.appendChild(myNewLink);</pre>
</div>
<p>另外DOM里还有一个insertBefore方法用于再节点前面附件内容，通过insertBefore和appendChild我们可以实现自己的insertAfter函数：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#008000">//</span><span style="color:#008000"> 'Target'是DOM里已经存在的元素</span><span style="color:#008000"><br>//</span><span style="color:#008000"> 'Bullet'是要插入的新元素</span><span style="color:#008000"><br></span>  <br><span style="color:#0000ff">function</span> insertAfter(target, bullet) {  <br>    target.nextSibling ?  <br>        target.parentNode.insertBefore(bullet, target.nextSibling)  <br>        : target.parentNode.appendChild(bullet);  <br>}  <br>  <br><span style="color:#008000">//</span><span style="color:#008000"> 使用了3目表达式:  </span><span style="color:#008000"><br>//</span><span style="color:#008000"> 格式：条件?条件为true时的表达式：条件为false时的表达式</span></pre>
</div>
<p>上面的函数首先检查target元素的同级下一个节点是否存在，如果存在就在该节点前面添加bullet节点，如果不存在，就说明target是最后一个节点了，直接在后面append新节点就可以了。DOM API没有给提供insertAfter是因为真的没必要了——我们可以自己创建。</p>
<p>DOM操作有很多内容，上面你看到的只是其中一部分。</p>
<p><strong>Event事件</strong></p>
<p>浏览器事件是所有web程序的核心，通过这些事件我们定义将要发生的行为，如果在页面里有个按钮，那点击此按钮之前你需要验证表单是否合法，这时候就可以使用click事件，下面列出的最标准的事件列表：</p>
<p>注：正如我们上章所说的，DOM和JavaScript语言是2个单独的东西，浏览器事件是DOM API的一部分，而不是JavaScript的一部分。</p>
<p><strong><strong>鼠标事件</strong></strong></p>
<ol>
<li> ‘<strong>mousedown</strong>’ – 鼠标设备按下一个元素的时候触发mousedown事件。</li>
<li> ‘<strong>mouseup</strong>’ – 鼠标设备从按下的元素上弹起的时候触发mouseup事件。</li>
<li> ‘<strong>click</strong>’ – 鼠标点击元素的时候触发click事件。</li>
<li> ‘<strong>dblclick</strong>’ – 鼠标双击元素的时候触发dblclick事件。</li>
<li>‘<strong>mouseover</strong>’ – 鼠标移动到某元素上的时候触发mouseover事件。</li>
<li> ‘<strong>mouseout</strong>’ – 鼠标从某元素离开的时候触发mouseout事件。</li>
<li> ‘<strong>mousemove</strong>’ – 鼠标在某元素上移动但未离开的时候触发mousemove事件。</li>
</ol>
<p><strong><strong>键盘事件</strong></strong></p>
<ol>
<li>‘<strong>keypress</strong>’ – 按键按下的时候触发该事件。</li>
<li>‘<strong>keydown</strong>’ – 按键按下的时候触发该事件，并且在keypress事件之前。</li>
<li>‘<strong>keyup</strong>’ – 按键松开的时候触发该事件，在keydown和keypress事件之后。</li>
</ol>
<p><strong>表单事件</strong></p>
<ol>
<li>‘<strong>select</strong>’ – 文本字段（input, textarea等）的文本被选择的时候触发该事件。</li>
<li>‘<strong>change</strong>’ – 控件失去input焦点的时候触发该事件（或者值被改变的时候）。</li>
<li>‘<strong>submit</strong>’ – 表单提交的时候触发该事件。</li>
<li>‘<strong>reset</strong>’ – 表单重置的时候触发该事件。</li>
<li>‘<strong>focus</strong>’ – 元素获得焦点的时候触发该事件，通常来自鼠标设备或Tab导航。</li>
<li>‘<strong>blur</strong>’ – 元素失去焦点的时候触发该事件，通常来自鼠标设备或Tab导航。</li>
</ol>
<p><strong><strong>其它事件</strong></strong></p>
<ol>
<li>‘<strong>load</strong>’ – 页面加载完毕（包括内容、图片、frame、object）的时候触发该事件。</li>
<li>‘<strong>resize</strong>’ – 页面大小改变的时候触发该事件（例如浏览器缩放）。</li>
<li>‘<strong>scroll</strong>’ – 页面滚动的时候触发该事件。</li>
<li>‘<strong>unload</strong>’ – 从页面或frame删除所有内容的时候触发该事件（例如离开一个页面）。</li>
</ol>
<p>还有很多各种各样的事件，上面展示的事件是我们在JavaScript里最常用的事件，有些事件在跨浏览器方面可能有所不同。还有其它浏览器实现的一些属性事件，例如Gecko实现的DOMContentLoaded或DOMMouseScroll等，Gecko的详细事件列表请<a href="https://developer.mozilla.org/en/Gecko-Specific_DOM_Events">查看这里</a>。</p>
<p><strong><strong>事件处理</strong></strong></p>
<p>我们将了事件，但是还没有将到如何将处理函数和事件管理起来，使用这些事件之前，你首先要注册这些事件句柄，然后描述该事件发生的时候该如何处理，下面的例子展示了一个基本的事件注册模型：</p>
<p>基本事件注册：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#008000">&lt;!--</span><span style="color:#008000"> HTML </span><span style="color:#008000">--&gt;</span>  <br><span style="color:#0000ff">&lt;</span><span style="color:#800000">button </span><span style="color:#ff0000">id</span><span style="color:#0000ff">="my-button"</span><span style="color:#0000ff">&gt;</span>Click me!<span style="color:#0000ff">&lt;/</span><span style="color:#800000">button</span><span style="color:#0000ff">&gt;</span>  </pre>
</div>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#008000">//</span><span style="color:#008000"> JavaScript:  </span><span style="color:#008000"><br></span><span style="color:#0000ff">var</span> myElement = document.getElementById('my-button');<br><br><span style="color:#008000">//</span><span style="color:#008000"> 事件处理句柄:  </span><span style="color:#008000"><br></span><span style="color:#0000ff">function</span> buttonClick() {<br>    alert('You just clicked the button!');<br>}<br><br><span style="color:#008000">//</span><span style="color:#008000"> 注册事件</span><span style="color:#008000"><br></span>myElement.onclick = buttonClick; </pre>
</div>
<p>使用document.getElementById命令，通过ID=my-button获取该button对象，然后创建一个处理函数，随后将该函数赋值给该DOM的onclick属性。就这么简单！</p>
<p>基本事件注册是非常简单的，在事件名称前面添加前缀on作为DOM的属性就可以使用了，这是事件处理的基本核心，但下面的代码我不推荐使用：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">&lt;</span><span style="color:#800000">button </span><span style="color:#ff0000">onclick</span><span style="color:#0000ff">="return buttonClick()"</span><span style="color:#0000ff">&gt;</span>Click me!<span style="color:#0000ff">&lt;/</span><span style="color:#800000">button</span><span style="color:#0000ff">&gt;</span></pre>
</div>
<p>上述Inline的事件处理方式不利用页面维护，建议将这些处理函数都封装在单独的js文件，原因和CSS样式的一样的。</p>
<p>高级事件注册：</p>
<p>别被标题迷惑了，“高级”不意味着好用，实际上上面讨论的基本事件注册是我们大部分时候用的方式，但有一个限制：不能绑定多个处理函数到一个事件上。这也是我们要讲解该小节原因：</p>
<p>该模型运行你绑定多个处理句柄到一个事件上，也就是说一个事件触发的时候多个函数都可以执行，另外，该模型也可以让你很容易里删除某个已经绑定的句柄。</p>
<p>严格来说，有2中不同的模型：W3C模型和微软模型，除IE之外W3C模型支持所有的现代浏览器，而微软模型只支持IE，使用<a href="https://developer.mozilla.org/En/DOM/Element.addEventListener">W3C模型</a>的代码如下：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#008000">//</span><span style="color:#008000"> 格式：target.addEventListener( type, function, useCapture );  </span><span style="color:#008000"><br>//</span><span style="color:#008000"> 例子:  </span><span style="color:#008000"><br></span><span style="color:#0000ff">var</span> myIntro = document.getElementById('intro');<br>myIntro.addEventListener('click', introClick, <span style="color:#0000ff">false</span>);</pre>
</div>
<p>使用<a href="http://msdn.microsoft.com/en-us/library/ms536343(VS.85).aspx">IE模型</a>的代码如下：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#008000">//</span><span style="color:#008000"> 格式: target.attachEvent ( 'on' + type, function );  </span><span style="color:#008000"><br>//</span><span style="color:#008000"> 例子:  </span><span style="color:#008000"><br></span><span style="color:#0000ff">var</span> myIntro = document.getElementById('intro');<br>myIntro.attachEvent('onclick', introClick);</pre>
</div>
<p>introClick的代码如下：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">function</span> introClick() {  <br>    alert('You clicked the paragraph!');  <br>}</pre>
</div>
<p>事实上，要做出通用的话，我们可以自定义一个函数以支持跨浏览器：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">function</span> addEvent(elem, type, fn) {<br>    <span style="color:#0000ff">if</span> (elem.attachEvent) {<br>        elem.attachEvent('on' + type, fn);<br>        <span style="color:#0000ff">return</span>;<br>    }<br>    <span style="color:#0000ff">if</span> (elem.addEventListener) {<br>        elem.addEventListener(type, fn, <span style="color:#0000ff">false</span>);<br>    }<br>}</pre>
</div>
<p>该函数首先检查attachEvent和addEventListener属性，谁可以就用谁，这两种类型的模型都支持删除句柄功能，参考下面的removeEvent函数。</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">function</span> removeEvent(elem, type, fn) {<br>    <span style="color:#0000ff">if</span> (elem.detachEvent) {<br>        elem.detachEvent('on' + type, fn);<br>        <span style="color:#0000ff">return</span>;<br>    }<br>    <span style="color:#0000ff">if</span> (elem.removeEventListener) {<br>        elem.removeEventListener(type, fn, <span style="color:#0000ff">false</span>);<br>    }<br>}</pre>
</div>
<p>你可以这样使用：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">var</span> myIntro = document.getElementById('intro');<br>addEvent(myIntro, 'click', <span style="color:#0000ff">function</span> () {<br>    alert('YOU CLICKED ME!!!');<br>});</pre>
</div>
<p>注意到我们传入了一个匿名函数作为第三个参数，JavaScript运行我们定义和执行匿名函数，这种匿名函数特别适合作为参数传递，实际上我们也可以传递有名的函数（代码如下），但是你们函数更容易做。</p>
<p>如果你只想在第一次click的时候触发一个函数，你可以这么做：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#008000">//</span><span style="color:#008000"> 注意：前提是我们已经定于好了addEvent/removeEvent函数</span><span style="color:#008000"><br>//</span><span style="color:#008000"> (定义好了才能使用哦)  </span><span style="color:#008000"><br></span><br><span style="color:#0000ff">var</span> myIntro = document.getElementById('intro');<br>addEvent(myIntro, 'click', oneClickOnly);<br><br><span style="color:#0000ff">function</span> oneClickOnly() {<br>    alert('WOW!');<br>    removeEvent(myIntro, 'click', oneClickOnly);<br>} </pre>
</div>
<p>当第一次触发以后，我们就立即删除该句柄，但是有匿名函数的话却很难将自身的引用删除，不过实际上可以通过如下的形式来做（只不过有点麻烦）：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre>addEvent(myIntro, 'click', <span style="color:#0000ff">function</span> () {<br>    alert('WOW!');<br>    removeEvent(myIntro, 'click', arguments.callee);<br>}); </pre>
</div>
<p>这里我们是有了arguments对象的callee属性，arguments对象包含了所有传递进来的参数以及该函数自身(callee)，这样我们就可以放心地删除自身的引用了。<br>关于W3C和微软模型还有其他的少许差异，比如this，在触发事件的时候函数中的this一般都是该元素上下文，，也就说this引用该元素自身，在基本事件注册和W3C模型中都没有问题，但在微软模型的实现里却可能出错，请参考如下代码：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">function</span> myEventHandler() {<br>    <span style="color:#0000ff">this</span>.style.display = 'none';<br>}<br><br><span style="color:#008000">//</span><span style="color:#008000"> 正常工作，this是代表该元素</span><span style="color:#008000"><br></span>myIntro.onclick = myEventHandler;<br><br><span style="color:#008000">//</span><span style="color:#008000"> 正常工作，this是代表该元素</span><span style="color:#008000"><br></span>myIntro.addEventListener('click', myEventHandler, <span style="color:#0000ff">false</span>);<br><br><span style="color:#008000">//</span><span style="color:#008000"> 不正常，这时候的this是代表Window对象</span><span style="color:#008000"><br></span>myIntro.attachEvent('onclick', myEventHandler);</pre>
</div>
<p>这里有一些方式可以避免这个问题，最简单的方式是使用前面的基本事件注册方式，或者是再做一个通用的addEvent，通用代码请参考<a href="http://ejohn.org/apps/jselect/event.html">John Resig</a>或<a href="http://dean.edwards.name/weblog/2005/10/add-event2/">Dean Edward</a>的文章。</p>
<p><strong>Event对象</strong></p>
<p>另外一个非常重要的内容是Event对象，当事件发生的时候出发某个函数，该Event对象将自动在函数内可用，该对象包含了很多事件触发时候的信息，但IE却没有这么实现，而是自己实现的，IE浏览器是通过全局对象window下的event属性来包含这些信息，虽然不是大问题，但我们也需要注意一下，下面的代码是兼容性的：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">function</span> myEventHandler(e) {<br><br>    <span style="color:#008000">//</span><span style="color:#008000"> 注意参数e</span><span style="color:#008000"><br></span>    <span style="color:#008000">//</span><span style="color:#008000"> 该函数调用的时候e是event对象（W3C实现）</span><span style="color:#008000"><br></span><br>    <span style="color:#008000">//</span><span style="color:#008000"> 兼容IE的代码</span><span style="color:#008000"><br></span>    e = e || window.event;<br><br>    <span style="color:#008000">//</span><span style="color:#008000"> 现在e就可以兼容各种浏览器了</span><span style="color:#008000"><br></span><br>}<br><br><span style="color:#008000">//</span><span style="color:#008000"> 这里可以自由地绑定事件了</span></pre>
</div>
<p>这里判断e对象（Event对象）是否存在我们使用了OR操作符：如果e不存在（为null, undefined,0等）的时候，将window.event赋值给e，否则的话继续使用e。通过这方式很快就能在多浏览器里得到真正的Event对象，如果你不喜欢这种方式的话，你可以使用if语句来处理：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">if</span> (!e) {<br>    e = window.event;<br>} <span style="color:#008000">//</span><span style="color:#008000"> 没有else语句，因为e在其它浏览器已经定义了</span></pre>
</div>
<p>另外Event对象下的命令和属性都很有用，遗憾的是不不能全兼容浏览器，例如当你想取消默认的行为的时候你可以使用Event对象里的preventDefault()方法，但IE里不得不使用对象的returnValue属性值来控制，兼容代码如下：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">function</span> myEventHandler(e) {<br>    e = e || window.event;<br>    <span style="color:#008000">//</span><span style="color:#008000"> 防止默认行为</span><span style="color:#008000"><br></span>    <span style="color:#0000ff">if</span> (e.preventDefault) {<br>        e.preventDefault();<br>    } <span style="color:#0000ff">else</span> {<br>        e.returnValue = <span style="color:#0000ff">false</span>;<br>    }<br>}</pre>
</div>
<p>例如，当你点击一个连接的时候，默认行为是导航到href里定义的地址，但有时候你想禁用这个默认行为，通过returnValue和preventDefault就可以实现，Event对象里的很多属性在浏览器里都不兼容，所以很多时候需要处理这些兼容性代码。</p>
<p>注意：现在很多JS类库都已经封装好了e.preventDefault代码，也就是说在IE里可用了，但是原理上依然是使用returnValue来实现的。</p>
<p><strong><strong>事件冒泡</strong></strong></p>
<p>事件冒泡，就是事件触发的时候通过DOM向上冒泡，首先要知道不是所有的事件都有冒泡。事件在一个目标元素上触发的时候，该事件将触发一一触发祖先节点元素，直到最顶层的元素：</p>
<p><img src="http://pic002.cnblogs.com/images/2012/349491/2012021515265084.png" alt=""></p>
<p>如图所示，如果a连接被点击，触发触发连接的click事件，然后触发p的click事件，以此再触发div和body的click事件。顺序不变，而且不一定是在同时触发的。<br>这样你就可以利用该特性去处理自己的逻辑了，并且再任何时候都可以停止冒泡，比如，如果你只想冒泡到文本节点上，而不再进一步冒泡，你可以在p的click事件处理函数里丁停止冒泡：</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">function</span> myParagraphEventHandler(e) {<br><br>    e = e || window.event;<br><br>    <span style="color:#008000">//</span><span style="color:#008000"> 停止向上冒泡</span><span style="color:#008000"><br></span>    <span style="color:#0000ff">if</span> (e.stopPropagation) {<br>        <span style="color:#008000">//</span><span style="color:#008000"> W3C实现  </span><span style="color:#008000"><br></span>        e.stopPropagation();<br>    } <span style="color:#0000ff">else</span> {<br>        <span style="color:#008000">//</span><span style="color:#008000"> IE实现  </span><span style="color:#008000"><br></span>        e.cancelBubble = <span style="color:#0000ff">true</span>;<br>    }<br><br>}<br><br><span style="color:#008000">//</span><span style="color:#008000"> 使用我们自定义的addEvent函数将myParagraphEventHandler绑定到click事件上：  </span><span style="color:#008000"><br></span>addEvent(document.getElementsByTagName('p')[0], 'click', myParagraphEventHandler);</pre>
</div>
<p>事件委托</p>
<p>举例来说，如果你有一个很多行的大表格，在每个&lt;tr&gt;上绑定点击事件是个非常危险的想法，因为性能是个大问题。流行的做法是使用事件委托。事件委托描述的是将事件绑定在容器元素上，然后通过判断点击的target子元素的类型来触发相应的事件。</p>
<div style="background-color:#f5f5f5;border:1px solid #cccccc;padding-left:5px;padding-right:5px">
<pre><span style="color:#0000ff">var</span> myTable = document.getElementById('my-table');<br><br>myTable.onclick = <span style="color:#0000ff">function</span> () {<br><br>    <span style="color:#008000">//</span><span style="color:#008000"> 处理浏览器兼容</span><span style="color:#008000"><br></span>    e = e || window.event;<br>    <span style="color:#0000ff">var</span> targetNode = e.target || e.srcElement;<br><br>    <span style="color:#008000">//</span><span style="color:#008000"> 测试如果点击的是TR就触发</span><span style="color:#008000"><br></span>    <span style="color:#0000ff">if</span> (targetNode.nodeName.toLowerCase() === 'tr') {<br>        alert('You clicked a table row!');<br>    }<br><br>}</pre>
</div>
<p>事件委托依赖于事件冒泡，如果事件冒泡到table之前被禁用的话，那上面的代码就无法工作了。</p>
<p><strong>总结</strong></p>
<p>本章我们覆盖到了DOM元素的操作以及相关的浏览器事件模型，希望大家能对DOM有了进一步的了解。有任何问题，请留言讨论。</p>
<p><strong>同步与推荐</strong></p>
<p>本文已同步至目录索引：<span style="font-size:14pt"><a href="http://www.cnblogs.com/TomXu/archive/2011/12/15/2288411.html">深入理解JavaScript系列</a></span></p>
<p>深入理解JavaScript系列文章，包括了原创，翻译，转载等各类型的文章，如果对你有用，请推荐支持一把，给大叔写作的动力。</p><img src="http://www.cnblogs.com/TomXu/aggbug/2351938.html?type=1" width="1" height="1" alt=""><p><a href="http://www.cnblogs.com/TomXu/archive/2012/02/17/2351938.html">本文链接</a></p>