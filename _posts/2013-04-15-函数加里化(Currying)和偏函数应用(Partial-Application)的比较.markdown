---
layout: post
title:  "函数加里化(Currying)和偏函数应用(Partial Application)的比较"
date:   2013-04-15 22:35:32
author: Aqee
categories: program
---

## 函数加里化(Currying)和偏函数应用(Partial Application)的比较
### by Aqee
### at 2013-04-15 22:35:32
### original <http://www.aqee.net/currying-partial-application/>

<img src="http://ittopic.gotoip1.com/qee/wordpress/wp-content/uploads/2013/04/f001-620x350.jpg" alt="函数式编程"><br><p><em>【名词解释】Currying：因为是美国数理逻辑学家哈斯凯尔·加里(Haskell Curry)发明了这种函数使用技巧，所以这样用法就以他的名字命名为Currying，中文翻译为“加里化”。</em></p><p>我感觉很多人都对函数加里化(Currying)和偏函数应用(Partial Application)之间的区别搞不清楚，尤其是在相似的上下文环境中它们同时出现的时候。</p><p>偏函数解决这样的问题：如果我们有函数是多个参数的，我们希望能固定其中某几个参数的值。</p><p>几乎所有编程语言中都有非常明显的偏函数应用。在C语言中：</p><div><pre><code><span>int</span> <span>foo</span><span>(</span><span>int</span> <span>a</span><span>,</span> <span>int</span> <span>b</span><span>,</span> <span>int</span> <span>c</span><span>)</span> <span>{</span>

  <span>return</span> <span>a</span> <span>+</span> <span>b</span> <span>+</span> <span>c</span><span>;</span>
<span>}</span>

<span>int</span> <span>foo23</span><span>(</span><span>int</span> <span>a</span><span>,</span> <span>int</span> <span>c</span><span>)</span> <span>{</span>
  <span>return</span> <span>foo</span><span>(</span><span>a</span><span>,</span> <span>23</span><span>,</span> <span>c</span><span>);</span>

<span>}</span>
</code></pre></div><p><code>foo23</code>函数实际上就是一个<code>foo</code>函数的偏函数应用，参数<code>b</code>的值被固定为23。</p><p>当然，像这样明显的偏函数并没有太大的用处；我们通常会希望编程语言能提供我们某些偏函数特征。</p><p>例如，在Python语言中，我们可以这样做：</p><div><pre><code><span>from</span> <span>functools</span> <span>import</span> <span>partial</span>

<span>def</span> <span>foo</span><span>(</span><span>a</span><span>,</span><span>b</span><span>,</span><span>c</span><span>):</span>

  <span>return</span> <span>a</span> <span>+</span> <span>b</span> <span>+</span> <span>c</span>

<span>foo23</span> <span>=</span> <span>partial</span><span>(</span><span>foo</span><span>,</span> <span>b</span><span>=</span><span>23</span><span>)</span>

<span>foo23</span><span>(</span><span>a</span> <span>=</span> <span>1</span><span>,</span> <span>c</span> <span>=</span> <span>3</span><span>)</span>  <span># =&gt; 27</span>

</code></pre></div><p>函数加里化(Currying)明显解决的是一个完全不同的问题：如果我们有几个<strong>单参数</strong>函数，并且这是一种支持一等函数(first-class)的语言，如何去实现一个多参数函数？函数加里化是一种<strong>实现多参数函数的方法。</strong></p><p>下面是一个单参数的Javascript函数:</p><div><pre><code><span>var</span> <span>foo</span> <span>=</span> <span>function</span><span>(</span><span>a</span><span>)</span> <span>{</span>

  <span>return</span> <span>a</span> <span>*</span> <span>a</span><span>;</span>
<span>}</span>
</code></pre></div><p>如果我们受限只能写单参数函数，可以像下面这样模拟出一个多参数函数：</p><div><pre><code><span>var</span> <span>foo</span> <span>=</span> <span>function</span><span>(</span><span>a</span><span>)</span> <span>{</span>

  <span>return</span> <span>function</span><span>(</span><span>b</span><span>)</span> <span>{</span>
    <span>return</span> <span>a</span> <span>*</span> <span>a</span> <span>+</span> <span>b</span> <span>*</span> <span>b</span><span>;</span>

  <span>}</span>
<span>}</span>
</code></pre></div><p>通过这样调用它：<code>(foo(3))(4)</code>，或直接 <code>foo(3)(4)</code>。</p><p>注意，函数加里化提供了一种非常自然的方式来实现某些偏函数应用。如果你希望函数<code>foo</code>的第一个参数值被固定成5，你需要做的就是<code>var foo5 = foo(5)</code>。这就OK了。函数<code>foo5</code>就是<code>foo</code>函数的偏函数。注意，尽管如此，我们没有很简单的方法对<code>foo</code>函数的第二个参数偏函数化(除非先偏函数化第一个参数)。</p><p>当然，Javascript是支持多参数函数的：</p><div><pre><code><span>var</span> <span>bar</span> <span>=</span> <span>function</span><span>(</span><span>a</span><span>,</span> <span>b</span><span>)</span> <span>{</span>

  <span>return</span> <span>a</span> <span>*</span> <span>a</span> <span>+</span> <span>b</span> <span>*</span> <span>b</span><span>;</span>

<span>}</span>
</code></pre></div><p>我们定义的<code>bar</code>函数并不是一个加里化的函数。调用<code>bar(5)</code>并不会返回一个可以输入12的函数。我们只能像<code>bar(5,12)</code>这样调用这个函数。</p><p>在一些其它语言里，比如 Haskell 和 OCaml，所有的多参数函数都是通过加里化实现的。</p><p>下面是一个把上面的<code>foo</code>函数用OCaml语言写成的例子：</p><div><pre><code><span>let</span> <span>foo</span> <span>=</span> <span>fun</span> <span>a</span> <span>-&gt;</span>

  <span>fun</span> <span>b</span> <span>-&gt;</span>
    <span>a</span> <span>*</span> <span>a</span> <span>+</span> <span>b</span> <span>*</span> <span>b</span>

</code></pre></div><p>下面是把上面的<code>bar</code>函数用OCaml语言写成的例子：</p><div><pre><code><span>let</span> <span>bar</span> <span>=</span> <span>fun</span> <span>a</span> <span>b</span> <span>-&gt;</span>

  <span>a</span> <span>*</span> <span>a</span> <span>+</span> <span>b</span> <span>*</span> <span>b</span>
</code></pre></div><p>头一个函数我们叫做“显式加里化”，第二个叫做“隐式加里化”。</p><p>跟Javascript不一样，在OCaml语言里，<code>foo</code>函数和<code>bar</code>函数是完全一样的。我们用完全一样的方式调用它们。</p><div><pre><code># foo 3 4;;
- : int = 25
# bar 3 4;;
- : int = 25
</code></pre></div><p>两个函数都能够通过提供一个参数值来创造一个偏函数：</p><div><pre><code># let foo5 = foo 5;;
val foo5 : int -&gt; int = &lt;fun&gt;

# let bar5 = bar 5;;
val bar5 : int -&gt; int = &lt;fun&gt;
# foo5 12;;
- : int = 169
# bar5 12;;
- : int = 169
</code></pre></div><p>事实上，我们可以把下面这个匿名函数：</p><div><pre><code><span>fun</span> <span>arg1</span> <span>arg2</span> <span>...</span> <span>argN</span> <span>-&gt;</span> <span>exp</span>

</code></pre></div><p>当作是下面这个函数的简写：</p><div><pre><code><span>fun</span> <span>arg1</span> <span>-&gt;</span> <span>fun</span> <span>arg2</span> <span>-&gt;</span> <span>...</span> <span>-&gt;</span> <span>fun</span> <span>argN</span> <span>-&gt;</span> <span>exp</span>

</code></pre></div><h3>函数加里化和偏函数应用的总结</h3><ul><li>偏函数应用是找一个函数，固定其中的几个参数值，从而得到一个新的函数。</li><li>函数加里化是一种使用匿名单参数函数来实现多参数函数的方法。</li><li>函数加里化能够让你轻松的实现某些偏函数应用。</li><li>有些语言(例如 Haskell, OCaml)所有的多参函数都是在内部通过函数加里化实现的。</li></ul><p><a href="http://t.cn/zTcmcnC"><img src="http://ittopic.gotoip1.com/cup-ad.jpg"></a></p><hr>本文由<a href="http://www.aqee.net">外刊IT评论网</a>(<a href="http://www.aqee.net">www.aqee.net</a>)原创发表，文章地址：<a href="http://www.aqee.net/currying-partial-application/">函数加里化(Currying)和偏函数应用(Partial Application)的比较</a>，[英文原文：<a href="http://alecbenzer.com/blog/currying-partial-application/">Currying vs. Partial Application</a> ]<br><br><br><br><br><br><br><br><img src="http://www1.feedsky.com/t1/730642860/aqee-net/feedsky/s.gif?r=http://www.aqee.net/currying-partial-application/" border="0" height="0" width="0">