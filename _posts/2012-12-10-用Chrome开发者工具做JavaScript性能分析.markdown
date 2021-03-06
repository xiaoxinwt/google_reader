---
layout: post
title:  "用Chrome开发者工具做JavaScript性能分析"
date:   2012-12-10 11:14:20
author: 
categories: program
---

## 用Chrome开发者工具做JavaScript性能分析
### by 
### at 2012-12-10 11:14:20
### original <http://blog.jobbole.com/31178/?utm_source=rss&utm_medium=rss&utm_campaign=%25e7%2594%25a8chrome%25e5%25bc%2580%25e5%258f%2591%25e8%2580%2585%25e5%25b7%25a5%25e5%2585%25b7%25e5%2581%259ajavascript%25e6%2580%25a7%25e8%2583%25bd%25e5%2588%2586%25e6%259e%2590>

<p>英文原文：<a href="http://coding.smashingmagazine.com/2012/06/12/javascript-profiling-chrome-developer-tools/" rel="author">Zack Grossbart</a>，编译：<a href="http://www.jobbole.com">伯乐</a>在线——<a href="http://blog.jobbole.com/author/%e7%8e%8b%e7%ad%b1/">王筱</a></p>
<p>你的网站正常运转。现在我们来让它运转的更快。网站的性能由页面载入速度和代码执行效率决定。一些服务可以让你的网站载入更快，比如<span style="color:#0000ff"><span style="color:#0000ff"><a href="http://stackoverflow.com/questions/3520285/is-there-a-good-javascript-minimizer">压缩</a>JS</span></span>和<span style="color:#0000ff"><a href="http://en.wikipedia.org/wiki/Content_delivery_network"><span style="color:#0000ff">CDN</span></a></span>，但是让代码执行的更快你要做的事情。</p>
<p>代码中很小的改动都可能对性能造成巨大的影响。快速灵活的网站和可怕的“无响应脚本”对话框可能只有几行代码的差别。这篇文章告诉你如何通过用Chrome开发者工具（Chrome Developer Tools）找到这几行关键的代码。</p>
<p align="left"><strong>设置一个基线</strong></p>
<p>我们来看一个简单的“颜色排序器”应用，这个应用展示了一个由各种颜色构成的网格，你可以拖拽这些颜色进行混合。每一个点都是一个div标签加上一些让它看起来是圆的的CSS。</p>
<p><a href="http://blog.jobbole.com/wp-content/uploads/2012/12/sorter1.png" rel="lightbox[31178]" title="用chrome开发者工具进行JS性能分析"><img title="用chrome开发者工具进行JS性能分析" src="http://blog.jobbole.com/wp-content/uploads/2012/12/sorter1-261x300.png" alt="用chrome开发者工具进行JS性能分析" width="261" height="300"></a></p>
<p>生成这些颜色是需要技巧的，所以我借助了<a href="http://krazydad.com/tutorials/makecolors.php">”Making Annoying Rainbows in Javascript”</a>。</p>
<p>页面载入的很快，但还是花费了一些时间，在渲染之前还闪了一下。是时候对这个页面进行性能分析让它更快了。</p>
<p>在开始做性能优化的时候要设置一个基线，来明确这个页面的速度到底怎样。这个基线可以让你知道自己是否做了优化并帮助你权衡利弊。在这片文章里我们要使用<a href="http://code.google.com/chrome/devtools/docs/overview.html">chrome开发者工具</a>。</p>
<p>性能分析器(profiler)是chrome开发者工具的一部分，点击小扳手下面的工具菜单就可以打开它。<a href="http://getfirebug.com/">Firebug</a>也有一些性能评测工具，但是webkit内核的<span><a href="http://blog.jobbole.com/12749/" title="浏览器">浏览器</a></span>（chrome和safari）在对代码进行性能分析和展示时间线方面是最棒的。Chrome还提供一种很棒的事件跟踪工具，叫做<a href="https://developers.google.com/web-toolkit/speedtracer/"> speed tracer</a>。</p>
<p>在时间线(timeline)标签下开始记录，载入页面然后停止记录，这样就设置了一个基线。（打开chrome开发者工具，点击“时间线”标签，然后点击窗口底部圆形的黑色“记录”图标开始记录）。chrome是很智能的，只有页面开始载入的时候才会开始记录。我记录了三次然后取了平均值，以防我的电脑在第一次测试的时候运行的很慢。</p>
<p><a href="http://blog.jobbole.com/wp-content/uploads/2012/12/baseline1.png" rel="lightbox[31178]" title="用chrome开发者工具进行JS性能分析"><img title="用chrome开发者工具进行JS性能分析" src="http://blog.jobbole.com/wp-content/uploads/2012/12/baseline1.png" alt="用chrome开发者工具进行JS性能分析" width="500" height="562"></a></p>
<p>我的平均基线，也就是从第一个请求到页面全部渲染结束所花费的时间是1.25秒。这个时间不是太长，但是对于这样一个小的页面来说也不算好。</p>
<p>我想让代码执行的更快，但是我并不知道是什么让它慢下来的。性能分析器(profiler)帮助我找到原因。</p>
<p> </p>
<p><strong>创建一个Profile</strong></p>
<p>时间线(timeline)告诉我们代码运行花费的时间，但是并没有帮助我们知道代码运行的时候发生了什么。我们可以做一些改动然后不断的测每次代码运行的时间，但这是盲目的。profiles给我们提供了更好的方法。profiler告诉我们哪些函数的执行占用了大部分时间。让我们切换到chrome开发者工具的“Profiles”标签页开始性能测试，这里一共提供了三种类型的性能测试。</p>
<p>1、      javascript cpu 性能测试</p>
<p>显示javascript占用了多少CPU</p>
<p>2、      css选择器性能测试</p>
<p>显示处理CSS选择器占用的CPU</p>
<p>3、      堆栈快照</p>
<p>显示javascript对象的内存占用情况</p>
<p>我们想要javascript代码执行的更快，所以我们进行CPU性能测试。我们开始性能测试，刷新页面然后停止。</p>
<p><a href="http://blog.jobbole.com/wp-content/uploads/2012/12/profile1.png" rel="lightbox[31178]" title="用chrome开发者工具进行JS性能分析"><img title="用chrome开发者工具进行JS性能分析" src="http://blog.jobbole.com/wp-content/uploads/2012/12/profile1.png" alt="用chrome开发者工具进行JS性能分析" width="500" height="562"></a></p>
<p>通过性能分析首先知道很多函数在执行。“颜色排序器”使用了jQuery和jQuery  UI,来处理些管理插件和解析表达式之类的事情。我发现列表最顶端的是decimalToHex和makeColorSorter两个函数。这两个函数占用了CPU13.2%的时间，这是做优化的好地方。</p>
<p>我们可以点击函数调用旁边的“下一个”箭头来查看完整的函数调用堆栈。展开后，可以看到decimalToHex是被makeColorSorter调用的，makeColorSorter是通过$(document).ready调用的。</p>
<p>代码如下</p>
<pre>$(document).ready(function() {
    makeColorSorter(.05, .05, .05, 0, 2, 4, 128, 127, 121);
    makeSortable();
});</pre>
<p>弄清楚这两个函数是哪里调用的，也就弄清楚了让颜色可以排序并不是最大的性能问题。通常情况下性能问题都是由多余的排序操作造成的，但是在我的代码中相比与排序增加DOM元素花费了更多时间。</p>
<p>我想要让这些函数执行的更快，但是首先我想要将我的改动区隔开。在页面载入过程中会发生很多事情，我不想要这些影响到我的性能分析。</p>
<p> </p>
<p><strong>区隔问题</strong></p>
<p>我做了第二个版本，这个版本中“颜色排序器”在我点击按钮之后才载入，而不是在document ready的时候载入。这就把文档载入的过程分离出去，让我可以只对颜色分类进行性能测试。调完性能之后我可以立刻改回去。</p>
<p>让我们调用新的函数testColorSorter并把它绑定到一个可点击的按钮上。</p>
<pre>function testColorSorter() {
    makeColorSorter(.05, .05, .05, 0, 2, 4, 128, 127, 121);
    makeSortable();
}</pre>
<pre>&lt;button id=&quot;clickMe&quot; onclick=&quot;testColorSorter();&quot;&gt;Click me&lt;/button&gt;</pre>
<pre></pre>
<p>在我们进行性能分析之前改变应用可能导致意外的结果。这个改动看起来很安全，但是我还是要重新运行性能检测器来看看我是不是无意中改变了什么。我会开始一次新的性能分析，点击应用中的按钮然后停止。</p>
<p><a href="http://blog.jobbole.com/wp-content/uploads/2012/12/profile2.png" rel="lightbox[31178]" title="用chrome开发者工具进行JS性能分析"><img title="用chrome开发者工具进行JS性能分析" src="http://blog.jobbole.com/wp-content/uploads/2012/12/profile2.png" alt="用chrome开发者工具进行JS性能分析" width="500" height="562"></a></p>
<p>我首先注意到decimalToHex函数的载入只占用了4.23%的时间。这是代码执行花费时间最多的地方。我们创建一个新的基线来看看这个方案对代码有多大的优化。</p>
<p><a href="http://blog.jobbole.com/wp-content/uploads/2012/12/baseline2.png" rel="lightbox[31178]" title="用chrome开发者工具进行JS性能分析"><img title="用chrome开发者工具进行JS性能分析" src="http://blog.jobbole.com/wp-content/uploads/2012/12/baseline2.png" alt="用chrome开发者工具进行JS性能分析" width="500" height="562"></a></p>
<p>有些事件在我点击按钮之前有触发了，但是我只关注从我点击鼠标到浏览器渲染“颜色排序器”花费的时间。鼠标在390毫秒时点击，渲染事件在726毫秒处被触发。726减去390得到我的基线336毫秒。和第一个基线一样我重复了3次来取平均值。</p>
<p>这时，我知道如何获得并且得到了代码确切的运行时间，我们已经准备好开始解决问题了。</p>
<p> </p>
<p><strong>让代码更高效</strong></p>
<p>性能分析器只告诉我们哪个函数造成的问题，所以我们要查看下函数的源码来了解函数做了些什么。</p>
<pre>function decimalToHex(d) {
    var hex = Number(d).toString(16);
    hex = &quot;00&quot;.substr(0, 2 - hex.length) + hex; 

    console.log(&#39;converting &#39; + d + &#39; to &#39; + hex);
    return hex;
}</pre>
<pre></pre>
<p>“颜色排序器”中的每一个颜色点都有一个16进制的色彩值，例如#86F01B和#2345FE.这些值表示一种颜色中红，绿，蓝三原色各自的数值。例如的背景色是#2456FE,代表红色的值是36，绿色的值是86，蓝色的是254，每一个数值必须是0到255之间的。</p>
<p>decimalToHex函数把这用RGB值表示的颜色转化为页面中我们使用的16进制颜色。这个函数十分的简单，但是我还是留下了一个可以去掉的调试代码console.log在那里。</p>
<p>decimalToHex 函数还在数字之前加上了补位。这是很重要的一点，因为有些10进制数字对应的是1个16进制数字。比如十进制中的10对应着16进制中的C，但是在CSS中需要一个两位数。为了让这个进制换算更快速，我们让这段代码不是那么泛化。我知道每个需要补位的数字长度都为1，所以我们可以这样重写这个函数。</p>
<pre>function decimalToHex(d) {
    var hex = Number(d).toString(16);
    return hex.length === 1 ? &#39;0&#39; + hex : hex; }</pre>
<pre></pre>
<p>第三个版本的“颜色排序器”只有在需要补位的时候才改变字符串，并且不用调用substr函数。有了这个新函数，运行时间是137毫秒。再次对代码进行性能测试，可以发现decimalToHex函数只占用了总时间的%0.04,到了列表的下部。</p>
<p><a href="http://blog.jobbole.com/wp-content/uploads/2012/12/profile3.png" rel="lightbox[31178]" title="用chrome开发者工具进行JS性能分析"><img title="用chrome开发者工具进行JS性能分析" src="http://blog.jobbole.com/wp-content/uploads/2012/12/profile3.png" alt="用chrome开发者工具进行JS性能分析" width="500" height="562"></a></p>
<p>我们还可以发现占用CPU最多的函数是 jQuery的e.extend.merge。我不知道这个函数的作用，因为代码是压缩过的。我可以使用开发版本的jQuery，但是我发现这个函数是被makeColorSorter调用的。所以下一步我们先让这个函数执行的更快。</p>
<p> </p>
<p><strong>减小改动</strong></p>
<p>“颜色排序器”中的多彩颜色是用过正弦曲线生成的。在光谱中设置一个中心点，然后以一定的偏移来创建这个曲线。这就把颜色变成了一个“彩虹模型”。我们还可以通过改变红绿蓝三原色的使用频率来改变颜色。</p>
<pre>function makeColorSorter(frequency1, frequency2, frequency3,
                         phase1, phase2, phase3,
                         center, width, len) {

    for (var i = 0; i &lt; len; ++i)
    {
       var red = Math.floor(Math.sin(frequency1 * i + phase1) * width + center);
       var green = Math.floor(Math.sin(frequency2 * i + phase2) * width + center);
       var blue = Math.floor(Math.sin(frequency3 * i + phase3) * width + center);

       console.log(&#39;red: &#39; + decimalToHex(red));
       console.log(&#39;green: &#39; + decimalToHex(green));
       console.log(&#39;blue: &#39; + decimalToHex(blue));

       var div = $(&#39;&lt;div class=&quot;colorBlock&quot;&gt;&lt;/div&gt;&#39;);
       div.css(&#39;background-color&#39;, &#39;#&#39; + decimalToHex(red) + decimalToHex(green) + decimalToHex(blue));
       $(&#39;#colors&#39;).append(div);

    }
}</pre>
<pre></pre>
<p>我们要去掉console.log函数。这些调用非常的糟糕，因为每次执行都会调用decimalToHex函数，这意味着decimalToHex函数会被多调用2倍的次数。这个函数大幅度的改变了DOM结构。每次循环，都向id为colors的div中添加一个新的div。这就让我怀疑这就是e.extend.mergefunction做的事情。用性能分析器做一个小实验就可以搞清楚。</p>
<p>我想要一次把所有的div添加进去，而不是在每个循环中添加一个新的div。创建一个变量来存储数据，然后在最后一次性添加进去。</p>
<pre>function makeColorSorter(frequency1, frequency2, frequency3,
                         phase1, phase2, phase3,
                         center, width, len) {

    var colors = &quot;&quot;;
    for (var i = 0; i &lt; len; ++i)
    {
       var red = Math.floor(Math.sin(frequency1 * i + phase1) * width + center);
       var green = Math.floor(Math.sin(frequency2 * i + phase2) * width + center);
       var blue = Math.floor(Math.sin(frequency3 * i + phase3) * width + center);

       colors += &#39;&lt;div class=&quot;colorBlock&quot; style=&quot;background-color: #&#39; + 
           decimalToHex(red) + decimalToHex(green) + decimalToHex(blue) + &#39;&quot;&gt;&lt;/div&gt;&#39;;
    }

    $(&#39;#colors&#39;).append(colors);
}</pre>
<pre></pre>
<p>这个小改动意味着DOM只在添加所有div的时候做一次改变。用时间线进行测试，我们发现从点击到渲染花费了31毫秒。这个dom变动，使得第四个版本的运行时间降低了86%。我可以再次打开性能分析器(profiler)，发现e.extend.merge函数占用了很少的时间，在列表中已经看不到它了。</p>
<p>我们还可以完全移除decimalToHex函数让代码更快一点。因为CSS支持RGB颜色，所以我们不需要把他们转换到16进制。现在我们可以这样写makeColorSorter函数。</p>
<pre>function makeColorSorter(frequency1, frequency2, frequency3,
                         phase1, phase2, phase3,
                         center, width, len) {

    var colors = &quot;&quot;;
    for (var i = 0; i &lt; len; ++i)
    {
       var red = Math.floor(Math.sin(frequency1 * i + phase1) * width + center);
       var green = Math.floor(Math.sin(frequency2 * i + phase2) * width + center);
       var blue = Math.floor(Math.sin(frequency3 * i + phase3) * width + center);

       colors += &#39;&lt;div class=&quot;colorBlock&quot; style=&quot;background-color: rgb(&#39; + 
           red + &#39;,&#39; + green + &#39;,&#39; + blue + &#39;)&quot;&gt;&lt;/div&gt;&#39;;
    }

    $(&#39;#colors&#39;).append(colors);
}</pre>
<pre></pre>
<p align="left">第五个版本的执行只用了26毫秒而且代码行数从28行减少到18行。</p>
<p align="left"><strong>在你的应用中进行Javascript性能分析</strong></p>
<p align="left">实际工作中的应用要比“颜色排序器”复杂的多，但是做性能分析要遵循同样的基本原则</p>
<p align="left">1、      设置一个基线，这样你就知道你是从何处开始的。</p>
<p align="left">2、      把问题从应用的其他代码隔离出来。</p>
<p align="left">3、      在一个可控的环境下进行优化，频繁的使用时间线（timelines）和性能分析器（profiles）</p>
<p align="left">还有一些性能优化的准则</p>
<p align="left">1、      从最慢的部分开始，这样在时间优化上可以得到最大的提升。</p>
<p align="left">2、      控制环境。如果你换了电脑或者做了任何大的改动，都要设置新的基线。</p>
<p align="left">3、      多次分析以防你电脑的异常导致得到不正确的结果。</p>
<p align="left">每个人都想要他的网站更快，你必须开发新的功能，但是新的功能通常会让网站更慢。所以花费时间来做性能优化是有价值的。</p>
<p align="left">性能分析和优化使得最终版颜色分类器的执行时间减少了92%。你的网站可以变快多少？</p>
<p> </p>
<p>英文原文：<a href="http://coding.smashingmagazine.com/2012/06/12/javascript-profiling-chrome-developer-tools/" rel="author">Zack Grossbart</a>，编译：<a href="http://www.jobbole.com">伯乐</a>在线——<a href="http://blog.jobbole.com/author/%e7%8e%8b%e7%ad%b1/">王筱</a></p>
<p>译文链接： <a href="http://blog.jobbole.com/31178/">http://blog.jobbole.com/31178/</a></p>
<p><span style="color:#ff0000">【如需转载，请在正文中标注并保留原文链接、译文链接和译者等信息，谢谢合作！】</span></p>
<p> </p>

<h2>相关文章</h2><ul><li><a href="http://blog.jobbole.com/30468/">测试：你自认为理解了JavaScript？</a></li><li><a href="http://blog.jobbole.com/30854/">JavaScript开发规范要求</a></li><li><a href="http://blog.jobbole.com/31166/">查询json数据结构的8种方式</a></li><li><a href="http://blog.jobbole.com/30922/">在Chrome浏览器中保存的密码有多安全？</a></li><li><a href="http://blog.jobbole.com/19203/">网易邮箱前端Javascript编码规范：类规范</a></li><li><a href="http://blog.jobbole.com/31213/">headtrackr：一个头部/脸部追踪的JavaScript库</a></li><li><a href="http://blog.jobbole.com/19197/">网易邮箱前端Javascript编码规范：基础规范</a></li><li><a href="http://blog.jobbole.com/8481/">JavaScript初学者应注意的七个细节</a></li><li><a href="http://blog.jobbole.com/30134/">Testacular：Google开源的JavaScript测试执行过程管理工具</a></li><li><a href="http://blog.jobbole.com/30738/">寻找更好的Javascript单元测试工具</a></li></ul>