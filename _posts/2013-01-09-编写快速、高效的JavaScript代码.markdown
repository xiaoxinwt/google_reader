---
layout: post
title:  "编写快速、高效的JavaScript代码"
date:   2013-01-09 00:00:01
author: 
categories: program
---

## 编写快速、高效的JavaScript代码
### by 
### at 2013-01-09 00:00:01
### original <http://blog.jobbole.com/31951/?utm_source=rss&utm_medium=rss&utm_campaign=%25e7%25bc%2596%25e5%2586%2599%25e5%25bf%25ab%25e9%2580%259f%25e3%2580%2581%25e9%25ab%2598%25e6%2595%2588%25e7%259a%2584javascript%25e4%25bb%25a3%25e7%25a0%2581>

<p>英文原文：<a href="http://coding.smashingmagazine.com/2012/11/05/writing-fast-memory-efficient-javascript/">Writing Fast,Memory-Efficient JavaScript</a>，编译：<a href="http://www.jobbole.com/">伯乐</a>在线——<a href="http://blog.jobbole.com/author/%E6%88%B4%E5%98%89%E5%8D%8E/">戴嘉华</a></p>
<p>许多Javascript引擎都是为了快速运行大型的JavaScript程序而特别设计的，例如Google的V8引擎（Chrome<span><a href="http://blog.jobbole.com/12749/" title="浏览器">浏览器</a></span>，Node均使用该引擎）。在你的开发过程中，如果你关心你程序的内存和性能的话，你应该了解并意识到，在你的代码背后，浏览器的JavaScript引擎中到底发生了什么事情。</p>
<p>不论的V8,SpiderMonkey(Firefox),Carakan(Opera),Chakra(IE)或者其它类型的引擎。了解引擎背后的一些运行机制可以帮助你更好的优化你的应用程序。这并不是意味着你只为一种浏览器或者一种引擎进行程序的优化，而且，永远不要这样做。</p>
<p>然而，你应该问自己下面这些问题：</p>
<p>● 我应该做点什么才能让我的代码更高效地运行。</p>
<p>● 流行的JavaScript引擎（通常）是怎么进行优化的。</p>
<p>● 有什么是引擎无法进行优化的，还有，垃圾回收器是不是按照我预想的那样，回收了我不需要的内存空间。</p>
<p style="text-align:center"><img src="http://media.smashingmagazine.com/wp-content/uploads/2012/10/fast_memory.jpg" alt="" width="490" height="323"></p>
<p>在我们编写高效、快速的代码的时候，有许多常见的陷阱。在这篇文章当中，我们会去探索一些方法，让你的代码拥有更加良好的性能，我们也会为这些代码提供测试样例。</p>
<p><strong>JavaScript在V8引擎中是如何工作的？</strong></p>
<p> </p>
<p>虽然在没有彻底了解JavaScript引擎的情况下，开发出大型的应用程序是有可能的，这就像车主开过车却没有看过引擎盖背后的东西一样。把我选择的Chrome浏览器作为例子，我将会谈谈它的JavaScript引擎的工作机制。V8引擎，是由几个核心的部分组成的。</p>
<p>● 一个基本的编译器（basecompiler），在你的代码运行之前，它会分析你的JavaScript代码并且生成本地的机器码，而不是通过字节码的方式来运行，也不是简单地解释它。这种机器码起初是没有被高度优化的。</p>
<p>● V8通过对象模型（objectmodel）来表达你的对象。对象是在JavaScript中是以关联数组的方式呈现的，但是在V8引擎中，它们是通过隐藏类（<a href="https://developers.google.com/v8/design">hiddenclasses</a>）的方式来表示的。这是一种可以优化查找的内部类型机制（internaltypesystem）。</p>
<p>● 一个运行期剖析器（runtimeprofiler），它会监视正在运行的系统，并且标识出“热点”函数（“hot”function），也就是那些最后会花费大量运行时间的代码。</p>
<p>● 一个优化编译器（optimizingcompiler），重新编译并优化运行期剖析器所标识“热点”代码，然后执行优化，例如，把代码进行内联化（inlining）（也就是在函数被调用的地方用函数主体去取代）。</p>
<p>● V8引擎支持逆优化（deoptimization），意味着如果优化编译器发现在某些假定的情况下，把一些已经优化的代码进行了过度的优化，它就会把它门从生成的代码中抽离出来。</p>
<p>● V8拥有垃圾回收器。理解它是如何运作的和理解如何优化你的JavaScript代码同等重要。</p>
<div></div>
<div>
<p><span style="color:#ff0000"><strong>垃圾回收</strong></span></p>
<p> </p>
<p>垃圾回收是一种内存管理机制。垃圾回收器的概念是，它会尝试去重新分配已经不需要的对象所占据的内存空间。在如JavaScript拥有垃圾回收机制的语言中，如果你的程序中仍然存在指向一个对象的引用，那么该对象将不会被回收。</p>
<p>在大多数的情况下，我们没有必要去手动得解除对象的引用（de-referencing）。只要简单地把变量放在它们应该的地方（在理想的情况下，变量应该尽量为局部变量，也就是说，在它们被使用的函数中声明它们，而不是在更外层的作用域），垃圾就能正确地被回收。</p>
<p><img src="http://media.smashingmagazine.com/wp-content/uploads/2012/10/robot-cleaner.jpg" alt="" width="500" height="456"></p>
<p>在JavaScript中强制进行垃圾回收是一件不可能的事情，而且你也不会想这样做。因为垃圾回收的过程是由运行期所控制的，回收器通常知道垃圾回收的最佳时机在什么时候。</p>
<p><strong>关于解除引用的误解</strong></p>
<p>在网上不少关于JavaScript的内存分配问题的讨论中，关键字delete被频繁提出。虽然它本意是用来删除映射（map）中的键（keys），但是不少的开发者认为也可以使用它来强制解除引用。在可能的情况下，尽量避免使用delete。在下面的例子中，删除o.x在的代码背后会发生一些弊大于利的事情，因为它会改变o的隐藏类，并且把它转化成一般的对象，而这些一般对象会更慢。</p>
<pre>var o = { x: 1 }; 
delete o.x; // true 
o.x; // undefined</pre>
<p>也就是说，在现在流行的JavaScript库中，你几乎肯定能找到delete删除引用的身影——它也确实存在这个语言目的。这里提出来的主旨是，让大家尽量避免在运行期改变热点对象（hotobjects）的结构。JavaScript引擎可以检测出这种的“热点”对象并尝试去优化它们，如果在对象的生命期中没有遇到重大的结构改变，引擎的检测和优化过程会来得更加容易，而使用delete则会触发对象结构上的这种改变。</p>
<p>不少人对null的使用上也存在误解。将一个对象的引用设为null，并不是意味着“清空”该对象，而是将该引用指向null。用o.x=null比用delete要好，但这甚至可能不是必要的。</p>
<pre>var o = { x: 1 }; 
o = null;
o; // null
o.x // TypeError</pre>
<p>如果被删除的引用是指向对象的最后一个引用，那么该对象就满足了垃圾回收的资格。如果该引用不是指向对象的最后一个引用，那么该对象仍然可以被获取，而不会被垃圾回收。</p>
<p>另外要重点注意的是，要意识到，在你页面的生命期中，全局变量不会被垃圾回收器所清理。只要你的页面保持打开状态，JavaScript运行期中的全局对象就会常驻在内存当中。</p>
<pre>var myGlobalNamespace = {};</pre>
<p>只有当你刷新页面，导航到不同的页面，关闭选项卡，或关闭你的浏览器，全局变量才会被清理。当函数作用域变量超出作用域范围，它就会被清理。当函数完全结束，并且再没有任何引用指向其中的变量，函数中的变量会被清理。</p>
<p><strong>经验法则</strong></p>
<p>为了给垃圾回收器尽早，尽量多地回收对象的机会，<strong>不要保留你不再需要的对像。</strong>这种情况大多会自动发生；这里有几件事是要谨记的：</p>
<p>● 就像之前所说的那样，一个比手动解除引用更好的选择是，在恰当的作用域中使用变量。也就是说，用可以自动从作用域中剔除的函数局部变量，去取代要手动清空的全局变量。这意味着你的代码会更加的整洁且要担忧的事情会更少。</p>
<p>● 确保要及时注销掉你不再需要的监听事件。特别是对那些必然要删除的DOM对象。</p>
<p>● 如果你正在使用本地数据缓存的话，确保要清除数据缓存或者使用老化机制（agingmechanism），以免保存了大量你不大可能复用的数据。</p>
<p> </p>
<div>
<p><span style="color:#ff0000"><strong>函数</strong></span></p>
<p> </p>
<p>接下来，让我们看看函数。正如我们所说的，垃圾回收是通过重新分配已经无法通过引用获得的内存块（对象）来工作的。为了更好地说明这一点，这里有一些例子。</p>
<pre>function foo() {
    var bar = new LargeObject();
    bar.someCall();
}</pre>
<p>当foo函数结束的时候，bar指向的对象就会自动地被垃圾回器所获取，因为已经没有任何引用指向该对象了。</p>
<p>对比以下代码：</p>
<pre>function foo() {
    var bar = new LargeObject();
    bar.someCall();
    return bar;
}

// somewhere else
var b = foo();</pre>
<p>现在我们有了一个指向该对象的引用，这个引用会在该次调用中保留下来，直到调用者将b赋值给其他东西（或者b超出了作用域范围）。</p>
<p><strong>闭包</strong></p>
<p>现在我们来看看一个返回内部函数的函数，那个内部函数可以访问到更外层的作用域，即使外部函数已经执行完毕。这基本上就是一个闭包——一种可以使用设置在特殊上下文中的变量的表现。例如：</p>
<pre>function sum (x) {
    function sumIt(y) {
        return x + y;
    };
    return sumIt;
}

// Usage
var sumA = sum(4);
var sumB = sumA(3);
console.log(sumB); // Returns 7</pre>
<p>在sum运行上下文中创造的函数对象不会被垃圾回收，因为它被一个全局变量所指向，仍然非常容易被访问到。它可以通过sumA（n）来运行。</p>
<p>让我们来看另外一个例子。这里，我们可以访问到largeStr吗？</p>
<pre>var a = function () {
    var largeStr = new Array(1000000).join(&#39;x&#39;);
    return function () {
        return largeStr;
    };
}();</pre>
<p>答案是肯定的，我们可以通过a（）来访问到它，所以它不会被回收。我们看看这个会怎么样：</p>
<pre>var a = function () {
    var smallStr = &#39;x&#39;;
    var largeStr = new Array(1000000).join(&#39;x&#39;);
    return function (n) {
        return smallStr;
    };
}();</pre>
<p>我们再也不能访问到它了，它会成为垃圾回收的候选对象。</p>
<p><strong>定时器</strong></p>
<p>最糟糕的状况之一是内存在循环中，或者在setTimeout()/setInterval()中泄露，但这相当的常见。</p>
<p>考虑下面的例子：</p>
<pre>var myObj = {
    callMeMaybe: function () {
        var myRef = this;
        var val = setTimeout(function () { 
            console.log(&#39;Time is running out!&#39;); 
            myRef.callMeMaybe();
        }, 1000);
    }
};</pre>
<p>如果我们这样运行：</p>
<pre>myObj.callMeMaybe();</pre>
<p>开始定时器，我们会看到每秒钟显示“Timeisrunningout!”然后如果我们运行下面代码：</p>
<pre>myObj = null;</pre>
<p>定时器仍然运作。myObj不会被垃圾回收，因为传入setTimout的闭包函数仍然需要它来保证正常运作。反过来，闭包函数保留了指向myObj的引用，因为它通过myRef来获取了该对象。如果我们把该闭包函数传入其他任何的函数，同样的事情一样会发生，函数中仍然会存在指向对象的引用。</p>
<p>同样值得牢牢记住的是，在setTimeout/setInterval的调用中的引用，例如函数引用，在运行完成之前是不会被垃圾回收的。</p>
<p> </p>
<p><span style="color:#ff0000"><strong>注意性能陷阱</strong></span></p>
<p> </p>
<p>很重要的一点是，除非你真正需要，否则没有必要优化你的代码，这个怎么强调都不为过。在大量的微基准测试中，你可以很轻易地发现，在V8引擎中N比M更加的优化，但是如果在真实的代码模型或者在真正的应用程序中进行测试，<strong>那些优化的实际影响可能比你期望的要小得多</strong>。</p>
<p><img src="http://media.smashingmagazine.com/wp-content/uploads/2012/10/speed-trap.jpg" alt="" width="500" height="350"></p>
<p>假设现在我们想要建立的一个模块：</p>
<p>● 通过数字ID取出本地存储的数据资源。</p>
<p>● 用获得的数据生成表格内容。</p>
<p>● 为每个表格单元添加事件处理，每当用户点击表格单元，切换表格单元的class。</p>
<div>
<p>即使这个问题解决起来很直观，但是有一些困难的因素。我们如何去存储这些数据，如何可以高效地生成一个表格并把它添加到DOM中去，如何优化地处理这个表格的事件处理？</p>
<p>第一个（也是幼稚的）采取的方案可能是将每块可获取的数据存放在一个对象中，然后把所有对象集合到一个数组当中。有的人可能会用jQuery去循环访问数据然后把生成表格内容，然后把它添加到DOM中。最后，有的人可能会用使用事件绑定添加点击我们需要的点击事件。</p>
<p><strong>注意：这不是你应该做的事情：</strong></p>
<pre>var moduleA = function () {

    return {

        data: dataArrayObject,

        init: function () {
            this.addTable();
            this.addEvents();
        },

        addTable: function () {

            for (var i = 0; i &lt; rows; i++) {
                $tr = $(&#39;&lt;tr&gt;&lt;/tr&gt;&#39;);
                for (var j = 0; j &lt; this.data.length; j++) {
                    $tr.append(&#39;&lt;td&gt;&#39; + this.data[j][&#39;id&#39;] + &#39;&lt;/td&gt;&#39;);
                }
                $tr.appendTo($tbody);
            }

        },
        addEvents: function () {
            $(&#39;table td&#39;).on(&#39;click&#39;, function () {
                $(this).toggleClass(&#39;active&#39;);
            });
        }

    };
}();</pre>
<p>代码简单，但它完成了我们需要的工作。</p>
<p>在这种情况下，我们唯一要迭代的只是ID，在一个标准的数组当中，数字属性可以更简单地表示出来。有趣的是，直接用DocumentFragment和原生的DOM方法生成表格内容，比你用jQuery（上面的jQuery用法）更加的优化。当然，使用事件委托通常比为每个td都进行事件绑定会有更好的性能。</p>
<p>注意jQuery内部确实使用DocumentFragment进行了优化，但在我们的例子中，代码中在循环中调用append（），每一次调用都要进行额外的操作，所以在这个例子中，它达到优化效果可能并不大。希望这应该不会是一个痛处，但是一定要用基准测试来确保自己的代码没有问题。</p>
<p>在我们的例子当中，添加这些以上的优化会得到一些不错（预期）的性能收益。相对于简单的绑定，事件委托提供了相当好的改进，且<a href="http://jsperf.com/first-pass">选择用documentFragment</a>会是一个真正的性能助推器。</p>
<pre>var moduleD = function () {

    return {

        data: dataArray,

        init: function () {
            this.addTable();
            this.addEvents();
        },
        addTable: function () {
            var td, tr;
            var frag = document.createDocumentFragment();
            var frag2 = document.createDocumentFragment();

            for (var i = 0; i &lt; rows; i++) {
                tr = document.createElement(&#39;tr&#39;);
                for (var j = 0; j &lt; this.data.length; j++) {
                    td = document.createElement(&#39;td&#39;);
                    td.appendChild(document.createTextNode(this.data[j]));

                    frag2.appendChild(td);
                }
                tr.appendChild(frag2);
                frag.appendChild(tr);
            }
            tbody.appendChild(frag);
        },
        addEvents: function () {
            $(&#39;table&#39;).on(&#39;click&#39;, &#39;td&#39;, function () {
                $(this).toggleClass(&#39;active&#39;);
            });
        }

    };

}();</pre>
<p>我们可能会寻找其他的方案来提高性能。你可能在某些文章中了解到用原型模式比用模块模式更加优化（我们不久前已经证明了事实并非如此），或者了解到JavaScript模板框架是经过高度的优化的。有时它们的确是这样，但是使用它们只是为了代码拥有更强的可读性。同时，还有预编译！让我们测试一下，实际上这有多少是能带来真正优化的。</p>
<pre>moduleG = function () {};

moduleG.prototype.data = dataArray;
moduleG.prototype.init = function () {
    this.addTable();
    this.addEvents();
};
moduleG.prototype.addTable = function () {
    var template = _.template($(&#39;#template&#39;).text());
    var html = template({&#39;data&#39; : this.data});
    $tbody.append(html);
};
moduleG.prototype.addEvents = function () {
   $(&#39;table&#39;).on(&#39;click&#39;, &#39;td&#39;, function () {
       $(this).toggleClass(&#39;active&#39;);
   });
};

var modG = new moduleG();</pre>
<p>正如结果所示，在这种情况下所带来的性能效益是微不足道的。<a href="http://jsperf.com/second-pass">选择模板和原型</a>不会真正提供得比我们原来拥有的东西更多的东西。据说，性能并不是现代开发者所真正使用它们的原因——而是它给你的代码库所带来的可读性，继承模型，以及可维护性。</p>
<p>更复杂的问题包括如何<a href="http://jsperf.com/canvas-drawimage-vs-webgl-drawarrays/6">高效地在canvas上绘制图像</a>，和如何使用或不使用<a href="http://jsperf.com/typed-arrays-for-pixel-manipulation">类型数组</a>去<a href="http://jsperf.com/canvas-pixel-manipulation/30">操作像素数据</a>。</p>
<p>在你的代码使用它们之前，要给你的微基准测试一个结束前的检验。你们其中有些人可能会回想起JavaScript模板语言shoot-off和它的之后扩展版的shoot-off。如果你想确保测试不会被现实的应用程序的中你不想见到的约束所影响——请在真实的代码中和优化一起测试。</p>
<p> </p>
<p><span style="color:#ff0000"><strong>V8优化技巧</strong></span></p>
<p> </p>
<p>同时详细的陈列每一个V8的每一种优化显然超出了本文的讨论范围，其中有许多特定的优化技巧都值得注意。记住以下的一些建议你就可以减少你写出低性能的代码的机会。</p>
<p>● 特定的模式会导致V8放弃优化。例如使用try-catch，就会导致这种情况的发生。如果想要了解跟多关于什么函数可以被优化，什么函数不可以，你可以使用V8引擎中附带的D8shell实用程序中的–trace-optfile.js。</p>
<p>● 如果你关心运行速度，那么就要尽量保持你的函数的功能的单一性，也就是说，确保变量（包括属性，数组，和函数参数）永远只是相同隐藏类的包含对象。例如，永远不要干这种事：</p>
<p>● 不要从未初始化的或已经被删除的元素上加载内容。这样做可能对你的程序运行结果不会造成影响。但是它会使得程序运行得更慢。</p>
<p>● 不要写过于庞大的函数，因为他们更难被优化。</p>
<p>如果想知道更多的优化技巧，可以观看DanielClifford的GoogleI/O大会上的演讲<a href="http://www.youtube.com/watchv=UJPdhx5zTaw">BreakingtheJavaScriptSpeedLimitwithV8</a>，它同时也涵盖了上面我们所说的优化技巧。<a href="http://floitsch.blogspot.co.uk/2012/03/optimizing-for-v8-introduction.html">OptimizingForV8—ASeries</a>也同样值得一读。</p>
<p><strong>对象和数组：我应该用哪一个？</strong></p>
<p><strong></strong>● 如果你想存储一组数字，或者一系列的同类型对象的话，那么就使用数组。</p>
<p>● 如果你想要的是一个语义上的有不同属性（不同类型）的对象，那么就使用包含属性的对象。这样从内存上来说会相当的高效，而且运行也相当的迅速。</p>
<p>● 用整数做索引的元素，不管它们是存储在数组还是对象中，都会<strong>比那些需通过迭代来获取的对象属性要快得多</strong>。</p>
<p>● 对象中的属性相当复杂：它们可以被setter所创建，拥有不同的可枚举性和可写性。数组中的元素不能有这样的定制性——它们只有存在或者不存在的状态。在一个引擎的层面，从组织表示结构的内存角度上来说，这允许有更多的优化。当一个数组中包含有数字的时候，这样会相当有好处。例如，当你需要一个向量，不要用一个包含有x，y，z属性的对象，用一个数组来存储就可以了。</p>
<div>
<p><strong>使用对象的技巧</strong></p>
<p><strong></strong>● 用构造函数构造对象。这样可以保证所有的由该构造函数构造的对象都具有相同的隐藏类，而且可以有助于避免修改这些隐藏类。有个附加的好处就是，它会比Object.create()稍快。</p>
<p>● 在你的程序中，对象的类型数目以及它们的复杂程度是没有限制的（不难理解的是：长原型链会可能会导致有害的结果，那些只有少数属性的对象的特殊表现就是，它们会比那些更大的对象运行得要快一点）。对于“热点”对象，尽量保持原型链的简短，以及属性数目较少。</p>
<p><strong>对象的复制</strong></p>
<p>对于应用的开发者来说，对象的复制是一个常见的问题。虽然基础测试可能表明V8在不同的情况下对这类问题都处理得很好，但是，当你要复制任何东西的时候，仍然需要小心。复制大的东西通常是缓慢的——所以不要这样做。JavaScript中的for…in循环处理这种事情特别的糟糕，因为它拥有可怕的规范，这使得它在任何引擎中处理任何对象，都不会获得良好的速度。</p>
<p>当你一定要在一段性能要求苛刻的代码中复制对象（并且你无法摆脱这种状况），那么就用数组或者一个自定义的“拷贝构造函数”，帮你逐一明确地复制对象的每一个属性。这可能是实现的最快的方式：</p>
<pre>function clone(original) {
  this.foo = original.foo;
  this.bar = original.bar;
}
var copy = new clone(original);</pre>
<p><strong>模块模式中的缓存函数</strong></p>
<p>在模块模式中缓存你的函数可以带来性能的提高。看看下面的例子，因为它总是会强制进行成员函数的复制，你习惯看到的变化可能会更慢。</p>
<p style="text-align:center"><img src="http://media.smashingmagazine.com/wp-content/uploads/2012/11/Screen-Shot-2012-11-06-at-10.42.10.png" alt="" width="555" height="404"></p>
<p>这里有个关于<a href="http://jsperf.com/prototypal-performance/12">原型对比模块模式的性能测试</a>。</p>
<pre>  // Prototypal pattern
  Klass1 = function () {}
  Klass1.prototype.foo = function () {
      log(&#39;foo&#39;);
  }
  Klass1.prototype.bar = function () {
      log(&#39;bar&#39;);
  }

  // Module pattern
  Klass2 = function () {
      var foo = function () {
          log(&#39;foo&#39;);
      },
      bar = function () {
          log(&#39;bar&#39;);
      };

      return {
          foo: foo,
          bar: bar
      }
  }

  // Module pattern with cached functions
  var FooFunction = function () {
      log(&#39;foo&#39;);
  };
  var BarFunction = function () {
      log(&#39;bar&#39;);
  };

  Klass3 = function () {
      return {
          foo: FooFunction,
          bar: BarFunction
      }
  }

  // Iteration tests

  // Prototypal
  var i = 1000,
      objs = [];
  while (i--) {
      var o = new Klass1()
      objs.push(new Klass1());
      o.bar;
      o.foo;
  }

  // Module pattern
  var i = 1000,
      objs = [];
  while (i--) {
      var o = Klass2()
      objs.push(Klass2());
      o.bar;
      o.foo;
  }

  // Module pattern with cached functions
  var i = 1000,
      objs = [];
  while (i--) {
      var o = Klass3()
      objs.push(Klass3());
      o.bar;
      o.foo;
  }
// See the test for full details</pre>
<p><strong>使用数组的技巧</strong></p>
<p>接下来我们来关谈论一下关于数组的一些技巧。通常情况下，<strong>不要删除数组的元素</strong>。否则会使得数组内部表现形式发生转变，从而变得更慢。当键变得稀疏的时候，V8会最终把元素转换成更慢的字典模式。</p>
<p><strong>数组字面量</strong></p>
<p>用数组字面量创建数组是有用的，因为它们会给VM一些暗示，让它知道数组的大小和类型。字面量通常对规模不大的数组是好处的。</p>
<pre>// Here V8 can see that you want a 4-element array containing numbers:
var a = [1, 2, 3, 4];

// Don&#39;t do this:
a = []; // Here V8 knows nothing about the array
for(var i = 1; i &lt;= 4; i++) {
     a.push(i);
}</pre>
<p><strong>存储单一类型VS混合类型</strong></p>
<p>在同一个数组中存储不同类型的数据（例如，数字，字符串，undefined，或者true/false），从来不是一个好主意（也就是像这样，vararr=[1,“1”,undefined,“true”]）。</p>
<p><a href="http://jsperf.com/type-inference-performance/2">类型推断的性能测试</a></p>
<p>我们可以从结果中看出，ints数组是最快的。</p>
<p><strong>稀疏数组VS满数组</strong></p>
<p>当你使用稀疏数组的时候，要意识到，在它们中访问元素的效率要比在满数组中要慢得多。这是因为如果数组中只有少数元素，V8不会为元素从新分配连续的内存空间。它们会被一个字典所管理，这样可以节约内存空间，但是会消耗访问时间。</p>
<p><a href="http://jsperf.com/sparse-arrays-vs-full-arrays">稀疏数组对比满数组的测试</a></p>
<p>满数组的加法和无0的数组的加法实际上是最快的。而一个满数组中是否含有0对它的运行效率没有影响。</p>
<p><strong>塞满的数组VS多孔的数组</strong></p>
<p>避免数组中的“孔”（可能通过删除元素或者用a[x]=foo，而x&gt;a.length来创建的孔）。在一个“满”的数组中，即使是仅仅是一个元素被删除掉，也会变得慢得多。</p>
<p><strong><a href="http://jsperf.com/packed-vs-holey-arrays">塞满的数组对比多孔数组的测试</a></strong></p>
<p><strong>预分配数组VS运行时分配</strong></p>
<p>不要根据数组最大的大小预分配一个大数组（例如大于64K的元素），应该让你的数组在运行的时候自我分配。在我们进行对这个技巧的性能测试之前，请记住，这只适合部分JavaScript浏览器。</p>
<p><img src="http://media.smashingmagazine.com/wp-content/uploads/2012/10/graph2.jpg" alt="" width="600" height="387"></p>
<p>在Nitro引擎（Safari）使用预分配的数组会更有好处。但是，在其他的引擎中（V8，SpiderMonkey），非预分配会更高效。</p>
<p><a href="http://jsperf.com/pre-allocated-arrays">预分配数组的测试</a></p>
<pre>// Empty array
var arr = [];
for (var i = 0; i &lt; 1000000; i++) {
    arr[i] = i;
}

// Pre-allocated array
var arr = new Array(1000000);
for (var i = 0; i &lt; 1000000; i++) {
    arr[i] = i;
}</pre>
<p> </p>
<p><strong><span style="color:#ff0000">优化你的应用程序</span></strong></p>
<p> </p>
<p>在web应用程序的世界里面，速度是一切。没有用户希望一个电子表格程序需要几秒钟的时间去计算一列数据的和，或者用一分钟的时间去得到表格的汇总信息。这就为什么你需要压榨你代码中的每一点的性能的原因，这有时可能会苛刻。</p>
<p><img src="http://media.smashingmagazine.com/wp-content/uploads/2012/10/improving-apps.jpg" alt="" width="500" height="375"></p>
<p>虽然理解和提高你的应用程序性能是有用的，但是它依然很困难。我们给出下面几步建议去解决你程序性能的瓶颈：</p>
<p>● 测量它：找出你应用程序中慢的地方（~45%）</p>
<p>● 理解它：发现实际的问题是什么（~45%）</p>
<p>● 解决它！（~10%）</p>
<div>
<p>一些推荐的工具和技术可以协助你进行这个过程。</p>
<p><strong>基准测试</strong></p>
<p>有许多方法可以测试JavaScript代码片断的性能——一般的设想是：基准测试就是简单地对两个时间戳进行比较。这样的模式已经被jsPerf团队所指出，并且应用在在SunSpider和Kraken的基准测试套件当中。</p>
<pre>var totalTime,
    start = new Date,
    iterations = 1000;
while (iterations--) {
  // Code snippet goes here
}
// totalTime → the number of milliseconds taken 
// to execute the code snippet 1000 times
totalTime = new Date - start;</pre>
<p>这里，测试代码被放在一个循环当中，并且运行一个设定的次数（例如六次）。接下来，用结束的时间去减开始开始的时间。这样，就可以测试出循环中操作所消耗的时间。</p>
<p>然而，这里对基准测试的工作过度简化了，尤其是如果你想在多个浏览器和环境中进行基准测试。垃圾回收本身会影响到你的测试结果。即使你使用了像window.preformance这样的解决方案，你仍然需要对那些陷阱做出相应的考虑。</p>
<p>不管你是简单地对你部分的代码进行基准测试，还是编写一个测试套件，或写一个基准测试的类库。关于JavaScript基准测试实际要做的事情比你想象的要多。如果你想获得关于基准测试更多的细节指导，我强烈建议你阅读MathiasBynens和John-DavidDalton编的<a href="http://mathiasbynens.be/notes/javascript-benchmarking">JavaScriptBenchmarking</a></p>
<p><strong>性能分析</strong></p>
<p>Chrome的开发者工具提供了对JavaScript性能分析良好的支持。你可以使用这些特性去检测那些函数消耗了你大部分的性能，然后你就可以对它们进行优化。这是很重要的一点，因为即使你的代码库中一点很小的改变都可以影响到你的整体性能。</p>
<p><img src="http://media.smashingmagazine.com/wp-content/uploads/2012/10/profiling.jpg" alt="" width="600" height="378"></p>
<p>性能分析以获取你代码当前性能的基线开始，你可以从时间轴上发现它。它会告诉你你的代码花费了多长的运行时间。Profiles选项卡提供了一个更好的视角去观察我们的应用程序内部到底放生了什么事情。JavaScriptCPUprofile展示了我们的代码到底占用了多少CPU的时间，CSSselectorprofile告诉我们选择器查找元素所花费的时间，Heapsnapsshots让我们知道我们的对象占用了多少内存。</p>
<p>使用这些工具，我们可以抽离，调整和重新分析来度量我们的对特定函数或操作的改变是否真正起到了性能优化的效果。</p>
<p><img src="http://media.smashingmagazine.com/wp-content/uploads/2012/10/profiling2.jpg" alt="" width="600" height="351"></p>
<p>想得到关于性能分析更好的介绍，可以阅读ZackGrossbart的<a href="http://coding.smashingmagazine.com/2012/06/12/javascript-profiling-chrome-developer-tools/">JavaScriptProfilingWithTheChromeDeveloperTools</a>,</p>
<p>提示：在理想情况下，如果你想保证的你性能分析没有受到你所安装的扩展程序或者应用所影响。以可以用usingthe–user-data-dir&lt;empty_directory&gt;的flag来运行Chrome。在大多数情况下，这样的性能测试已经足够了，但是有些时候你会需要的更多。V8的flags就可以为你提供帮助。</p>
<p><strong>避免内存泄露——用三快照（THREESNAPSHOT）技术发现问题</strong></p>
<p>在Google内部，Chrome的开发者工具被例如Gmail这样的团队大量使用，可以帮助我们发现并解决内存泄露问题。</p>
<p><img src="http://media.smashingmagazine.com/wp-content/uploads/2012/10/devtools.jpg" alt="" width="600" height="339"></p>
<p>我们团队关注的一些内存数据，包括私有内存使用，JavaScript堆大小，DOM节点数目，内存清理，事件监听数目和垃圾回收器的运行情况。如果你对事件驱动架构比较熟悉，你或许比较有兴趣了解最常见问题之一就是，我们过去常常用listen()，unlisten()（闭包），和缺失的dispose()去处理事件监听对象。</p>
<p>幸运的是，DevTools可以帮我们解决其中的一些问题，强烈建议阅读LoreenaLee的很棒的一个展示，它记录了如何使用“<a href="https://docs.google.com/presentation/d/1wUVmf78gG-ra5aOxvTfYdiLkdGaR9OhXRnOlIcEmu2s/pubstart=false&amp;loop=false&amp;delayms=3000">三快照技术</a>”找出DevTools中的内存泄露。</p>
<p>这项技术的要点是，记录你应用程序中的一些行为，强制进行垃圾回收，检测DOM的节点数目是否会返回到你所期望的基线，然后分析三个堆上的快照来决定内存是否泄露。</p>
<p><strong>单页面程序的内存管理</strong></p>
</div>
<p>内存管理对于现代的单页面应用程序（例如AngularJS，Backbone，Ember）相当的重要，因为它们几乎从来不会刷新页面。这就意味着内存泄露会相当的明显和迅速。这在移动单页面应用程序中存在相当大的陷阱，因为内存的限制，且存在大量的例如email客户端这样的长期运行的程序或者社交网络应用程序。<strong>能力越大，责任越大。</strong>（推荐阅读：《<a href="http://blog.jobbole.com/297/">每位开发人员都应铭记的10句编程谚语</a>》）</p>
<p>有许多方法可以避免这中情况。在Backbone中，确保你总是用dispose()（当前可以在Backbone(edge)中使用）来处理了旧的视图和引用。这个函数是最近新增的，可以移除任何添加到视图的“event”对象的所有处理函数，以及视图作为第三个参数（回调上下文）传入的任何集合或者模型的事件监听器，dispose()同样可以被视图的remove()所调用，当元素从页面中被移除时管理主要的基本内存清理工作。其他的类库，如Ember，当它检测到元素已经从视图中被移除的时候，它会移除相应的观察者，以避免内存泄露。</p>
<p>DerickBailey给了我们一些明智的建议：</p>
<blockquote><p>“除了了解事件处理在引用层面是如何工作的，在JavaScript按照标准规则来管理你的内存，一切就会没问题。如果你想向一个塞满用户数据的Backbone集合中加载数据，如果你想那些集合稍后被清理并不占据内存，你必须移除集合所有的引用以及其中独立的对象。一旦你移除所有的引用，清理工作就可以进行。这就是标准的JavaScript垃圾回收规则”</p></blockquote>
<p>在这篇文章中，Derick涵盖了在使用Backbone.js中许多常见的内存陷阱以及解决方案。</p>
<p>这里也有一篇由FelixGeisendrfer编写关于<a href="https://github.com/felixge/node-memory-leak-tutorial">如何调试Node中的内存泄露</a>的指导，很值得一读，尤其它形成了你的更广泛SPA堆里面的一部分。</p>
<p><strong>最小化重排</strong></p>
<p>当浏览器为了重绘（re-rendering）必须重新计算元素的位置和几何形状的时候，我们把这个过程称作<a href="https://www.youtube.com/watchfeature=player_embedded&amp;v=ZHxbs5WEQzE">重排</a>（reflow）。在浏览器中，重排是一个用户阻塞的操作，所以了解如何去提高重排的性能会很有帮助。</p>
<p><img src="http://media.smashingmagazine.com/wp-content/uploads/2012/10/reflow.jpg" alt="" width="600" height="372"></p>
<p>你应该用批量处理的方法去触发重排或重绘，并且要有节制地使用这些方法。尽量不进行DOM操作也很重要。用轻量级的<a href="http://blog.jobbole.com/#ID-B63ED1A3">DocumentFragment</a>（文本片段）对象来达到这样的效果。你可以把它当做是获取部分DOM树的方法，或者是创建新的文本“片段”的方法。对比不断地向DOM树添加节点，我们使用文本片段构建起我们需要的内容并且只进行一次DOM插入操作。这样可以避免过度的重排。</p>
<p>例如，我们写了一个向一个元素中添加20个div函数。简单地添加每个div到元素中会触发20次重排。</p>
<pre>function addDivs(element) {
  var div;
  for (var i = 0; i &lt; 20; i ++) {
    div = document.createElement(&#39;div&#39;);
    div.innerHTML = &#39;Heya!&#39;;
    element.appendChild(div);
  }
}</pre>
<p>为了解决这个问题，我们使用DocumentFragment来代替逐个添加div。使用我们像appendChild这样的方法把DocumentFragment添加到元素中的时候，文本片段中所有的子节点都会被添加到元素中，这样只会触发仅仅一次重排。</p>
<pre>function addDivs(element) {
  var div; 
  // Creates a new empty DocumentFragment.
  var fragment = document.createDocumentFragment();
  for (var i = 0; i &lt; 20; i ++) {
    div = document.createElement(&#39;a&#39;);
    div.innerHTML = &#39;Heya!&#39;;
    fragment.appendChild(div);
  }
  element.appendChild(fragment);
}</pre>
<p>你可以在<a href="https://developers.google.com/speed/articles/javascript-dom">MaketheWebFaster,</a><a href="http://blog.tojicode.com/2012/03/javascript-memory-optimization-and.html">JavaScriptMemoryOptimization</a>和<a href="http://gent.ilcore.com/2011/08/finding-memory-leaks.html">FindingMemoryLeaks.</a>阅读到更多关于这方面的主题。</p>
<p><strong>JavaScript内存泄露检测器</strong></p>
<p>为了帮助发现JavaScript内存泄露，我两个谷歌的同事（MarjaHlttandJochenEisinger）开发了一个和Chrome开发者工具共同使用的工具（具体来说，就是一个远程检测协议），可以检索堆快照和探测出哪些对象导致了内存泄露。</p>
<p><img src="http://media.smashingmagazine.com/wp-content/uploads/2012/10/leak.jpg" alt="" width="600" height="325"></p>
<p>有一篇文章完整地介绍了<a href="http://google-opensource.blogspot.de/2012/08/leak-finder-new-tool-for-javascript.html">怎么使用这个工具</a>，我鼓励你去看看或者去看<a href="http://code.google.com/p/leak-finder-for-javascript/">LeakFinder的项目主页</a>。</p>
<p>更多信息：也许你想知道为什么这个工具没有集成到我们的开发者工具当中。有两个原因，第一，它最初是为了帮助我们在Closure库中为我们检测特定的内存场景。第二，它作为一个扩展工具来使用将会更有意义（甚至可以作为一个扩展程序，如果我们可以适当地获得堆性能分析扩展程序的API的话）。</p>
<p><strong>V8标签：调试性能&amp;垃圾回收</strong></p>
<p>Chrome支持通过js-flags直接传入一些标签到V8引擎中来获取关于引擎优化过程中更多细节。例如，这可以追溯V8的优化：</p>
<pre>&quot;/Applications/Google Chrome/Google Chrome&quot; --js-flags=&quot;--trace-opt --trace-deopt&quot;</pre>
<p>Windows用户可以运行chrome.exe–js-flags=”–trace-opt–trace-deopt”</p>
<p>在开发你的应用程序的过程中，这些V8标签会有所帮助：</p>
<p>● trace-opt–记录已经被优化的函数，已经显示优化器无法识别并且跳过的代码。</p>
<p>● trace-deopt–记录运行过程中需要逆优化的代码列表。</p>
<p>● trace-gc–记录每次进行垃圾回收的跟踪线。<br>
V8的tick-processing脚本用*标注已经进行过优化的函数，用~标记没有被优化的函数。</p>
<p>如果你想了解更多关于V8引擎标签以及V8内部工作原理的话，我强烈建议你浏览一篇<a href="http://mrale.ph/blog/2011/12/18/v8-optimization-checklist.html">关于V8引擎内部工作原理的文章</a>，它总结了一些目前为止最好的资源。</p>
<p><strong>高精度时间以及导航计时API</strong></p>
<p>高精度时间（HRT）是一个不受系统时钟以及用户调整影响的亚毫秒级的JavaScript接口。它提供了比newDate和Date.now()更为精准的时间测量。这样可以帮助我们写出性能良好的基础测试。</p>
<p><img src="http://media.smashingmagazine.com/wp-content/uploads/2012/10/perfnow.jpg" alt="" width="578" height="165"></p>
<p>HRT目前在Chrome（稳定版）中可以通过window.performance.webkitNow()来获得，但是前缀在ChromCanary中被省略了，可以通过window.performance.now()来获取。PaulIrish在HTML5Rocks中写了一篇<a href="http://updates.html5rocks.com/2012/08/When-milliseconds-are-not-enough-performance-now">关于HRT</a>的文章。</p>
<p>所以，我们现在知道了目前的时间，但是如果我们需要API给出更精确的时间去测量web中的性能呢？</p>
<p>现在，我们有个<a href="http://dvcs.w3.org/hg/webperf/raw-file/tip/specs/NavigationTiming/Overview.html">NavigationTimingAPI</a>的东西可以使用。这个API提供了一个简单的方法去获取当页面加载完毕并展示给用户时的精确和详细的时间测量。时间信息可以通过window.performance.timing暴露出来，你可以在控制台中简单地使用它：</p>
<p><img src="http://media.smashingmagazine.com/wp-content/uploads/2012/10/performance.jpg" alt="" width="600" height="350"></p>
<p>观察上面的数据，我们可以抽离出一些相当有用的信息。例如，网络延迟为responseEnd-fetchStart，从服务器加载页面时间为loadEventEnd-responseEnd，以及导航和页面加载之间的的耗时为loadEventEnd-navigationStart。</p>
<p>正如你所看到的，一个performance.memory属性同样可以提供例如总堆大小的JavaScript内存使用情况。</p>
<p>关于导航计时API更多的细节，你可以阅读SamDutton的一篇相当好的文章<a href="http://www.html5rocks.com/en/tutorials/webperformance/basics/">MeasuringPageLoadSpeedWithNavigationTiming.</a></p>
<p><strong>ABBOUT:MEMORY和ABOUT:TRACING</strong></p>
<p>Chrome中的about:tracing提供了有效的视图，帮助我们观察浏览器的性能，记录Chrome如每个线程，选项卡，和进程的所有活动。</p>
<p><img src="http://media.smashingmagazine.com/wp-content/uploads/2012/10/tracing.jpg" alt="" width="600" height="330"></p>
<p>这个工具真正的有用的地方是可以允许你获取Chrome的浏览器引擎盖下的分析数据，然后你可以恰当地调整你的JavaScript程序，或者优化你资源加载过程。</p>
<p>LilliThompson有一篇<a href="http://www.html5rocks.com/en/tutorials/games/abouttracing/">写给游戏开发者</a>的文章，关于如何使用about:tracing去分析WebGL游戏。这篇文章对于普通的JavaScripters依然适用。</p>
<p>Chrome中使用about:memory也很有帮助，因为它显示了每个选项卡精确的内存使用，这样可以有效的跟踪潜在的内存泄露。</p>
<p> </p>
<p><span style="color:#ff0000"><strong>总结</strong></span></p>
<p> </p>
<p>正如我们所见，<strong>在JavaScript的引擎世界里面，有许多的隐藏的性能陷阱</strong>，事实上并没有性能提高的银弹。只有当你在（现实世界的）测试环境中结合一系列的优化，你才会意识到最大的性能获益。但是即使这样，理解引擎内部原理以及优化你的代码可以给你提高你应用程序性能的灵感。</p>
<p><strong>测量它，理解它，解决它</strong>。不断重复这个过程。</p>
<p><img src="http://media.smashingmagazine.com/wp-content/uploads/2012/11/barometer.jpg" alt="" width="496" height="500"></p>
<p>记得关注优化，但要避免一些小的优化从而获得更大的便利。例如，一些开发者在循环中为便利而是用.forEach和Object.keys来代替for和forin，即使它们更慢，但是在可以接受范围内，它们更为方便。你需要一个清醒的头脑去分析你的应用程序中哪些是需要优化的，哪些是不需要的。</p>
<p>同样，意识到即使JavaScript引擎不断变得更快，一个真证的瓶颈其实是DOM。重排和重绘的最小化是相当重要的，所以记住如果不是非不得已，不要触碰DOM。并且关注网络情况。HTTP请求是珍贵的，尤其是在移动终端，你应该使用HTTP缓存来减少资源的消耗。</p>
<div>
<p>记住所有这一切可以确保你已经获得这篇文章的大部分信息。我希望这会对你有所帮助！</p>
<p> </p>
<p><strong>英文原文：<a href="http://coding.smashingmagazine.com/2012/11/05/writing-fast-memory-efficient-javascript/">Writing Fast,Memory-Efficient JavaScript</a>，编译：<a href="http://www.jobbole.com/">伯乐</a>在线——<a href="http://blog.jobbole.com/author/%E6%88%B4%E5%98%89%E5%8D%8E/">戴嘉华</a></strong></p>
<p><strong>译文链接：<a href="http://blog.jobbole.com/30550/">http://blog.jobbole.com/30550/</a></strong></p>
<p><strong><span style="color:#ff0000"> 【如需转载，请在正文中标注并保留原文链接、译文链接和译者等信息，谢谢合作！】</span></strong></p>
<p> </p>
</div>
</div>
</div>
</div>
</div>
<hr><small>Copyright © 2008<br> This feed is for personal, non-commercial use only. <br> The use of this feed on other websites breaches copyright. If this content is not in your news reader, it makes the page you are viewing an infringement of the copyright. (Digital Fingerprint:<br> )</small>
<h2>相关文章</h2><ul><li><a href="http://blog.jobbole.com/30468/">测试：你自认为理解了JavaScript？</a></li><li><a href="http://blog.jobbole.com/31178/">用Chrome开发者工具做JavaScript性能分析</a></li><li><a href="http://blog.jobbole.com/31213/">headtrackr：一个头部/脸部追踪的JavaScript库</a></li><li><a href="http://blog.jobbole.com/31166/">查询json数据结构的8种方式</a></li><li><a href="http://blog.jobbole.com/30854/">JavaScript开发规范要求</a></li><li><a href="http://blog.jobbole.com/31104/">简讯：Amazon拥抱Node.js</a></li><li><a href="http://blog.jobbole.com/31274/">拥抱原型面向对象编程</a></li><li><a href="http://blog.jobbole.com/30046/">Javascript模块化编程（三）：require.js的用法</a></li><li><a href="http://blog.jobbole.com/29454/">常用的Javascript设计模式</a></li><li><a href="http://blog.jobbole.com/18191/">如何编写可维护的面向对象JavaScript代码</a></li></ul>