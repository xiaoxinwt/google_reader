---
layout: post
title:  "setTimeout(0) 即将退役"
date:   2011-07-20 23:50:09
author: puterjam@gmail.com(puterjam)
categories: program
---

## setTimeout(0) 即将退役
### by puterjam@gmail.com(puterjam)
### at 2011-07-20 23:50:09
### original <http://www.pjhome.net/default.asp?id=1036>

<p><img style="display:block;margin-left:auto;margin-right:auto" title="blog picture template.artx 530x185 px.png" border="0" alt="Blog picture template artx 530x185 px" width="527" height="183" src="http://www.pjhome.net/attachments/month_1107/42011720234956.png"></p>
<p>相信所有做前端开发的同学都会经常使用 <span style="text-decoration:underline"><strong>setTimeout(0)</strong></span> 来做很多事情，这个一度成为解决了很多前端疑难杂症的法宝。而且大家也知道 <span style="text-decoration:underline"><strong>setTImeout(0)</strong></span> 的极限在16ms左右。也许很多人没想到这个是一个很严重的问题，但是在w3c性能小组的专家眼里这是一个非常纠结的设置。</p>
<p><strong>那setTimeout和16ms会带来什么问题呢？</strong></p>
<p><span style="font-size:18px"> </span><span style="font-size:20px">1. </span>我们都知道，我们在做页面动画的时候大多数都是在用setTImeout来控制每一帧的动画的， 而多个setTimeout的存在会导致很多次CPU中断调度的开销，为了减少这些开销，我们希望同一个统一的CPU中断调度管理调度单元来管理所有动画，</p>
<p>于是就出现了 <span style="text-decoration:underline"><span style="font-size:16px"><strong>requestAnimationFrame</strong></span></span>。 <span style="text-decoration:underline"><strong>requestAnimationFrame</strong></span> 的出现不仅仅可以解决中断调度的问题，还可以更加优化得统一管理动画单元里dom元素的repaint方式。</p>
<p><span style="font-size:20px">2.</span> 杯具的16ms，在早期。js的callback执行，是依赖CPU的中断来进行控制的，如果两个中断之间时间太短会导致，CPU性能消耗很高，同时影响能耗，于是微软和英特公司为了解决这个问题，就约定每个中断之间的间隔是15.6ms<em>（64 fps）</em>所以就是我们常见的约等于16ms的间隔。不过随着web的要求不断增加，大家对这个要求希望是放宽的态度，于是在高端浏览器，这个性能被提升了4倍左右，所以在chrome，ie10等浏览器，setTimeout的间隔缩短到了 4ms <em>（250 fps）</em>。 但是问题来了，这么高的消耗以传统的方式，并不能从根本上解决CPU的调度问题，而且能耗也会提升40%</p>
<p>于是W3C，提出需要取代 <span style="text-decoration:underline"><strong>setTimeout(0)</strong></span> 的代替品 --- <span style="text-decoration:underline"><span style="font-size:16px"><strong>setImmediate</strong></span></span></p>
<p>不过到底底层如何解决了这个CPU调度，还能保证脚本能够很快执行，这块舜子还在研究，了解的朋友也欢迎一起交流哈。</p>
<p> </p>
<p>这是两个非常好的方法用来取代setTimeout的api，舜子也做了一些小小的实验来验证 requestAnimationFrame,setImmediate 和 setTimeout 之间的执行效率区别，在IE10下可以看到，setImmediate 接口的callback次数可以达到每秒 6000 次的的执行。而requestAnimationFrame主要目的是为了保证动画的圆滑播放，所以基本上是控制在60 fps的范围，而且根据文档介绍，只是一个时钟控制器在进行调度，而且会更加需要来进行按需渲染。</p>
<p><img alt="" src="http://www.pjhome.net/attachments/month_1107/20110720232212.png"></p>
<p><a href="http://www.pjhome.net/web/html5/timing_test.htm">http://www.pjhome.net/web/html5/timing_test.htm</a></p>
<p> </p>
<p> </p>