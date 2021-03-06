---
layout: post
title:  "超简单的MapReduce Javascript实现"
date:   2011-05-18 10:15:25
author: yuanyi
categories: program
---

## 超简单的MapReduce Javascript实现
### by yuanyi
### at 2011-05-18 10:15:25
### original <http://heikezhi.com/2011/05/17/simple-mapreduce-with-javascript/>

<p>要理解MapReduce，最好的办法就是通过它的实现来了解它的工作原理，<a href="http://www.makuchaku.in/">Mayank Jain</a>为我们提供了一个可能是<a href="http://www.makuchaku.in/blog/simple-mapreduce-with-javascript">最简单的Javascript实现</a>，通过这个实现，你会发现，MapReduce最核心的理念其实并不复杂(<a href="https://gist.github.com/961172">Gist链接</a>）。</p>
<pre name="code">
var Job = {

  data : [
          &quot;We are glad to see you here. This site is dedicated to&quot;,
          &quot;poetry and to the people who make poetry possible&quot;,
          &quot;poets and their readers. FamousPoetsAndPoems.com is&quot;,
          &quot;a free poetry site. On our site you can find a large&quot;,
          &quot;collection of poems and quotes from over 631 poets&quot;,
          &quot;Read and Enjoy Poetry&quot;,
          &quot;I, too, sing America&quot;,
          &quot;I am the darker brother&quot;,
          &quot;They send me to eat in the kitchen&quot;,
          &quot;When company comes&quot;,
          &quot;But I laugh&quot;,
          &quot;And eat well&quot;,
          &quot;And grow strong&quot;,
          &quot;Tomorrow&quot;,
          &quot;Ill be at the table&quot;,
          &quot;When company comes&quot;,
          &quot;Nobodyll dare&quot;,
          &quot;Say to me&quot;,
          &quot;Eat in the kitchen&quot;,
          &quot;Then&quot;,
          &quot;Besides&quot;,
          &quot;Theyll see how beautiful I am&quot;,
          &quot;And be ashamed&quot;,
          &quot;I, too, am America&quot;
        ],

  map : function(line) {
    var splits = line.split(&quot; &quot;);
    var temp = [];
    for(var i=0; i&lt;splits.length; i++)
    {
      temp.push({key : splits[i], value : 1});
    }
    return temp;
  },

  reduce : function(allSteps) {
    var result = {};
    for(var i=0; i&lt;allSteps.length; i++)
    {
      var step = allSteps[i];
      result[step.key] = result[step.key] ? (result[step.key] + 1) : 1;
    }
    return result;
  },

  init : function() {
    var allSteps = [];
    for(var i=0; i&lt;Job.data.length; i++)
      allSteps = allSteps.concat(Job.map(Job.data[i]));

    var result = Job.reduce(allSteps)
    console.log(JSON.stringify(result));
  }

}; // Job

Job.init();
</pre>
<p>下面是代码的运行结果：</p>
<pre name="code">
{"631":1,"We":1,"are":1,"glad":1,"to":5,"see":2,"you":2,"here.":1,"This":1,"site":2,"is":2,"dedicated":1,"poetry":3,"and":4,"the":5,"people":1,"who":1,"make":1,"possible":1,"poets":2,"their":1,"readers.":1,"FamousPoetsAndPoems.com":1,"a":2,"free":1,"site.":1,"On":1,"our":1,"can":1,"find":1,"large":1,"collection":1,"of":1,"poems":1,"quotes":1,"from":1,"over":1,"Read":1,"Enjoy":1,"Poetry":1,"I,":2,"too,":2,"sing":1,"America":2,"I":3,"am":3,"darker":1,"brother":1,"They":1,"send":1,"me":2,"eat":2,"in":2,"kitchen":2,"When":2,"company":2,"comes":2,"But":1,"laugh":1,"And":3,"well":1,"grow":1,"strong":1,"Tomorrow":1,"Ill":1,"be":2,"at":1,"table":1,"Nobodyll":1,"dare":1,"Say":1,"Eat":1,"Then":1,"Besides":1,"Theyll":1,"how":1,"beautiful":1,"ashamed":1}
</pre>
<p>想和我们一道传播黑客精神？<a href="http://heikezhi.com/join">快来加入吧！</a></p>
<table cellspacing="0" cellpadding="3" border="0" style="clear:both">
    
    <tr>
        <td colspan="4"><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">无觅猜您也喜欢：</font></b></td>
    </tr>
    
        <tr>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important">
                    <a title="Handlebars.js入门介绍" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F30%2Fhandlebars-js%25E5%2585%25A5%25E9%2597%25A8%25E4%25BB%258B%25E7%25BB%258D%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F05%2F17%2Fsimple-mapreduce-with-javascript%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/03/30/4588419.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Handlebars.js入门介绍</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="JavaScript版查找树介绍及性能分析" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F26%2Fjavascript-trie-performance-analysis%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F05%2F17%2Fsimple-mapreduce-with-javascript%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/03/27/4332087.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">JavaScript版查找树介绍及性能分析</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Javascript版Heroku: Akshell" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F22%2Fakshell-a-heroku-clone-for-javascript%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F05%2F17%2Fsimple-mapreduce-with-javascript%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/03/23/4089256.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Javascript版Heroku: Akshell</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Traceur: 体验下一代Javascript语言" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F05%2F06%2Ftraceur-compiler-next-to-javascript-of-today%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F05%2F17%2Fsimple-mapreduce-with-javascript%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/images/blogWidget/wordpress_default.gif" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Traceur: 体验下一代Javascript语言</font>
                    </a>
                </td>
        </tr>
    
    <tr>
        <td colspan="4" align="right">
            <a style="text-decoration:none!important" href="http://www.wumii.com/widget/relatedItems.htm" title="无觅相关文章插件">
                <font size="-1" color="#bbbbbb" style="display:block!important;font-family:arial!important;padding:5px 0!important;font-size:12px!important;color:#bbb!important">无觅</font>
            </a>
        </td>
    </tr>
</table><img src="http://www1.feedsky.com/t1/518923423/heikezhi/feedsky/s.gif?r=http://heikezhi.com/2011/05/17/simple-mapreduce-with-javascript/" border="0" height="0" width="0">