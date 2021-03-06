---
layout: post
title:  "just another event model"
date:   2011-06-08 20:47:20
author: 
categories: program
---

## just another event model
### by 
### at 2011-06-08 20:47:20
### original <http://yiminghe.iteye.com/blog/1073547>

<p>事件模型也算是客户端兼容性的一个长期问题，早期 jquery 作者甚至因为这方面<a href="http://ejohn.org/projects/flexible-javascript-events/">获过奖</a>
？一般来说 <a href="http://msdn.microsoft.com/en-us/library/ms536343%28v=vs.85%29.aspx">attachEvent </a>
以及 <a href="http://www.w3.org/TR/DOM-Level-2-Events/events.html#Events-EventTarget-addEventListener">addEventListener</a>
 已经基本够用，特别是 attachEvent 可以<a href="http://www.davidflanagan.com/2006/10/ie-event-objects-and-attachevent.html">给回调传递事件参数</a>
了，不用考虑怪异的 <a href="http://msdn.microsoft.com/en-us/library/ms535863%28v=vs.85%29.aspx">window.event</a>
</p>
<p> </p>
<pre name="code">div.onclick=function(){
alert(window.event==window.event); // =&gt; false
setTimeout(function(){
  alert(window.event) // =&gt; null
},0);
}</pre>
 
<p> </p>
<p>可仍然不是完美的.</p>
<p> </p>
<p>attachEvent 传入的参数似乎仅仅是 window.event 的指针：</p>
<p> </p>
<pre name="code">document.getElementById("input1").attachEvent("onclick",function(e){
        var e2=e;
        alert(e2==e);
        setTimeout(function(){
            alert(e2==e);
            alert(e2.type);
            alert(e.type);
        },10);
    });</pre>
<p> </p>
<p>并且只在当前事件处理器同步处理内有效，一旦异步读取就会抛出 error</p>
<p> </p>
<p> </p>
<h1>attachEvent ：</h1>
<p> </p>
<p>1. 没有 <a href="http://msdn.microsoft.com/en-us/library/ff975962%28v=vs.85%29.aspx">event.currentTarget</a>
 ，重用函数就不行了</p>
<p> </p>
<p>2.回调中 this <a href="http://www.quirksmode.org/js/events_advanced.html">无意义得指向</a>
 window .</p>
<p> </p>
<p>3.多个回调绑定同一事件，触发顺序竟然是 random</p>
<p> </p>
<p>4.标准事件缺失：<a href="http://www.w3.org/TR/html5/history.html#event-hashchange">hashchange</a>
</p>
<p> </p>
<p>5.需要模拟一些有用的事件：valuechange</p>
<p> </p>
<p>6.修正：其实这种方式不会导致循环引用而造成内容泄露，节点并没有属性指向绑定的事件函数。</p>
<p> </p>
<h2>addEventListener</h2>
<p> </p>
<p>1.标准控制下缺失了不少有用事件，需要事件模型来进行辅助模拟： <a href="http://msdn.microsoft.com/en-us/library/ms536935%28v=vs.85%29.aspx">focusin</a>
/out , <a href="http://msdn.microsoft.com/en-us/library/ms536945%28v=vs.85%29.aspx">mouseenter</a>
/leave</p>
<p> </p>
<p>2. this 死板，只能是当前监听元素</p>
<p> </p>
<h1>JAEM</h1>
<p> </p>
<p>so 在巨人( jquery )的肩上，kissy 有了个<a href="https://github.com/kissyteam/kissy/tree/master/src/event">不一样的事件模型</a>
 :</p>
<p> </p>
<p><img src="http://dl.iteye.com/upload/attachment/495174/b5066619-0cdb-347d-b659-d4a89a6ab880.png" alt=""></p>
<p> </p>
<p> </p>
<p>区分普通对象（继承 EventTarget）的自定义事件监听与触发以及 dom 节点的原生事件监听与触发.</p>
<p> </p>
<p> </p>
<p>1.每个元素每个对象对应一个系统事件回调，系统回调关联当前注册源解决 currentTarget 与 this，normalize 后调用用户事件回调</p>
<p> </p>
<p>2.通过绑定时传入 scope 来实现用户事件回调定制 this</p>
<p> </p>
<p>3.自身实现用户回调队列，保证用户回调触发顺序（先进先出）</p>
<p> </p>
<p>4.通过钩子实现特殊事件，模拟事件的无缝接入。</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
              
              <br><br>
              <span style="color:red">
                <a href="http://yiminghe.iteye.com/blog/1073547#comments" style="color:red">已有 <strong>2</strong> 人发表留言，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
              </span>
              <br><br><br>
<span style="color:#e28822">ITeye推荐</span>
<br>
<ul><li><a href="http://yiminghe.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>