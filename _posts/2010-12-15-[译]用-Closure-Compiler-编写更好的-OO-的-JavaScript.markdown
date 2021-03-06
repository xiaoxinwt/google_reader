---
layout: post
title:  "[译]用 Closure Compiler 编写更好的 OO 的 JavaScript"
date:   2010-12-15 23:25:00
author: George Wing
categories: program
---

## [译]用 Closure Compiler 编写更好的 OO 的 JavaScript
### by George Wing
### at 2010-12-15 23:25:00
### original <http://www.cnblogs.com/georgewing/archive/2010/12/15/1902902.html>

<p><div> <p>原贴：<a href="http://calendar.perfplanet.com/2010/coding-better-object-oriented-javascript-with-closure-compiler/">Coding Better Object-Oriented JavaScript with Closure Compiler</a></p> <p>作者：<a href="http://hedgerwow.blogspot.com/">Hedger Wang</a></p> <h3>前面的话</h3> <p>许多程序员觉得OO 的 JavaScript 是种不错的方法，但也明白由于语言自身的本质和它所运行的环境(主要是在 web 浏览器中)，编写 OO 风格的 JavaScript 是比较痛苦的。</p> <p>使用 <a href="http://code.google.com/closure/compiler/">Google Closure Compiler</a> 不仅可以压缩代码，而且可以像别的编译器那样编译代码！</p> <p>当编译器的标帜“<a href="http://code.google.com/closure/compiler/docs/api-tutorial3.html">ADVANCED_OPTIMIZATIONS</a>(高级选项)” 被贴签时，与大多的 JavaScript 压缩工具如 <a href="http://developer.yahoo.com/yui/compressor/">YUI Compressor</a>、<a href="http://o.dojotoolkit.org/docs/shrinksafe">Dojo Compressor</a> 相比，它拥有更多的优化。</p> <p>我会讲几种通用的 OO 风格的模式，以及是怎样实现的。</p> <p>通过这篇文章，你将学会使用 Closure Compiler 来编写具有 OO 风格的JS代码。</p> <h3>OO 风格的 JavaScript 决不简易</h3> <p>长期以来，由于 JavaScript 的本质与浏览器这个宿主环境，造成了普通认为：编写纯 OO 的 JavaScript 应用太难了。<a href="http://jonraasch.com/blog/10-javascript-performance-boosting-tips-from-nicholas-zakas">Nicholas Zakas</a> 是最有名的<a href="http://www.nicholaszakas.com/2008/02/18/don-t-give-up/">黑腰带级空手道</a> JavaScript 程序员之一，曾在 blog 的贴文中写过 OO 风格的 JS 代码所带来的痛苦：</p> <blockquote>在传统的OO语言中，类、继承是信手拈来。它们的编译器理解其中的工作原理，所以不会因为加几个类或多几个继承而在执行时受任何的影响。而在 JavaScript 中，引用类型、继承是2大要命的伤痛处。 </blockquote> <h3>JavaScript 没有类</h3> <p>虽然 OO 风格的JS 在运行时有执行性能上的缺陷，人们还是在尝试着用各种不同的OO风格的JS，以换掉函数化的JS。</p> <p><a href="http://ejohn.org/">John Resig</a>用了一种<a href="http://ejohn.org/apps/learn/#36">有趣的方式</a>以确保在“类”函数作为构造函数时用不用 new 关键字都无所谓。</p><pre>// John Resig 的方法
function User(name){
  if ( !(this instanceof User) )
    return new User(name);
  this.name = name;
}

var userA = new User('John'); // 是一个实例
var userB = User('Jane'); // 也是一个实例
</pre>
<h3>JavaScript没有访问控制</h3>
<p><a href="http://www.crockford.com/">Douglas Crockford</a>(老道)提出了一种<a href="http://www.crockford.com/javascript/private.html">模块模式</a>(module pattern)来说明如何来保护与对象的外部进行读或写的私有成员。</p><pre>// Crockford 的方法
function User(name) {
  var _name = name;

  // 私有 name 的 getter
  this.getName = function() {
    return _name;
  };
}
</pre>
<h3>全局变量是糟粕，过深命名空间(deeply name-spaced)的变量也垃圾得很</h3>
<p><a href="http://javascript.crockford.com/style2.html">全局变量是魔鬼</a>，而用过深命名空间的变量也<a href="http://james.padolsey.com/javascript/zakas-javascript-performance-tips/">超慢</a>。</p><pre>// 全局是魔鬼
var myProjectDemoUserName = 'foo';
// 撒旦...
my.project.demo.user.Name = 'foo';
</pre>
<h3>开发人员不要弄得冗余</h3>
<p>在文档中加载了<a href="http://yui.yahooapis.com/2.8.2r1/build/dom/dom-min.js">整个 YUI DOM 库</a>，却只用到了其中的一个静态方法<code>YAHOO.util.Dom.getStyle(document.body, ‘backgroundColor’)</code>时怎样呢？</p>
<h3>简化不是简单</h3>
<p>也可以在写 <a href="http://jquery.com/">jQuery 库</a>的插件，但很快就会发现，不能去处理 DOM 范围、选择器模式，也没有数据集合、组件框架、类式继承，没有任何构建复杂 Web 应用开发所需要的东东。</p>
<h3>只有 JavaScript 忍者能活吗？</h3>
<p>所以，我们只好去<a href="http://jobs.jsninja.com/">招聘</a>那些请不动的 JavaScript 忍者或是成为其中<a href="http://dmitry.baranovskiy.com/post/91403200">之一</a>。</p>
<p>那么，JavaScript 代码非得写得这样吗：</p><pre>  if (!("a" in window)) {
    var a = 1;
  }
  alert(a);
</pre>
<p>其实这是 JavaScript 的糟粕，代码不能写成这种鬼样。比如说 C++、Java 程序员就不会这样来编写代码。程序员的重心应该是在算法和数据结构，而不是这些旁门左道的技巧。</p>
<h3>我们能有所有的精华吗</h3>
<p>看来几乎不像是在编写轻量的、通用的、强壮的 JavaScript 来提供功能丰富的、可维护的能力，并且用在很多地方。</p>
<p>想要让 JavaScript 不再是玩具语言吗？</p>
<p>那就使用 Closure Compiler 吧。</p>
<h3>让 Closure Compiler 使你变身为 JavaScript 武将</h3>
<blockquote><a href="http://code.google.com/closure/compiler/">Closure Compiler</a> 是可以使 JavaScript 下载、运行更快的工具。它是真正的 JavaScript 编译器。将源代码编译为机器代码的替换，将 JavaScript 编译为更优的 JavaScript。会解析并分析你的 JavaScript，移除死代码、重写并最小化。也可以检查语法、变量引用、类型，并警告 JavaScript 常见的相关陷阱。 </blockquote>
<p>在<a href="http://code.google.com/closure/compiler/">官网</a>上已经有了很多不错的资源来告诉你如何使用编译器。写这篇文章，其实是为了写<a href="http://books.google.com/books?id=p7uyWPcVGZsC&amp;printsec=frontcover&amp;dq=closure+the+definitive+guide&amp;hl=en&amp;ei=tz33TObSFoKCsQPP_d2eAg&amp;sa=X&amp;oi=book_result&amp;ct=book-thumbnail&amp;resnum=1&amp;ved=0CCgQ6wEwAA#v=onepage&amp;q&amp;f=false">一本书中的某章节</a>。让我们先来解决之前说到的问题。</p>
<h3>用 @constructor 来标注函数作为类</h3>
<p>想要在运行时会检查构造函数，可以使用编译器来为你服务。而且不用在运行时去检查，要记住的这个核心理念是：在编译器完成时就可以了。</p><pre>/**
 * @constructor
 */
function MyClass() {
}

// Pass.
var obj1 = new MyClass();

// ERROR: Constructor function (this:MyClass):
// class should be called with the "new" keyword.
var obj2 = MyClass(); // Error.
</pre>
<h3>用 @private 进行访问控制</h3><pre>// File demo1.js //

/**
 * A User.
 * @constructor
 */
function User() {

  /**
   * The creation date.
   * @private
   * @type {Date}
   */
  this._birthDay = new Date();
}

/**
 * @return {number} The creation year.
 */
User.prototype.getBirthYear = function() {
  return this._birthDay.getYear();
};

// File demo2.js //

// Create a user.
var me = new User();

// Print out its birth year.
document.write(me.getBirthYear().toString());
</pre>
<p>编译器会确保私有成员 <code>_birthDay</code> 在整个应用程序的外部不会读或写。只在相同的标为 <code>@private</code>的 JS 代码中才可以访问到对象。当然，也可以用 <code>@protected </code>在代码里标注。</p>
<h3>用 @extend 来管理类继承</h3>
<p>假设我们有3个类：<code>Shape</code>、<code>Box</code>、<code>Cube</code>。</p>
<p><code>Shape</code> 类定义了一个通用的方法：<code>getSize()<code>。</code></code></p><code><code>
<p><code>Box</code> 类继承 <code>Shape</code> 类。</p>
<p><code>Cube</code> 类继承<code>Box</code> 类</p><pre>/**
 * Helper function that implements (pseudo)Classical inheritance inheritance.
 * @see http://www.yuiblog.com/blog/2010/01/06/inheritance-patterns-in-yui-3/
 * @param {Function} childClass
 * @param {Function} parentClass
 */
function inherits(childClass, parentClass) {
  /** @constructor */
  var tempClass = function() {
  };
  tempClass.prototype = parentClass.prototype;
  childClass.prototype = new tempClass();
  childClass.prototype.constructor = childClass;
}

//////////////////////////////////////////////////////////////////////////////

/**
 * The shape
 * @constructor
 */
function Shape() {
  // No implementation.
}

/**
 * Get the size
 * @return {number} The size.
 */
Shape.prototype.getSize = function() {
  // No implementation.
};

//////////////////////////////////////////////////////////////////////////////

/**
 * The Box.
 * @param {number} width The width.
 * @param {number} height The height.
 * @constructor
 * @extends {Shape}
 */
function Box(width, height) {
  Shape.call(this);

  /**
   * @private
   * @type {number}
   */
  this.width_ = width;

  /**
   * @private
   * @type {number}
   */
  this.height_ = height;
}
inherits(Box, Shape);

/**
 * @return {number} The width.
 */
Box.prototype.getWidth = function() {
  return this.width_;
};

/**
 * @return {number} The height.
 */
Box.prototype.getHeight = function() {
  return this.height_;
};

/** @inheritDoc */
Box.prototype.getSize = function() {
  return this.height_ * this.width_;
};

////////////////////////////////////////////////////////////////////////////

/**
 * The Box.
 * @param {number} width The width.
 * @param {number} height The height.
 * @param {number} depth The depth.
 * @constructor
 * @extends {Box}
 */
function Cube(width, height, depth) {
  Box.call(this, width, height);

  /**
   * @private
   * @type {number}
   */
  this.depth_ = depth;
}
inherits(Cube, Box);

/**
 * @return {number} The width.
 */
Cube.prototype.getDepth = function() {
  return this.depth_;
};

/** @inheritDoc */
Cube.prototype.getSize = function() {
  return this.depth_ * this.getHeight() * this.getWidth();
};

////////////////////////////////////////////////////////////////////////////

var cube = new Cube(3, 6, 9);
document.write(cube.getSize().toString());
</pre>
<p>上面的 JavaScript 代码有些长，但是源代码的大小会被简单的看作是输入的字符数。</p>
<p>那些文档中描述编码的注释、变量的名称、方法的名称会被编译器重新命名或移除掉。</p>
<p>3层级的类继承树会被看作是简单的函数，编译器会进行优化。</p>
<p>下面是编译后的代码：</p><pre>function d(a, b) {
  function c() {
  }
  c.prototype = b.prototype;
  a.prototype = new c
}
function e() {
}
e.prototype.a = function() {
};
function f(a, b) {
  this.c = a;
  this.b = b
}
d(f, e);
f.prototype.a = function() {
  return this.b * this.c
};
function g(a, b, c) {
  f.call(this, a, b);
  this.d = c
}
d(g, f);
g.prototype.a = function() {
  return this.d * this.b * this.c
};
document.write((new g(3, 6, 9)).a().toString());
</pre>
<p>虽然所有的变量和方法都改名了，但是你也注意到：有些方法被移除掉，有些方法合成了一行内。比如：</p><pre>Cube.prototype.getSize = function() {
  return this.depth_ * this.getHeight() * this.getWidth();
};
</pre>
<p>变成了：</p><pre>g.prototype.a = function() {
  return this.d * this.b * this.c
};
</pre>
<p>显然，2个 getter 方法 <code>getWidth()</code>、<code>getHeight() </code>被 <code>this._width</code>和<code>this._height</code>安全的替换掉。因此，那些 getter 已经没有用，并且被编译器移除掉了。</p>
<p>同时使用了 <code>@private</code> 和 getter 的方法是指私有属性 <code>_width</code> 对开发人员来说是只读的，无妨对其添加一个 getter 方法。</p>
<h3>使用 @interface 和 @implements</h3>
<p>我们对编写 OO 风格的 JavaScript 感兴趣了后，将上面的示例改成下面的代码。</p><pre>// skip example code.

////////////////////////////////////////////////////////////////////////////

/**
 * The shape
 * @interface
 */
function Shape() {
}

/**
 * Get the size
 * @return {number} The size.
 */
Shape.prototype.getSize = function() {};

////////////////////////////////////////////////////////////////////////////

/**
 * The Box.
 * @param {number} width The width.
 * @param {number} height The height.
 * @constructor
 * @implements {Shape}
 */
function Box(width, height) {
  Shape.call(this);

  /**
   * @private
   * @type {number}
   */
  this.width_ = width;

  /**
   * @private
   * @type {number}
   */
  this.height_ = height;
}

/**
 * @return {number} The width.
 */
Box.prototype.getWidth = function() {
  return this.width_;
};

// skip example code.
</pre>
<p>由于 @interface 只用在编译的时候，经过编译后的代码更小了，并且不会输出包含接口的代码。</p><pre>function d(a, b) {
  this.c = a;
  this.b = b
}
d.prototype.a = function() {
  return this.b * this.c
};
function e(a, b, c) {
  d.call(this, a, b);
  this.d = c
}
(function(a, b) {
  function c() {
  }
  c.prototype = b.prototype;
  a.prototype = new c
})(e, d);
e.prototype.a = function() {
  return this.d * this.b * this.c
};
document.write((new e(3, 6, 9)).a().toString());
</pre>
<h3>使用包(命名空间的 JS 对象)</h3>
<p>想要对 JS 对象使用命名空间的话，命名层级过深的问题不会影响运行时的性能，因为编译器会帮你解决掉。</p><pre>// Create namespaces.
var demo = {};
demo.example = {};
demo.example.exercise = {};

/**
 * @constructor
 */
demo.example.exercise.Foo = function() {
  demo.example.exercise.Foo.print(this.value1);
  demo.example.exercise.Foo.print(this.value2);
};

/**
 * Static method
 * @param {string} str String to print.
 */
demo.example.exercise.Foo.print = function(str) {
  document.write(str);
};

/**
 * @type {string}
 */
demo.example.exercise.Foo.prototype.value1 = 'abc';

/**
 * @type {string}
 */
demo.example.exercise.Foo.prototype.value2 = 'def';

var foo = new demo.example.exercise.Foo();
</pre>
<p>编译后的代码：</p><pre>function a() {
  document.write(this.a);
  document.write(this.b)
}
a.prototype.a = "abc";
a.prototype.b = "def";
new a;
</pre>
<p>也许，想要保留 JS 代码而避免与页面中其他脚本产生冲突的话，可以使用标帜 <a href="http://code.google.com/p/closure-compiler/wiki/FAQ">-output_wrapper</a>，也不是全局的对象(除非是明确的<a href="http://code.google.com/closure/compiler/docs/api-tutorial3.html#no">导出</a>)。</p>
<p>编译后的代码如下：</p><pre>(function() {function a() {
  document.write(this.a);
  document.write(this.b)
}
a.prototype.a = "abc";
a.prototype.b = "def";
new a;})()
</pre>
<p>编译器会确保那些长的命名空间、属性、方法已经重命名，尽可能多的短名称。</p>
<h3>进行类型检查是在构建时，而非运行时</h3>
<p>在构建时进行类型检查可以减少不必要的在运行时进行的类型检查。比如：</p><pre>function User() {
}

function UsersGroup() {
  this.users_ = [];
}

UsersGroup.prototype.add = function(user) {
  // Make sure that only user can be added.
  if (!(user instanceof User)) {
    throw new Error('Only user can be added.');
  }
  this.users_.push(user);
};

var me = new User();
var myGroup = new UsersGroup();
myGroup.add(me);
</pre>
<p>这种方法可以完成。</p><pre>/**
 * @constructor
 */
function User() {
}

/**
 * @constructor
 */
function UsersGroup() {
  /**
   * @private
   * @type {Array.&lt;User&gt;}
   */
  this.users_ = [];
}

/**
* @param {User} user
*/
UsersGroup.prototype.add = function(user) {
  this.users_.push(user);
};
</pre>
<p>注意 <code>this.users_</code> 的数据类型为 <code>@type {Array.&lt;user&gt;}</code> 表示是 User 的一个列表。</p>
<p>应该使用有意义的数据结构，而不是视任何事物为原生的对象，否则非常容易出错。</p>
<h3>使用 @enum</h3>
<p>有些时候你想要处理多种情形：</p><pre>function Project(status) {
  this.status_ = status;
}

Project.prototype.isBusy = function() {
  switch (this.status_) {
    case 'busy':;
    case 'super_busy':
      return true;
    default:
      return false;
  }
};

var p1 = new Project('busy');
var p2 = new Project('super_busy');
var p3 = new Project('idle');

document.write(p1.isBusy().toString());
document.write(p2.isBusy().toString());
document.write(p3.isBusy().toString());
</pre>
<p>可以考虑使用 <code>@enum</code>：</p><pre>/**
 * @constructor
 * @param {Project.Status} status
 */
function Project(status) {
  /**
   * @type {Project.Status}
   * @private
   */
  this.status_ = status;
}

/**
 * @enum {number}
 */
Project.Status = {
  BUSY: 0,
  SUPER_BUSY: 1,
  IDLE: 2
};

/**
 * @return {boolean}
 */
Project.prototype.isBusy = function() {
  switch (this.status_) {
    case Project.Status.BUSY:;
    case Project.Status.SUPER_BUSY:
      return true;
    default:
      return false;
  }
};

var p1 = new Project(Project.Status.BUSY);
var p2 = new Project(Project.Status.SUPER_BUSY);
var p3 = new Project(Project.Status.IDLE);

document.write(p1.isBusy().toString());
document.write(p2.isBusy().toString());
document.write(p3.isBusy().toString());
</pre>
<p>编译后为：</p><pre>function a(b) {
  this.a = b
}
function c(b) {
  switch(b.a) {
    case 0:
    ;
    case 1:
      return true;
    default:
      return false
  }
}
var d = new a(1), e = new a(2);
document.write(c(new a(0)).toString());
document.write(c(d).toString());
document.write(c(e).toString());
</pre>
<p>枚举变量被原始的数替换。使用枚举能够编写更多的可维护性的代码。</p>
<h3>使用 @define 启用或禁用消息的记录</h3>
<p>如果你想要在一个类中记录某些重要的消息的话，像每一位谨慎的程序员那样都可以做到。</p><pre>/**
 * namespace for the Logger.
 */
var Logger = {};

/**
 * Whether logging should be enabled.
 * @define {boolean}
 */
Logger.ENABLED = true;

/**
 * the log API.
 * @param {...*} args
 */
Logger.log = function(args) {
  if (!Logger.ENABLED) {
    // Don't do anything if logger is disabled.
    return;
  }
  var console = window['console'];
  if (console) {
    console['log'].apply(console, arguments);
  }
};

/**
 * A User.
 * @param {string} name
 * @constructor
 */
function User(name) {
  Logger.log('New User', name);
}

var me = new User('me');
</pre>
<p>代码会编译为：</p><pre>function b() {
  var a = window.console;
  a &amp;&amp; a.log.apply(a, arguments)
}
new function(a) {
  b(&quot;New User&quot;, a)
}(&quot;me&quot;);
</pre>
<p>你可以添加标帜 <code>–define Logger.ENABLED=false</code> 来禁用记录器。也可以添加标帜 <code>–jscomp_error unknownDefines</code> 来捕获未知的 <code>@define</code>。</p><pre>java -jar compiler.jar \
  --js src/demo.js \
  --js_output_file compiled/demo.js \
  --warning_level VERBOSE \
  --formatting PRETTY_PRINT \
  --jscomp_error accessControls \
  --jscomp_error checkTypes \
  --jscomp_error unknownDefines
  --define Logger.ENABLED=false
  --compilation_level ADVANCED_OPTIMIZATIONS;
</pre>
<p>对开发人员来说，允许在生成代码时启用记录器，或是完全由编译器带所有的记录器调用到生产布署。</p>
<h3>使用类型转换</h3>
<p>有些时候你想要把 JSON 对象转换为未知的引用类型。比如：</p><pre>/**
 * The Model definition.
 * @constructor
 */
function UserModel() {
  /**
   * @type {string}
   */
  this.firstName = '';

  /**
   * @type {string}
   */
  this.lastName = '';
}

/////////////////////////////////////////////////////////////////////////////

/**
 * The User constructor.
 * @constructor
 * @param {string} firstName
 * @param {string} lastName
 */
function User(firstName, lastName) {
  /**
   * @type {string}
   */
  this.fullName = firstName + ' ' + lastName;
}

/**
 * A static method that creates a User from a model.
 * @param {UserModel} model
 * @return {User} The user created.
 */
User.createFromUserModel = function(model) {
  return new User(model.firstName, model.lastName);
};

/////////////////////////////////////////////////////////////////////////////

// Cast a simple JSON Object as {UserModel}.
var data = /** @type {UserModel} */({
  firstName : 'foo',
  lastName : 'bar'
});

// Create a user from the model.
var user = User.createFromUserModel(data);

document.write(user.fullName);
</pre>
<p>正如你的意料之中，model definition 会移除掉，属性 <code>firstName</code> 和 <code>lastName</code> 也会重命名。</p><pre>var a = {a:"foo", c:"bar"};
document.write((new function(b, c) {
  this.b = b + " " + c
}(a.a, a.c)).b);
</pre>
<p>在上面的示例中，纯对象转换为未知的引用类型，可以给该对象更详细的指定。</p>
<p>在 <a href="http://api.jquery.com/jQuery.isPlainObject/#comment-31062800">jQuery 1.4</a> 中添加了新的 API <a href="http://api.jquery.com/jQuery.isPlainObject/#comment-31062800">isPlainObject</a> 它是在运行时进行类型检查，而我将会不推荐，如果你有编译器在手的话，其实<a href="http://forum.jquery.com/topic/improvement-to-isplainobject">在 JS 中看来似乎是解决一大难题</a>。</p>
<h3>还有...</h3>
<p>还有很多其他使用的东西，比如对常量使用 <code>@const</code>。</p>
<p>在此本人推荐 <a href="http://code.google.com/closure/">Closure 工具官网</a>去学习更多的知识。</p>
<p>另外，有一本不错的书《<a href="http://books.google.com/books?id=p7uyWPcVGZsC&amp;printsec=frontcover&amp;dq=closure+definitive+guide&amp;hl=en&amp;ei=8Or3TPK_PJP0swOU3qDfAQ&amp;sa=X&amp;oi=book_result&amp;ct=result&amp;resnum=1&amp;ved=0CCYQ6AEwAA#v=onepage&amp;q&amp;f=false">Closure 权威指南</a>》(打个广告)有所有 closure 工具的详细内容。</p>
<h3>总结</h3>
<p>本人已经使用 Google Closure Compiler 有两年多了，它完全改变了 JavaScript 开发方式。</p>
<p>总之，在此有以下的东西要分享：</p>
<ol>
<li>
<p>想要坚持不错的、统一的代码、风格(比如缩进)、80或120字符宽度限制等等，请参考 <a href="http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml">Google JavaScript Style Guide</a>(<a href="http://kissyteam.github.com/docs/html/styleguide/google/javascriptguide.xml">中文</a>)。</p>
<p>请确保代码的可读性和可维护性。</p>
<li>编写兼具描述性与信息性的文档，有些时候需要<a href="http://code.google.com/p/closure-library/source/browse/trunk/closure/goog/events/pastehandler.js?r=156">编写更多的 JsDoc</a>。 
<li>把时间和精力更多的放在不错的 OO 设计、算法、数据结构上，而不是浪费在细微的优化代码或是使用任何的忍者技，那样会不易读或搞昏。 
<li>编写代码快速而频繁，并且生成代码。 
<li>使用大型 JavaScript 库没有错误，只要你可以用编译器来生成代码。其实能够得到更少的代码。 
<li>
<p>在 Closure Compiler 的 ADVANCED_OPTIMIZATIONS 模式中确保代码的兼容。</p>
<p>确保很多质量更高的代码，会使生成整合其他现有的编译器兼容的 JS 代码更加容易。</p></li></li></li></li></li></li></ol>
<p>(完)</p></code></code></div><img src="http://www.cnblogs.com/georgewing/aggbug/1902902.html?type=1" width="1" height="1" alt=""><p>作者: <a href="http://www.cnblogs.com/georgewing/">George Wing</a> 发表于 2010-12-15 23:25 <a href="http://www.cnblogs.com/georgewing/archive/2010/12/15/1902902.html">原文链接</a></p><p>评论: 3　<a href="http://www.cnblogs.com/georgewing/archive/2010/12/15/1902902.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/georgewing/archive/2010/12/15/1902902.html#commentform">发表评论</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/84818/">如何面试程序员？</a><span style="color:gray">(2010-12-16 15:25)</span><br>· <a href="http://news.cnblogs.com/n/84817/">2010年移动领域十大创新：iPhone 4居首</a><span style="color:gray">(2010-12-16 15:14)</span><br>· <a href="http://news.cnblogs.com/n/84816/">雅虎CEO裁员备忘录：减少重复岗位</a><span style="color:gray">(2010-12-16 15:12)</span><br>· <a href="http://news.cnblogs.com/n/84815/">2011年Windows 7 Macs和HTML5成攻击重点</a><span style="color:gray">(2010-12-16 15:09)</span><br>· <a href="http://news.cnblogs.com/n/84811/">平板电脑OS未来之争 MeeGo值得期待</a><span style="color:gray">(2010-12-16 15:08)</span><br></p><p>编辑推荐：<a href="http://news.cnblogs.com/n/84812/">程序员阿士顿的故事</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">我的园子</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://kb.cnblogs.com">知识库</a></p></p>