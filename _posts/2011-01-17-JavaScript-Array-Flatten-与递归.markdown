---
layout: post
title:  "JavaScript Array Flatten 与递归"
date:   2011-01-17 19:26:48
author: sofish
categories: program
---

## JavaScript Array Flatten 与递归
### by sofish
### at 2011-01-17 19:26:48
### original <http://sofish.de/1628>

<p>如何用 JavaScript 将 <code>[1,2,3,[4,5, [6,7]], [[[8]]]]</code> 这样一个 Array 变成 <code>[1,2,3,4,5, 6,7,8]</code> 呢？传说中的 Array Flatten。</p>
<p>处理这种问题，通常我们会需要递归，来让程序自己按照一种算法去循环。在某书说写着，“递归是一种强大的编程技术”，好吧，她不仅仅属于 JavaScript。递归可以很难，也可以比较简单（总得来说还是比较难）。处理上面这个问题，用递归来解决，应该是比较适合的。之前工友这样实现了，算是一个简单的递归使用实例吧：</p>
<pre>
flatten: function(ac){
    var array = [];
    var group = this.arr;
    if(ac) group = ac;
    for (var i = 0; i &lt; group.length; i++){
        if(group[i] instanceof Array){
            array = array.concat(this.flatten(group[i]));
        }else{
            array = array.concat(group[i]);
        }
    }
    return array;
}</pre>
<p>在 <code>if(group[i] instanceof Array)</code> 的时候，调用函数自身，通过传参数的形式进行递归。只是在重构 Array.js 的时候，就觉得既然是框架，那么多抽象出来的东西不用，是不是太浪费了。所以，最好调用已经抽象出来的静态函数，而不是又重新一遍。这里有 for 循环，也就是说我们会需要有 each。结果呢？四个字，不好实现。因为我们始终要创建一个数组，最终 return 这个新的数组，得重新抽出来一个函数来调。这不就违背了初衷？</p>
<p>网上瞄了一下，最终盯在 <a href="http://www.prototypejs.org/">prototype</a> 上。他的实现方法是抽象出一个处理递归增量的函数，再利用这个函数来做递归。怎么说呢？想说，这就叫框架。下面是一个处理递归的函数：</p>
<pre>
function inject(memo, iterator, context) {
    this.each(function(value, index) {
        memo = iterator.call(context, memo, value, index);
    });
    return memo;
}
</pre>
<p>而这个 flatten 函数，最终的实现是这样的，这代码真漂亮：</p>
<pre>
function flatten() {
    return this.inject([], function(array, value) {
        if (Object.isArray(value))
            return array.concat(value.flatten());
        array.push(value);
        return array;
    });
}
</pre>
<p>当然，这里面还需要另外一个抽象出来的函数，来处理 for 循环，就是我们的 each 函数了。顺路在 flatten 中，带出这个 each 函数吧，学习了 jQuery 的做法，加入原生支持；当然，还可以处理纯对象，而不仅仅是数组：</p>
<pre>
each: function (callback, bind) {
    var isObject = arale.typeOf(this.obj) === &#39;object&#39;,
        i = 0,
        key;

    if (isObject) {
        var obj = this.obj;
        for (key in obj) {
            if (callback.call(bind, key, obj[key]) === false) {
                break;
            }
        }
    } else {
        var arr = this.obj;
        if (Array.prototype.forEach) {

            // 用户 return false; 的时候还会继续执行
            // 原生的很囧，去还是舍呢? marked TODO;
            return [].forEach.call(arr, callback, bind);
        };
        for (var value = arr[0], length = arr.length; i &lt; length &amp;&amp; callback.call(bind, i, value) !== false; value = arr[++i]) {};
    }
}</pre>
<p>=========</p>
<p>最近玩 Javascript 比较多。瞄了一下最近的文章，还有在团队内部博客上发的文章，全都是 JS的。囧。似乎是一个很大的改变。需要平衡一下了。</p>
<p><hr>©2011 <a href="http://sofish.de" title="幸福收藏夹">幸福收藏夹</a>. 版权所有,转载务必注明. 幸福收藏夹域名已经更新为：<a href="http://sofish.de" title="幸福收藏夹">sofish.de</a> .</p><img src="http://www1.feedsky.com/t1/484474961/sofish/feedsky/s.gif?r=http://sofish.de/1628" border="0" height="0" width="0"><p><a href="http://www1.feedsky.com/r/l/feedsky/sofish/484474961/art01.html"><img border="0" ismap src="http://www1.feedsky.com/r/i/feedsky/sofish/484474961/art01.gif"></a></p>