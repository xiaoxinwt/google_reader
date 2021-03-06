---
layout: post
title:  "Terse JavaScript 101 – part 1"
date:   2011-10-19 05:23:00
author: James
categories: program
---

## Terse JavaScript 101 – part 1
### by James
### at 2011-10-19 05:23:00
### original <http://james.padolsey.com/javascript/terse-javascript-101-part-1/>

<p>While some folk will argue that verbose code aids readability, I think almost the opposite, and in this post I’m going to list some basic tips for minimising redundant clutter in your code. JavaScript is a fun language at its core — it’s worth learning the tiny details.</p>

<p>I hope this post is useful to someone. I wrote it <strong>not</strong> for beginners (although it will be helpful to them), but for all those people writing JavaScript on a daily basis yet haven’t been afforded the time/motivation to get to know the language properly.</p>

<h3>new Object()</h3>

<p>Nope, don’t do this:</p>


<div><div><pre style="font-family:monospace"><span>var</span> o <span>=</span> <span>new</span> Object<span>(</span><span>)</span><span>;</span></pre></div></div>




<p>Or any of this:</p>


<div><div><pre style="font-family:monospace"><span>var</span> a <span>=</span> <span>new</span> Array<span>(</span><span>)</span><span>;</span>
<span>var</span> b <span>=</span> <span>new</span> Object<span>(</span><span>)</span><span>;</span>
<span>var</span> c <span>=</span> <span>new</span> RegExp<span>(</span><span>&quot;1(.)3&quot;</span><span>)</span><span>;</span></pre></div></div>




<p>Do this instead:</p>


<div><div><pre style="font-family:monospace"><span>var</span> a <span>=</span> <span>[</span><span>]</span><span>;</span>
<span>var</span> b <span>=</span> <span>{</span><span>}</span><span>;</span>
<span>var</span> c <span>=</span> <span>/1(.)3/</span><span>;</span></pre></div></div>




<p>Array literal, object literal, regular expression literal. They rock. There’s usually no need to use the constructor functions when literals are available (please see the note below though).</p>

<p><em>Note</em>: There are situations when calling <code>Array</code> or <code>RegExp</code> directly can be useful. In such situations, you needn’t bother with the <code>new</code> operator. <code>Array(1,2,3)</code> will produce the same as <code>new Array(1,2,3)</code>. Calling <code>RegExp</code> directly can be useful for dynamically building a regular expression from various strings.</p>

<h3>Calling a constructor</h3>

<p>If you’re <em>not</em> passing any arguments then the parenthesis are not required. E.g.</p>


<div><div><pre style="font-family:monospace"><span>var</span> a <span>=</span> <span>new</span> Animal<span>(</span><span>)</span><span>;</span></pre></div></div>




<p>… and this becomes:</p>


<div><div><pre style="font-family:monospace"><span>var</span> a <span>=</span> <span>new</span> Animal<span>;</span></pre></div></div>




<p>Petty, perhaps. Still worth knowing though.</p>

<h3>var;var;var</h3>

<p>Quick and easy. Less of this:</p>


<div><div><pre style="font-family:monospace"><span>var</span> fun <span>=</span> <span>123</span><span>;</span>
<span>var</span> tun <span>=</span> <span>456</span><span>;</span>
<span>var</span> run <span>=</span> <span>789</span><span>;</span></pre></div></div>




<p>And more of this:</p>


<div><div><pre style="font-family:monospace"><span>var</span> fun <span>=</span> <span>123</span><span>,</span>
    tun <span>=</span> <span>456</span><span>,</span>
    run <span>=</span> <span>789</span><span>;</span></pre></div></div>




<p><em>This is why you should use a tab width of FOUR (or 4 spaces).</em></p>

<h3>Undefined &amp; Null</h3>

<p>The following is universally true in JavaScript (assuming <code>undefined</code> hasn’t been mutilated):</p>


<div><div><pre style="font-family:monospace">undefined <span>==</span> <span>null</span></pre></div></div>




<p>“So what?”, you say? Well, it means you can replace this:</p>


<div><div><pre style="font-family:monospace"><span>if</span> <span>(</span>foo <span>===</span> undefined <span>||</span> foo <span>===</span> <span>null</span><span>)</span> <span>{</span>
    <span>//...</span>
<span>}</span></pre></div></div>




<p>… With this:</p>


<div><div><pre style="font-family:monospace"><span>if</span> <span>(</span>foo <span>==</span> <span>null</span><span>)</span> <span>{</span>
    <span>//...</span>
<span>}</span></pre></div></div>




<p>If something is <code>==</code> to <code>null</code> then it is either <code>null</code> or <code>undefined</code>. Those are the only possible values.</p>

<h3>Returning undefined</h3>

<p>Not returning something from a function is the same as returning undefined, so you may as well not bother explicitly returning undefined:</p>


<div><div><pre style="font-family:monospace"><span>function</span> a<span>(</span><span>)</span> <span>{</span><span>}</span>
<span>function</span> b<span>(</span><span>)</span> <span>{</span> <span>return</span><span>;</span> <span>}</span>
<span>function</span> c<span>(</span><span>)</span> <span>{</span> <span>return</span> undefined<span>;</span> <span>}</span>
 
a<span>(</span><span>)</span> <span>===</span> b<span>(</span><span>)</span><span>;</span> <span>// true</span>
b<span>(</span><span>)</span> <span>===</span> c<span>(</span><span>)</span><span>;</span> <span>// true</span></pre></div></div>




<p><em>Note</em>: you should be careful with the <code>undefined</code> value as it can be changed. E.g. You can do <code>undefined = true</code> beforehand. <strong>The universe would shatter if you did that though, <em>so please don’t</em></strong>. Many libraries declare an empty (undefined) variable in an enclosed scope, which provides a real <code>undefined</code>:</p>


<div><div><pre style="font-family:monospace"><span>(</span><span>function</span><span>(</span><span>)</span><span>{</span>
 
    <span>// My private scope. They can't see me in here.</span>
 
    <span>var</span> undef<span>;</span>
 
    undef<span>;</span> <span>// =&gt; definitely undefined</span>
 
<span>}</span><span>(</span><span>)</span><span>)</span><span>;</span></pre></div></div>




<p>Or, more succinctly:</p>


<div><div><pre style="font-family:monospace"><span>(</span><span>function</span><span>(</span>undef<span>)</span><span>{</span>
 
    undef<span>;</span> <span>// =&gt; definitely undefined</span>
 
<span>}</span><span>(</span><span>)</span><span>)</span><span>;</span> <span>// &lt;- not passing anything in!</span></pre></div></div>




<h3>Empty array values</h3>

<p>This is entirely legal in JavaScript:</p>


<div><div><pre style="font-family:monospace"><span>var</span> a <span>=</span> <span>[</span><span>,,,</span><span>'foo'</span><span>]</span><span>;</span>
a<span>[</span><span>3</span><span>]</span><span>;</span> <span>// =&gt; &quot;foo&quot;</span></pre></div></div>




<p>One potential use-case of this was discussed in my recent post, <a href="http://james.padolsey.com/javascript/match-trick/">`match.()` trick</a>.</p>

<h3>Less blocks, more expressions</h3>

<p>I’m sure we’ve all seen something like this before:</p>


<div><div><pre style="font-family:monospace"><span>function</span> validateFoo<span>(</span>foo<span>)</span> <span>{</span>
    <span>var</span> regexResult <span>=</span> <span>/^foo+$/</span>.<span>test</span><span>(</span>foo<span>)</span><span>;</span>
    <span>if</span> <span>(</span>regexResult<span>)</span> <span>{</span>
        <span>return</span> <span>true</span><span>;</span>
    <span>}</span>
    <span>return</span> <span>false</span><span>;</span>
<span>}</span></pre></div></div>




<p>A cleaner and more sensible solution is this:</p>


<div><div><pre style="font-family:monospace"><span>function</span> validateFoo<span>(</span>foo<span>)</span> <span>{</span>
    <span>return</span> <span>/^foo+$/</span>.<span>test</span><span>(</span>foo<span>)</span><span>;</span>
<span>}</span></pre></div></div>




<p>No fussing with <code>if</code> statements and multiple returns. You can do everything within a single return statement.</p>

<p>Another example. We want to create a function that logs a name, specified by the arguments <code>forename</code> and <code>surname</code>, but if these arguments aren’t passed then the function should use the default values, “Bob” and “Smith”, respectively:</p>


<div><div><pre style="font-family:monospace"><span>function</span> printName<span>(</span>forename<span>,</span> surname<span>)</span> <span>{</span>
 
    <span>if</span> <span>(</span>forename <span>==</span> <span>null</span> <span>||</span> forename <span>==</span> <span>&quot;&quot;</span><span>)</span> <span>{</span>
        forename <span>=</span> <span>'Bob'</span><span>;</span>
    <span>}</span>
 
    <span>if</span> <span>(</span>surname <span>==</span> <span>null</span> <span>||</span> surname <span>==</span> <span>&quot;&quot;</span><span>)</span> <span>{</span>
        surname <span>=</span> <span>'Smith'</span><span>;</span>
    <span>}</span>
 
    console.<span>log</span><span>(</span>forename <span>+</span> <span>' '</span> <span>+</span> surname<span>)</span><span>;</span>
 
<span>}</span></pre></div></div>




<p>This can be condensed to:</p>


<div><div><pre style="font-family:monospace"><span>function</span> printName<span>(</span>forename<span>,</span> surname<span>)</span> <span>{</span>
 
    forename <span>=</span> forename <span>||</span> <span>'Bob'</span><span>;</span>
    surname <span>=</span> surname <span>||</span> <span>'Smith'</span><span>;</span>
 
    console.<span>log</span><span>(</span>forename <span>+</span> <span>' '</span> <span>+</span> surname<span>)</span><span>;</span>
 
<span>}</span></pre></div></div>




<p>Here, we’re using the logical OR operator (<code>||</code>) to provide a default value (right hand side) if the left hand side evaluates to false. Values that evaluate to false (i.e. “falsey”) are: An empty string (<code>""</code>), <code>null</code>, <code>undefined</code>, <code>0</code> and <code>false</code>.</p>

<p>So, this:</p>


<div><div><pre style="font-family:monospace">forename <span>=</span> forename <span>||</span> <span>'Bob'</span></pre></div></div>




<p>… is essentially saying: if <code>forename</code> is a truthy value, then assign <code>forename</code> to <code>forename</code> (in other words: don’t do anything), but if it is a falsey value (e.g. an empty string), then assign the string <code>"Bob"</code> to <code>forename</code>.</p>

<p>So, our new <code>printName</code> function will behave like so:</p>


<div><div><pre style="font-family:monospace"><span>//                            -- Logged --</span>
printName<span>(</span><span>)</span><span>;</span>                  <span>// Bob Smith</span>
printName<span>(</span><span>0</span><span>,</span> <span>0</span><span>)</span><span>;</span>              <span>// Bob Smith</span>
printName<span>(</span><span>false</span><span>,</span> <span>null</span><span>)</span><span>;</span>       <span>// Bob Smith</span>
printName<span>(</span><span>'James'</span><span>)</span><span>;</span>           <span>// James Smith</span>
printName<span>(</span><span>''</span><span>,</span> <span>'Jones'</span><span>)</span><span>;</span>       <span>// Bob Jones</span>
printName<span>(</span><span>'Bill'</span><span>,</span> <span>'Jones'</span><span>)</span><span>;</span>   <span>// Bill Jones</span></pre></div></div>




<h3>Part 1 [OVER]</h3>

<p>That’s it for today. There may be a part two soon enough, depending on how useful this is to readers. I know many of you have surpassed this stage of learning, so sorry for boring you.</p>

<h3>Recommended viewing/reading</h3>

<p>Seriously, if you’re writing JavaScript as part of your job then you definitely owe it to future maintainers of your code to learning JS properly.</p>

<p><strong>EDIT</strong>: Crockford links don’t work. The <a href="http://www.youtube.com/watch?v=v2ifWcnQs6M">entire video can be seen on youtube</a> instead.

<ul>
<li><a href="http://video.yahoo.com/watch/111593/1710507">Douglas Crockford: “The JavaScript Programming Language”/1 of 4</a></li>
<li><a href="http://video.yahoo.com/watch/111594/1710553">Douglas Crockford: “The JavaScript Programming Language”/2 of 4</a></li>
<li><a href="http://video.yahoo.com/watch/111595/1710607">Douglas Crockford: “The JavaScript Programming Language”/3 of 4</a></li>
<li><a href="http://video.yahoo.com/watch/111596/1710658">Douglas Crockford: “The JavaScript Programming Language”/4 of 4</a></li>
<li><a href="http://net.tutsplus.com/tutorials/javascript-ajax/javascript-and-the-dom-series-lesson-1/">Bonus: Intro to JS &amp; the DOM</a></li>
</ul></p>