---
layout: post
title:  "从 RequireJS 到 SeaJS（4）"
date:   2011-10-30 18:31:00
author: lifesinger
categories: program
---

## 从 RequireJS 到 SeaJS（4）
### by lifesinger
### at 2011-10-30 18:31:00
### original <http://lifesinger.wordpress.com/2011/10/30/comparing-requirejs-with-seajs-4/>

<p>理解了 <a href="http://lifesinger.wordpress.com/2011/10/30/compareing-requirejs-wth-seajs-3/">ID 规则</a>，再来看 RequireJS <a href="http://requirejs.org/docs/api.html">API 文档</a> 就轻松多了。</p>
<h2>模块书写格式</h2>
<p>作为模块加载器，需要明确模块应该怎样写，这就是<strong>模块书写格式（Module Authoring Format）</strong>。<br>
对于文件加载器来说，约定非常少，比如 LABjs 只约定文件里不能有 document.write 等语句。</p>
<p>RequireJS 遵守的是 <a href="http://wiki.commonjs.org/wiki/Modules/AsynchronousDefinition">AMD</a> 规范，SeaJS 遵守的是 <a href="https://github.com/seajs/seajs/blob/master/docs/specification.md">Simple Wrappings</a> 规范。</p>
<p>从表面上看，AMD 规范和 Wrappings 规范最大的不同是 factory 函数的参数不一样：</p>
<pre>
// 两者的基本格式都是：
define(id?, denpendencies?, factory);

// 在 AMD 中，factory 的参数由 dependencies 指定：
define(['a'], function(a) {
});

// 在 Wrappings 中，factory 的参数始终是 require, exports, module 三个：
define(function(require, exports, module) {
  var a = require('a');
});
</pre>
<p> </p>
<p>factory 的参数差异，直接导致 AMD 中的模块是立刻执行的，而 Wrappings 中的模块可以等到第一次 require 时才执行。这是当初 CommonJS 社区讨论最为激烈的争执点，彼此形成了不同的派系。理念上的差异，加上其他一些因素，最后直接导致 RequireJS 从 CommonJS 中脱离，自立门户成为了独立的社区。</p>
<p>理念上无对错。<strong>对于 SeaJS 来说，选择的是延迟执行，尽量与 CommonJS 以及 NodeJS 的模型保持一致。</strong>有兴趣的可以阅读我之前的博文：<a href="http://lifesinger.wordpress.com/2011/05/17/the-difference-between-seajs-and-requirejs/">SeaJS 和 RequireJS 的异同</a></p>
<p>补充：每次想起这个话题，当初 CommonJS 社区的激烈讨论就历历在目。也正是因为理念上的差异，让我有了实现 SeaJS 的想法。当初 RequireJS 作者非常强势，虽然后来也支持了 Simplified CommonJS Wrapper, 但 CommonJS 原社区的不少人都不太认可 RequireJS 遵循的规范，并开始推出 BravoJS, FlyScript 等 loader, 可惜的是这些 loader 更新非常缓慢，目前 SeaJS 还算活跃的。</p>
<h2>加载启动</h2>
<p>对于安装了操作系统的计算机，最常用的启动方法是摁一下开机键。<br>
对于浏览器，加载页面的普适方式是在地址栏上输入 url 并回车。</p>
<p>对于 NodeJS, 是在命令行中输入：</p>
<pre>
$ node xxx.js
</pre>
<p> </p>
<p>所有这些都是“启动”。在 RequireJS 和 SeaJS 中，最便捷的启动是：</p>
<pre>
&lt;script src=&quot;loader.js&quot; data-main=&quot;main&quot;&gt;&lt;/script&gt;
</pre>
<p> </p>
<p>RequireJS 可以通过全局 require 方法来启动：</p>
<pre>
require(['path/to/main.js']);
</pre>
<p> <br>
注意：作为启动用的 require, 参数必须是数组，即便只加载一个文件。这是 require 的陷阱之一，是由其设计导致的（在 RequireJS 里，require 还承担了获取模块接口的功能），这种不纯粹的设计是 SeaJS 不认可的。</p>
<p>SeaJS 里，普适的启动方式是：</p>
<pre>
seajs.use('path/to/main');
</pre>
<p> </p>
<p>可以认为 <code>seajs.use('xx')</code> 就是 <code>$ node xx</code>. 启动是和具体加载器相关的，启动之后，模块代码里就不必再出现加载器相关的东西了。比如 node 的模块代码里不会再出现 node. seajs 里，也不推荐在模块代码里出现 seajs （除了初始模块里可以用 <code>seajs.config</code> 进行配置）。</p>
<h2>优化工具</h2>
<p>RequireJS 的优化工具是 r.js<br>
SeaJS 的是打包部署工具 spm</p>
<p>两者设计理念差异很大，在此就不比较了。</p>
<br>  <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gocomments/lifesinger.wordpress.com/596/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/comments/lifesinger.wordpress.com/596/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/godelicious/lifesinger.wordpress.com/596/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/delicious/lifesinger.wordpress.com/596/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gofacebook/lifesinger.wordpress.com/596/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/facebook/lifesinger.wordpress.com/596/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gotwitter/lifesinger.wordpress.com/596/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/twitter/lifesinger.wordpress.com/596/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gostumble/lifesinger.wordpress.com/596/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/stumble/lifesinger.wordpress.com/596/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/godigg/lifesinger.wordpress.com/596/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/digg/lifesinger.wordpress.com/596/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/goreddit/lifesinger.wordpress.com/596/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/reddit/lifesinger.wordpress.com/596/"></a> <img alt="" border="0" src="http://stats.wordpress.com/b.gif?host=lifesinger.wordpress.com&amp;blog=38365&amp;post=596&amp;subd=lifesinger&amp;ref=&amp;feed=1" width="1" height="1">