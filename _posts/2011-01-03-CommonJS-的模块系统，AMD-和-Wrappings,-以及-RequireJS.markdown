---
layout: post
title:  "CommonJS 的模块系统，AMD 和 Wrappings, 以及 RequireJS"
date:   2011-01-03 23:29:07
author: lifesinger
categories: program
---

## CommonJS 的模块系统，AMD 和 Wrappings, 以及 RequireJS
### by lifesinger
### at 2011-01-03 23:29:07
### original <http://lifesinger.org/blog/2011/01/commonjs-amd-wrappings-and-requirejs/>

<p>与 LABjs, HeadJS, ControlJS 等 script loader 不同，<a href="http://requirejs.org/">RequireJS</a> 是 module loader. 什么是 module 呢？</p>
<h3>CommonJS 模块系统</h3>
<p>孟岩在 <a href="http://blog.csdn.net/myan/archive/2010/10/09/5928531.aspx">救赎</a> 一文中提出，在对象范式里，一个对象系统的构建有两个基本观念：</p>
<ol>
<li>程序是由<strong>对象</strong>组成的；</li>
<li>对象之间互相<strong>发送消息</strong>，协作完成任务。</li>
</ol>
<p>以上观念，可以普适化。对于一般系统的构建，可以归结为：</p>
<ol>
<li>系统由系统<strong>成员</strong>组成；</li>
<li>成员之间互相<strong>通讯</strong>，协作完成任务。</li>
</ol>
<p>系统成员可大可小，可方可扁，可以从不同角度去定义。当成员是对象时，构建的是对象范式系统。当成员是模块时，就是模块系统。</p>
<p>在 <a href="http://wiki.commonjs.org/wiki/Modules/1.1.1">CommonJS Modules/1.1.1</a> 规范里，定义了构建 CommonJS 模块系统所需要的最小特性：</p>
<ol>
<li>module 拥有 id, uri 属性；在 module 中，有 require, exports, module 三个自由变量；</li>
<li>module 可通过 require 引入外部 module. 通过 exports 等方式向外部提供 api.</li>
</ol>
<p>上面的第 1 点，定义了模块的基本属性和上下文环境，第 2 点定义了模块之间的通讯方式。来看一个例子：<br>
<span></span><br>
module a:</p>
<pre>
exports.add = function(n, m) {
  return n + m;
};
</pre>
<p>module b:</p>
<pre>
var add = require('a').add;
exports.increment = function(n) {
  return add(n, 1);
};
</pre>
<p>module main:</p>
<pre>
var inc = require('b').increment;
inc(1);

module.id = 'main';
</pre>
<p>有了这些基本特性，我们就可以在此基础上构建任意复杂的模块系统了。</p>
<h3>模块传送</h3>
<p>Modules/1.1.1 规范里，只定义了模块的基本特性，并没有定义模块的存在形态。比如上面例子中的 module a, 可以是文件系统中的 a.js, 也可以是数据库中的某个字段，或者仅是封装在闭包里的一段代码。在服务器端，最常见的场景是一个模块一个文件，上面的例子中：</p>
<p>module b:</p>
<pre>
var add = require('a').add;
...
</pre>
<p>可以用伪代码表示为：</p>
<pre>
var content = 读取文件 a.js 的内容;
var api = 解析 content 的内容;
var add = api.add;
...
</pre>
<p>在服务器端，比如 Node.JS 环境下，文件读取操作是同步的，因此上面的代码不会有问题。模块的通讯，不需要引入更多概念。</p>
<p>但在浏览器端，事情不那么美妙。直接通过 script 引入模块文件，明显不能满足模块系统的基本特性。为了让模块能在不同的环境下都适用，CommonJS 需要定义 Module/Transport 规范。Module/Transport（模块传送），可以同步也可以异步。如果同步，我们可以很容易想到下面的实现方式：</p>
<pre>
&lt;script src=&quot;require.js&quot;&gt;&lt;/script&gt;
&lt;script src=&quot;mod-a.js&quot;&gt;&lt;/script&gt;
&lt;script src=&quot;mod-b.js&quot;&gt;&lt;/script&gt;
&lt;script src=&quot;mod-main.js&quot;&gt;&lt;/script&gt;
</pre>
<p>首先引入 require.js, 实现模块定义和模块加载等方法，比如 declareModule 方法。<br>
然后在服务器端，部署时，将 a.js 自动转换为：</p>
<p>mod-a.js:</p>
<pre>
declareModule(function(require, exports, module) {
  exports.add = function(n, m) {
    return n + m;
  };
});
</pre>
<p>mod-b.js:</p>
<pre>
declareModule(function(require, exports, module) {
  var add = require('a').add;
  exports.increment = function(n) {
    return add(n, 1);
  };
});
</pre>
<p>将上面的代码文档化，就能定义出一个模块同步传送规范。</p>
<h3>AMD</h3>
<p>在网站性能优化正在逐步成为产业的今天，同步方案明显很不给力。CommonJS 的 Module/Transport 规范里，目前认可度最高的提议是 <a href="http://wiki.commonjs.org/wiki/Modules/AsynchronousDefinition">Modules/AsynchronousDefinition</a>（简称 AMD）。AMD 定义了用于异步加载的一种模块定义方式：</p>
<pre>
define(id?, dependencies?, factory);
</pre>
<p>同步方案中，依赖关系由页面中引入的静态 script 来保障。异步方案中，依赖关系管理就不那么简单了。对于模块a, 对应文件 a.js, 其加载执行过程可分解为：</p>
<ol>
<li>脚本的下载过程：浏览器将 a.js 从服务器下载到本地。</li>
<li>脚本的解析(parse)和执行(execute)过程：浏览器解析脚本，并执行 define 函数。</li>
<li>模块的 attach 过程：执行模块的 factory 函数。</li>
</ol>
<p>AMD 规定 dependencies 中的模块，可以作为 factory 的参数，这就隐性要求在执行 factory 前，所有 dependencies 的 factory 都必须已执行，这种方式可称之为 execution 模式。文字太枯燥，来点例子：</p>
<p>a.js:</p>
<pre>
define({
  add: function(n, m) {
    return n + m;
  }
});
</pre>
<p>b.js:</p>
<pre>
define(['a'], function(a) {
  return {
    increment: function(n) {
      return a.add(n, 1);
    }
  };
});
</pre>
<p>b.js 的写法可以有很多种，下面是另一种很常见的写法：</p>
<pre>
define(['require', 'exports', 'a'], function(require, exports) {
  var add = require('a').add;
  exports.increment = function(n) {
    return add(n, 1);
  };
});
</pre>
<p>但下面这种写法是不允许的：</p>
<pre>
define(['require', 'exports'], function(require, exports) {
  var add = require('a').add;
  exports.increment = function(n) {
    return add(n, 1);
  };
});
</pre>
<p>当 dependencies 参数存在时，模块 b 依赖的模块，必须全部显式指定。在上面的例子中，模块 b 明显还依赖模块 a, 但在 dependencies 中没有，因此不符合 AMD 规范。</p>
<p>但很多时候，开始书写模块代码时，我们并不能很明确的知道需要依赖哪些模块。除非有很强大的 IDE 工具支持，否则在调用某个依赖模块时，还得跳转到模块顶部，手动添加下 dependencies. 这对开发者来说，不太友好。因此 AMD 允许以下写法：</p>
<pre>
define(function(require, exports) {
  var add = require('a').add;
  exports.increment = function(n) {
    return add(n, 1);
  };
});
</pre>
<p>当 define 只有 factory 参数时，dependencies 无需开发者提前指定，define 会调用 factory.toString 方法，通过正则匹配，自动找出需要依赖的模块。这省心多了。</p>
<p>理解了 AMD, RequireJS 的 api 也就很容易上手了，RequireJS 是遵循 AMD 规范的。实际上，RequireJS 的作者 James Burke, 为 AMD 规范贡献了很多 idea.</p>
<h3>Wrappings</h3>
<p>AMD 规范已经很不错，RequireJS 也很流行，jQuery 近期也加入了对 AMD 规范的支持。然而，CommonJS 社区近期有件不大不小的事，有人提出了另一种异步加载模块的定义方式：<a href="http://wiki.commonjs.org/wiki/Modules/Wrappings">Modules/Wrappings</a>:</p>
<pre>
module.declare(id?, dependencies?, factory)
</pre>
<p>注意：wiki 的当前版本是 <code>module.declare(factory)</code>. 但在这篇讨论里 <a href="http://groups.google.com/group/commonjs/browse_thread/thread/acc2835e4839076b">AMD vs Wrappings</a> 里，已经有了更完善的方案。</p>
<p>从表面上看，AMD 和 Wrappings 唯一的不同是 define 还是 module.declare 的命名差异。如果仅是这点差异的话，实在不值得新增加一个提议。Wrappings 和 AMD 最大的不同，在于 Wrappings 方案里，factory 的参数更简单，和 dependencies 无对应关系。也就是说，可以如下写代码：</p>
<pre>
module.declare(['a'], function(require, exports) {
  var add = require('a').add;
  exports.increment = function(n) {
    return add(n, 1);
  };
});
</pre>
<p>这个看似非常小的差异，可以让下面的代码合理存在并达到预期目的：</p>
<pre>
module.declare(function(require, exports) {
  ...
  var a;
  if(someCondition) {
    a = require('a1');
  } else {
    a = require('a2');
  }
  ...
});
</pre>
<p>还记得 AMD 里的 download/parse/execute/attach 一条龙服务吗？在 Wrappings 里，attach 过程可以延后，可以等到第一次 require 时，才调用 factory. 这种模式称之为 availability 模式。</p>
<h3>AMD vs. Wrappings</h3>
<p>很明显，Wrappings 的 availability 模式，可以按需执行，可以减少 CPU 的初始开销甚至总开销。但 availability 模式，有一个缺陷，James 的博文中有指出：</p>
<blockquote><p>
2) The “execution” model fits better for projects that use libraries like jQuery, Prototype or MooTools, where many of the modules augment other objects, and they are assumed to have already run before executing the current module function. jQuery plugins augment the jQuery object, Prototype and MooTools augment JavaScript object prototypes.
</p></blockquote>
<p>看例子：</p>
<pre>
module.declare(function(require) {
  ...
  [1,2].each(...);
  ...
  var mootools = require('mootools');
  ...
});
</pre>
<p>开发者有可能在 require(‘mootools’) 之前，就调用了 MooTools 在原生对象上添加的方法。上面的代码，在 availability 模式下，就有可能会报错。类似的，Prototype 和 jQuery 也有类似问题。jQuery 没破坏原生对象，但 jQuery 插件会往 jQuery 对象中添加方法，因此存在的问题是类似的。</p>
<p>James 还提到，AMD 和 Simple Modules 规范更兼容，不过这个是浮云。Simple Modules 还是稻草人提案，Wrappings 也可以通过简单修改，来兼容 Simple Modules.</p>
<p>AMD 是现实主义者，Wrappings 更有点理想主义色彩。James 指出的 availability 模式的问题，也都是小问题，可以很容易解决掉。比如对于 jqurery, mootools 等类库，提前 require 即可。如果代码出错，可以认为是使用者的错误，而不是规范的问题。</p>
<p>AMD 目前还没到投票阶段，个人期待 AMD 接纳 Wrappings 的建议：</p>
<ol>
<li>将 define 命名更改为 module.declare.</li>
<li>提供参数配置，可以切换到 availability 模式。</li>
</ol>
<h3>RequireJS</h3>
<p>终于说到 RequireJS 了。RequireJS 很优秀，用户群也不少。从目前的特性和功能来看，感觉有以下不如意：</p>
<ol>
<li>文件太大，用 google closure compiler 压缩后，12.2k. 这是在页头必须引入的脚本，还是希望越小越好。</li>
<li>功能太多。这本是优点，比如能够在各种环境下跑。但对于真实的 web 应用来说，还是希望用情专一，尽量无无用代码。</li>
<li>给 require 方法赋予了双重含义。一重含义是 CommonJS/Modules/1.1.1 规范里定义的 require, 另一重是 RequireJS 里用来加载模块和调用回调。这导致 require 的 dependencies 参数的格式，和 define 中的 dependencies 参数的格式不一致。我刚用的时候，没意识到这一点，经常很迷惑。</li>
<li>目前不支持 availability 模式。</li>
<li>require.js 代码里，有 only for jQuery 的代码。这让不用 jQuery 的用户，情何以堪？</li>
</ol>
<p>有个特别喜欢的功能：</p>
<pre>
&lt;script data-main=&quot;main&quot; src=&quot;scripts/require.js&quot;&gt;&lt;/script&gt;
</pre>
<p>上面的写法，会自动加载 main.js. 不过官方的例子里感觉不好：</p>
<pre>
//Inside scripts/main.js
require(["some/module", "a.js", "b.js"], function(someModule) {
    //...
});
</pre>
<p>main.js 也是 module, 和其它模块的写法应该保持一致：</p>
<pre>
//Inside scripts/main.js
define(["some/module", "a.js", "b.js"], function(someModule) {
    //...
});
</pre>
<p>是否又要崩溃了？在 RequireJS 里，require 和 define 经常会被搞混淆。这是 require 的双重含义导致的。</p>
<p>RequireJS 还有个非常值得推荐的是优化工具，也就是在将开发和部署分开，在部署前，有 pre-compile, 相信这将会成为前端专业开发的必备流程。</p>
<h3>总结</h3>
<p>CommonJS 规范的目的是让 JavaScript 组件能在各种环境中共享，打造 JavaScript 世界的生态圈，其关键词是“协同”。</p>
<p>无论是 AMD 还是 Wrappings, 特别让我感动的是，参与者的热情、批判式思维，同时又能彼此包容，最后推动 CommonJS 的前行。很希望国内的有识之士，也能参与进去，一起为社区做贡献。</p>
<p>最后，做个广告。如果你是个爱思考的人，如果上面的文字，你都能通读下来，我非常希望你能参与到下面这个 group 中来：</p>
<p><a href="http://groups.google.com/group/seajs">http://groups.google.com/group/seajs</a></p>
<p>我们不相信银弹，但我们相信：有梦，就要去追。行动改变世界。</p>