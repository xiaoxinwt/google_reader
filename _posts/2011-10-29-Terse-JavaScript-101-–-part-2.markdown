---
layout: post
title:  "Terse JavaScript 101 – part 2"
date:   2011-10-29 19:05:57
author: James
categories: program
---

## Terse JavaScript 101 – part 2
### by James
### at 2011-10-29 19:05:57
### original <http://james.padolsey.com/javascript/terse-javascript-101-part-2/>

<p><a href="http://james.padolsey.com/javascript/terse-javascript-101-part-1/">Part one</a>, posted last week, explored a few different ways of de-cluttering your JavaScript, and, in the process, making it more readable.</p>

<p>Some developers don’t like the idea of using a language’s more idiosyncratic features because it does, potentially, make your code less readable in the eyes of those who haven’t learned the language properly. I think it’s still up for debate. While you’re pondering that, part II awaits…</p>

<p>If you haven’t checked out the <a href="http://james.padolsey.com/javascript/truthy-falsey/">“Truthy &amp; Falsey” introduction</a> then please do so before continuing.</p>

<h3>Looping</h3>

<p>It’s something we barely think about but JavaScript’s expressive diversity allows us to invent new patterns for looping. First, there’s the standard:</p>


<div><div><pre style="font-family:monospace"><span>for</span> <span>(</span><span>var</span> i <span>=</span> <span>0</span><span>;</span> i <span>&lt;</span> array.<span>length</span><span>;</span> i<span>++</span><span>)</span> <span>{</span><span>}</span></pre></div></div>




<p>This is conventional and will be known to developers coming from any language. There’s nothing inherently wrong with it. That said, for me, it’s not ideal.</p>

<p>The first improvement I usually make is to cache the <code>length</code> property of the array:</p>


<div><div><pre style="font-family:monospace"><span>for</span> <span>(</span><span>var</span> i <span>=</span> <span>0</span><span>,</span> l <span>=</span> array.<span>length</span><span>;</span> i <span>&lt;</span> l<span>;</span> i<span>++</span><span>)</span> <span>{</span><span>}</span></pre></div></div>




<p>This doesn’t make it shorter, but it does make it more efficient, although marginally so…</p>

<p>Another change we can make is to combine the iteration expression (<code>i++</code>) and the conditional expression (<code>i &lt; l</code>), like so:</p>


<div><div><pre style="font-family:monospace"><span>for</span> <span>(</span><span>var</span> i <span>=</span> <span>-</span><span>1</span><span>,</span> l <span>=</span> array.<span>length</span><span>;</span> <span>++</span>i <span>&lt;</span> l<span>;</span><span>)</span> <span>{</span><span>}</span></pre></div></div>




<p>Notice that we’re starting with an index of <code>-1</code>, but won’t worry because before the loop’s body runs, the conditional expression is run, and in the above code, the conditional expression is iterating the index and testing it against the length all in one.</p>

<p>It’s important to note that the prefix increment/decrement operators (<code>++i</code>/<code>--i</code>) return the changed value, while the postfix increment/decrement operators (<code>i++</code>/<code>i--</code>) return the old value. To make this clearer:</p>


<div><div><pre style="font-family:monospace"><span>var</span> x <span>=</span> <span>5</span><span>;</span>
<span>alert</span><span>(</span>x<span>++</span><span>)</span><span>;</span> <span>// alerts 5 (old value)</span>
<span>alert</span><span>(</span>x<span>)</span><span>;</span> <span>// alerts 6</span>
 
<span>var</span> y <span>=</span> <span>5</span><span>;</span>
<span>alert</span><span>(</span><span>++</span>y<span>)</span><span>;</span> <span>// alerts 6 (new value)</span>
<span>alert</span><span>(</span>y<span>)</span><span>;</span> <span>// alerts 6</span></pre></div></div>




<p>Another looping “pattern” in JavaScript is to assign and rely on what’s returned from that assignment for the conditional expression:</p>


<div><div><pre style="font-family:monospace"><span>var</span> elements <span>=</span> document.<span>getElementsByTagName</span><span>(</span><span>'a'</span><span>)</span><span>;</span>
 
<span>for</span> <span>(</span><span>var</span> i <span>=</span> <span>-</span><span>1</span><span>,</span> el<span>;</span> el <span>=</span> elements<span>[</span><span>++</span>i<span>]</span><span>;</span><span>)</span> <span>{</span>
  el<span>;</span> <span>// =&gt; a single element</span>
<span>}</span></pre></div></div>




<p>Yesterday’s post on truthy &amp; falsey bears some relevance here. The expression <code>el=elements[++i]</code> will return the value of <code>elements[++i]</code>. If this value is falsey the loop will not continue. An element list is full of truthy values (objects). We don’t need to be worried about falsey values. Note that in this approach, you may be sacrificing performance for terseness.</p>

<p>In many situations, we do want to iterate over falsey values too, e.g.</p>


<div><div><pre style="font-family:monospace"><span>var</span> myArray <span>=</span> <span>[</span><span>1</span><span>,</span> <span>2</span><span>,</span> <span>3</span><span>,</span> <span>null</span><span>,</span> <span>8</span><span>,</span> <span>54</span><span>,</span> <span>null</span><span>,</span> <span>0</span><span>,</span> <span>32</span><span>]</span><span>;</span></pre></div></div>




<p>With something like this it would be best to use the traditional <code>i&lt;length</code> conditional expression, but if you’re so inclined feel free to mix the conditional and iteration expressions together like shown further up (<code>++i&lt;l</code>).</p>

<p>If we were feeling a bit crazy, we could combine everything — iteration, conditional test, and assignment of indexed value — in a single expression:</p>


<div><div><pre style="font-family:monospace"><span>var</span> array <span>=</span> <span>[</span><span>'a'</span><span>,</span><span>'b'</span><span>,</span><span>'c'</span><span>]</span><span>;</span>
 
<span>for</span> <span>(</span><span>var</span> i <span>=</span> <span>-</span><span>1</span><span>,</span> l <span>=</span> array.<span>length</span><span>,</span> <span>item</span><span>;</span> <span>item</span> <span>=</span> array<span>[</span><span>++</span>i<span>]</span><span>,</span> i <span>&lt;</span> l<span>;</span><span>)</span> <span>{</span>
  <span>alert</span><span>(</span><span>item</span><span>)</span><span>;</span>
<span>}</span>
 
<span>// alerts, a, b, c</span></pre></div></div>




<p>Here we used the useful comma operator which always returns its right-hand operand. In this example we used it to make sure that <code>i &lt; l</code> is what the condition expression returns. Before the comma we’re assigning the value at the iterated index within the array.</p>

<p>I don’t really suggest doing all of this in one line, and to be honest, there’s nothing wrong with conventional looping structures. What I am trying to put across is how much expressive diversity JavaScript provides.</p>

<h3>Assignments</h3>

<p>Assignments are expressions too, so you can slot them in wherever you want:</p>


<div><div><pre style="font-family:monospace"><span>var</span> a <span>=</span> <span>1</span><span>;</span>
<span>alert</span><span>(</span>a <span>=</span> <span>2</span><span>)</span><span>;</span> <span>// alerts 2</span>
<span>alert</span><span>(</span>a<span>)</span><span>;</span> <span>// alerts 2</span></pre></div></div>




<p>An assignment operator will always return whatever is on its right-hand side. Be careful with slotting in assignment expressions wherever you want though, because they can be misconstrued for equality operators. For example:</p>


<div><div><pre style="font-family:monospace"><span>var</span> match<span>;</span>
 
<span>if</span> <span>(</span>match <span>=</span> <span>'1/2/3'</span>.<span>match</span><span>(</span><span>/\d/</span><span>)</span><span>)</span> <span>{</span>
  <span>//...</span>
<span>}</span></pre></div></div>




<p>To some, upon initial inspection, it may appear that we’re testing <code>match</code> for equality against <code>'1/2/3'.match(/\d/)</code> when in fact the latter is being assigned to the former, and the <code>if</code> statement will run if the assignment expression returns a truthy value (i.e. if its right-hand side operand is truthy).</p>

<h3>Casting to a number</h3>

<p>Quick and easy:</p>


<div><div><pre style="font-family:monospace"><span>var</span> str <span>=</span> <span>'222'</span><span>;</span>
<span>var</span> num <span>=</span> Number<span>(</span>str<span>)</span><span>;</span> <span>// =&gt; 222</span>
 
<span>typeof</span> num<span>;</span> <span>// =&gt; &#39;number&#39;</span></pre></div></div>




<p>A shortcut is the unary plus (<code>+</code>) operator, used like so:</p>


<div><div><pre style="font-family:monospace"><span>var</span> str <span>=</span> <span>'222'</span><span>;</span>
<span>var</span> num <span>=</span> <span>+</span>str<span>;</span> <span>// =&gt; 222</span>
 
<span>typeof</span> num<span>;</span> <span>// =&gt; &#39;number&#39;</span></pre></div></div>




<p>It works in exactly the same way.</p>

<h3>Casting to a string</h3>

<p>Once again, quick and easy:</p>


<div><div><pre style="font-family:monospace"><span>var</span> arr <span>=</span> <span>[</span><span>1</span><span>,</span><span>2</span><span>,</span><span>3</span><span>]</span><span>;</span>
<span>var</span> str <span>=</span> String<span>(</span>arr<span>)</span><span>;</span> <span>// =&gt; &#39;1,2,3&#39;</span>
 
<span>typeof</span> str<span>;</span> <span>// =&gt; &#39;string&#39;</span></pre></div></div>




<p>The shortcut, this time, is to simply concatenate an empty string to the value that you’re casting:</p>


<div><div><pre style="font-family:monospace"><span>var</span> arr <span>=</span> <span>[</span><span>1</span><span>,</span><span>2</span><span>,</span><span>3</span><span>]</span><span>;</span>
<span>var</span> str <span>=</span> <span>''</span> <span>+</span> arr<span>;</span> <span>// =&gt; &#39;1,2,3&#39;</span>
 
<span>typeof</span> str<span>;</span> <span>// =&gt; &#39;string&#39;</span></pre></div></div>




<h3>Saving references</h3>

<p>Property lookup can be verbose and inefficient. To save space and to benefit performance it’s common to save references and access those properties via the new reference instead of having to evaluate the entire expression every time you want the nested value:</p>


<div><div><pre style="font-family:monospace"><span>// Original:</span>
document.<span>body</span>.<span>style</span>.<span>color</span><span>;</span>
 
<span>// &quot;Caching&quot;</span>
<span>var</span> style <span>=</span> document.<span>body</span>.<span>style</span><span>;</span>
 
<span>// Whenever I need to access the `color` value:</span>
style.<span>color</span><span>;</span></pre></div></div>




<p>It’s often referred to as caching. Essentially, all that’s happening is that a new reference is being created for an object and being assigned to an identifier that you specify. Let’s start with this:</p>


<div><div><pre style="font-family:monospace"><span>var</span> data <span>=</span> <span>{</span>
    config<span>:</span> <span>{</span>
        doSomething<span>:</span> <span>function</span><span>(</span><span>item</span><span>)</span><span>{</span>
            <span>alert</span><span>(</span><span>item</span><span>)</span><span>;</span>
        <span>}</span>
    <span>}</span>
<span>}</span><span>;</span>
 
<span>for</span> <span>(</span><span>var</span> i <span>=</span> <span>-</span><span>1</span><span>,</span> l <span>=</span> someArray.<span>length</span><span>;</span> <span>++</span>i <span>&lt;</span> l<span>;</span><span>)</span> <span>{</span>
    data.<span>config</span>.<span>doSomething</span><span>(</span>someArray<span>[</span>i<span>]</span><span>)</span><span>;</span>
<span>}</span></pre></div></div>




<p>It would make sense to minimise the amount of property access that needs to occur within the loop, and thus, potentially, anywhere else in our application that references <code>data.config</code> too:</p>


<div><div><pre style="font-family:monospace"><span>var</span> config <span>=</span> data.<span>config</span><span>;</span></pre></div></div>




<p>Now the loop can simply reference <code>config</code> instead of <code>data.config</code>:</p>


<div><div><pre style="font-family:monospace"><span>for</span> <span>(</span><span>var</span> i <span>=</span> <span>-</span><span>1</span><span>,</span> l <span>=</span> someArray.<span>length</span><span>;</span> <span>++</span>i <span>&lt;</span> l<span>;</span><span>)</span> <span>{</span>
    config.<span>doSomething</span><span>(</span>someArray<span>[</span>i<span>]</span><span>)</span><span>;</span>
<span>}</span></pre></div></div>




<p>We could even “cache” the function itself:</p>


<div><div><pre style="font-family:monospace"><span>var</span> doSomething <span>=</span> data.<span>config</span>.<span>doSomething</span><span>;</span></pre></div></div>




<p>And then call it directly within the loop:</p>


<div><div><pre style="font-family:monospace"><span>for</span> <span>(</span><span>var</span> i <span>=</span> <span>-</span><span>1</span><span>,</span> l <span>=</span> someArray.<span>length</span><span>;</span> <span>++</span>i <span>&lt;</span> l<span>;</span><span>)</span> <span>{</span>
    doSomething<span>(</span>someArray<span>[</span>i<span>]</span><span>)</span><span>;</span>
<span>}</span></pre></div></div>




<p><strong>IMPORTANT</strong>: It’s important to note that, when you assign a member function (that is, a function that is a property of an object) you are de-binding its <code>this</code> value, i.e. the context in which it runs. Here’s an example:</p>


<div><div><pre style="font-family:monospace"><span>var</span> obj <span>=</span> <span>{</span>
    value<span>:</span> <span>234</span><span>,</span>
    fn<span>:</span> <span>function</span><span>(</span><span>)</span><span>{</span> <span>return</span> <span>this</span>.<span>value</span><span>;</span> <span>}</span>
<span>}</span><span>;</span>
 
obj.<span>fn</span><span>(</span><span>)</span><span>;</span> <span>// =&gt; 234</span>
 
<span>var</span> fn <span>=</span> obj.<span>fn</span><span>;</span>
fn<span>(</span><span>)</span><span>;</span> <span>// =&gt; undefined</span></pre></div></div>




<p>De-binding the <code>fn</code> method will make its context the global (window) object instead of the <code>obj</code> which we defined, so when our <code>fn</code> function looks for its `this.value` it won’t find it (because <code>this</code> is the <code>window</code>, which doesn’t have `value` defined).</p>

<h3>End of part 2</h3>

<p>Comment if you feel compelled.</p>