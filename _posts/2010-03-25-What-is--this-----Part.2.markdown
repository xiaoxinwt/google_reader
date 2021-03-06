---
layout: post
title:  "What is 'this'? - Part.2"
date:   2010-03-25 11:18:14
author: 明城
categories: program
---

## What is 'this'? - Part.2
### by 明城
### at 2010-03-25 11:18:14
### original <http://www.gracecode.com/archives/3019/>

<p>好了，我们<a href="http://www.gracecode.com/archives/3018/" title="http://www.gracecode.com/archives/3018/">继续上次留下的问题</a>。</p>

<pre>var x = 10;
var foo = {
    x: 20,
    bar: function () {
        var x = 30;
        return this.x;
    }
};

console.log(
    foo.bar(),             // 1.
    (foo.bar)(),           // 2.
    (foo.bar = foo.bar)(), // 3.
    (foo.bar, foo.bar)()   // 4.
);</pre><p>我们考虑语句 3. 和上面的两个语句有什么不同</p>

<pre>(foo.bar = foo.bar)(), // 3.</pre><p>相比语句 2.，语句 3. 中的 Grouping Operator 中有赋值（“=”）语句。那么，我们首先得明白赋值语句干了啥，继续<a href="http://bclary.com/2004/11/07/#a-11.1.6" title="http://bclary.com/2004/11/07/#a-11.1.6">参考对应的 ECMA 文档</a></p>

<pre>11.13.1 Simple Assignment (= )

The production 
    AssignmentExpression : LeftHandSideExpression = AssignmentExpression 

is evaluated as follows:

1. Evaluate LeftHandSideExpression.
2. Evaluate AssignmentExpression.
3.Call GetValue(Result(2)).
4.Call PutValue(Result(1), Result(3)).
5.Return Result(3).</pre><p>其中，最重要的步骤就是 PutValue，我们<a href="http://bclary.com/2004/11/07/#a-8.7.2" title="http://bclary.com/2004/11/07/#a-8.7.2">继续刨根问底</a></p>

<pre>8.7.2 PutValue(V, W)

1. If Type(V) is not Reference, throw a 
   ReferenceError exception.
2. Call GetBase(V).
3. If Result(2) is null, go to step 6.
4. Call the [[Put]] method of Result(2), passing GetPropertyName(V) 
   for the property name and W for the value.
5. Return.
6. Call the [[Put]] method for the global object, passing 
   GetPropertyName(V) for the property name and W for the value.
7. Return.</pre><p>所以，我们根据上面的定义可以得知，语句返回的是 foo.bar 的函数值。因此，赋值操作符返回的是“值（Value）”而不是“引用（Reference）”。</p>

<p>因为函数体需要 this 值获取 x 属性的值，那么接下来我们考虑改函数时调用时的上下文作用域以及背后的具体流程。 尤其注意第七条规则</p>

<pre>...
6. If Type(Result(1)) is Reference, Result(6) is GetBase( Result(1)). 
    Otherwise, Result(6) is null.
7. If Result(6) is an activation object, Result(7) is null. Otherwise,
    Result(7) is the same as Result(6).
8. Call the [[Call]] method on Result(3), providing Result(7) as 
    the this value and providing the list Result(2) as the 
    argument values.
…</pre><p>那么在这种情况下，<tt>GetBase</tt> 操作实际上返回的是 <tt>null</tt>，因此此条语句函数执行的作用域为 global ，在浏览器中也就是 window 。</p>

<pre>(foo.bar = foo.bar)()</pre><p>那么，上面的语句中我们可以得知</p>

<ol>
    <li>Grouping Operator 中的赋值语句返回的是 foot.bar 的函数值（“Value”）</li>
    <li>该函数执行的上下文作用域为 window</li>
</ol>

<p>那么，在该函数中执行获取 this.x 也就是获取 window.x 的值。因此，这条语句返回的就是 10 。如果还不理解，考虑下面的代码段</p>

<pre>var x = 10;
(function() {
    return this.x; // 这里会返回什么？
})();</pre><p>如果理解了上面的语句的前因后果，那么题目中的语句 4. 就能举一反三给推导出来。首先我们来了解逗号运算符（“,”）的定义，我们就可以得之语句</p>

<pre>(foo.bar, foo.bar)</pre><p>返回的也是 foo.bar 的值“Value”而非引用“Reference”，那么接下来的事情其实就是和语句 3. 一样的了。因此，语句 4. 返回的液是 window.x 的值，也就是 10 。</p>

<p>总结下，那么上面的输出总的来说是</p>

<pre>20 20 10 10</pre><p><tt>-- Split --</tt></p>

<p>似乎目前为止，我们已经完全回答出了当初设定的问题。但恐怕会留下疑虑，就是传值“Value”和引用“Reference”之间到低有何不同、函数的作用域以及 this 的指向是否已经真正了解？</p>

<p>是的，这个题目已经完了，而我们的问题似乎还是没有怎麽搞清楚。OK，下次我们来详细讨论下这个问题…</p>

<p><tt>-- To be continued --</tt></p>