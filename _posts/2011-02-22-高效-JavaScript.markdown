---
layout: post
title:  "高效 JavaScript"
date:   2011-02-22 12:52:18
author: 疯狂小强
categories: program
---

## 高效 JavaScript
### by 疯狂小强
### at 2011-02-22 12:52:18
### original <http://www.woiweb.net/efficient-javascript.html>

<p>传统上，网页中不会有大量的脚本，至少脚本很少会影响网页的性能。但随着网页越来越像 Web 应用程序，脚本的效率对网页性能影响越来越大。而且使用 Web 技术开发的应用程序现在越来越多，因此提高脚本的性能变得很重要。<span></span></p><p>对于桌面应用程序，通常使用编译器将源代码转换为二进制程序。编译器可以花费大量时间优化最终二进制程序的效率。Web 应用程序则不同。因为 Web 应用程序需要运行在不同的浏览器、平台和架构中，不可能事先完全编译。浏览器在获得脚本后要执行解释和编译工作。用户要求不仅要求网页能快速的载入，而且要求最终 Web 应用程序执行的效果要和桌面应用程序的一样流畅。Web 应用程序应能运行在多种设备上，从普通的桌面电脑到手机。</p><p>浏览器并不很擅长此项工作。虽然 Opera 有着当前最快的脚本引擎，但浏览器有不可避免的局限性，这时就需要 Web 开发者的帮助。Web 开发者提高 Web 应用程序的性能的方法很多而且也很简单，如只需要将一种循环变成另一种、将组合样式分解成三个或者只添加实际需要的脚本。</p><p>本文从 ECMAScript/<span><a href="http://www.woiweb.net/category/javascript" title="javascript" rel="nofollow">javascript</a></span>, DOM, 和页面载入方面分别介绍几种简单的能提高 Web 应用程序性能的方法。</p><h2>目录</h2><h3>ECMAScript</h3><ol><li><a href="http://www.woiweb.net/#avoideval">避免使用 <code>eval</code> 或 <code>Function</code> 构造函数</a><ol><li><a href="http://www.woiweb.net/#rewriteeval">重写 <code>eval</code></a></li><li><a href="http://www.woiweb.net/#usefunction">如果你需要函数，那就用函数</a></li></ol></li><li><a href="http://www.woiweb.net/#avoidwith">避免使用<code> with</code></a></li><li><a href="http://www.woiweb.net/#trycatch">不要在影响性能的关键函数中使用 <code>try-catch-finally</code> </a></li><li><a href="http://www.woiweb.net/#isolate">分隔 <code>eval</code> 和 <code>with</code></a></li><li><a href="http://www.woiweb.net/#avoidglobal">避免使用全局变量</a></li><li><a href="http://www.woiweb.net/#implicitconversion">注意隐式对象转换</a></li><li><a href="http://www.woiweb.net/#forin">在关键函数中避免 <code>for-in</code> </a></li><li><a href="http://www.woiweb.net/#stringaccumulator">优化 string 合并</a></li><li><a href="http://www.woiweb.net/#primitiveoperator">基本运算符比函数调用更快</a></li><li><a href="http://www.woiweb.net/#timeouts">向 <code>setTimeout()</code> 和 <code>setInterval()</code>传送函数名，而不要传送字符串</a></li></ol><h3>DOM</h3><ol><li><a href="http://www.woiweb.net/#reflow">重绘和 reflow</a><ol><li><a href="http://www.woiweb.net/#minimumreflow">减少 reflow 次数</a></li><li><a href="http://www.woiweb.net/#minimalreflow">最小化 reflow 影响</a></li></ol></li><li><a href="http://www.woiweb.net/#modifyingtree">修改 DOM 树</a></li><li><a href="http://www.woiweb.net/#invisibleelement">修改不可见元素</a></li><li><a href="http://www.woiweb.net/#measuring">测量大小</a></li><li><a href="http://www.woiweb.net/#stylechanges">一次修改多个样式值</a></li><li><a href="http://www.woiweb.net/#smoothspeed">用流畅性换取速度</a></li><li><a href="http://www.woiweb.net/#manynodes">避免搜索大量节点</a></li><li><a href="http://www.woiweb.net/#xpath">使用 XPath 提高速度</a></li><li><a href="http://www.woiweb.net/#traversemodify">避免在遍历 DOM 时修改 DOM</a></li><li><a href="http://www.woiweb.net/#cachevalues">使用变量保存 DOM 值</a></li></ol><h3>页面载入</h3><ol><li><a href="http://www.woiweb.net/#docreferences">避免保存来自其他文档的引用</a></li><li><a href="http://www.woiweb.net/#fasthistory">快速历史浏览</a></li><li><a href="http://www.woiweb.net/#xmlhttprequest">使用 XMLHttpRequest</a></li><li><a href="http://www.woiweb.net/#dynamicscript">动态创建 SCRIPT 元素</a></li><li><a href="http://www.woiweb.net/#locationnreplace"><code>location.replace()</code> 控制历史项</a></li></ol><h2>ECMAScript</h2><h3>避免使用 <code>eval</code> 或 <code>Function</code> 构造函数</h3><p>每次 <code>eval</code> 或<code> Function</code> 构造函数作用于字符串表示的源代码时，脚本引擎都需要将源代码转换成可执行代码。这是很消耗<span><a href="http://www.woiweb.net/category/resource" title="资源" rel="nofollow">资源</a></span>的操作 —— 通常比简单的函数调用慢100倍以上。</p><p><code>eval</code> 函数效率特别低，由于事先无法知晓传给 <code>eval</code> 的字符串中的内容，<code>eval</code>在其上下文中解释要处理的代码，也就是说编译器无法优化上下文，因此只能有浏览器在运行时解释代码。这对性能影响很大。</p><p><code>Function</code> 构造函数比 <code>eval</code> 略好，因为使用此代码不会影响周围代码；但其速度仍很慢。</p><h4>重写 <code>eval</code></h4><p><code>eval</code> 不仅效率低下，而且绝大部分情况下完全没有使用的必要。很多情况下使用 eval 是因为信息以字符串形式提供，开发者误认为只有 eval 能使用此信息。下例是一个典型的错误：</p><pre>function getProperty(oString) {
  var oReference;
  eval('oReference = test.prop.'+oString);
  return oReference;
}</pre><p>下面的代码执行完全相同的函数，但没有使用<code> eval</code>：</p><pre>function getProperty(oString) {
  return test.prop[oString];
}</pre><p>在 Opera 9, Firefox, 和 Internet Explorer 中后者比前者快95%，在 Safari 中快85%。(注意此比较中不含函数本身调用时间。)</p><h4>如果你需要函数，那就用函数</h4><p>下面是常见的<code> Function</code> 构造函数使用：</p><pre>function addMethod(oObject,oProperty,oFunctionCode) {
  oObject[oProperty] = new Function(oFunctionCode);
}
addMethod(myObject,'rotateBy90','this.angle=(this.angle+90)%360');
addMethod(myObject,'rotateBy60','this.angle=(this.angle+60)%360');</pre><p>下面的代码没有使用<code> Function</code> 构造函数，但提供了相同的功能：通过创建匿名函数：</p><pre>function addMethod(oObject,oProperty,oFunction) {
  oObject[oProperty] = oFunction;
}
addMethod(myObject,'rotateBy90',function () { this.angle=(this.angle+90)%360; });
addMethod(myObject,'rotateBy60',function () { this.angle=(this.angle+60)%360; });</pre><h3>避免使用<code> with</code></h3><p>尽管看起来挺方便，但<code> with</code> 效率很低。<code>with</code> 结构又创建了一个作用域，以便使用变量时脚本引擎搜索。这本身只轻微的影响性能。但严重的是编译时不知道此作用域内容，因此编译器无法像对其他作用域（如函数产生的作用域）那样对之优化。</p><p>另一个高效而且也挺方便的方法是使用变量引用对象，然后使用变量访问对象属性。但只有属性不是 literal type 时才适用，如字符串或布尔值。</p><p>考虑下面的代码：</p><pre>with( test.information.settings.files ) {
  primary = 'names';
  secondary = 'roles';
  tertiary = 'references';
}</pre><p>下面的代码效率更高：</p><pre>var testObject = test.information.settings.files;
testObject.primary = 'names';
testObject.secondary = 'roles';
testObject.tertiary = 'references';</pre><h3>不要在影响性能的关键函数中使用 <code>try-catch-finally</code></h3><p><code>try-catch-finally</code> 结构比较特殊。和其他语法结构不同，它在 runtime 的当前作用域中创建新变量。每当 <code>catch</code> 执行时，就会将捕获到的 exception 对象赋给一个变量。这个变量不属于任何脚本。它在 <code>catch</code> 语句开始时被创建，在结束时被销毁。</p><p>由于此函数比较特殊，且是在运行时动态创建动态销毁，有些浏览器对其的处理并不高效。把 catch 语句放在关键循环中将极大影响性能。</p><p>如果可能，应在脚本中不频繁被调用的地方进行异常处理，或通过检查某种动作是否被支持来避免使用。下面的例子中，如果所需的属性不存在，将在循环语句中抛出许多异常：</p><pre>var oProperties = [&#39;first&#39;,&#39;second&#39;,&#39;third&#39;,...,&#39;nth&#39;], i;
for( i = 0; i &lt; oProperties.length; i++ ) {
  try {
    test[oProperties[i]].someproperty = somevalue;
  } catch(e) {
    ...
  }
}</pre><p>很多情况下，可把 <code>try-catch-finally</code> 结构移到循环外部。这样做稍微改变了程序语义，因为如果抛出异常，将停止整个循环：</p><pre>var oProperties = [&#39;first&#39;,&#39;second&#39;,&#39;third&#39;,...,&#39;nth&#39;], i;
try {
  for( i = 0; i &lt; oProperties.length; i++ ) {
    test[oProperties[i]].someproperty = somevalue;
  }
} catch(e) {
  ...
}</pre><p>有时可用属性检测或其他检测代替<code> try-catch-finally</code> 结构：</p><pre>var oProperties = [&#39;first&#39;,&#39;second&#39;,&#39;third&#39;,...,&#39;nth&#39;], i;
for( i = 0; i &lt; oProperties.length; i++ ) {
  if( test[oProperties[i]] ) {
    test[oProperties[i]].someproperty = somevalue;
  }
}</pre><h3>分隔 <code>eval</code> 和 <code>with</code></h3><p>因为 eval 和 with 结构严重影响性能，应该尽量避免使用这些结构。但如不得不使用时， 避免在频繁被调用的函数中或循环中使用这些结构。最好将这些结构放在只运行一次，或少量几次的代码中，并不要将其放在对性能要求较高的代码中。</p><p>如果可能，尽量将这些结构和其他代码分隔开，这样他们就不会影响脚本性能。如将其放在顶级函数中，或只执行一次然后保存运行结果，避免再次使用。</p><p><code>try-catch-finally</code> 结构在一些浏览器中也会影响性能，包括 Opera ，因此最好也将其分隔。</p><h3>避免使用全局变量</h3><p>全局变量使用简单，因此很容易禁不住诱惑在脚本中使用全局变量。但有时全局变量也会影响脚本性能。</p><p>首先，如果函数或其他作用域内引用了全局变量，则脚本引擎不得不一级一级查看作用域直到搜索到全局作用域。查询本地作用域变量更快。</p><p>其次，全局变量将始终存在在脚本生命周期中。而本地变量在本地作用域结束后就将被销毁，其所使用的内存也会被垃圾收集器回收。</p><p>最后，<var>window</var> 对象也共享全局作用域，也就是说本质上是两个作用域而不是一个。使用全局变量不能像使用本地变量那样使用前缀，因此脚本引擎要花更多时间查找全局变量。</p><p>也可在全局作用域中创建全局函数。函数中可以调用其他函数，随着函数调用级数增加，脚本引擎需要花更多时间才能找到全局变量以找到全局变量。</p><p>考虑下面的简单例子，<var>i</var> 和 <var>s</var> 是全局作用域且函数使用这两个全局变量：</p><pre>var i, s = &#39;&#39;;
function testfunction() {
  for( i = 0; i &lt; 20; i++ ) {
    s += i;
  }
}
testfunction();</pre><p>下面的函数效率更高。在大多数浏览器中，包括 Opera 9、最新版 Internet Explorer, Firefox, Konqueror 和 Safari，后者执行速度比上面代码快30%。</p><pre>function testfunction() {
  var i, s = &#39;&#39;;
  for( i = 0; i &lt; 20; i++ ) {
    s += i;
  }
}
testfunction();</pre><h3>注意隐式对象转换</h3><p>Literal，如字符串、数字和布尔值在 ECMAScript 中有两种表示方法。 每个类型都可以创建变量值或对象。如 <code>var oString = 'some content';</code>, 创建了字符串值，而<code> var oString = new String('some content');</code>创建了字符串对象。</p><p>所有的属性和方法都定义在 string 对象中，而不是 string 值中。每次使用 string 值的方法或属性， ECMAScript 引擎都会隐式的用相同 string 值创建新的 string 对象。此对象只用于此请求，以后每次视图调用 string 值方法是都会重新创建。</p><p>下面的代码将要求脚本引擎创建21个新 string 对象，每次使用 <var>length</var> 属性时都会产生一个，每一个 <var>charAt</var> 方法也会产生一个：</p><pre>var s = &#39;0123456789&#39;;
for( var i = 0; i &lt; s.length; i++ ) {
  s.charAt(i);
}</pre><p>下面的代码和上面相同，但只创建了一个对象，因此其效率更高：</p><pre>var s = new String(&#39;0123456789&#39;);
for( var i = 0; i &lt; s.length; i++ ) {
  s.charAt(i);
}</pre><p>如果代码中常调用 literal 值的方法，你应像上面例子那样考虑创建对象。</p><p>注意本文中大部分技巧对于所有浏览器都有效，但此技巧特别针对于 Opera。此优化技巧在 Internet Explorer 和 Firefox 中改进效果没有在 Opera 中明显。</p><h3>在关键函数中避免 <code>for-in</code></h3><p><code>for-in</code> 常被误用，特别是简单的<code> for</code> 循环更合适时。<code>for-in</code> 循环需要脚本引擎创建所有可枚举的属性列表，然后检查是否存在重复。</p><p>有时脚本已知可枚举的属性。这时简单的 <code>for</code> 循环即可遍历所有属性，特别是当使用顺序数字枚举时，如数组中。</p><p>下面是不正确的 <code>for-in</code> 循环使用：</p><pre>var oSum = 0;
for( var i in oArray ) {
  oSum += oArray[i];
}</pre><p><code>for</code> 循环无疑会更高效：</p><pre>var oSum = 0;
var oLength = oArray.length;
for( var i = 0; i &lt; oLength; i++ ) {
  oSum += oArray[i];
}</pre><h3>优化 string 合并</h3><p>字符串合并是比较慢的。<code>+</code> 运算符并不管是否将结果保存在变量中。它会创建新 string 对象，并将结果赋给此对象；也许新对象会被赋给某个变量。下面是一个常见的字符串合并语句：</p><pre>a += 'x' + 'y';</pre><p>此代码首先创建临时string对象保存合并后的’xy’值，然后和<var>a</var>变量合并，最后将结果赋给<var>a</var>。下面的代码使用两条分开的命令，但每次都直接赋值给<var>a</var> ，因此不需要创建临时string对象。结果在大部分浏览器中，后者比前者快20%，而且消耗更少的内存：</p><pre>a += 'x';
a += 'y';</pre><h3>基本运算符比函数调用更快</h3><p>尽管单独使用效果不明显，但如果在需要高性能的关键循环和函数中使用基本运算符代替函数调用将可能提高脚本性能。例子包括数组的<var> push</var> 方法，其效率低于直接在数组末位赋值。另一个例子是<var> Math</var> 对象方法，大部分情况下，简单的数学运算符效率更高更合适。</p><pre>var min = Math.min(a,b);
A.push(v);</pre><p>下面代码实现相同功能，但效率更高：</p><pre>var min = a &lt; b ? a : b;
A[A.length] = v;</pre><h3>向 <code>setTimeout()</code> 和 <code>setInterval()</code>传送函数名，而不要传送字符串</h3><p><code>setTimeout()</code> 和 <code>setInterval()</code> 方法近似于 <code>eval</code>。如果传进参数是字符串，则在一段时间之后，会和 <code>eval</code>一样执行字符串值，当然其低效率也和 <code>eval </code>一样。</p><p>但这些方法也可以接受函数作为第一个参数。在一段时间后将调用此函数，但此函数可在编译时被解释和优化，也就是说会有更好的性能。典型的使用 string 作为参数例子如下：</p><pre>setInterval('updateResults()',1000);
setTimeout('x+=3;prepareResult();if(!hasCancelled){runmore();}',500);</pre><p>第一个语句可以直接传递函数名。第二个语句中，可以使用匿名函数封装代码：</p><pre>setInterval(updateResults,1000);
setTimeout(function () {
  x += 3;
  prepareResult();
  if( !hasCancelled ) {
    runmore();
  }
},500);</pre><p>需要注意的是 timeout 或时间延迟可能并不准确。通常浏览器会花比要求更多的时间。有些浏览器会稍微提早完成下一个延迟以补偿。有些浏览器每次可能都会等待准确时间。很多因素，如 CPU 速度、线程状态和 <span><a href="http://www.woiweb.net/category/javascript" title="javascript" rel="nofollow">javascript</a></span> 负载都会影响时间延迟的精度。大多数浏览器无法提供1ms以下的延迟，可能会设置最小可能延迟，通常在10 和 100 ms之间。</p><h2>DOM</h2><p>通常主要有三种情况引起 DOM 运行速度变慢。第一就是执行大量 DOM 操作的脚本，如从获取的数据中建造新的 DOM 树。第二种情况是脚本引起太多的 reflow 或重绘。第三种情况是使用较慢的 DOM 节点定位方法。</p><p>第二种和第三种情况比较常见且对性能影响比较严重，因此先介绍前两种情况。</p><h3>重绘(Repaint)和 reflow</h3><p>重绘也被称为重画，每当以前不可见的元素变得可见（或反之）时就需要重绘操作；重绘不会改变页面布局。如给元素添加轮廓、改变背景颜色、改变样式。重绘对性能影响很大，因为需要脚本引擎搜索所有元素以确定哪些是可见的及哪些是应被显示的。</p><p>Reflow 是更大规模的变化。当 DOM 数被改变时、影响布局的样式被修改时、当元素的 <var>className</var> 属性被修改时或当浏览器窗口大小变化时都会引起 reflow。脚本引擎必须 reflow 相关元素以确定哪些部分不应被现实。其子节点也会被 reflow 以考虑其父节点的新布局。DOM 中此元素之后出现的元素也被 reflow 以计算新布局，因为它们的位置可能已被移动了。祖先节点也需要 reflow 以适应子节点大小的改变。总之，所有元素都需被重绘。</p><p>Reflow 从性能角度来说是非常耗时的操作，是导致 DOM 脚本较慢的主要原因之一，特别在手机等处理能力较弱的设备上。很多情况下，reflow 和重新布局整个网页耗时相近。</p><h4>减少 reflow 次数</h4><p>很多情况下脚本需要进行会引起 reflow 或重绘的操作，如动画就需要 reflow 操作，因此 reflow 是 Web 开发不可或缺的特性。为了让脚本能快速运行，应在不影响整体视觉效果的情况下尽量减少 reflow 次数。</p><p>浏览器可以选择缓存 reflow 操作，如可以等到脚本线程结束后才 reflow 以呈现变化。Opera 可以等待足够数量的改变后才 reflow、或等待足够长时间后才 reflow、或等待脚本线程结束后才 reflow。也就是说如果一个脚本线程中的发生很多间隔很小的改变时，可能只引起一个 reflow 。但开发者不能依赖此特性，特别是考虑到运行 Opera 的不同设备的运算速度有很大差异。</p><p>注意不同元素的 reflow 消耗时间不同。Reflow 表格元素消耗的时间最多是 Reflow 块元素时间的3倍。</p><h4>最小化 reflow 影响</h4><p>正常的 reflow 可能影响整个页面。reflow 的页面内容越多，则 reflow 操作的时间也越长。Reflow 的页面内容越多，需要的时间也就越长。位置固定的元素不影响页面的布局，因此如果它们 reflow 则只需 reflow 其本身。其背后的网页需要被重绘，但这比 reflow 整个页面要快得多。</p><p>所以动画不应该被用于整个页面，最好用于固定位置元素。大部分动画符合此要求。</p><h3>修改 DOM 树</h3><p>修改 DOM 树<em>会</em>导致 reflow 。向 DOM 中添加新元素、修改 text 节点值或修改属性都可能导致 reflow。顺序执行多个修改会引起超过一个 reflow，因此最好将多个修改放在不可见的 DOM 树 fragment 中。这样就只需要一次 DOM 修改操作：</p><pre>var docFragm = document.createDocumentFragment();
var elem, contents;
for( var i = 0; i &lt; textlist.length; i++ ) {
  elem = document.createElement(&#39;p&#39;);
  contents = document.createTextNode(textlist[i]);
  elem.appendChild(contents);
  docFragm.appendChild(elem);
}
document.body.appendChild(docFragm);</pre><p>也可以在元素的克隆版本中进行多个 DOM 树修改操作，在修改结束后用克隆版本替换原版本即可，这样只需要一个 reflow 操作。注意如果元素中包含表单控件，则不能使用此技巧，因为用户所做修改将无法反映在 DOM 树种。此技巧也不应该用于绑定事件处理器的元素，因为理论上不应该克隆这些元素。</p><pre>var original = document.getElementById(&#39;container&#39;);
var cloned = original.cloneNode(true);
cloned.setAttribute(&#39;width&#39;,&#39;50%&#39;);
var elem, contents;
for( var i = 0; i &lt; textlist.length; i++ ) {
  elem = document.createElement(&#39;p&#39;);
  contents = document.createTextNode(textlist[i]);
  elem.appendChild(contents);
  cloned.appendChild(elem);
}
original.parentNode.replaceChild(cloned,original);</pre><h3>修改不可见元素</h3><p>如果一个元素的 <var>display</var> 样式被设置为 <var>none</var>，即使其内容变化也不再需要重绘此元素，因为根本就不会显示此元素。可以利用这一点。如果需要对一个元素或其内容做出多个修改，又无法将这些更改放在一个重绘中，则可以先将元素设置为 <code>display</code>:none ，做出修改后，在把元素改回原来状态。</p><p>上面方法将导致两个额外的 reflow，一个是隐藏元素时另一个是重新显示此元素时，但此方法的总体效率仍较高。如果隐藏的元素影响滚动条位置，上面的方法也有可能会引起滚动条跳动。但此技术也被用于固定位置元素而不会引起任何不好看的影响。</p><pre>var posElem = document.getElementById('animation');
posElem.style.display = 'none';
posElem.appendChild(newNodes);
posElem.style.width = '10em';
... other changes ...
posElem.style.display = 'block';</pre><h3>测量大小</h3><p>如上面所述，浏览器可能会缓存多个修改一起执行，并只执行一次 reflow 。但注意为保证结果正确，测量元素大小也会引起 reflow 。尽管这不会造成任何重绘，但仍会在后台进行 reflow 操作。</p><p>使用<var> offsetWidth</var> 这样的属性或 <var>getComputedStyle</var> 这样的方法都会引起 reflow 。即使不使用返回的结果，上述操作也会引起立即 reflow。如果重复需要测量结果，可以考虑只测量一次但用变量保存结果。</p><pre>var posElem = document.getElementById('animation');
var calcWidth = posElem.offsetWidth;
posElem.style.fontSize = ( calcWidth / 10 ) + 'px';
posElem.firstChild.style.marginLeft = ( calcWidth / 20 ) + 'px';
posElem.style.left = ( ( -1 * calcWidth ) / 2 ) + 'px';
... other changes ...</pre><h3>一次修改多个样式值</h3><p>与 DOM 树修改相似，可将多个样式修改一次进行，以尽量减少重绘或 reflow数目。常见设置样式方法是逐个设置：</p><pre>var toChange = document.getElementById('mainelement');
toChange.style.background = '#333';
toChange.style.color = '#fff';
toChange.style.border = '1px solid #00f';</pre><p>上面代码可能引起多次 reflow 和重绘。有两种改进方法。如果元素采用了多个样式，而且这些样式值事先知道，可以通过修改元素 class 使用新样式：</p><pre>div {
  background: #ddd;
  color: #000;
  border: 1px solid #000;
}
div.highlight {
  background: #333;
  color: #fff;
  border: 1px solid #00f;
}
...
document.getElementById('mainelement').className = 'highlight';</pre><p>第二种方法是为元素定义新样式，而不是一个个赋值。这主要用于动态修改，如在动画中，无法事前知道新样式值。通过使用<var> style</var> 对象的 <var>cssText</var> 属性，或者通过 <var>setAttribute</var>. 可以实现此技巧。Internet Explorer 不允许第二种形式，支持第一种形式。有些较老的浏览器，包括 Opera 8 需要使用第二种形式，不支持第一种形式。最简单的方式是测试看是否支持第一种形式，如果支持就使用，如果不支持则使用第二种形式。</p><pre>var posElem = document.getElementById('animation');
var newStyle = 'background: ' + newBack + ';' +
  'color: ' + newColor + ';' +
  'border: ' + newBorder + ';';
if( typeof( posElem.style.cssText ) != 'undefined' ) {
  posElem.style.cssText = newStyle;
} else {
  posElem.setAttribute('style',newStyle);
}</pre><h3>用流畅性换取速度</h3><p>作为开发者，当然想要动画运行的越流畅越好，通常使用较小的时间间隔或较小的变化。如每10ms更新一次动画，或者每次移动1个像素。此动画可能在桌面电脑上或某些浏览器中可以完美运行。但10ms时间间隔可能是浏览器使用100%CPU才能达到的最小值。有些浏览器甚至不能完成——要求每秒100个 reflow 对大部分浏览器来说都不容易。低性能的电脑或者其他设备可能无法达到此种速度，在这些设备上动画可能非常慢甚至失去响应。</p><p>因此最好暂时把开发者的骄傲放在一边，牺牲流畅性而换取速度。把时间间隔改为50ms或把动画步长设为5个像素，会消耗更少的计算<span><a href="http://www.woiweb.net/category/resource" title="资源" rel="nofollow">资源</a></span>，在低性能设备上也能正常运行。</p><h3>避免搜索大量节点</h3><p>当需要查找节点时，尽量使用 DOM 内置方法和集合缩小搜索范围。如你想要定位某个包含某种属性的元素，可使用下面代码：</p><pre>var allElements = document.getElementsByTagName(&#39;*&#39;);
for( var i = 0; i &lt; allElements.length; i++ ) {
  if( allElements[i].hasAttribute(&#39;someattr&#39;) ) {
    ...
  }
}</pre><p>即使没听说过 XPath 这样的高级技巧，也可以看出上面的代码有两个问题导致速度变慢。首先它搜索每一个元素，而不是尝试缩小搜索范围。其次即使已经找到所需元素上卖弄代码还继续搜索。如果已知要找的元素在 id 为 <var>inhere</var>的 div 中，最好使用下面的代码：</p><pre>var allElements = document.getElementById(&#39;inhere&#39;).getElementsByTagName(&#39;*&#39;);
for( var i = 0; i &lt; allElements.length; i++ ) {
  if( allElements[i].hasAttribute(&#39;someattr&#39;) ) {
    ...
    break;
  }
}</pre><p>如果已知要找元素是 div 的直接子节点，则下面的代码速度更快：</p><pre>var allChildren = document.getElementById(&#39;inhere&#39;).childNodes;
for( var i = 0; i &lt; allChildren.length; i++ ) {
  if( allChildren[i].nodeType == 1 &amp;&amp; allChildren[i].hasAttribute(&#39;someattr&#39;) ) {
    ...
    break;
  }
}</pre><p>基本的思想就是尽量避免逐个查看 DOM 节点。DOM 有很多更好更快的方法，如 DOM 2 Traversal TreeWalker，效率要高于递归查找 <var>childNodes</var> 集合。</p><h3>使用 XPath 提高速度</h3><p>假如需要基于 H2-H4 元素在 <span><a href="http://www.woiweb.net/category/html_css" title="html" rel="nofollow">html</a></span> 网页中创建目录。在 <span><a href="http://www.woiweb.net/category/html_css" title="html" rel="nofollow">html</a></span> 中标题元素可以出现在很多地方，因此无法使用递归函数获取这些元素。传统 DOM 可能使用如下方法：</p><pre>var allElements = document.getElementsByTagName(&#39;*&#39;);
for( var i = 0; i &lt; allElements.length; i++ ) {
  if( allElements[i].tagName.match(/^h[2-4]$/i) ) {
    ...
  }
}</pre><p>若网页有超过2000个元素，此方法速度会很慢。如果支持 XPath，则可以使用一个快得多的方法，因为 XPath 查询引擎可比需被解释的 JavaScript 更好的被优化。在有些情况下，XPath 速度可能会快2个数量级以上。下面代码和上面完成一样的功能，但使用 XPath 因此速度要更快：</p><pre>var headings = document.evaluate( '//h2|//h3|//h4', document, null, XPathResult.ORDERED_NODE_ITERATOR_TYPE, null );
var oneheading;
while( oneheading = headings.iterateNext() ) {
  ...
}</pre><p>下面版本代码融合上述两种方法；在支持 XPath 的地方使用快速方法，在不支持时使用传统 DOM 方法：</p><pre>if( document.evaluate ) {
  var headings = document.evaluate( &#39;//h2|//h3|//h4&#39;, document, null, XPathResult.ORDERED_NODE_ITERATOR_TYPE, null );
  var oneheading;
  while( oneheading = headings.iterateNext() ) {
    ...
  }
} else {
  var allElements = document.getElementsByTagName(&#39;*&#39;);
  for( var i = 0; i &lt; allElements.length; i++ ) {
    if( allElements[i].tagName.match(/^h[2-4]$/i) ) {
      ...
    }
  }
}</pre><h3>避免在遍历 DOM 时修改 DOM</h3><p>有些 DOM 集合是实时的，如果在你的脚本遍历列表时相关元素产生变化，则此集合会立刻变化而不需要等待脚本遍历结束。<var>childNodes</var> 集合和<var> getElementsByTagName </var>返回的节点列表都是这样的实时集合。</p><p>如果在遍历这样的集合的同时向其中添加元素，则可能会遇到无限循环，因为你不停的向列表中添加元素，永远也不会碰到列表结束。这不是唯一的问题。为提高性能，可能会对这些集合做出优化，如记住其长度、记住脚本中上一个访问元素序号，这样在你访问下一个元素时可快速定位。</p><p>如果你此时修改 DOM 树，即使修改的元素不在此集合中，集合还是会重新搜索以查看是否有新元素。这样就无法记住上一个访问元素序号或记住集合长度，因为集合本身可能已经变了，这样就无法使用优化：</p><pre>var allPara = document.getElementsByTagName(&#39;p&#39;);
for( var i = 0; i &lt; allPara.length; i++ ) {
  allPara[i].appendChild(document.createTextNode(i));
}</pre><p>下面的代码在 Opera 和 Internet Explorer 等主流浏览器中比上面代码快10倍以上。先创建一个要修改元素的静态列表，然后遍历静态列表并作出相应修改，而不是遍历 <var>getElementsByTagName </var>返回的节点列表：</p><pre>var allPara = document.getElementsByTagName(&#39;p&#39;);
var collectTemp = [];
for( var i = 0; i &lt; allPara.length; i++ ) {
  collectTemp[collectTemp.length] = allPara[i];
}
for( i = 0; i &lt; collectTemp.length; i++ ) {
  collectTemp[i].appendChild(document.createTextNode(i));
}
collectTemp = null;</pre><h3>使用变量保存 DOM 值</h3><p>有些 DOM 返回值无法缓存，每次调用时都会重新调用函数。如<var> getElementById</var> 方法。下面是一个低效率代码的例子：</p><pre>document.getElementById('test').property1 = 'value1';
document.getElementById('test').property2 = 'value2';
document.getElementById('test').property3 = 'value3';
document.getElementById('test').property4 = 'value4';</pre><p>此代码为定位同一个对象调用了四次 <var>getElementById</var> 方法。下面的代码只调用了一次并将结果保存在变量中，单看这一个操作可能比上面单个操作要略慢，因为需要执行赋值语句。但后面不再需要调用 getElementById 方法！下面的代码比上面的代码要快5-10倍：</p><pre>var sample = document.getElementById('test');
sample.property1 = 'value1';
sample.property2 = 'value2';
sample.property3 = 'value3';
sample.property4 = 'value4';</pre><h2>页面载入</h2><h3>避免保存来自其他文档的引用</h3><p>如果文档访问过其他文档中的节点或对象，在脚本结束后避免保留这些引用。如果在全局变量或对象属性中保存过这些引用，通过设置为 null 清除之或者直接删除之。</p><p>原因是另一个文档被销毁后，如弹出窗口被关闭，尽管那个文档已经不再了，所有对那个文档中对象的引用都会在内存中保存整个 DOM 树和脚本环境。这也适用那些包含在frame，内联 frame，或 OBJECT 元素中的网页。.</p><pre>var remoteDoc = parent.frames['sideframe'].document;
var remoteContainer = remoteDoc.getElementById('content');
var newPara = remoteDoc.createElement('p');
newPara.appendChild(remoteDoc.createTextNode('new content'));
remoteContainer.appendChild(newPara);
//remove references
remoteDoc = null;
remoteContainer = null;
newPara = null;</pre><h3>快速历史浏览（history navigation）</h3><p>Opera (和许多其他浏览器) 默认使用快速历史浏览。当用户点击后退或前进时，将记录当前页面的状态及页面中的脚本。当用户回到刚才的页面时，将立即显示刚才的页面，如同从没有离开此页一样。不需要重新载入页面也不需要重新初始化。脚本继续运行，DOM 也和离开此页前完全相同。对用户来说这样反应很快，载入较慢的网页应用程序会有更好的性能。</p><p>尽管 Opera 提供<a href="http://www.opera.com/support/search/supsearch.dml?index=827">开发者控制此行为</a>的方式，最好还是尽量保持快速历史浏览功能。也就是说最好避免会影响此功能的动作，包括提交表单时禁用表单控件或让页面内容透明或不可见的渐出特效。</p><p>简单的解决方法是使用 onunload 监听器 reset 渐出效果或重新 enable 表单控件。注意对有些浏览器来说，如 Firefox 和 Safari，为 <var>unload</var> 事件添加监听器会禁用历史浏览。而在 Opera 中禁用提交按钮会导致禁用历史浏览。</p><pre>window.onunload = function () {
  document.body.style.opacity = '1';
};</pre><h3>使用 XMLHttpRequest</h3><p>此技巧不一定适用于每一个项目，但它能显著降低从服务器下载数据量，也能避免重载页面时销毁及创建脚本环境的开销。开始时正常载入页面，然后使用 XMLHttpRequest 下载最少量的新内容。这样 JavaScript 环境会一直存在。</p><p>注意此方法也可能会导致问题。首先此方法完全破坏历史浏览。尽管可通过内联frame储存信息来解决此问题，但这显然不符合使用 XMLHttpRequest 的初衷。因此尽量少用，只在不需要回退到先前内容时使用。此方法还会影响辅助器具的使用（ assistive device），因为将无法察觉 DOM 已被更改，因此最好在不会引起问题的地方使用XMLHttpRequest。</p><p>若 JavaScript 不可用或不支持 XMLHttpRequest 则此技巧也会失效。最简单避免此问题的方法是使用正常链接指向新页面。增加一个检测链接是否被激活的事件处理器。处理器可以探测是否支持 XMLHttpRequest ，如果支持则载入新数据并阻止链接默认动作。载入新数据后，用其取代页面的部分内容，然后 request 对象就可以被销毁并允许垃圾收集器回收内存资源。</p><pre>document.getElementById(&#39;nextlink&#39;).onclick = function () {
  if( !window.XMLHttpRequest ) { return true; }
  var request = new XMLHttpRequest();
  request.onreadystatechange = function () {
    if( request.readyState != 4 ) { return; }
    var useResponse = request.responseText.replace( /^[\w\W]*&lt;div id=&quot;container&quot;&gt;|&lt;\/div&gt;\s*&lt;\/body&gt;[\w\W]*$/g , &#39;&#39; );
    document.getElementById(&#39;container&#39;).innerHTML = useResponse;
    request.onreadystatechange = null;
    request = null;
  };
  request.open( &#39;GET&#39;, this.href, true );
  request.send(null);
  return false;
}</pre><h3>动态创建 SCRIPT 元素</h3><p>加载和处理脚本需要时间，但有些脚本在载入后却从来未被使用。载入这样的脚本浪费时间和资源，并影响当前的脚本执行，因此最好不要引用这种不用的脚本。可以通过简单的加载脚本判断需要哪些脚本，并只为后面需要的脚本创建 script 元素。</p><p>理论上，这个加载脚本可在页面载入结束后通过创建 SCRIPT 元素加入 DOM。这在所有主流浏览器中都可以正常工作，但这可能对浏览器的提出更多的要求，甚至大于要载入的脚本本身。而且在页面载入之前可能就需要脚本，因此最好在页面加载过程中，通过<code> document.write</code> 创建 script 标签。记住一定要转义‘/’字符防止终止当前脚本运行：</p><pre>if( document.createElement &amp;&amp; document.childNodes ) {
  document.write(&#39;&lt;script type=&quot;text\/javascript&quot; src=&quot;dom.js&quot;&gt;&lt;\/script&gt;&#39;);
}
if( window.XMLHttpRequest ) {
  document.write(&#39;&lt;script type=&quot;text\/javascript&quot; src=&quot;xhr.js&quot;&gt;&lt;\/script&gt;&#39;);
}</pre><h3><code>location.replace()</code> 控制历史项</h3><p>有时需要通过脚本修改页面地址。常见的方法是给<code> location.href</code> 赋新地址。这将和打开新链接一样添加新历史项、载入新页面。</p><p>有时不想添加新历史项，因为用户不需要回到前面的页面。这在内存资源有限的设备中很有用。通过替换历史项恢复当前页面所使用的内存。可以通过<code> location.replace()</code>方法实现。</p><pre>location.replace('newpage.html');</pre><p>注意页面仍被保存在 cache 中，仍占用内存，但比保存在历史中要少的多。</p><p>作者 <a href="http://dev.opera.com/author/28143">Mark ‘Tarquin’ Wilton-Jones</a> · 2006年11月2日<br> 本文翻译自 <a href="http://dev.opera.com/articles/view/efficient-javascript/">Efficient JavaScript</a></p>