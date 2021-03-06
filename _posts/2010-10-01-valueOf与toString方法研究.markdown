---
layout: post
title:  "valueOf与toString方法研究"
date:   2010-10-01 23:25:00
author: 司徒正美
categories: program
---

## valueOf与toString方法研究
### by 司徒正美
### at 2010-10-01 23:25:00
### original <http://www.cnblogs.com/rubylouvre/archive/2010/10/01/1839748.html>

<p><a href="http://www.cnblogs.com/rubylouvre/"><img src="http://pic.cnblogs.com/face/u65123.jpg" alt="" border="0"></a><br>作者: <a href="http://www.cnblogs.com/rubylouvre/">司徒正美</a> 发表于 2010-10-01 23:25 <a href="http://www.cnblogs.com/rubylouvre/archive/2010/10/01/1839748.html">原文链接</a> 阅读: 856 评论: 0</p><p>最后群里讨论一些怪题，有高手就想歪脑想hack这两个东西了，虽然最后失败了，但这精神是非常值得鼓励的。于是决定写一篇文章专门来介绍它们。</p>
<p>基本上，所有JS数据类型都拥有这两个方法，null除外。它们俩解决javascript值运算与显示的问题。</p>
<p>先看一例：</p>
<pre>
//by 司徒正美
      var aaa = {
        i: 10,
        valueOf: function() { return this.i+30; },
        toString: function() { return this.valueOf()+10; }
      }
      alert(aaa &gt; 20); // true
      alert(+aaa); // 40
      alert(aaa); // 50
</pre>
 
      var aaa = {
        i: 10,
        valueOf: function() { return this.i+30; },
        toString: function() { return this.valueOf()+10; }
      }
      alert(aaa &gt; 20); // true
      alert(+aaa); // 40
      alert(aaa); // 50
 
<p>运行代码</p>
<p>之所以有这样的结果，因为它们偷偷地调用valueOf或toString方法。但如何区分什么情况下是调用了哪个方法呢，我们可以通过另一个方法测试一下。由于用到console.log，请在装有firebug的FF中实验！</p>
<pre>
      var bbb = {
        i: 10,
        toString: function() {
          console.log('toString');
          return this.i;
        },
        valueOf: function() {
          console.log('valueOf');
          return this.i;
        }
      }
      
      alert(bbb);// 10 toString
      alert(+bbb); // 10 valueOf
      alert(''+bbb); // 10 valueOf
      alert(String(bbb)); // 10 toString
      alert(Number(bbb)); // 10  valueOf
      alert(bbb == '10'); // true valueOf
      alert(bbb === '10'); // false 
</pre>
 
      var bbb = {
        i: 10,
        toString: function() {
          console.log('toString');
          return this.i;
        },
        valueOf: function() {
          console.log('valueOf');
          return this.i;
        }
      }
      
      alert(bbb);// 10 toString
      alert(+bbb); // 10 valueOf
      alert(''+bbb); // 10 valueOf
      alert(String(bbb)); // 10 toString
      alert(Number(bbb)); // 10  valueOf
      alert(bbb == '10'); // true valueOf
      alert(bbb === '10'); // false
 
<p>运行代码</p>
<p>乍一看结果，大抵给人的感觉是，如果转换为字符串时调用toString方法，如果是转换为数值时则调用valueOf方法，但其中有两个很不和谐。一个是alert(''+bbb)，字符串合拼应该是调用toString方法……另一个我们暂时可以理解为===操作符不进行隐式转换，因此不调用它们。为了追究真相，我们需要更严谨的实验。</p>
<pre>
      var aa = {
        i: 10,
        toString: function() {
          console.log('toString');
          return this.i;
        }
      }

      alert(aa);// 10 toString
      alert(+aa); // 10 toString
      alert(''+aa); // 10 toString
      alert(String(aa)); // 10 toString
      alert(Number(aa)); // 10 toString
      alert(aa == '10'); // true toString
</pre>
 
      var aa = {
        i: 10,
        toString: function() {
          console.log('toString');
          return this.i;
        }
      }

      alert(aa);// 10 toString
      alert(+aa); // 10 toString
      alert(''+aa); // 10 toString
      alert(String(aa)); // 10 toString
      alert(Number(aa)); // 10 toString
      alert(aa == '10'); // true toString
 
<p>运行代码</p>
<p>再看valueOf。</p>
<pre>
     var bb = {
        i: 10,
        valueOf: function() {
          console.log('valueOf');
          return this.i;
        }
      }

      alert(bb);// [object Object]
      alert(+bb); // 10 valueOf
      alert(''+bb); // 10 valueOf
      alert(String(bb)); // [object Object]
      alert(Number(bb)); // 10 valueOf
      alert(bb == '10'); // true valueOf
</pre>
 
      var bb = {
        i: 10,
        valueOf: function() {
          console.log('valueOf');
          return this.i;
        }
      }

      alert(bb);// [object Object]
      alert(+bb); // 10 valueOf
      alert(''+bb); // 10 valueOf
      alert(String(bb)); // [object Object]
      alert(Number(bb)); // 10 valueOf
      alert(bb == '10'); // true valueOf
 
<p>运行代码</p>
<p>发现有点不同吧？！它没有像上面toString那样统一规整。对于那个[object Object]，我估计是从Object那里继承过来的，我们再去掉它看看。</p>
<pre>
      Object.prototype.toString = null;

      var cc = {
        i: 10,
        valueOf: function() {
          console.log('valueOf');
          return this.i;
        }
      }

      alert(cc);// 10 valueOf
      alert(+cc); // 10 valueOf
      alert(''+cc); // 10 valueOf
      alert(String(cc)); // 10 valueOf
      alert(Number(cc)); // 10 valueOf
      alert(cc == '10'); // true valueOf
</pre>
 
      Object.prototype.toString = null;

      var cc = {
        i: 10,
        valueOf: function() {
          console.log('valueOf');
          return this.i;
        }
      }

      alert(cc);// 10 valueOf
      alert(+cc); // 10 valueOf
      alert(''+cc); // 10 valueOf
      alert(String(cc)); // 10 valueOf
      alert(Number(cc)); // 10 valueOf
      alert(cc == '10'); // true valueOf
 
<p>运行代码</p>
<p>如果只重写了toString，对象转换时会无视valueOf的存在来进行转换。但是，如果只重写了valueOf方法，在要转换为字符串的时候会优先考虑valueOf方法。在不能调用toString的情况下，只能让valueOf上阵了。对于那个奇怪的字符串拼接问题，可能是出于操作符上，翻开ECMA262-5 发现都有一个getValue操作。嗯，那么谜底应该是揭开了。重写会加大它们调用的优化高，而在有操作符的情况下，valueOf的优先级本来就比toString的高。</p>

<img src="http://www.cnblogs.com/rubylouvre/aggbug/1839748.html?type=1" width="1" height="1" alt=""><p>评论: 0　<a href="http://www.cnblogs.com/rubylouvre/archive/2010/10/01/1839748.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/rubylouvre/archive/2010/10/01/1839748.html#commentform">发表评论</a></p><p><a href="http://job.cnblogs.com/">程序员找工作，就在博客园</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/76309/">评论：百度开放正逐步走向“百度局域网”</a><span style="color:gray">(2010-10-03 22:28)</span><br>· <a href="http://news.cnblogs.com/n/76308/">LG拟本月开售Android智能手机Optimus One</a><span style="color:gray">(2010-10-03 22:21)</span><br>· <a href="http://news.cnblogs.com/n/76307/">联通建成全球最大WCDMA网络 3G用户破千万</a><span style="color:gray">(2010-10-03 22:03)</span><br>· <a href="http://news.cnblogs.com/n/76306/">陈永正从NBA“退赛”：三年业绩增长未达预期</a><span style="color:gray">(2010-10-03 22:03)</span><br>· <a href="http://news.cnblogs.com/n/76305/">日科学家山中伸弥或问鼎本年度诺贝尔医学奖</a><span style="color:gray">(2010-10-03 21:54)</span><br></p><p>编辑推荐：<a href="http://news.cnblogs.com/n/76302/">2010年10月编程语言排行榜：Java的混乱之治</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">个人主页</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://space.cnblogs.com">社区</a>  <a href="http://kb.cnblogs.com">知识库</a></p>