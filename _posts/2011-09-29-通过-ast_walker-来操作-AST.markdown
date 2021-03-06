---
layout: post
title:  "通过 ast_walker 来操作 AST"
date:   2011-09-29 13:34:46
author: lifesinger
categories: program
---

## 通过 ast_walker 来操作 AST
### by lifesinger
### at 2011-09-29 13:34:46
### original <http://lifesinger.wordpress.com/2011/09/29/ast-walker/>

<p>通过 <a href="https://github.com/mishoo/UglifyJS">UglifyJS</a> 可以得到 js 代码的 AST:</p>
<pre>
var fs = require('fs');
var jsp = require('uglify-js').parser;

var code = fs.readFileSync('test.js', 'utf-8');
var ast = jsp.parse(code);
</pre>
<p>得到的 ast 是以数组形式表示的抽象语法树。</p>
<p>在 <a href="https://github.com/seajs/spm">spm</a> 项目里，要提取 js 模块里的依赖信息。最裸的做法是通过正则来实现：</p>
<pre>
function parseStatic(inputFile, charset) {
  var ast = getAst(inputFile, charset);
  var deps;

  var pattern = /,stat,call,name,define,(?:(?:[^,]+,){2})?array(,.*?,)(?:function|name),/;
  var match = ast.toString().match(pattern);

  if (match &amp;&amp; match[1]) {
    deps = [];
    var t = match[1].match(/,string,[^,]+/g);

    if (t &amp;&amp; t.length) {
      deps = t.map(function(s) {
        // s: ,string,xxx
        return s.slice(8);
      });
    }
  }

  return deps;
}
</pre>
<p>详见：<a href="https://github.com/seajs/spm/blob/357962ba72f8e70dcd65f7bd4aabfadc81ce397a/lib/utils/Dependences.js#L24">Dependences.js</a></p>
<p>正则的实现方式在绝大部分情况下不会有问题，但代码的可读性和扩展性不好。<br>
<span></span><br>
查看 UglifyJS 的源码，发现内部提供了 <a href="https://github.com/mishoo/UglifyJS/blob/master/lib/process.js#L69">ast_walker</a> 方法。我们可以简单封装成 <a href="https://github.com/seajs/spm/blob/master/lib/utils/Ast.js">Ast.js</a>：</p>
<pre>
var uglifyjs = require('uglify-js');
var pro = uglifyjs.uglify;
var Ast = exports;

Ast.walk = function(ast, type, walker) {
  var w = pro.ast_walker();

  var walkers = {};
  walkers[type] = function() {
    walker(this);
  };

  ast = w.with_walkers(walkers, function() {
    return w.walk(ast);
  });

  return ast;
};
</pre>
<p>这样，获取依赖信息的代码可以调整为：</p>
<pre>
function parseStatic(inputFile, charset) {
  var ast = getAst(inputFile, charset);
  var deps, times = 0;

  Ast.walk(ast, &#39;stat&#39;, function(stat) {
    if (stat.toString().indexOf(&#39;stat,call,name,define,&#39;) !== 0) {
      return stat;
    }

    if (++times &gt; 1) {
      throw &#39;Found multiple &quot;define&quot; in one module file. It is NOT allowed.&#39;;
    }

    // stat[1]:
    //     [ &#39;call&#39;,
    //       [ &#39;name&#39;, &#39;define&#39; ],
    //       [ [Object], [Object], [Object ] ] ]
    var argsAst = stat[1][2];
    var depsAst;

    // argsAst:
    //   [ [ &#39;string&#39;, &#39;a&#39; ],
    //     [ &#39;array&#39;, [ [Object], [Object] ] ],
    //     [ &#39;function&#39;, null, [], [] ] ]
    argsAst.some(function(item) {
      // NOTICE: the deps MUST be literal, it can NOT be a reference.
      if (item[0] === &#39;array&#39;) {
        depsAst = item[1];
        return true;
      }
    });

    if (!depsAst) {
      return stat;
    }

    // depsAst:
    //   [ [ &#39;string&#39;, &#39;b&#39; ], [ &#39;string&#39;, &#39;c&#39; ] ]
    deps = [];
    depsAst.forEach(function(item) {
      if (item[0] === &#39;string&#39;) {
        deps.push(item[1]);
      }
    });

    return stat;
  });

  return deps;
}
</pre>
<p>详见：<a href="https://github.com/seajs/spm/blob/master/lib/utils/Dependences.js">Dependences.js</a>. 功能更可靠，可读性也更好了。</p>
<p>还可以通过 UglifyJS 隐藏的第三参数来让 AST 带上 type/value/start/end 等信息：</p>
<pre>
var ast = jsp.parse(code, false, true);
</pre>
<p>通过 ast_walker 来操作 AST, 可以完成很多意想不到的工作。比如获取某个闭包里的所有局部变量、删除某个特定函数、或者给所有 constructor 添加上调试信息等。老赵的 Jscex 也是基于这一原理，有兴趣的可以深入挖掘。</p>
<br>  <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gocomments/lifesinger.wordpress.com/517/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/comments/lifesinger.wordpress.com/517/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/godelicious/lifesinger.wordpress.com/517/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/delicious/lifesinger.wordpress.com/517/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gofacebook/lifesinger.wordpress.com/517/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/facebook/lifesinger.wordpress.com/517/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gotwitter/lifesinger.wordpress.com/517/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/twitter/lifesinger.wordpress.com/517/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/gostumble/lifesinger.wordpress.com/517/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/stumble/lifesinger.wordpress.com/517/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/godigg/lifesinger.wordpress.com/517/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/digg/lifesinger.wordpress.com/517/"></a> <a rel="nofollow" href="http://feeds.wordpress.com/1.0/goreddit/lifesinger.wordpress.com/517/"><img alt="" border="0" src="http://feeds.wordpress.com/1.0/reddit/lifesinger.wordpress.com/517/"></a> <img alt="" border="0" src="http://stats.wordpress.com/b.gif?host=lifesinger.wordpress.com&amp;blog=38365&amp;post=517&amp;subd=lifesinger&amp;ref=&amp;feed=1" width="1" height="1">