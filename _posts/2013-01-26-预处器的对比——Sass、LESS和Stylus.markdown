---
layout: post
title:  "预处器的对比——Sass、LESS和Stylus"
date:   2013-01-26 23:07:16
author: Airen
categories: program
---

## 预处器的对比——Sass、LESS和Stylus
### by Airen
### at 2013-01-26 23:07:16
### original <http://www.w3cplus.com/css/sass-vs-less-vs-stylus-a-preprocessor-shootout.html>

<div><div><div><blockquote><p>本文根据<a href="http://net.tutsplus.com/author/johnathan-croom/" rel="author" title="Posts by Johnathan Croom">Johnathan Croom</a>的《<a href="http://net.tutsplus.com/tutorials/html-css-techniques/sass-vs-less-vs-stylus-a-preprocessor-shootout/">Sass vs. LESS vs. Stylus: Preprocessor Shootout</a>》所译，整个译文带有我们自己的理解与思想，如果译得不好或不对之处还请同行朋友指点。如需转载此译文，需注明英文出处：<a href="http://net.tutsplus.com/tutorials/html-css-techniques/sass-vs-less-vs-stylus-a-preprocessor-shootout/">http://net.tutsplus.com/tutorials/html-css-techniques/sass-vs-less-vs-stylus-a-preprocessor-shootout/</a>，以及作者相关信息</p>
<p style="text-align:right">——作者：<a href="http://net.tutsplus.com/author/johnathan-croom/" rel="author" title="Posts by Johnathan Croom">Johnathan Croom</a></p>
<p style="text-align:right">——译者：<a href="http://www.w3cplus.com">大漠</a></p>
</blockquote>
<p>发挥CSS预处器的作用是一种很有挑战性的事情。CSS预处器有不同的语言，有不同的语法和功能。</p>
<p>在这篇文章中,我们将介绍三种不同CSS预处器的蛮量、功能以及他们的好处——<a href="http://sass-lang.com/">Sass</a>、<a href="http://lesscss.org/">LESS</a>和<a href="http://learnboost.github.com/stylus/">Stylus</a></p>
<h4>
	介绍</h4>
<p>CSS预处理器是一种语言，用来编写一些CSS的特性,而且无需考虑浏览器兼容性的问题。他们通过编译的代码编写成一般的CSS,不要在停留在石器时代了。CSS预处器有成千上万的特性,在本文中我们将一一介绍。让我们开始。</p>
<h4>
	语法</h4>
<p>在使用CSS预处器之前最重要的是对语法的理解，幸运的是，这三种CSS预处器的语法和CSS都差不多。</p>
<h4>
	Sass和LESS</h4>
<p>Sass和LESS都使用的是标准的CSS语法。这使用CSS预处器非常容易的将预处器代码转换成CSS代码。默认Sass使用“.scss”扩展名，而LESS使用“.less”扩展名。Sass和LESS基本设置可以像下面所示：</p>
<pre>
/* style.scss 或者 style.less */
h1 {
  color: #0982C1;
}
</pre><p>正如你所看到的，在Sass和LESS样式中，这样的代码是在简单不过的了。</p>
<p>重要的一点是，Sass也同时支持老的语法,就是不使用花括号和分号，而且文件使用“.sass”扩展名，他的语法类似于：</p>
<pre>
/* style.sass */
h1
  color: #0982c1
</pre><h4>
	Stylus</h4>
<p>Stylus的语法花样多一些，它使用“.styl”的扩展名，Stylus也接受标准的CSS语法，但是他也接受不带花括号和分号的语法，如下所示：</p>
<pre>
/* style.styl */
h1 {
  color: #0982C1;
}
/* 省略花括号 */
h1
  color: #0982C1;
/* 省略花括号和分号 */
h1
  color #0982C1
</pre><p>你也可以在同一个样式单中使用不同的变量，例如下面的写法也不会报错：</p>
<pre>
h1 {
  color #0982c1
}
h2
  font-size: 1.2em	
</pre><h4>
	变量(Variables)</h4>
<p>你可以在CSS预处理器中声明变量,并在整个样式表中使用。CSS预处理器支持任何变量（例如：颜色、数值（不管是否包括单位）、文本）。然后你可以在任意地方引用变量。</p>
<p><strong>Sass</strong></p>
<p>Sass声明变量必须是“$”开头，后面紧跟变量名和变量值，而且变量名和变量值需要使用冒号（：）隔开。就像CSS属性一样：</p>
<pre>
$mainColor: #0982c1;
$siteWidth: 1024px;
$borderStyle: dotted;
body {
  color: $mainColor;
  border: 1px $borderStyle $mainColor;
  max-width: $siteWidth;	
}	
</pre><p><strong>LESS</strong></p>
<p>LESS声明变量和Sass声明变量一样，唯一区别是变量名前面使用是的“@”字符：</p>
<pre>
@mainColor: #0982c1;
@siteWidth: 1024px;
@borderStyle: dotted;
body {
  color: @mainColor;
  border: 1px @borderStyle @mainColor;
  max-width: @siteWidth;
}	
</pre><p><strong>Stylus</strong></p>
<p>Stylus声明变量没有任何限定,你可以使用"$"符号开始。结尾的分号(;)可有可无,但变量名和变量值之间的等号(=)是需要的。有一点需要注意的是,如果我们使用"@"符号开头来声明（0.22.4）变量，Stylus会进行编译，但其对应的值并不会赋值给变量。换句话说，在Stylus中不要使用“@”符号开头声明变量。</p>
<pre>
mainColor = #0982c1
siteWidth = 1024px
$borderStyle = dotted
body
  color mainColor
  border 1px $borderStyle mainColor
  max-width siteWidth
</pre><p><strong>转译出来的CSS</strong></p>
<p>上面的代码都将转译成相同的CSS。你可以想像一下,变量的作用有多大。我们不需要为了修改一个颜色而输入许多次,也不需要为了修改一个宽度去到找寻找他.(我们只需要修改定义好的变量,修改一次就足够).下面是转译过来的CSS代码:</p>
<pre>
 body {
  color: #0982c1;
  border: 1px dotted #0982c1;
  max-width: 1024px;
} 
</pre><h4>
	嵌套（Nesting）</h4>
<p>如果我们在CSS中多个元素有一个相同的父元素，那么写样式会变得很乏味，我们需要一遍一遍的在每个元素前写这个父元素.</p>
<pre>
section {
  margin: 10px;
}
section nav {
  height: 25px;
}
section nav a {
  color: #0982C1;
}
section nav a:hover {
  text-decoration: underline;
}  
</pre><p>相反，使用CSS预处理器，我们可以在父元素的花括号({})写这些元素。同时可以使用“&amp;”符号来引用父选择器。</p>
<p><strong>Sass、LESS和Stylus</strong></p>
<p>对于嵌套选择器来说，三个CSS预处器都具有相同的语法：</p>
<pre>
section {
  margin: 10px;
  nav {
    height: 25px;
    a {
      color: #0982C1;
      &amp;:hover {
        text-decoration: underline;
      }
    }
  }
} 
</pre><p><strong>转译出来的CSS</strong></p>
<p>上面的预处器转译出来的CSS代码。和我们开始展示的CSS代码是相同的。非常的方便吧！</p>
<pre>
section {
  margin: 10px;
}
section nav {
  height: 25px;
}
section nav a {
  color: #0982C1;
}
section nav a:hover {
  text-decoration: underline;
}  
</pre><h4>
	混合（Mixins）</h4>
<p>Mixins是预处器中的函数。平时你在写样式时肯定有碰到过，某段CSS样式经常要用到多个元素中，这样你就需要重复的写多次。在CSS预处器中，你可以为这些公用的CSS样式定义一个Mixin，然后在你CSS需要使用这些样式的地方，直接调用你定义好的Mixin。这是一个非常有用的特性。Mixins是一个公认的选择器，还可以在Mixins中定义变量或者是默认参数。</p>
<p><strong>Sass</strong></p>
<pre>
 /* Sass定义了一个名叫error的mixin，这个error设置了一个参数“$borderWidth”,在没特别定义外，这个参数的值是默认值2px*/
@mixin error($borderWidth: 2px) {
  border: $borderWidth solid #F00;
  color: #F00;
}
.generic-error {
  padding: 20px;
  margin: 4px;
  @include error(); /* 调用error mixins */
}
.login-error {
  left: 12px;
  position: absolute;
  top: 20px;
  @include error(5px); /* 调用error mixins，并将参数$borderWidth的值指定为5px*/
} 
</pre><p><strong>LESS</strong></p>
<pre>
 /* LESS 定义了一个名叫error的mixin，这个error设置了一个参数“$borderWidth”,在没特别定义外，这个参数的值是默认值2px */
.error(@borderWidth: 2px) {
  border: @borderWidth solid #F00;
  color: #F00;
}
.generic-error {
  padding: 20px;
  margin: 4px;
  .error(); /* 调用error mixins */
}
.login-error {
  left: 12px;
  position: absolute;
  top: 20px;
  .error(5px); /* 调用error mixins，并将参数$borderWidth的值指定为5px */
} 
</pre><p><strong>Stylus</strong></p>
<pre>
 /* Stylus 定义了一个名叫error的mixin，这个error设置了一个参数“$borderWidth”,在没特别定义外，这个参数的值是默认值2px */
error(borderWidth= 2px) {
  border: borderWidth solid #F00;
  color: #F00;
}
.generic-error {
  padding: 20px;
  margin: 4px;
  error(); /* 调用error mixins */
}
.login-error {
  left: 12px;
  position: absolute;
  top: 20px;
  error(5px); /* 调用error mixins，并将参数$borderWidth的值指定为5px */
} 
</pre><p><strong>转译出来的CSS</strong></p>
<p>上面三种CSS预处器转译出来的CSS代码都是一样的</p>
<pre>
.generic-error {
  padding: 20px;
  margin: 4px;
  border: 2px solid #f00;
  color: #f00;
}
.login-error {
  left: 12px;
  position: absolute;
  top: 20px;
  border: 5px solid #f00;
  color: #f00;
}
</pre><blockquote>
<p>Mixins在三种预处器中都有所不同：</p>
<ol>
<li>
			<strong>Sass</strong>：在Sass定义Mixins和LESS、Stylus有所不同，在声明Mixins时需要使用“@mixin”,然后后面紧跟Mixins的名，他也可以定义参数，同时可以给这个参数设置一个默认值，但参数名是使用“$”符号开始，而且和参数值之间需要使用冒号（：）分开。另外在Sass中调用Mixins需要使用“@include”，然后在其后紧跟你要调用的Mixins名。</li>
<li>
			<strong>LESS</strong>：LESS中声明Mixins和CSS定义样式非常类似，可以将Mixins看成是一个选择器，当然Mixins也可以设置参数，并给参数设置默认值。不过设置参数的变量名是使用“@”开始，同样参数和默认参数值之间需要使用冒号（：）分开。</li>
<li>
			<strong>Stylus</strong>：Stylus和前两者也略有不同，他可以不使用任何符号，就是直接定义Mixins名，然后在定义参数和默认值之间用等号（=）来连接。</li>
</ol>
<p>上面只是Mixins在三个CSS预处器的简单区别，详细的还可以进入他们的官网了解，或者对比一下上面的三段代码。</p>
<p style="text-align:right">—— <a href="http://www.w3cplus.com" title="大漠">大漠</a></p>
</blockquote>
<h4>
	继承（Inheritance）</h4>
<p>在多个元素应用相同的样式时，我们在CSS通常都是这样写：</p>
<pre>
p,
ul,
ol {
  /* 样式写在这 */
}  
</pre><p>这样做非常的好，但往往我们需要给单独元素添加另外的样式，这个时候我们就需要把其中选择器单独出来写样式，这样一回来我们维护样式就相当的麻烦。为了应对这个问题，CSS预处理器可以从一个选择继承另一个选择器下的所有样式。</p>
<p><strong>Sass和Stylus</strong></p>
<pre>
.block {
  margin: 10px 5px;
  padding: 2px;
}
p {
  @extend .block; /* 继承.block所有样式 */
  border: 1px solid #EEE;
}
ul, ol {
  @extend .block; /* 继承.block所有样式 */
  color: #333;
  text-transform: uppercase;
}  
</pre><p><strong>上面的代码转译成CSS</strong></p>
<pre>
.block, p, ul, ol {
  margin: 10px 5px;
  padding: 2px;
}
p {
  border: 1px solid #EEE;
}
ul, ol {
  color: #333;
  text-transform: uppercase;
}  
</pre><p><strong>LESS</strong></p>
<p>LESS支持的继承和Sass与Stylus不一样,他不是在选择器上继承，而是将Mixins中的样式嵌套到每个选择器里面。这种方法的缺点就是在每个选择器中会有重复的样式产生。</p>
<pre>
 .block {
  margin: 10px 5px;
  padding: 2px;
}
p {
  .block; /* 继承 '.block'中的样式 */
  border: 1px solid #EEE;
}
ul, ol {
  .block; /*继承'.block' 中的样式*/
  color: #333;
  text-transform: uppercase;
} 
</pre><p><strong>转译出来的CSS代码</strong></p>
<pre>
.block {
  margin: 10px 5px;
  padding: 2px;
}
p {
  margin: 10px 5px;
  padding: 2px;
  border: 1px solid #EEE;
}
ul,
ol {
  margin: 10px 5px;
  padding: 2px;
  color: #333;
  text-transform: uppercase;
}
</pre><p>正如所看到的，上面的代码“.block”的样式将会被插入到相应的你要继承的选择器中，但需要注意是的优先级的问题。</p>
<h4>
	导入（import）</h4>
<p>在CSS中，并不喜欢用@import来导入样式，因为这样的做法会增加HTTP的请求。但是在CSS预处理器中的导入（@import）规则和CSS的有所不同，它只是在语义上导入不同的文件，但最终结果是生成一个CSS文件。如果你是通过“@import 'file.css'”导入“file.css”样式文件，那效果跟普通CSS导入样式文件一样。注意：导入文件中定义了变量、混合等信息也将会被引入到主样式文件中，因此需要避免他们的相互冲突。</p>
<p><strong>Sass、LESS和Stylus</strong></p>
<pre>
/* file.{type} */
body {
  background: #EEE;
}
</pre><pre>
@import "reset.css";
@import "file.{type}";
p {
  background: #0982C1;
} 
</pre><p><strong>转译出来的CSS</strong></p>
<pre>
@import "reset.css";
body {
  background: #EEE;
}
p {
  background: #0982C1;
}  
</pre><h4>
	颜色函数</h4>
<p>颜色函数是CSS预处里器中内置的颜色函数功能，这些功能可以对颜色值进行处理，例如颜色的变亮、变暗、渐变颜色等处理十分的方便。</p>
<p><strong>Sass</strong></p>
<pre>
lighten($color, 10%); /* 返回的颜色在$color基础上变亮10% */
darken($color, 10%);  /* 返回的颜色在$color基础上变暗10% */
saturate($color, 10%);   /* 返回的颜色在$color基础上饱和度增加10% */
desaturate($color, 10%); /* 返回的颜色在$color基础上饱和度减少10% */
grayscale($color);  /* 返回$color的灰度色*/
complement($color); /* returns complement color of $color */
invert($color);     /* 返回$color的反相色 */
mix($color1, $color2, 50%); /* mix $color1 with $color2 with a weight of 50% */ 
</pre><p>这只是Sass中颜色函数的一个简单列表,更多详细的介绍你可以阅读<a href="http://sass-lang.com/docs/yardoc/Sass/Script/Functions.html">Sass文档</a>。</p>
<p>颜色函数何以运用到任何一个元素上都是一个有颜色的CSS属性，下面是一个简单的例子：</p>
<pre>
$color: #0982C1;
h1 {
  background: $color;
  border: 3px solid darken($color, 50%);/*边框颜色在$color的基础上变暗50%*/
}  
</pre><p><strong>LESS</strong></p>
<pre>
lighten(@color, 10%); /* 返回的颜色在@color基础上变亮10% */
darken(@color, 10%);  /* 返回的颜色在@color基础上变暗10%*/
saturate(@color, 10%);   /* 返回的颜色在@color基础上饱和度增加10% */
desaturate(@color, 10%); /* 返回的颜色在@color基础上饱和度降低10%*/
spin(@color, 10);  /* 返回的颜色在@color基础上色调增加10 */
spin(@color, -10); /* 返回的颜色在@color基础上色调减少10 */
mix(@color1, @color2); /* 返回的颜色是@color1和@color2两者的混合色 */  
</pre><p>LESS的完整颜色函数功能，请阅读<a href="http://lesscss.org/#-color-functions">LESS 文档</a></p>
<p>下面是LESS中如何使用一个颜色函数的简单例子</p>
<pre>
@color: #0982C1;
h1 {
  background: @color;
  border: 3px solid darken(@color, 50%);
}  
</pre><p><strong>Stylus</strong></p>
<pre>
lighten(color, 10%); /* 返回的颜色在'color'基础上变亮10% */
darken(color, 10%);  /* 返回的颜色在'color'基础上变暗10% */
saturate(color, 10%);   /* 返回的颜色在'color'基础上饱和度增加10% */
desaturate(color, 10%); /* 返回的颜色在'color'基础上饱和度降低10% */  
</pre><p>有关于Stylus的颜色函数介绍，请阅读<a href="http://learnboost.github.com/stylus/docs/bifs.html">Stylus文档</a></p>
<p>下面是Stylus颜色函数的一个简单实例</p>
<pre>
color = #0982C1
h1
  background color
  border 3px solid darken(color, 50%)  
</pre><h4>
	运算符（Operations）</h4>
<p>我们都向往在CSS做一些运算，但是无法实现。但是在CSS预处器中对样式做一些运算是一点问题都没有了，例如：</p>
<p><strong>Sass、LESS和Stylus</strong></p>
<pre>
body {
  margin: (14px/2);
  top: 50px + 100px;
  right: 100px - 50px;
  left: 10 * 10;
}  
</pre><h4>
	实际运用</h4>
<p>我们介绍了CSS预处理器各方面的特性，但我们还没有实战过。下面是CSS预处理器应用的一些例子。</p>
<p><strong>属性前缀</strong></p>
<p>这是宣传CSS预处理器的原因之一，并且是一个很好的理由，这样可以节省大量的时间和汗水。创建一个minxin来处理浏览器的前缀问题是一个很简单的，并且能节省大量的重复工作和痛苦的代码编辑，我们来看一个例子。</p>
<p><strong>Sass</strong></p>
<pre>
 @mixin border-radius($values) {
  -webkit-border-radius: $values;
     -moz-border-radius: $values;
          border-radius: $values;
}
div {
  @include border-radius(10px);
} 
</pre><p><strong>LESS</strong></p>
<pre>
.border-radius(@values) {
  -webkit-border-radius: @values;
     -moz-border-radius: @values;
          border-radius: @values;
}
div {
  .border-radius(10px);
}  
</pre><p><strong>Stylus</strong></p>
<pre>
border-radius(values) {
  -webkit-border-radius: values;
     -moz-border-radius: values;
          border-radius: values;
}
div {
  border-radius(10px);
}  
</pre><p><strong>转译出来的CSS</strong></p>
<pre>
div {
  -webkit-border-radius: 10px;
     -moz-border-radius: 10px;
          border-radius: 10px;
}  
</pre><blockquote>
<p>浏览器前缀的问题，主要是针对于CSS3属性的运用，众所周知，CSS3的属性有并不是所有浏览器都支持属性的标准语法，因此在实际运用中时，不得不加上各浏览器的前缀来识别，这对于我们前端人员来说是多么苦逼的一件事情。虽然有prefix这样的js脚本帮我们解决，但对于怎么说也需要额外添加一个脚本文件，这对于追求完美的同学来说可能完法接受。</p>
<p>现在多了一种解决方案，就是使用CSS预处理器，如上面圆角的实现方法，这样减轻了我们很多工作量。如果你对这一部分实现方法感兴趣，不仿花时间阅读一下以下源码：</p>
<ol>
<li>
			<a href="https://github.com/airen/cssmixins">cssmixins</a>：由 <a href="https://github.com/wagerfield">Matthew Wagerfield</a>整理的CSS3属性在三种预处理器中的Mixins的定义： <a href="https://github.com/airen/cssmixins/blob/master/less/css3.less">LESS</a>、 <a href="https://github.com/airen/cssmixins/blob/master/sass/css3.sass">Sass</a> (还有 <a href="https://github.com/airen/cssmixins/blob/master/scss/css3.scss">SCSS</a>)和 <a href="https://github.com/airen/cssmixins/blob/master/styl/css3.styl">Stylus</a></li>
<li>
			<a href="https://github.com/JoelSutherland/LESS-Prefixer/blob/master/prefixer.less">LESS-Prefixer</a></li>
<li>
			<a href="http://css-tricks.com/custom-user-mixins/">Custom User @mixins</a></li>
</ol>
<p style="text-align:right">—— <a href="http://www.w3cplus.com" title="大漠">大漠</a></p>
</blockquote>
<h4>
	3D文本</h4>
<p>使用text-shadow的多重属性制作 <a href="http://www.w3cplus.com/demo/pure-css3-3d-text-effects.html" title="Pure CSS3 3D Text Effects">3D文本</a>效果是一个很好的方法。唯一麻烦的问题就是修改文本阴影的颜色。如果我们使用Mixin和颜色函数的话，实现3D文本效果就非常的轻松了，我们来尝试一下。</p>
<p><strong>Sass</strong></p>
<pre>
@mixin text3d($color) {
  color: $color;
  text-shadow: 1px 1px 0px darken($color, 5%),
               2px 2px 0px darken($color, 10%),
               3px 3px 0px darken($color, 15%),
               4px 4px 0px darken($color, 20%),
               4px 4px 2px #000;
}
h1 {
  font-size: 32pt;
  @include text3d(#0982c1);
}  
</pre><p><strong>LESS</strong></p>
<pre>
.text3d(@color) {
  color: @color;
  text-shadow: 1px 1px 0px darken(@color, 5%),
               2px 2px 0px darken(@color, 10%),
               3px 3px 0px darken(@color, 15%),
               4px 4px 0px darken(@color, 20%),
               4px 4px 2px #000;
}
span {
  font-size: 32pt;
  .text3d(#0982c1);
}  
</pre><p><strong>Stylus</strong></p>
<pre>
text3d(color)
  color: color
  text-shadow: 1px 1px 0px darken(color, 5%), 2px 2px 0px darken(color, 10%), 3px 3px 0px darken(color, 15%), 4px 4px 0px darken(color, 20%), 4px 4px 2px #000
span
  font-size: 32pt
  text3d(#0982c1)  
</pre><p>在Stylus中text-shadow的样式写在一行，是因为stylus中省略了花括号（｛｝）和分号（;）。</p>
<p>上面三种方法实现的效果都是一样的：</p>
<p><img alt="Less vs Sass" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/2013/LessAndSassAndStylus/less-sass5.jpg" style="padding:2px;border:1px solid #ccc;width:530px"></p>
<h4>
	列（Columns）</h4>
<p>我第一次接触CSS预处理器的时候，我就想着使用数字或者变量进行运算。使用数值和变量运算，可以很方便的实现适应屏幕大小的布局处理。只需要定义宽度的变量，我们就可以很方便的根据需求实现布局。下面的例子就是这么做的。</p>
<p><strong>Sass</strong></p>
<pre>
$siteWidth: 1024px;
$gutterWidth: 20px;
$sidebarWidth: 300px;
body {
  margin: 0 auto;
  width: $siteWidth;
}
.content {
  float: left;
  width: $siteWidth - ($sidebarWidth+$gutterWidth);
}
.sidebar {
  float: left;
  margin-left: $gutterWidth;
  width: $sidebarWidth;
} 
</pre><p><strong>LESS</strong></p>
<pre>
@siteWidth: 1024px;
@gutterWidth: 20px;
@sidebarWidth: 300px;
body {
  margin: 0 auto;
  width: @siteWidth;
}
.content {
  float: left;
  width: @siteWidth - (@sidebarWidth+@gutterWidth);
}
.sidebar {
  float: left;
  margin-left: @gutterWidth;
  width: @sidebarWidth;
}  
</pre><p><strong>Stylus</strong></p>
<pre>
siteWidth = 1024px;
gutterWidth = 20px;
sidebarWidth = 300px;
body {
  margin: 0 auto;
  width: siteWidth;
}
.content {
  float: left;
  width: siteWidth - (sidebarWidth+gutterWidth);
}
.sidebar {
  float: left;
  margin-left: gutterWidth;
  width: sidebarWidth;
}  
</pre><p><strong>转译出来的CSS</strong></p>
<pre>
body {
  margin: 0 auto;
  width: 1024px;
}
.content {
  float: left;
  width: 704px;
}
.sidebar {
  float: left;
  margin-left: 20px;
  width: 300px;
}  
</pre><h4>
	明显的怪癖</h4>
<p>使用CSS预处器都是有些怪癖的，我需要去寻找一些这样的人，如果你真的对这些感兴趣，你可以去搜索他们以及相关的文档，因为在你使用CSS预处理器非常有帮助。</p>
<p><strong>错误报告</strong></p>
<p>如果你经常使用CSS，你会发现很难找到CSS中出错的地方。也许你也会像我一样，花一下午的时间，发了疯的注解每行样式代码来寻找这个CSS错误。</p>
<p>CSS预处理器就轻松多了，他会给你报告错误。你可以阅读这篇<a href="http://tjholowaychuk.com/post/5002088731/stylus-vs-sass-vs-less-error-reporting">文章</a>，学习如何让CSS预处理器报告错误。</p>
<p><strong>注释(Comments)</strong></p>
<p>CSS预处理器支持“/* */”这样的多行注释方式（类似于CSS的注释方式），也支持“//”单行注释方式（类似于Javascript的注释方式）。</p>
<p>需要注意，如果你要压缩文件，你需要把所有注释都删除。</p>
<h4>
	总结</h4>
<p>三个预处理器我们都覆盖了（Sass、LESS和Stylus）,都以他们独特的特性完成了相同的效果。这样让开发人员更好的选择适合自己的CSS预处理器，从而更好的维护自己的代码，提高开发效率。</p>
<blockquote><p>虽然不是开发的要求,但CSS预处理器可以节省大量的时间，并且有一些非常有用的功能。</p>
</blockquote>
<p>我鼓励大家尽可能的尝试使用CSS预处理器，这样就可以有效的让你选择一个你最喜欢的和知道他为什么是受人青睐的。如果你还没有尝试过使用CSS预处理器来写你的CSS样式，我强烈建议你尝试一下。</p>
<p>如果你有最喜欢的CSS预处理器的功能，我并没有提到的，希望在下面的评论中与我们分享。</p>
<p><strong>译者手语：</strong>整个翻译依照原文线路进行，并在翻译过程略加了个人对技术的理解。如果翻译有不对之处，还烦请同行朋友指点。谢谢！</p>
<p>如需转载烦请注明出处：</p>
<p>英文原文：<a href="http://net.tutsplus.com/tutorials/html-css-techniques/sass-vs-less-vs-stylus-a-preprocessor-shootout/">http://net.tutsplus.com/tutorials/html-css-techniques/sass-vs-less-vs-stylus-a-preprocessor-shootout</a></p>
<p>中文译文：<a href="http://www.w3cplus.com/css/sass-vs-less-vs-stylus-a-preprocessor-shootout.html">http://www.w3cplus.com/css/sass-vs-less-vs-stylus-a-preprocessor-shootout.html</a></p>
</div></div></div><div><ul><li><a href="http://www.w3cplus.com/blog/tags/8.html">css</a></li></ul></div><div><ul><li><a href="http://www.w3cplus.com/blog/tags/302.html">sass</a></li><li><a href="http://www.w3cplus.com/blog/tags/48.html">less</a></li><li><a href="http://www.w3cplus.com/blog/tags/303.html">stylus</a></li></ul></div><div><div><div><div>
      <div>4</div>
                  <a href="http://www.w3cplus.com/vote/node/621/1/vote/alternate/cZokd-umRE1UUcpXqVJuDkjXEQjmfh-10P4Ms6a5onM/nojs" rel="nofollow">
                <div title="Vote up!"></div>
          <div>Vote up!</div>
              </a>
                </div>
</div></div></div><img src="http://www1.feedsky.com/t1/711684948/W3CPlus/feedsky/s.gif?r=http://www.w3cplus.com/css/sass-vs-less-vs-stylus-a-preprocessor-shootout.html" border="0" height="0" width="0">