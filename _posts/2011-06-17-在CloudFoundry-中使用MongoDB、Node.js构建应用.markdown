---
layout: post
title:  "在CloudFoundry 中使用MongoDB、Node.js构建应用"
date:   2011-06-17 10:50:34
author: nosqlfan
categories: program
---

## 在CloudFoundry 中使用MongoDB、Node.js构建应用
### by nosqlfan
### at 2011-06-17 10:50:34
### original <http://item.feedsky.com/~feedsky/nosqlfan/~8149226/524010759/6253001/1/item.html>

<p><span><a href="http://blog.nosqlfan.com/tags/cloudfoundry" title="查看 CloudFoundry 的全部文章">CloudFoundry</a></span> 是<span><a href="http://blog.nosqlfan.com/tags/vmware" title="查看 VMware 的全部文章">VMware</a></span>公司前段时间发布的云计算平台，NoSQL存储<span><a href="http://blog.nosqlfan.com/tags/mongodb" title="查看 MongoDB 的全部文章">MongoDB</a></span>和Redis都在上面有很好的支持，下面是一篇10gen官方博客中的文章，算是一篇在<span><a href="http://blog.nosqlfan.com/tags/cloudfoundry" title="查看 CloudFoundry 的全部文章">CloudFoundry</a></span>平台中使用<span><a href="http://blog.nosqlfan.com/tags/mongodb" title="查看 MongoDB 的全部文章">MongoDB</a></span>和Node.js 搭建应用的入门教程，步骤详尽，描述清楚，如果你对云平台感兴趣，不妨一看。</p>
<h2>1.工欲善其事</h2>
<p>第一步当然是在平台上注册并安装各种工具了，按照教程中的指导，先完成环境搭建</p>
<ol>
<li><a href="http://cloudfoundry.com/signup">Sign up for a Cloud Foundry account</a>.</li>
<li><a href="http://www.mongodb.org/display/DOCS/Quickstart">Local installation of MongoDB</a> &amp; <a href="https://github.com/joyent/node/wiki/Installation">Node.JS</a>.</li>
<li>Cloud Foundry <a href="http://support.cloudfoundry.com/entries/20012337-getting-started-guide-command-line-vmc-users">VMC tools</a>.</li>
<li>All of the code is available on <a href="https://github.com/gatesvp/cloudfoundry_node_mongodb">github</a>.</li>
</ol>
<p>在本地启动一个<span><a href="http://blog.nosqlfan.com/tags/mongodb" title="查看 MongoDB 的全部文章">MongoDB</a></span>，这样你就能在本地时用本地的MongoDB，在上传代码后用云服务中的MongoDB了。</p>
<h2>2.<span><a href="http://blog.nosqlfan.com/tags/vmc" title="查看 vmc 的全部文章">vmc</a></span>登录</h2>
<pre>mongo@ubuntu:~$ sudo gem install vmc
mongo@ubuntu:~$ vmc target api.cloudfoundry.com
Succesfully targeted to [http://api.cloudfoundry.com]

mongo@ubuntu:~$ vmc login
Email: gates@10gen.com
Password: ********
Successfully logged into [http://api.cloudfoundry.com]</pre>
<h2>3.Hello World程序</h2>
<p>创建一个叫<a href="https://github.com/gatesvp/cloudfoundry_node_mongodb/blob/master/app.js.1">app.js</a>的程序，输入如下代码，这段代码会在localhost:3000端口上启动一个输出“Hello World”字样的HTTP服务。</p>
<pre>var port = (process.env.VMC_APP_PORT || 3000);
var host = (process.env.VCAP_APP_HOST || 'localhost');
var http = require('http');

http.createServer(function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello World\n');
}).listen(port, host);</pre>
<p>在本地命令行下测试：</p>
<pre>$ node app.js
$ curl localhost:3000
Hello World
# kill node with CTRL+C</pre>
<p>好，没有问题，那么将代码部署到云服务，使用<span><a href="http://blog.nosqlfan.com/tags/vmc" title="查看 vmc 的全部文章">vmc</a></span>的push方法：</p>
<pre>$ vmc push
Would you like to deploy from the current directory? [Yn]: Y
Application Name: gvp_node_test
Application Deployed URL: 'gvp_node_test.cloudfoundry.com'?
Detected a Node.js Application, is this correct? [Yn]: Y
Memory Reservation [Default:64M] (64M, 128M, 256M, 512M, 1G or 2G)
Creating Application: OK
Would you like to bind any services to 'gvp_node_test'? [yN]: y
The following system services are available::
1. mongodb
2. mysql
3. redis
Please select one you wish to provision: 1
Specify the name of the service [mongodb-55516]:
Creating Service: OK
Binding Service: OK
Uploading Application:
  Checking for available resources: OK
  Packing application: OK
  Uploading (0K): OK
Push Status: OK
Staging Application: OK
Starting Application: OK</pre>
<p>现在你使用curl命令就能在平台上测试你的服务了</p>
<pre>$ curl your_app_name.cloudfoundry.com
Hello World</pre>
<h2>4.用MongoDB记录客户端信息</h2>
<p>下一步开始配置MongoDB，先加入下面<a href="https://github.com/gatesvp/cloudfoundry_node_mongodb/blob/master/app.js.2">代码</a>，这个if else判断是因为在本地和云端都需要测试，用于区分不同的环境。</p>
<pre>if(process.env.VCAP_SERVICES){
  var env = JSON.parse(process.env.VCAP_SERVICES);
  var mongo = env['mongodb-1.8'][0]['credentials'];
}
else{
  var mongo = {"hostname":"localhost","port":27017,"username":"",
    "password":"","name":"","db":"db"}
}</pre>
<p>再加入一个将配置转成MongoDB url的形式即可。代码请看<a href="https://github.com/gatesvp/cloudfoundry_node_mongodb/blob/master/app.js.2">这里</a>。</p>
<p>然后将刚刚更新的内容同步到平台，运行如下命令：</p>
<pre>$ vmc update your_app_name
Uploading Application:
...
Stopping Application: OK
Staging Application: OK
Starting Application: OK
# test again
$ curl your_app_name.cloudfoundry.com
# bunch of environment variables</pre>
<p>然后安装<span><a href="http://blog.nosqlfan.com/tags/node-js" title="查看 node.js 的全部文章">node.js</a></span>的mongodb扩展</p>
<pre>$ npm install mongodb</pre>
<p>奖安装好的扩展包含进来，然后将具体的HTTP处理脚本改成如下的方式，这个功能是将IP和访问时间记录到MongoDB中</p>
<pre>var record_visit = function(req, res){
  /* Connect to the DB and auth */
  require('mongodb').connect(mongourl, function(err, conn){
    conn.collection('ips', function(err, coll){
      /* Simple object to insert: ip address and date */
      object_to_insert = { 'ip': req.connection.remoteAddress, 'ts': new Date() };

      /* Insert the object then print in response */
      /* Note the _id has been created */
      coll.insert( object_to_insert, {safe:true}, function(err){
        res.writeHead(200, {'Content-Type': 'text/plain'});
        res.write(JSON.stringify(object_to_insert));
        res.end('\n');
      });
    });
  });
}</pre>
<p>再启动服务，访问两次，应该能在你的mongodb里找到对应的记录</p>
<pre>http.createServer(function (req, res) {
  record_visit(req, res);
}).listen(port, host);</pre>
<p>在<a href="https://github.com/gatesvp/cloudfoundry_node_mongodb/blob/master/app.js.3">这里</a>可以看到这一步的代码</p>
<h2>5.获取MongoDB中的访问记录</h2>
<p>我们记录了登录IP和时间，下面再增加一个功能，获取IP和时间，添加如下函数</p>
<pre>var print_visits = function(req, res){
  /* Connect to the DB and auth */
  require(&#39;mongodb&#39;).connect(mongourl, function(err, conn){
    conn.collection(&#39;ips&#39;, function(err, coll){
      /*find with limit:10 &amp; sort */
      coll.find({}, {limit:10, sort:[[&#39;_id&#39;,&#39;desc&#39;]]}, function(err, cursor){
        cursor.toArray(function(err, items){
          res.writeHead(200, {&#39;Content-Type&#39;: &#39;text/plain&#39;});
          for(i=0; i &lt; items.length; i++){
            res.write(JSON.stringify(items[i]) + &quot;\n&quot;);
          }
          res.end();
        });
      });
    });
  });
}</pre>
<p>然后修改httpserver相关的<span><a href="http://blog.nosqlfan.com/tags/nodejs" title="查看 nodejs 的全部文章">nodejs</a></span>代码，使其支持一个/history的访问，返回最近的十条访问记录：</p>
<pre>http.createServer(function (req, res) {
  params = require('url').parse(req.url);
  if(params.pathname === '/history') {
    print_visits(req, res);
  }
  else{
    record_visit(req, res);
  }
}).listen(port, host);</pre>
<p>代码可以在<a href="https://github.com/gatesvp/cloudfoundry_node_mongodb/blob/master/app.js.4">这里</a>看到。然后再把修改的代码部署上去进行测试，可以看到如下输出。</p>
<pre>$ vmc update your_app_name
...
$ curl your_app_name.cloudfoundry.com
{"ip":"172.30.49.42","ts":"2011-06-15T20:14:18.977Z","_id":"4df9129af354f8682d000001"}
$ curl your_app_name.cloudfoundry.com
{"ip":"172.30.49.43","ts":"2011-06-15T20:14:21.745Z","_id":"4df9129df354f8682d000002"}

# now let's test history
$ curl gvp_node_test.cloudfoundry.com/history
{"ip":"172.30.49.43","ts":"2011-06-15T20:14:21.745Z","_id":"4df9129df354f8682d000002"}
{"ip":"172.30.49.42","ts":"2011-06-15T20:14:18.977Z","_id":"4df9129af354f8682d000001"}
...</pre>
<h2>6.更多</h2>
<ol>
<li>关注 <a href="http://www.10gen.com/webinars/nodejs">Node.js Panel Discussion webinar</a>.</li>
<li>还有下面一些MongoDB的node客户端封装
<ul>
<li><a href="http://mongoosejs.com/">Mongoose</a>, an ORM / ODM wrapper</li>
<li><a href="http://github.com/craftgear/node-mongoskin">MongoSkin</a>, a layer over node-mongodb-native to help reduce callbacks.</li>
</ul>
</li>
<li>还可以用 <a href="http://expressjs.com/">Express framework</a> 框架建立更复杂的web应用</li>
</ol>
<p>原文链接：<a href="http://blog.mongodb.org/post/6587009156/cloudfoundry-mongodb-and-nodejs">Getting started with VMware CloudFoundry, MongoDB and Node.js</a>
<p style="font-weight:bold"><span style="padding-top:10px;float:left">觉得文章还不错？快分享给更多的人吧！</span><a href="http://twitter.com/share?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2044.html&amp;text=%E5%9C%A8CloudFoundry%20%E4%B8%AD%E4%BD%BF%E7%94%A8MongoDB%E3%80%81Node.js%E6%9E%84%E5%BB%BA%E5%BA%94%E7%94%A8%20@nosqlfan" title="Twitter" style="text-decoration:none;margin:2px"><img src="http://pic.yupoo.com/iammutex/B8hVKEJk/ga3Zw.png"></a><a href="http://v.t.sina.com.cn/share/share.php?title=%E5%9C%A8CloudFoundry%20%E4%B8%AD%E4%BD%BF%E7%94%A8MongoDB%E3%80%81Node.js%E6%9E%84%E5%BB%BA%E5%BA%94%E7%94%A8%20@nosqlfan%20&amp;url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2044.html" title="新浪微博" style="text-decoration:none;margin:2px"><img src="http://pic.yupoo.com/iammutex/B8hVKrzm/b6giQ.png"></a><a href="http://v.t.qq.com/share/share.php?title=%E5%9C%A8CloudFoundry%20%E4%B8%AD%E4%BD%BF%E7%94%A8MongoDB%E3%80%81Node.js%E6%9E%84%E5%BB%BA%E5%BA%94%E7%94%A8%20@nosqlfan%20&amp;url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2044.html" title="腾讯微博" style="text-decoration:none;margin:2px"><img src="http://pic.yupoo.com/iammutex/B8hVJX6o/HMY8k.png"></a><a href="http://sns.qzone.qq.com/cgi-bin/qzshare/cgi_qzshare_onekey?title=%E5%9C%A8CloudFoundry%20%E4%B8%AD%E4%BD%BF%E7%94%A8MongoDB%E3%80%81Node.js%E6%9E%84%E5%BB%BA%E5%BA%94%E7%94%A8%20@nosqlfan%20&amp;url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2044.html" title="QQ空间" style="text-decoration:none;margin:2px"><img src="http://pic.yupoo.com/iammutex/B8hVJSKI/6hVj7.png"></a><a href="http://www.douban.com/recommend/?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2044.html&amp;title=%E5%9C%A8CloudFoundry%20%E4%B8%AD%E4%BD%BF%E7%94%A8MongoDB%E3%80%81Node.js%E6%9E%84%E5%BB%BA%E5%BA%94%E7%94%A8%20@nosqlfan" title="豆瓣9点" style="text-decoration:none;margin:2px"><img src="http://pic.yupoo.com/iammutex/B8hVJrri/SB2B.png"></a><a href="http://xianguo.com/service/submitdigg?link=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2044.html&amp;title=%E5%9C%A8CloudFoundry%20%E4%B8%AD%E4%BD%BF%E7%94%A8MongoDB%E3%80%81Node.js%E6%9E%84%E5%BB%BA%E5%BA%94%E7%94%A8%20@nosqlfan%20&amp;content=utf-8" title="鲜果" style="text-decoration:none;margin:2px"><img src="http://pic.yupoo.com/iammutex/B8hVJ4v4/3CHaH.png"></a><a href="http://share.renren.com/share/buttonshare.do?link=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2044.html" title="人人网" style="text-decoration:none;margin:2px"><img src="http://pic.yupoo.com/iammutex/B8hVI86k/1yDki.png"></a><a href="http://www.facebook.com/sharer.php?u=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2044.html&amp;title=%E5%9C%A8CloudFoundry%20%E4%B8%AD%E4%BD%BF%E7%94%A8MongoDB%E3%80%81Node.js%E6%9E%84%E5%BB%BA%E5%BA%94%E7%94%A8%20@nosqlfan" title="FaceBook" style="text-decoration:none;margin:2px"><img src="http://pic.yupoo.com/iammutex/B8hVHr67/ftAKQ.png"></a></p>
<table cellspacing="0" cellpadding="3" border="0" style="clear:both">
    
    <tr>
        <td colspan="5"><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">您可能还喜欢：</font></b></td>
    </tr>
    
        <tr>
                <td width="106" valign="top" style="padding:5px!important;margin:0!important">
                    <a title="在 VMware CloudFoundry 平台上构建 MongoDB ＋ Rails 应用" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F1635.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2044.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:100px!important;height:100px!important" src="http://static.wumii.com/site_images/2011/06/10/11691672.png" width="100px" height="100px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:106px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">在 VMware CloudFoundry 平台上构建 MongoDB ＋ Rails 应用</font>
                    </a>
                </td>
                <td width="106" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="*nix、node.js、MongoDB 下一代的LAMP" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F639.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2044.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:100px!important;height:100px!important" src="http://pic.yupoo.com/iammutex/B8sPBDdj/9MymB.png" width="100px" height="100px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:106px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">*nix、node.js、MongoDB 下一代的LAMP</font>
                    </a>
                </td>
                <td width="106" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="VMware构建云平台，MongoDB成核心存储服务" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F1598.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2044.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:100px!important;height:100px!important" src="http://pic.yupoo.com/iammutex/B8sPBDdj/9MymB.png" width="100px" height="100px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:106px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">VMware构建云平台，MongoDB成核心存储服务</font>
                    </a>
                </td>
                <td width="106" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Instagram的实时图片Demo：Node.js, Redis 加 Web Sockets" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2007.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2044.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:100px!important;height:100px!important" src="http://static.wumii.com/site_images/2011/06/16/12611411.jpg" width="100px" height="100px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:106px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Instagram的实时图片Demo：Node.js, Redis 加 Web Sockets</font>
                    </a>
                </td>
                <td width="106" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="使用Node.Js 操作 Riak" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect.htm?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F1549.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2044.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:100px!important;height:100px!important" src="http://pic.yupoo.com/iammutex/B8sPBDdj/9MymB.png" width="100px" height="100px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:106px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">使用Node.Js 操作 Riak</font>
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
</table><img src="http://www1.feedsky.com/t1/524010759/nosqlfan/feedsky/s.gif?r=http://item.feedsky.com/~feedsky/nosqlfan/~8149226/524010759/6253001/1/item.html" border="0" height="0" width="0"></p>