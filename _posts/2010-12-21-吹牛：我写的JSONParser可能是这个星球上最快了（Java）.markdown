---
layout: post
title:  "吹牛：我写的JSONParser可能是这个星球上最快了（Java）"
date:   2010-12-21 16:36:08
author: 
categories: program
---

## 吹牛：我写的JSONParser可能是这个星球上最快了（Java）
### by 
### at 2010-12-21 16:36:08
### original <http://www.javaeye.com/topic/848392>

我拥有多年手写Parser的经验，在以前，编写过多个Parser，有SQL的Parser，也有JavaScript的Parser，也有Java的Parser。
<br>
<br>在最近的项目中，也需要使用JSON，其中client部分不希望存在依赖，所以就写了一个JSON     Parser。最初是直接使用SimpleJSON的，因为其代码少，接口简洁。一个同事说，SimpleJSON存在性能问题，噢，我想，那算了，我自己写一个吧，JSON语法这么简单，对于有“多年行医经验”（来自典故《日本兵与老中医》）的我，自然不是困难的事情，也就是一个晚上就写完了，应用于项目中，测试结果，网络协议的性能提升了两倍，从原来的80,000 TPS提升到了240,000 TPS。顺带说一个花絮，周六晚上加班写完的，直接就应用到项目中了，虽然有TestCase，但还是有一个地方没注意好，出现了一个Bug，星期一还影响了测试工作，早会的时候被同事血泪控诉。<img src="http://www.iteye.com/images/smiles/icon_sad.gif">
<br>
<br>我跟朋友炫耀了一下，朋友说，为啥不用jackson，jackson速度很快。好吧，我就和jackson做了一个性能比较，在大多数场景都是比jackson性能好很多，但是在某一个场景，竟然比jackson稍慢。我不服气啊，因为一看就觉得jackson的Parser写得不够专业，没理由比我这种多年经验的老鸟快的。跟踪其实现代码找到了其优化的办法，觉得那是取巧，不屑于使用。于是优化一下我写的Parser，优化的结果自然是全面超越它。好吧，废话少说，直接上结果。
<br>
<br>场景1:
<br><pre name="code">[{"S":321061,"T":"GetAttributeResp"},{"ERROR":null,"TS":0,"VAL":{"SqlList":[{"BatchSizeMax":0,"BatchSizeTotal":0,"ConcurrentMax":1,"DataSource":"jdbc:wrap-jdbc:filters=default,encoding:name=ds-offer:jdbc:mysql://100.10.10.10:8066/xxxx","EffectedRowCount":0,"ErrorCount":0,"ExecuteCount":5,"FetchRowCount":5,"File":null,"ID":2001,"LastError":null,"LastTime":1292742908178,"MaxTimespan":16,"MaxTimespanOccurTime":1292742668191,"Name":null,"RunningCount":0,"SQL":"SELECT @@SQL_MODE","TotalTime":83}]}}] </pre>
<br>fast-json 	: 8,608,334 (我写的parser)
<br>jackson 	: 17,172,500
<br>simple-json : 36,258,008 
<br>
<br>
<br>场景2：
<br><pre name="code">{"S":321061,"T":"GetAttributeResp"}</pre>
<br>fast-json 	: 1,028,024
<br>jackson 	: 6,673,850
<br>simple-json : 20,294,257
<br>
<br>场景3：
<br><div>引用</div><div>{"name":null,"flag":true}</div>
<br>fast-json 	: 842,032
<br>jackson 	: 7,284,100
<br>simple-json : 20,553,188
<br>
<br>场景4：
<br><pre name="code">[-5.041598256063065E-20,-7210028408342716000]</pre>
<br>fast-json 	: 1,338,822
<br>jackson 	: 10,000,239
<br>simple-json : 20,202,679
<br>
<br>场景5：
<br><pre name="code">-6470204979932713723</pre>
<br>fast-json 	: 389,004
<br>jackson 	: 5,405,129
<br>simple-json : 20,418,252
<br>
<br>以上的结果都是执行一千次的时间总和，单位是nano。连续执行测试50次，取最后一次的结果，让JVM能够充分优化。JVM的参数是-server。
<br>
<br>结论，越简单的对象，偶写的parser（fast-json）性能越好。如场景3，都快差不多100倍了。
<br>
<br>好了，吹牛结束啦，有什么后续动作呢？有同事建议开源，嗯，这个建议挺好的，我喜欢用开源的东西，也希望对开源有所贡献，我整理好代码之后，就会开源出来。
<br>
<br>注明，我写的Parser是能通过JSON官方的兼容性测试的，不是缺乏功能的残废！<img src="http://www.iteye.com/images/smiles/icon_smile.gif">
          
          <br><br>
          作者: <a href="http://wenshao.javaeye.com">wenshao</a> 
          <br>
          声明: 本文系JavaEye网站发布的原创文章，未经作者书面许可，严禁任何网站转载本文，否则必将追究法律责任！
          <br><br>
          <span style="color:red">
            <a href="http://www.javaeye.com/topic/848392" style="color:red">已有 <strong>73</strong> 人发表回复，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">JavaEye推荐</span>
<br>
<ul><li><a href="http://www.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>