---
layout: post
title:  "flex的pureMVC+Fabrication的使用例子--HelloWorld"
date:   2010-09-24 23:02:09
author: 
categories: program
---

## flex的pureMVC+Fabrication的使用例子--HelloWorld
### by 
### at 2010-09-24 23:02:09
### original <http://www.javaeye.com/topic/770710>

<p style="margin:0cm 0cm 0pt"><span><span style="font-size:small">我还是新手哈，有很多地方可能写的不好，不要见怪。<br><br> 第一步：</span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span lang="EN-US"><span style="font-family:Calibri">1. </span></span><span>创建我们的</span><span lang="EN-US"><span style="font-family:Calibri">Flex4</span></span><span>的</span><span lang="EN-US"><span style="font-family:Calibri">Web</span></span><span>项目，需要导入的</span><span lang="EN-US"><span style="font-family:Calibri">lib</span></span><span>如下图所示</span></span></p>
<p style="margin:0cm 0cm 0pt"> </p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span><img src="http://dl.javaeye.com/upload/attachment/315822/6d03fedf-3023-32a5-ab3b-4f58dd520107.jpg" alt=""></span></span></p>
<p style="margin:0cm 0cm 0pt"> </p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span lang="EN-US"><span style="font-family:Calibri">2. </span></span><span>看看我们该如何构建</span><span lang="EN-US"><span style="font-family:Calibri">Fabrication</span></span><span>下的</span><span lang="EN-US"><span style="font-family:Calibri">flex</span></span><span>项目，以及弄清楚各个层的作用和关系，简单的可以看看下面的图</span></span></p>
<p style="margin:0cm 0cm 0pt"> </p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span lang="EN-US"><span style="font-family:Calibri"><br><img src="http://dl.javaeye.com/upload/attachment/315824/10339d3e-724f-328c-851b-3467aa197f95.jpg" alt=""><br> </span></span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span lang="EN-US"><span style="font-family:Calibri">3. </span></span><span>首先我们应该明白的是基于</span><span lang="EN-US"><span style="font-family:Calibri">pureMVC</span></span><span>的</span><span lang="EN-US"><span style="font-family:Calibri">Fabrication</span></span><span>框架下的原理是如何运作的，看看下面的该项目的运作流程图：</span></span></p>
<p><span lang="EN-US">
<p style="margin:0cm 0cm 0pt"> </p>
<table><tbody><tr>
<td style="border-bottom:#f0f0f0;border-left:#f0f0f0;background-color:transparent;border-top:#f0f0f0;border-right:#f0f0f0">
<div>
<p style="margin:0cm 0cm 0pt"><span lang="EN-US"></span></p>
</div>
</td>
</tr></tbody>
<tbody><tr>
<td style="border-bottom:#f0f0f0;border-left:#f0f0f0;background-color:transparent;border-top:#f0f0f0;border-right:#f0f0f0">
<div>
<p style="text-align:center;margin:0cm 0cm 0pt" align="center"> </p>
</div>
</td>
</tr></tbody>
<tbody><tr>
<td style="border-bottom:#f0f0f0;border-left:#f0f0f0;background-color:transparent;border-top:#f0f0f0;border-right:#f0f0f0">
<div>
<p style="margin:0cm 0cm 0pt"> </p>
</div>
</td>
</tr></tbody>
<tbody><tr>
<td style="border-bottom:#f0f0f0;border-left:#f0f0f0;background-color:transparent;border-top:#f0f0f0;border-right:#f0f0f0">
<div>
<p style="text-align:center;margin:0cm 0cm 0pt" align="center"><span style="font-family:Calibri"><strong></strong></span></p>
</div>
</td>
</tr></tbody>
<tbody><tr>
<td style="border-bottom:#f0f0f0;border-left:#f0f0f0;background-color:transparent;border-top:#f0f0f0;border-right:#f0f0f0">
<div>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"></span></p>
</div>
</td>
</tr></tbody>
<tbody><tr>
<td style="border-bottom:#f0f0f0;border-left:#f0f0f0;background-color:transparent;border-top:#f0f0f0;border-right:#f0f0f0">
<div>
<p style="text-align:center;margin:0cm 0cm 0pt" align="center"><span lang="EN-US"></span></p>
</div>
</td>
</tr></tbody>
<tbody><tr>
<td style="border-bottom:#f0f0f0;border-left:#f0f0f0;background-color:transparent;border-top:#f0f0f0;border-right:#f0f0f0">
<div>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span lang="EN-US"></span></span></p>
</div>
</td>
</tr></tbody>
<tbody><tr>
<td style="border-bottom:#f0f0f0;border-left:#f0f0f0;background-color:transparent;border-top:#f0f0f0;border-right:#f0f0f0">
<div>
<p style="text-align:center;margin:0cm 0cm 0pt" align="center"><span lang="EN-US"></span></p>
</div>
</td>
</tr></tbody>
<td><span style="font-family:Calibri"></span></td></table></span></p>
<p> <br><img src="http://dl.javaeye.com/upload/attachment/315826/c77b3a46-f203-3fa6-a3b4-f102ec99e286.jpg" alt=""><br> </p>
<p> </p>
<p> </p>
<p> </p>
<p> <br><img src="http://dl.javaeye.com/upload/attachment/315828/fbb0d72b-2943-3999-8b0b-3c4dc0ca64d9.jpg" alt=""><br>  </p>
<p style="margin:0cm 0cm 0pt"><span><span style="font-size:small">第二：</span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span>其中需要说明一下的是，</span><span lang="EN-US"><span style="font-family:Calibri">Fabrication</span></span><span>的</span><span style="font-family:Calibri"><span style="color:#00b050">reactTo</span><span style="color:red">TestButton1</span><span lang="EN-US">$<span style="color:#7030a0">Click</span>(evt:Event)</span></span><span>方法的命名原因：</span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span lang="EN-US"><span style="font-family:Calibri">1. <span style="color:#00b050">reactTo</span></span></span><span>开头是响应</span><span lang="EN-US"><span style="font-family:Calibri">View</span></span><span>层那些组件的事件的绑定的规则</span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span lang="EN-US"><span style="font-family:Calibri">2. <span style="color:red">TestButton1</span></span></span><span>是</span><span lang="EN-US"><span style="font-family:Calibri">component</span></span><span>包下的</span><span lang="EN-US"><span style="font-family:Calibri">HelloWorldGroup.mxml</span></span><span>的按钮</span><span lang="EN-US"><span style="font-family:Calibri">ID</span></span><span>，可看源代码如图所示：</span></span></p>
<p style="margin:0cm 0cm 0pt"> </p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span lang="EN-US"><span style="font-family:Calibri"><br><img src="http://dl.javaeye.com/upload/attachment/315834/aae8be19-b1ef-34d8-9bb0-bffe837e9b8a.jpg" alt=""><br>  </span></span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span lang="EN-US"><span style="font-family:Calibri">3. <span style="color:#7030a0">Click</span></span></span><span>是指绑定该组件</span><span lang="EN-US"><span style="font-family:Calibri">(</span></span><span>这里是</span><span lang="EN-US"><span style="font-family:Calibri">Button)</span></span><span>的点击事件，只要是</span><span lang="EN-US"><span style="font-family:Calibri">Event</span></span><span>都可以绑定，后面那个美元符号＄就是间隔区分的。可以看看工具中显示的那些组件有什么事件类型：</span></span></p>
<p style="margin:0cm 0cm 0pt"> </p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span><img src="http://dl.javaeye.com/upload/attachment/315830/0e7891d4-ffed-384b-96f9-926208c84d40.jpg" alt=""></span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span>这里的都可以绑定写成相关的方法，例如我要改成是测试消息</span><span lang="EN-US"><span style="font-family:Calibri">2</span></span><span>按钮的双击的事件响应，命名规范则是：</span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-family:Calibri"><span style="font-size:small"><span style="color:#00b050">reactTo</span><span style="color:red">TestButton2</span><span lang="EN-US">$<span style="color:#7030a0">DoubleClick</span></span></span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span>特别要注意的就是大小写的规范和方法的属性都是</span><span lang="EN-US"><span style="font-family:Calibri">public</span></span><span>的。</span></span></p>
<p style="margin:0cm 0cm 0pt"><span lang="EN-US"><span style="font-family:Calibri;font-size:small"> </span></span></p>
<p style="margin:0cm 0cm 0pt"><span><span style="font-size:small">第三：就是响应消息的机制是如何处理的解释。</span></span></p>
<p style="margin:0cm 0cm 0pt"><span><span style="font-size:small">消息响应有两种方式，</span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span>第一种方式例如该项目中的方式，就是通过</span><span style="background:fuchsia" lang="EN-US"><span style="font-family:Calibri">namespace</span></span><span>命名空间的方式响应。</span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span lang="EN-US"><span style="font-family:Calibri">1. </span></span><span>首先看看我们命名空间中是如何定义代码的：</span></span></p>
<p style="text-align:left;margin:0cm 0cm 0pt" align="left"><strong><span style="font-family:&#39;Courier New&#39;;color:#9900cc;font-size:10pt">package</span></strong><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> flex.helloworld.notifications</span></p>
<p style="text-align:left;margin:0cm 0cm 0pt" align="left"><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt">{</span></p>
<p style="text-align:left;margin:0cm 0cm 0pt" align="left"><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"><span>    </span></span><strong><span style="font-family:&#39;Courier New&#39;;color:#0033ff;font-size:10pt">public</span></strong><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> </span><strong><span style="font-family:&#39;Courier New&#39;;color:#0033ff;font-size:10pt">namespace</span></strong><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> message2 = </span><strong><span style="font-family:&#39;Courier New&#39;;color:#990000;font-size:10pt">"message2"</span></strong><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt">;</span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt">}</span></p>
<p style="margin:0cm 0cm 0pt"><span>很简单</span><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt">o(</span><span>∩</span><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt">_</span><span>∩</span><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt">)o </span><span>哈哈。这里是说我们定义一个我们将要用到的消息变量。</span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span lang="EN-US"><span style="font-family:Calibri">2. </span></span><span>在</span><span lang="EN-US"><span style="font-family:Calibri">Mediator</span></span><span>里面响应发过来的消息：</span></span></p>
<p style="text-align:left;margin:0cm 0cm 0pt" align="left"><span style="font-family:&#39;Courier New&#39;;background:silver;color:black;font-size:10pt">message2</span><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> </span><strong><span style="font-family:&#39;Courier New&#39;;color:#339966;font-size:10pt">function</span></strong><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> processNotification(note:INotification):</span><strong><span style="font-family:&#39;Courier New&#39;;color:#0033ff;font-size:10pt">void</span></strong><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> {</span></p>
<p style="text-align:left;margin:0cm 0cm 0pt" align="left"><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"><span>            </span>helloWorldProxy.createMessage(note.getBody() </span><strong><span style="font-family:&#39;Courier New&#39;;color:#0033ff;font-size:10pt">as</span></strong><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> String);</span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"><span>        </span>}</span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span>处理不同的消息，唯一要改的就是</span><span lang="EN-US"><span style="font-family:Calibri">message2</span></span><span>这个命名空间，写该方法的时候，在</span><span lang="EN-US"><span style="font-family:Calibri">mediator</span></span><span>里面必须导入</span><span lang="EN-US"><span style="font-family:Calibri">message2</span></span><span>那个命名空间的包，看代码：</span></span></p>
<p style="margin:0cm 0cm 0pt"><strong><span style="font-family:&#39;Courier New&#39;;color:#0033ff;font-size:10pt">import</span></strong><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> flex.helloworld.notifications.message2;</span></p>
<p style="margin:0cm 0cm 0pt"><span><span style="font-size:small">好了。</span></span><strong><span style="font-family:&#39;Courier New&#39;;color:#339966;font-size:10pt">function</span></strong><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> processNotification(note:INotification)</span><span style="font-size:small"><span>这些都是一样的，现在例如我们要接受</span><span style="font-family:&#39;Courier New&#39;;color:black">message1</span><span>的消息，那个接受消息的方法要怎么写，相信你已经会了。</span></span></p>
<p style="margin:0cm 0cm 0pt"><span>如下所示：</span></p>
<p style="text-align:left;margin:0cm 0cm 0pt" align="left"><span style="font-family:&#39;Courier New&#39;;background:silver;color:black;font-size:10pt">message1</span><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> </span><strong><span style="font-family:&#39;Courier New&#39;;color:#339966;font-size:10pt">function</span></strong><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> processNotification(note:INotification):</span><strong><span style="font-family:&#39;Courier New&#39;;color:#0033ff;font-size:10pt">void</span></strong><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> {</span></p>
<p style="text-align:left;margin:0cm 0cm 0pt" align="left"><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"><span>            </span>helloWorldProxy.createMessage(note.getBody() </span><strong><span style="font-family:&#39;Courier New&#39;;color:#0033ff;font-size:10pt">as</span></strong><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> String);</span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"><span>        </span>}</span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span>是的，就这样第一种接受消息的方式处理完了。</span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-family:&#39;Courier New&#39;;color:black"><span style="font-size:small"> </span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span>现在说一下第二种方式响应相关的消息的解释：</span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span>第二种方式也是跟命名方式有关的，我们在</span><span lang="EN-US"><span style="font-family:Calibri">Mediator</span></span><span>里面接收消息</span><span lang="EN-US"><span style="font-family:Calibri">message1</span></span><span>的方法如下：</span></span></p>
<p style="text-align:left;margin:0cm 0cm 0pt" align="left"><strong><span style="font-family:&#39;Courier New&#39;;background:silver;color:#0033ff;font-size:10pt">public</span></strong><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> </span><strong><span style="font-family:&#39;Courier New&#39;;color:#339966;font-size:10pt">function</span></strong><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> respondToMessage1():</span><strong><span style="font-family:&#39;Courier New&#39;;color:#0033ff;font-size:10pt">void</span></strong><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"> {</span></p>
<p style="text-align:left;margin:0cm 0cm 0pt" align="left"><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"><span>            </span>//</span><span>。。。。。</span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt"><span>        </span>}</span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span>其中</span><span lang="EN-US"><span style="font-family:Calibri">respondTo</span></span><span>也是固定不变的，后面接着的就是定义好消息常量：</span></span></p>
<p style="margin:0cm 0cm 0pt"><span lang="EN-US"><span style="font-family:Calibri;font-size:small">Public static const MESSAGE1 = “message1”;</span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span>是跟字符串</span><span lang="EN-US"><span style="font-family:Calibri">message1</span></span><span>相匹配的。但是这种方式还要定义一个消息常量类，把那些消息常量定义进去，具体的就不说了，可以去</span><span lang="EN-US"><span style="font-family:Calibri">Fabrication</span></span><span>在</span><span lang="EN-US"><span style="font-family:Calibri">google</span></span><span>上的官网是什么样的，有很多例子，下一个看看就知道了。我就只说我们用到的方式。</span></span></p>
<p style="margin:0cm 0cm 0pt"><span lang="EN-US"><span><span style="font-family:Calibri;font-size:small"> </span></span></span></p>
<p style="margin:0cm 0cm 0pt"><span><span style="font-size:small">看看我们运行的程序吧：</span></span></p>
<p style="margin:0cm 0cm 0pt"> </p>
<p style="margin:0cm 0cm 0pt"><span><span style="font-size:small"><br><img src="http://dl.javaeye.com/upload/attachment/315839/05a96f25-ac40-3732-83de-8ee5b72cad70.jpg" alt=""><br> </span></span></p>
<p style="margin:0cm 0cm 0pt"><span><span style="font-size:small">点击以后，得到响应：在下面现实消息内容：</span></span></p>
<p style="margin:0cm 0cm 0pt"> </p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span><br><img src="http://dl.javaeye.com/upload/attachment/315841/1575c4f3-2465-3f1a-88eb-ae5c20cdcfee.jpg" alt=""><br> </span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span>对了，我们发消息时在同一个模块内发消息是通过</span><span lang="EN-US"><span style="font-family:Calibri">sendNotification()</span></span><span>这个</span><span lang="EN-US"><span style="font-family:Calibri">API</span></span><span>的。</span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-size:small"><span>如果在不同的模块发消息，例如要从这个模块的</span><span lang="EN-US"><span style="font-family:Calibri">flash</span></span><span>文件模块发送到外部的另一个</span><span lang="EN-US"><span style="font-family:Calibri">flash</span></span><span>模块，则用到的是</span></span><span style="font-family:&#39;Courier New&#39;;background:silver;color:black;font-size:10pt">routeNotification</span><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt">()</span><span>这个</span><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt">API</span><span>，</span><span style="font-size:small"><span>具体的参数请自己查阅啦。</span></span></p>
<p style="margin:0cm 0cm 0pt"><span style="font-family:&#39;Courier New&#39;;color:black"><span style="font-size:small"> </span></span></p>
<p style="margin:0cm 0cm 0pt"><span><span style="font-size:small">好了基于</span></span><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt">pureMVC</span><span>的</span><span style="font-family:&#39;Courier New&#39;;color:black;font-size:10pt">Fabrication</span><span><span style="font-size:small">框架其中一个方式的使用介绍就说完了。</span></span></p>
<p> </p>
          
  <br><br>
  <ul>
    本文附件下载:
    
      <li><a href="http://dl.javaeye.com/topics/download/7d6c4d5d-2483-3182-9791-10ca9e2718eb">helloworld.rar</a> (2.5 MB)</li>
    
  </ul>

          <br><br>
          作者: <a href="http://hay.javaeye.com">hay</a> 
          <br>
          声明: 本文系JavaEye网站发布的原创文章，未经作者书面许可，严禁任何网站转载本文，否则必将追究法律责任！
          <br><br>
          <span style="color:red">
            <a href="http://www.javaeye.com/topic/770710" style="color:red">已有 <strong>0</strong> 人发表回复，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">JavaEye推荐</span>
<br>
<ul><li><a href="http://www.iteye.com/clicks/138"><span style="color:red;font-weight:bold">上海：高薪诚聘php开发人员</span></a></li><li><a href="http://www.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li><li><a href="http://www.iteye.com/clicks/439"><span style="color:blue;font-weight:bold">限时报名参加Oracle技术大会</span></a></li></ul>
<br><br><br>