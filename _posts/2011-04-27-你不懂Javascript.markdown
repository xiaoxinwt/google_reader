---
layout: post
title:  "你不懂Javascript"
date:   2011-04-27 01:45:58
author: yuanyi
categories: program
---

## 你不懂Javascript
### by yuanyi
### at 2011-04-27 01:45:58
### original <http://heikezhi.com/2011/04/26/you-dont-know-javascript/>

<p>过去几年我注意到技术圈一个很奇怪的现象，有太多程序员将那些他们只是有过非常浅显的了解，但其实根本就不懂的技术写到他们的简历中，这个现象几乎每种语言都有，但这其中最严重的就要数Javascript了。</p>
<p><strong>你不知道你不懂</strong></p>
<p>出现这种状况的一个很大的原因就是现如今几乎每个开发者的工作都或多或少要依赖于Javascript，但大多数人并不真的理解这门语言，他们常用的学习方式是复制粘贴，使用这种方式，你永远不会真正学会这门语言，而只能得到一个你已经懂了的假象。根据我过去几年学习和使用Javascript的经验，在你了解之前，你不会知道你其实不懂，这听起来有点绕，你真正需要的就是有个人来告诉你你其实不懂，你需要全面系统的学习。我经常面试一些自豪的将Javascript列在他们简历中的家伙，但他们通常也就只能做个简单的onClick处理或是表单验证。使用jQuery或者Dojo这样的框架当然没什么问题，但是除非你能了解隐藏在这些框架之后的Javascript技术，否则你不可能真正的掌握这些工具包的能量所在，下面就是我划分的从低到高的Javascript知识点，分为基础，中级和高级3个部分，看看你知道多少：</p>
<p><strong>Javascript的基础知识</strong></p>
<ul>
<li>知道基本的编程语法，比如循环，判断，try/catch等等</li>
<li>理解包括多种函数定义以及赋值的方式，包括匿名函数</li>
<li>理解基本的命名空间，全局（window）空间以及对象空间（不包括闭包）</li>
<li>理解上下文的角色以及this变量的使用</li>
<li>理解各种对象以及函数的初始化和声明方式</li>
<li>理解Javascript比较操作符，如&lt;, &gt;, ==, ===，以及对象和字符串比较的原理和对象映射</li>
<li>理解对象属性和函数的数组索引，以及这和真实的数组之间的区别。</li>
</ul>
<p><strong>Javascript中级知识</strong></p>
<ul>
<li>理解定时器，以及它的工作原理，包括何时以及如何使用定时器来异步执行方法调用</li>
<li>关于回调的深度支持，以及如何通过call和apply方法来控制上下文和函数参数传递</li>
<li>理解JSON标记以及eval函数</li>
<li>理解闭包以及他们如何影响你的代码效率</li>
<li>AJAX以及对象序列化</li>
</ul>
<p><strong>Javascript高级知识点</strong></p>
<ul>
<li>理解方法的“arguments’变量，包括如何使用它来通过arguments.length重载函数，以及通过arguments.callee来进行递归调用，需要注意使用这个特性有一定的危险性，因为ECMAScript 5 的Strict模式不支持此功能，但jQuery和Dojo都用到了它。</li>
<li>高级闭包比如 self-memoizing函数，partially applied函数，以及最可爱的 (function(){})()调用。</li>
<li>函数以及HTML prototype，prototype chain，以及如何使用基本的Javascript对象和函数（比如Array）来简化代码。</li>
<li>对象类型以及instanceof的使用</li>
<li>正则表达式和表达式编译</li>
<li>With语句以及为什么不要使用它们</li>
<li>最困难的部分，知道如果利用所有这些工具，并产生处干净，整洁，健壮，快速，可维护以及兼容不同浏览器的代码。</li>
</ul>
<p>这里的最后一点特别重要并且也是最难实现的，因为Javascript天生不够严格的语法，你的程序很容易就会变成意大利面条式的难以维护的灾难代码，一旦你开始学习Javascript语言本身，你只能通过在大型的Web程序中不断实践才能真正掌握它，这可能要花好几年的时间，并且你没法从书本上学到这些，我自己每天使用javascript好几小时，已经好几年了，但我还是在不断寻找更好的方式来编写和重构我的代码，出于这个原因，一开始就使用jQuery这样的框架会很危险，它会很容易让你的代码变得不可维护，Dojo的Class和Package系统则多少会帮上点忙。</p>
<p>考虑到javascript现在已经通过Node.js这样的项目渗透到了后端，我决定将和Web相关的部分单独拉出来讲讲，也就是说，如果你想要在Web开发中使用javascript，那么下面这些就是每一个优秀程序员都应该了解的：</p>
<ul>
<li>如何高效的操作Dom（添加，删除以及更新），还有如何通过使用document fragments这样的工具来最小化浏览器的re-flows。</li>
<li>夸浏览器的DOM元素属性提取（比如，style，position等等），jQuery和Dojo都可以很好的完成这些工作，尽管如此，理解从CSS和style标签中提取属性的差异，以及如何计算position和size还是很重要的。</li>
<li>夸浏览器的事件处理，绑定，反绑定，冒泡，以及如何取得期望的回调上下文。在一次，现成的框架也可以很好的处理这些事情，但是你应该对IE浏览器和W3C标准浏览器之间的不同有所了解。</li>
<li>正则表达式选取DOM节点</li>
<li>浏览器功能检测以及智能降级</li>
</ul>
<p>就像你从上面的列表中看到的，对于Javascript，除了alert（myval）或者myBtn.onclick=…，你还需要了解更多东西，这些都是粘贴复制无法学到的，只有通过阅读和练习，你才能成为一名真正的javascript程序员。这里推荐两本关于Javascript的好书，“the good parts”和“Javascript忍者的秘密”，如果你只是为了装饰简历，我建议你至少也要搞懂基础阶段的那些知识点，并且至少尝试过中极阶段的那些技术。一旦你发现当你开始自己开发你想要的功能，而不是从其他人那里拷贝粘贴时，你就可以宣称自己懂Javascript了，在那之前，请先别这样说。</p>
<p>如果我错过了什么关于Javascript的概念，欢迎给我留言，同时也别忘了分享任何你关于JS或者其它语言的经验。</p>
<p>需要说明的是我并不是一个前端开发者，我实际上是一个后台开发者，但是因为形势需要，渐渐变成了一个全协议栈的开发者，现如今，几乎每个后端开发者都需要了解Javascript，这也是我写这篇文章的目的，我并不是想表达我对Javascript有多了解，我只是想说javascript是一门非常强大和庞杂的语言，它远比你第一眼看到的要复杂。</p>
<p>本文翻译自”<a href="http://www.w2lessons.com/2011/04/you-dont-know-javascript.html">You Don’t Know JavaScript</a>“， 作者：<a href="http://twitter.com/mwbiz">Michael Woloszynowicz</a>，翻译: <a href="http://heikezhi.com/author/yuanyi">@yuanyiz</a></p>
<p>译者注： 因本人对Javascript了解有限，处在这篇文章给出的初级到中级之间，如翻译中有错误和纰漏，还请不吝赐教。</p>
<p>想和我们一道传播黑客精神？<a href="http://heikezhi.com/join">快来加入吧！</a></p>
<table cellspacing="0" cellpadding="3" border="0" style="clear:both">
    
    <tr>
        <td colspan="4"><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">无觅猜您也喜欢：</font></b></td>
    </tr>
    
        <tr>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important">
                    <a title="Handlebars.js入门介绍" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F30%2Fhandlebars-js%25E5%2585%25A5%25E9%2597%25A8%25E4%25BB%258B%25E7%25BB%258D%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F04%2F26%2Fyou-dont-know-javascript%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/03/30/4588419.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Handlebars.js入门介绍</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Javascript版Heroku: Akshell" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F22%2Fakshell-a-heroku-clone-for-javascript%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F04%2F26%2Fyou-dont-know-javascript%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/03/23/4089256.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Javascript版Heroku: Akshell</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="JavaScript版查找树介绍及性能分析" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F26%2Fjavascript-trie-performance-analysis%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F04%2F26%2Fyou-dont-know-javascript%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/03/27/4332087.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">JavaScript版查找树介绍及性能分析</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="所以，你要试试SICP" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fheikezhi.com%2F2011%2F03%2F27%2Fso-you-want-to-do-the-sicp%2F&amp;from=http%3A%2F%2Fheikezhi.com%2F2011%2F04%2F26%2Fyou-dont-know-javascript%2F">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/03/28/4419890.jpg" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">所以，你要试试SICP</font>
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
</table><img src="http://www1.feedsky.com/t1/506230068/heikezhi/feedsky/s.gif?r=http://heikezhi.com/2011/04/26/you-dont-know-javascript/" border="0" height="0" width="0"><p><a href="http://www1.feedsky.com/r/l/feedsky/heikezhi/506230068/art01.html"><img border="0" ismap src="http://www1.feedsky.com/r/i/feedsky/heikezhi/506230068/art01.gif"></a></p>