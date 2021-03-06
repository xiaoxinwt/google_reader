---
layout: post
title:  "MongoDB的JavaScript性能"
date:   2011-07-31 11:11:21
author: nosqlfan
categories: program
---

## MongoDB的JavaScript性能
### by nosqlfan
### at 2011-07-31 11:11:21
### original <http://item.feedsky.com/~feedsky/nosqlfan/~8149226/543387933/6253001/1/item.html>

<p>两个多月前，我与下文作者<a href="http://www.dulao5.com/">dulao5</a>兄有过一次关于<span><a href="http://blog.nosqlfan.com/tags/mongodb" title="查看 MongoDB 的全部文章">MongoDB</a></span> JavaScript执行效率的聊天，昨天dulao5完成此文，将链接发给我，我看完后对测试结果很是震惊，下面是原文，欢迎对MongoDB JavaScript引擎有研究的同学共同探讨。</p>
<p>原文：<a href="http://www.dulao5.com/javascript/2011/07/30/mongodb-javascript-performance.html">www.dulao5.com</a></p>
<blockquote>
<h3>mongodb的db.eval</h3>
<p>mongodb使用<span><a href="http://blog.nosqlfan.com/tags/javascript" title="查看 javascript 的全部文章">javascript</a></span>做shell, mongodb的db.eval可以提供给数据驱动与这种javascript shell类似的js接口. 这算是一种移动代码风格(Mobile Code Sytles)的架构设计吧. 我期望可以利用这个接口, 将一些处理逻辑放在mongodb的节点上运行. 避免在PHP里面多次获取和操作mongodb的数据. (这个需求类似传统数据库的存储过程)</p>
<p>但是测试却发现, 在mongodb里面跑javascript性能很不理想. 我写了一段测试代码, 跑10000次循环:</p>
<pre>&lt;?php
$mongo = new Mongo(&quot;mongodb://localhost:20237/&quot;);
$db = $mongo-&gt;selectDB(&quot;attl&quot;);

$echo_func = &lt;&lt;&lt;JAVASCRIPT
function() {
        var str = &quot;xxxxxxxxxxxxxxxxx0000000000&quot;;
        var data = str + str + str + str;
        var data = data + data + data + data;
        var max = 10000;
        var arr = [];
        var total = 0;

        for(var a=0; a&lt;100; a++) {
                for(var i=0; i&lt;max; i++) {
                        arr.push( data + &quot; . &quot; + data);
                }
                for(var i=0; i&lt;arr.length; i++) {
                        total += arr[i].length;
                }
                arr = [];
        }
        return total;
}
JAVASCRIPT;

$bgtime = microtime(true);

$echo_code = new MongoCode($echo_func);
$obj = (object)array(&quot;name&quot;=&gt;&quot;dzg&quot;, &quot;values&quot;=&gt;array(1,2,3), &quot;bool&quot;=&gt;true) ;
$arr = array(1,2,3,4,5,6,7,8);
$map = array(&quot;a&quot;=&gt;1, &quot;b&quot;=&gt;2);
$response = $db-&gt;execute($echo_code, array($obj, $arr, $map));
var_dump($response);

$endtime = microtime(true);

echo &quot;\n time : &quot;.($endtime - $bgtime).&quot;\n&quot;;</pre>
<p>执行 :</p>
<pre>php ab-mongo.php
array(2) {
  [&quot;retval&quot;]=&gt;  float(867000000)
  [&quot;ok&quot;]=&gt;  float(1)
}

time : 6.0353651046753</pre>
<p>这段js居然执行<span style="color:#ff0000">6秒</span>之久! 在其他环境下测试同样功能代码:</p>
<ul>
<li>Google Chrome : 只需<span style="color:#ff0000">70毫秒</span></li>
<li>Firefox 5 : 只需<span style="color:#ff0000">180毫秒</span></li>
<li>用PHP 5执行类似代码: 只需<span style="color:#ff0000">630毫秒</span></li>
</ul>
<p>显然相比以上测试, mongodb的javascript性能差太大了.</p>
<h3>排除测试干扰</h3>
<p>根据mongodb官方文档 Server-side Code Execution : Limitations of eval &gt; Write locks 一节, 我怀疑也许是Write locks导致的. 但这种怀疑和本例关系不大, locks应该只影响并发执行, 现在的问题是一次执行都这么慢.</p>
<p>另外, 我还担心从PHP到mongodb的js代码传递环节慢.</p>
<p>于是花了两种手段排除这两个因素:</p>
<p>使用 db.system.js.save({_id: “mytestfunc”, value: function () { … }}); 的方式, 将测试的js代码作成mongodb支持的Stored JavaScript</p>
<p>使用 db.runCommand({$eval: function() {return mytestfunc();}, nolock: true}) 的方式, 进行nolock调用</p>
<p>结果发现问题依旧. 结论是mongodb的JavaScript执行环节有问题</p>
<h3>测试Mongodb + <span><a href="http://blog.nosqlfan.com/tags/v8" title="查看 V8 的全部文章">V8</a></span> JavaScript引擎</h3>
<p>我知道Mongodb的JavaScript引擎是SpiderMonkey, 于是想尝试再编译一个v8版本的Mongodb, 准备用如下JavaScript文件测试一下.</p>
<p>准备测试代码</p>
<pre>function dotest() {
        var str = &quot;xxxxxxxxxxxxxxxxx0000000000&quot;;
        var data = str + str + str + str;
        var data = data + data + data + data;
        var max = 10000;
        var arr = [];
        var total = 0;

        for(var a=0; a&lt;100; a++) {
                for(var i=0; i&lt;max; i++) {
                        arr.push( data + &quot; . &quot; + data);
                }
                for(var i=0; i&lt;arr.length; i++) {
                        total += arr[i].length;
                }
                arr = [];
        }
        return total;
}

myecho = (typeof console !== &#39;undefined&#39; &amp;&amp; typeof console.log == &#39;function&#39;) ? console.log : print;

a = new Date();
myecho(&quot;begin:\t&quot; + a);

myecho(&quot;result:\t&quot; + dotest());

b = new Date();
myecho(&quot;end:\t&quot; + b);

myecho(&quot;total time:\t&quot; + (b - a));</pre>
<p>这个JavaScript脚本能同时跑在4种环境下:</p>
<ul>
<li>Chrome / Firefox等浏览器环境</li>
<li>node js的shell</li>
<li>SpiderMonkey 的js shell</li>
<li>mongo 的js shell</li>
</ul>
<p>我准备编译完Mongodb的v8版本后, 四个环境都比较测试一下.</p>
<h3>编译mongodb + v8</h3>
<p>我在OpenSuse11.4下, 用linux的包管理工具准备编译环境, 还是很容易的:</p>
<p>#已安装的包: g++ subversion git python</p>
<pre>sudo zypper install scons tcsh boost-devel pcre-devel readline-devel

svn checkout http://v8.googlecode.com/svn/trunk/ v8
cd v8
scons
cd ..

git clone git://github.com/mongodb/mongo.git
cd mongov8
scons

#run mongodb v8
./mongod --quiet --shardsvr --dbpath /home/dzg/data/mongodb/test/mongod --port 20237 --nohttpinterface --fork --pidfilepath /home/dzg/data/mongodb/test/mongod.pid --logpath /home/dzg/log/mongodb/test/mongod.log --logappend</pre>
<h3>执行测试</h3>
<p>执行~/opt/mongov8/mongo localhost:20237 dotest.js</p>
<ul>
<li>mongo+v8 : <span style="color:#ff0000">93毫秒</span></li>
<li>nodejs : <span style="color:#ff0000">68毫秒</span></li>
<li>SpiderMonkey : <span style="color:#ff0000">442毫秒</span></li>
</ul>
<p>初步结论:</p>
<ul>
<li>mongodb + v8性能非常好, 接近node.js里面的执行性能</li>
<li>SpiderMonkey 1.9 比v8慢5倍</li>
<li>mongodb使用SpiderMonkey肯定有一些环节还存在问题, 导致mongodb+SpiderMonkey比mongodb+v8慢60多倍
<ul>
<li>也许是mongodb的SpiderMonkey版本低? 我目前还不知道mongodb的发行版内, SpiderMonkey的确切版本</li>
<li>我上面测试SpiderMonkey 的js命令是来自xulrunner-192</li>
</ul>
</li>
<li>测试代码很短, 比较片面, 上面的性能倍数只是大体估计, 不是全面精确的评估</li>
</ul>
</blockquote>
<p style="font-weight:bold"><span style="padding-top:5px;float:left">技术传播，需要你我共同努力！</span><a href="http://twitter.com/share?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2637.html&amp;text=MongoDB%E7%9A%84JavaScript%E6%80%A7%E8%83%BD%20@nosqlfan" title="Twitter" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVKEJk/custom.png"></a><a href="http://v.t.sina.com.cn/share/share.php?title=MongoDB%E7%9A%84JavaScript%E6%80%A7%E8%83%BD%20@nosqlfan%20&amp;url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2637.html" title="新浪微博" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVKrzm/custom.png"></a><a href="http://v.t.qq.com/share/share.php?title=MongoDB%E7%9A%84JavaScript%E6%80%A7%E8%83%BD%20@nosqlfan%20&amp;url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2637.html" title="腾讯微博" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVJX6o/custom.png"></a><a href="http://sns.qzone.qq.com/cgi-bin/qzshare/cgi_qzshare_onekey?title=MongoDB%E7%9A%84JavaScript%E6%80%A7%E8%83%BD%20@nosqlfan%20&amp;url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2637.html" title="QQ空间" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVJSKI/custom.png"></a><a href="http://www.douban.com/recommend/?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2637.html&amp;title=MongoDB%E7%9A%84JavaScript%E6%80%A7%E8%83%BD%20@nosqlfan" title="豆瓣9点" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVJrri/custom.png"></a><a href="http://xianguo.com/service/submitdigg?link=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2637.html&amp;title=MongoDB%E7%9A%84JavaScript%E6%80%A7%E8%83%BD%20@nosqlfan%20&amp;content=utf-8" title="鲜果" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVJ4v4/custom.png"></a><a href="http://share.renren.com/share/buttonshare.do?link=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2637.html" title="人人网" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVI86k/custom.png"></a><a href="http://www.facebook.com/sharer.php?u=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2637.html&amp;title=MongoDB%E7%9A%84JavaScript%E6%80%A7%E8%83%BD%20@nosqlfan" title="FaceBook" style="text-decoration:none;margin:2px;border:none"><img style="border:none;padding:0px" src="http://pic.yupoo.com/iammutex/B8hVHr67/custom.png"></a>    </p>
<table cellspacing="0" cellpadding="3" border="0" style="clear:both">
    
    <tr>
        <td colspan="5"><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">相关文章：</font></b></td>
    </tr>
    
        <tr>
                <td width="106" valign="top" style="padding:5px!important;margin:0!important">
                    <a title="MongoDB paddingFactor的含义" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2654.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2637.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:100px!important;height:100px!important" src="http://pic.yupoo.com/iammutex/B8sPBDdj/square.jpg" width="100px" height="100px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:106px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">MongoDB paddingFactor的含义</font>
                    </a>
                </td>
                <td width="106" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="MongoDB1.6版本与最新1.8版本性能测试——写入篇" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F1603.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2637.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:100px!important;height:100px!important" src="http://static.wumii.com/site_images/2011/06/09/11640869.jpg" width="100px" height="100px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:106px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">MongoDB1.6版本与最新1.8版本性能测试——写入篇</font>
                    </a>
                </td>
                <td width="106" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="性能测试：MongoDB vs. SQL Server" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F484.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2637.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:100px!important;height:100px!important" src="http://static.wumii.com/site_images/2011/06/09/11639017.png" width="100px" height="100px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:106px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">性能测试：MongoDB vs. SQL Server</font>
                    </a>
                </td>
                <td width="106" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="MongoDB、HandlerSocket和MySQL性能测试及其结果分析" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F1329.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2637.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:100px!important;height:100px!important" src="http://static.wumii.com/site_images/2011/06/10/11643952.jpg" width="100px" height="100px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:106px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">MongoDB、HandlerSocket和MySQL性能测试及其结果分析</font>
                    </a>
                </td>
                <td width="106" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="MongoDB 与 CouchDB 全方位对比" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F1519.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2637.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:100px!important;height:100px!important" src="http://pic.yupoo.com/iammutex/B8sPBDdj/square.jpg" width="100px" height="100px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:106px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">MongoDB 与 CouchDB 全方位对比</font>
                    </a>
                </td>
        </tr>
    
    <tr>
        <td colspan="5" align="right">
            <a style="text-decoration:none!important" href="http://www.wumii.com/widget/relatedItems.htm" title="无觅相关文章插件">
                <font size="-1" color="#bbbbbb" style="display:block!important;font-family:arial!important;padding:5px 0!important;font-size:12px!important;color:#bbb!important">无觅</font>
            </a>
        </td>
    </tr>
</table><img src="http://www1.feedsky.com/t1/543387933/nosqlfan/feedsky/s.gif?r=http://item.feedsky.com/~feedsky/nosqlfan/~8149226/543387933/6253001/1/item.html" border="0" height="0" width="0">