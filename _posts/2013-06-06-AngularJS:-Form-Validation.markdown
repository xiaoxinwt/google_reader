---
layout: post
title:  "AngularJS: Form Validation"
date:   2013-06-06 07:00:00
author: 
categories: program
---

## AngularJS: Form Validation
### by 
### at 2013-06-06 07:00:00
### original <http://feedproxy.google.com/~r/dailyjs/~3/rXGzr3n94wg/angularjs-7>

<ul>
  <li><a href="http://dailyjs.com/2013/04/11/angularjs-1/">Part 1: Google, Twitter, and AngularJS</a></li>
  <li><a href="http://dailyjs.com/2013/04/18/angularjs-2/">Part 2: Let's Make a Feed Reader</a></li>
  <li><a href="http://dailyjs.com/2013/04/25/angularjs-3/">Part 3: Rendering Feeds</a></li>
  <li><a href="http://dailyjs.com/2013/05/09/angularjs-4/">Part 4: Managing Feeds</a></li>
  <li><a href="http://dailyjs.com/2013/05/16/angularjs-5/">Part 5: Tests</a></li>
  <li><a href="http://dailyjs.com/2013/05/30/angularjs-6/">Part 6: Adding Dependencies</a></li>
  <li><a href="http://dailyjs.com/2013/06/06/angularjs-7/"><strong>Part 7: Form Validation</strong></a></li>
  <li><a href="http://dailyjs.com/2013/06/13/angularjs-8/">Part 8: Iterators and Data</a></li>
</ul>
<p>This week we’re going to look at form validation with AngularJS. Angular has several directives that support form field validation, and they’re based on the <a href="http://diveintohtml5.info/forms.html">HTML5 form validators</a>. You can specify that a field is <em>required</em>, a certain size, a certain type, and should match a given pattern.</p>

<h3>URL Validation</h3>
<div>
  <img src="http://dailyjs.com/images/posts/angular-7-invalid.png" alt="">
  <small>Chrome's validation message</small>
</div>
<p>This tutorial series is about a feed reader, so it’s lucky that one of the standard HTML5 validators is for checking URLs. It can be used by adding the <code>type=&quot;url&quot;</code> attribute to an <code>input</code>. Angular supports this through the <a href="http://docs.angularjs.org/api/ng.directive:input.url">input url directive</a>. It takes various options, of which we’re interested in <code>required</code> and <code>ng-model</code>.</p>

<p>The <code>ng-model</code> directive allows the <code>input</code> to be linked to a model, but any Angular expression can be used. The <a href="http://docs.angularjs.org/api/ng.directive:form">form directive</a> allows forms to be managed with Angular, and bound to controllers.</p>

<p>Just by adding a <code>form</code> and an <code>input</code> with <code>type=&quot;url&quot;</code> will result in some basic validation support (in <code>app/views/main.html</code>):</p>
<div><pre><code><span>&lt;form</span> <span>name=</span><span>&quot;newFeed&quot;</span><span>&gt;</span>
  URL: <span>&lt;input</span> <span>size=</span><span>&quot;80&quot;</span> <span>name=</span><span>&quot;url&quot;</span> <span>ng-model=</span><span>&quot;newFeed.url&quot;</span> <span>type=</span><span>&quot;url&quot;</span> <span>required</span><span>&gt;</span>
  <span>&lt;button</span> <span>ng-click=</span><span>&quot;addFeed(newFeed)&quot;</span><span>&gt;</span>Add Feed<span>&lt;/button&gt;</span>
<span>&lt;/form&gt;</span>
</code></pre>
</div>
<p>However, this won’t quite work with the controller code that I wrote in the previous parts because <code>addFeed</code> isn’t set up to check validation.</p>

<h3>Checking Validation State</h3>

<p>In a controller, a bound value can be interrogated for the validation status by checking the <code>$valid</code> property. The previous <code>addFeed</code>, in <code>app/scripts/controllers/main.js</code>, can be changed as follows:</p>
<div><pre><code><span>$scope</span><span>.</span><span>addFeed</span> <span>=</span> <span>function</span><span>(</span><span>feed</span><span>)</span> <span>{</span>
  <span>if</span> <span>(</span><span>feed</span><span>.</span><span>$valid</span><span>)</span> <span>{</span>
    <span>// Copy this feed instance and reset the URL in the form</span>
    <span>$scope</span><span>.</span><span>feeds</span><span>.</span><span>push</span><span>(</span><span>feed</span><span>);</span>
    <span>$scope</span><span>.</span><span>newFeed</span><span>.</span><span>url</span> <span>=</span> <span>{};</span>
  <span>}</span>
<span>};</span>
</code></pre>
</div>
<p>This should work, but it does one thing wrong: <code>$scope.newFeed.url</code> can’t be reset by assigning it to an object literal, because <code>newFeed</code> is now decorated with internal properties to support validation. Instead, <em>copy</em> the new object, and reset the values in <code>newFeed</code>:</p>
<div><pre><code><span>$scope</span><span>.</span><span>addFeed</span> <span>=</span> <span>function</span><span>(</span><span>feed</span><span>)</span> <span>{</span>
  <span>if</span> <span>(</span><span>feed</span><span>.</span><span>$valid</span><span>)</span> <span>{</span>
    <span>// Copy this feed instance and reset the URL in the form</span>
    <span>var</span> <span>newFeed</span> <span>=</span> <span>angular</span><span>.</span><span>copy</span><span>(</span><span>feed</span><span>);</span>
    <span>$scope</span><span>.</span><span>feeds</span><span>.</span><span>push</span><span>(</span><span>newFeed</span><span>);</span>
    <span>$scope</span><span>.</span><span>fetchFeed</span><span>(</span><span>newFeed</span><span>);</span>
    <span>$scope</span><span>.</span><span>newFeed</span><span>.</span><span>url</span> <span>=</span> <span>&#39;&#39;</span><span>;</span>
  <span>}</span>
<span>};</span>
</code></pre>
</div>
<h3>Fighting with HTML5</h3>

<p>We should probably add error messages that are cross-browser compatible. To do that, you can use the <code>ng-show</code> directive:</p>
<div><pre><code><span>&lt;form</span> <span>name=</span><span>&quot;newFeed&quot;</span> <span>novalidate</span><span>&gt;</span>
  URL: <span>&lt;input</span> <span>size=</span><span>&quot;80&quot;</span> <span>name=</span><span>&quot;url&quot;</span> <span>ng-model=</span><span>&quot;newFeed.url&quot;</span> <span>type=</span><span>&quot;url&quot;</span> <span>required</span><span>&gt;</span>
  <span>&lt;button</span> <span>ng-click=</span><span>&quot;addFeed(newFeed)&quot;</span><span>&gt;</span>Add Feed<span>&lt;/button&gt;</span>
  <span>&lt;span</span> <span>class=</span><span>&quot;error&quot;</span> <span>ng-show=</span><span>&quot;newFeed.$error.required&quot;</span><span>&gt;</span>Required!<span>&lt;/span&gt;</span>
  <span>&lt;span</span> <span>class=</span><span>&quot;error&quot;</span> <span>ng-show=</span><span>&quot;newFeed.$error.url&quot;</span><span>&gt;</span>Invalid URL format!<span>&lt;/span&gt;</span>
<span>&lt;/form&gt;</span>
</code></pre>
</div>
<p>The <a href="http://docs.angularjs.org/api/ng.directive:ngShow(">ngShow</a> directive can conditionally show part of the DOM based on an Angular expression – in this case the validation results are checked. Incidentally, validation results can be found in the <code>$error</code> property fo the model.</p>

<p>Also notice that I added the <code>novalidate</code> attribute to the form; if you don’t do this HTML5 validations will still kick in, which causes confusing behaviour.</p>

<h3>Disabling the Button</h3>

<p>Another nice touch is to use <code>ng-disabled</code> to disable the button when an invalid URL has been entered. The <a href="http://docs.angularjs.org/api/ng.directive:ngDisabled">ngDisabled</a> directive takes an Angular expression, like the previous directives discussed here:</p>
<div><pre><code><span>&lt;form</span> <span>name=</span><span>&quot;newFeed&quot;</span> <span>novalidate</span><span>&gt;</span>
  URL: <span>&lt;input</span> <span>size=</span><span>&quot;80&quot;</span> <span>name=</span><span>&quot;url&quot;</span> <span>ng-model=</span><span>&quot;newFeed.url&quot;</span> <span>type=</span><span>&quot;url&quot;</span> <span>required</span><span>&gt;</span>
  <span>&lt;button</span> <span>ng-disabled=</span><span>&quot;!newFeed.$valid&quot;</span> <span>ng-click=</span><span>&quot;addFeed(newFeed)&quot;</span><span>&gt;</span>Add Feed<span>&lt;/button&gt;</span>
  <span>&lt;span</span> <span>class=</span><span>&quot;error&quot;</span> <span>ng-show=</span><span>&quot;newFeed.$error.required&quot;</span><span>&gt;</span>Required!<span>&lt;/span&gt;</span>
  <span>&lt;span</span> <span>class=</span><span>&quot;error&quot;</span> <span>ng-show=</span><span>&quot;newFeed.$error.url&quot;</span><span>&gt;</span>Invalid URL format!<span>&lt;/span&gt;</span>
<span>&lt;/form&gt;</span>
</code></pre>
</div>
<p>The difference here is I’ve used <code>!</code> to negate the expression: <code>!newFeed.$valid</code>. Yes, it’s really that easy!</p>

<h3>Conclusion</h3>

<p>There’s more to expressions than simple model-based truth tests – you can do pretty much anything short of control flow statements. For more, see <a href="http://docs.angularjs.org/guide/expression">Angular Developer Guide, Expressions</a>.</p>

<p>The latest commit for this project was <a href="https://github.com/alexyoung/djsreader/tree/0dcc99699824e6a75cff66a52bbbdc9c197721e2">0dcc996</a>.</p>
   <img src="http://feeds.feedburner.com/~r/dailyjs/~4/rXGzr3n94wg" height="1" width="1">