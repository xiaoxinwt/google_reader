---
layout: post
title:  "Flexbox制作CSS布局易如反掌"
date:   2013-05-24 12:08:22
author: Airen
categories: program
---

## Flexbox制作CSS布局易如反掌
### by Airen
### at 2013-05-24 12:08:22
### original <http://www.w3cplus.com/css3/designing-css-layout-with-flexbox.html>

<div><div><div><blockquote>
	<p>本文由<a href="http://www.w3cplus.com">大漠</a>根据<a href="http://coding.smashingmagazine.com/author/david-storey" rel="author" title="Posts by David Storey">David Storey</a>的《<a href="http://coding.smashingmagazine.com/2013/05/22/centering-elements-with-flexbox">Designing CSS Layouts With Flexbox Is As Easy As Pie</a>》所译，整个译文带有我们自己的理解与思想，如果译得不好或不对之处还请同行朋友指点。如需转载此译文，需注明英文出处：<a href="http://coding.smashingmagazine.com/2013/05/22/centering-elements-with-flexbox">http://coding.smashingmagazine.com/2013/05/22/centering-elements-with-flexbox</a>，以及作者相关信息</p>
	<p style="text-align:right">——作者：<a href="http://coding.smashingmagazine.com/author/david-storey" rel="author" title="Posts by David Storey">David Storey</a></p>
	<p style="text-align:right">——译者：<a href="http://www.w3cplus.com">大漠</a></p>
</blockquote>
<blockquote>
	<p>这篇文章的内容来自于Lea Verou 和 David Storey写的《<a href="https://shop.smashingmagazine.com/smashing-book-3-print-bundle.html">Smashing Book #3</a>》一书中的“使用CSS3重塑、重新编码、重新想像”一节。</p>
	<p style="text-align:right"><a href="http://coding.smashingmagazine.com/author/david-storey" rel="author" title="Posts by David Storey">David Storey</a></p>
</blockquote>
<p>伸缩盒模型（flexbox）是一个新的盒子模型，主要优化了UI布局。作为实际布局的第一个CSS模块（浮动真的应该主要用来制作文本围绕图片这样的效果），它使很多任务容易多。Flexbox的功能主要包手：简单使用一个元素居中（包括水平垂直居中），可以让扩大和收缩元素来填充容器的可利用空间，可以改变源码顺序独立布局，以及还有其他的一些功能。</p>
<p>Flexbox一直都存在。它最开始作为Mozilla XUL的一个功能，被用来制作程序界面，如Firefox的工具栏，就多次使用这个属性。该规范最近才达到稳定，在主要的浏览器对新的版本有相当完整的支持。</p>
<p>然而有一些事项需要注意。在IE中规范更改了他的语法，因此你将需要使用一个稍微不同的语法。Chrome当前版本仍然需要添加前缀“-webkit-”，而Firefox和Safari仍然还在使用最老版本的语法。Firefox已经更新为最新的规范，但是，在实际项目中目前最好还先别使用最新的规范，直到它被认为没有bug了或者更稳定了，在使用。在这之前，Firefox还是使用最老的语法规范。</p>
<p>当你给一直元素使用了Flexbox模块，那么他的子元素就会指定的方向在水平或者纵向方向排列。这些子元素会按照一定的比例进行扩展或收缩来填补容器的可用空间。</p>
<h2>
	案例：水平和垂直居中（网页设计的圣杯）</h2>
<p>网页设计师都很希望元素能够居于页面的中心——是的，可能通过选择器确定父元素的高或者让摆脱IE6（第二可能将不存在）。使用Flexbox,这是非常简单的。让我们先创建一个基本的HTML模板，<a href="http://jsfiddle.net/pnNqd/">想让一个标题显示在中心位置</a>。最后，一旦我们添加所有的样式，他最后的样子像<a href="https://dl.dropboxusercontent.com/u/444684/flexbox/flexbox1.html">案例</a>演示的一样水平垂直居中。</p>
<pre>
&lt;!DOCTYPE html&gt;
&lt;html lang=&quot;en&quot;&gt;
&lt;head&gt;
  &lt;meta charset=&quot;utf-8&quot;/&gt;
  &lt;title&gt;Centering an Element on the Page&lt;/title&gt;
&lt;/head&gt;
&lt;body&gt;
  &lt;h1&gt;OMG, I’m centered&lt;/h1&gt;
&lt;/body&gt;
&lt;/html&gt;	
</pre>
<p>这里没有什么特别之处，甚至没一个div容器。一切魔力都在CSS之中：</p>
<pre>
html {
  height: 100%;
} 

body {
  display: -webkit-box;  /* 老版本语法: Safari,  iOS, Android browser, older WebKit browsers.  */
  display: -moz-box;    /* 老版本语法: Firefox (buggy) */ 
  display: -ms-flexbox;  /* 混合版本语法: IE 10 */
  display: -webkit-flex;  /* 新版本语法： Chrome 21+ */
  display: flex;       /* 新版本语法： Opera 12.1, Firefox 22+ */

  /*垂直居中*/	
  /*老版本语法*/
  -webkit-box-align: center; 
  -moz-box-align: center;
  /*混合版本语法*/
  -ms-flex-align: center; 
  /*新版本语法*/
  -webkit-align-items: center;
  align-items: center;

  /*水平居中*/
  /*老版本语法*/
  -webkit-box-pack: center; 
  -moz-box-pack: center; 
  /*混合版本语法*/
  -ms-flex-pack: center; 
  /*新版本语法*/
  -webkit-justify-content: center;
  justify-content: center;

  margin: 0;
  height: 100%;
  width: 100% /* needed for Firefox */
} 
/*实现文本垂直居中*/
h1 {
  display: -webkit-box; 
  display: -moz-box;
  display: -ms-flexbox;
  display: -webkit-flex;
  display: flex;
 
  -webkit-box-align: center; 
  -moz-box-align: center;
  -ms-flex-align: center;
  -webkit-align-items: center;
  align-items: center;

  height: 10rem;
}	
</pre>
<p>在上面的CSS中已经包括了不同的版本，从仍然需要的老版本到最新版本和混合版本的语法。这可能看起来让人困惑，不同的语法适应不同的版本，在文章末尾创建对应的语法查询表格。</p>
<p>在我们的例子中并不是所有的CSS都包括在里面，因为我已经去掉了额外的样式，您可能知道如何使用以节省空间。</p>
<p>让我们看看需要将标题显示在页面中心需要的CSS样式。首先，要给html和body元素设置一个height为“100%”并且移去所有的margin。这使h1的容器占用浏览器的窗口的所有高度。在Firefox浏览器中，还需要设置一个width为100%来完成相应的效果。现在我们只需要让所有东西居中。</p>
<h3>
	启用Flexbox</h3>
<p>因为body元素包含了想要居中的标题元素，所以我们将他的display属性值设置为“flex”：</p>
<pre>
body {
  display: flex;	
}	
</pre>
<p>主要作用是让元素body使用flexbox布局，而不是普通的块布局。在文档流中的所有子元素（即不是绝对定位的元素）现在都变成了伸缩项目。</p>
<p>在IE10中使用的语法是“display:-ms-flexbox”,而老版本的Firefox和Webkit内核的浏览器需要使用“display:-prefix-box”(其中prefix是“moz”或者“webkit”)。你可以到文章末尾查看各种版本语法对照的表格。</p>
<p>这个时候，我们的元素都具有伸缩特性。那么他们具有哪些优势：他们可以相对于可用的空间展示他们的大小和位置；他们可以水平或垂直居中，甚至可以改变文档流顺序。</p>
<h3>
	水平居中</h3>
<p>接下来，我们希望我们的h1元素水平居中。你可能会说没什么大不了的，有比设置宽度并定义margin为auto设置更简单的方法。我们只要让伸缩容器使用他们的伸缩项目居中。默认情况之下，伸缩项目是水平排列，因为只需要给伸缩项目设置“justify-content”属性，让他们沿着主轴方向居中：</p>
<pre>
body {
  display: flex;
  <strong style="color:green">justify-content: center;</strong>
}	
</pre>
<p>在IE10中，这个属性称作“flex-pack”，在老的浏览器中它称为“box-pack”(再一次需要使用浏览器的私有前缀)。其他的值还有“flex-start”、“flex-end”、“space-between”和“space-around”。在IE10和老的规范中，他们分别是“start”、“end”、“justify”和“distribute”（在老的规范中不支持“distribute”）。“flex-start”表示左对齐（如果书写模式是“rtl”表示右对齐），“flex-end”表示右对齐，“space-between”表示伸缩项目平均分布布局轴上，“space-around”均匀分布在布局轴上，而且第一个伸缩项目前和最后一个伸缩项目后的空间为中间伸缩项目间距的一半。</p>
<p>显示设置元素在主轴上的对齐方式，你可以使用“flex-flow”属性。默认值为“row”,这和我们刚才得到效果相同。如果想让伸缩项目沿着侧轴（垂直于主轴）排列，可以将“flex-flow”设置为“column”。如果我们将它添加到我们的实例中，你将看到的元素是垂直居中，而不是水平居中。当row和column添加后缀“-reverse”将会反向排列（flex-flow:row-reverse或者flex-flow:column-reverse），但这些属性并没有运用到我们例子中，因为我们只有一个项目。</p>
<p>各种版本规范对这些属性的解析都略有差异，详细的可以查看文章末尾的表格。另一个大家要记在心里的就是“flex-flow”属性和“writing-mode”有直接的关系。即，当使用“writing-mode:vertical-rl”时转向垂直文布局（如传统的中文、日文和韩文排版本民，也就是竖排），flex-flow:row将垂直排列伸缩项目，和column将水平排列伸缩项目。</p>
<h3>
	垂直居中</h3>
<p>定义垂直居中和定义水平居中一样的容易。我们只需要使用适当的属性控制伸缩项目沿着侧轴的排列方向。那是什么？侧轴基本上是与主轴垂直的另一个轴。因此，如果伸缩项目水平对齐，那么侧轴就是垂直对齐。我们可以通过align-item来设置（在IE10中使用flex-align，在老的浏览器使用box-align）。</p>
<pre>
body {
  /*记住，在IE10和其他老版本浏览中要使用其他版本的规范 */
  display: flex;
  justify-content: center;
  <strong style="color:green">align-items: center;</strong>
}	
</pre>
<p>这就是flexbox制作居中的所有样式。我们也可以使用“flex-start”(start)和flex-end(end)值，以及baselineT和stretch。我们来看另一个完成的<a href="https://dl.dropboxusercontent.com/u/444684/flexbox/flexbox1.html">案例</a></p>
<p><a href="https://dl.dropboxusercontent.com/u/444684/flexbox/flexbox1.html"><img alt="Flexbox制作CSS布局易如反掌" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/2013/flexbox-s1.jpg" style="padding:2px;border:1px solid #ccc;width:560px"></a></p>
<p>使用flexbox制作的一个简单的水平垂直居中效果。<a href="https://dl.dropboxusercontent.com/u/444684/flexbox/flexbox1.html">更大窗口浏览</a></p>
<p>你可以注意到了，h1元素的文本也在内部垂直居中。这里没使用margin或者line-height，但是我们再次使用了flexbox，让文本变成了一个匿名伸缩项目（在这个案例中，行文本是h1内的元素）。无论h1元素有多高，文本将永远垂直居中。</p>
<pre>
h1 {
  /* 记住，在IE10和其他老版本浏览中要使用其他版本的规范 */
  display: flex;
  align-items: center;
  height: 10rem;
}	
</pre>
<h2>
	伸缩尺寸</h2>
<p>如果制作元素居中，flexbox肯定可以实现，而且非常的爽。但是有更多的时候不只是要这样的效果。让我们来看看伸缩项目扩大和收缩适合伸缩容器的可用空间。用的浏览器打开这个<a href="http://www.w3cplus.com/">案例</a>。</p>
<p><a href="https://dl.dropboxusercontent.com/u/444684/flexbox/flexbox2.html"><img alt="Flexbox制作CSS布局易如反掌" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/2013/flexbox-s2.jpg" style="padding:2px;border:1px solid #ccc;width:560px"></a></p>
<p>一个使用flexbox创建的交互式幻灯片，<a href="https://dl.dropboxusercontent.com/u/444684/flexbox/flexbox2.html">更大窗口浏览</a></p>
<p>HTML和CSS类似于前一个示例。使用相同的方法，把所有元素在页面中居中显示。此外，我们想让标题（header元素内）保持一样的尺寸，其他五个盒子（section元素）根据浏览器宽度自动调整大小。因此我们要使用一个新的属性“flex”。</p>
<pre>
section {
  /* removed other styles to save space */
  -prefix-box-flex: 1; /* old spec webkit, moz */
  flex: 1;
  height: 250px;
}	
</pre>
<p>我们做的是让每个section元素占有1flex单元。因为我们还没有给五个section元素明确的设置宽度，而每个section元素都有相同的宽度。把“header”设置了一个宽度（277px），因为他不是伸缩性的。我们把body剩下的宽度计算到每个section元素中。现在，我们来改变浏览器窗口大小，section元素将会扩展或收缩。</p>
<p>在这个例子中，我们已经设置了一个一样的高度，但是使用同样的方法也可以实现伸缩性。我们可能不会总是希望所有元素是相同的大小，所以让我来做一个更大的。在悬浮状下，我们设置元素为2flex单元。</p>
<pre>
section:hover {
  -prefix-box-flex: 2;
  flex: 2;
  cursor: pointer;
}	
</pre>
<p>现在可用的空间除以6，而在悬浮状态是占有2份。注意：一个元素的2flex单元并不一定就是1flex单元宽度的两倍。它只获得了添加两倍比例到他的可用空间的首先宽度。在我们的示例中，首先宽度是0（默认状态下）。</p>
<h2>
	独立的源顺序</h2>
<p>我们最后要介绍的一个技巧，研究如何在布局中实现源顺序独立。当我们点击盒子时，我们想把元素移动到所有盒子元素的左边，也就是直接移到标题的后面。我们可以使用"order"属性来实现。默认情况下，伸缩项目的order值是0，他们是按照文档流顺序排列。<a href="https://dl.dropboxusercontent.com/u/444684/flexbox/flexbox3.html">在更新的示例中点击你最喜欢的人</a>你可以看到order的变化。</p>
<p><a href="https://dl.dropboxusercontent.com/u/444684/flexbox/flexbox3.html"><img alt="Flexbox制作CSS布局易如反掌" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/2013/flexbox-s3.jpg" style="padding:2px;border:1px solid #ccc;width:560px"></a></p>
<p>让我们选择的元素移动到第一的位置，我们必须要设定一个较低的order值。我选择了“-1”。我们也需要把head元素的order设置为“-1”。这样选择的元素才不会移动到标题的前面。</p>
<pre>
header {
  -prefix-box-ordinal-group: 1; /* old spec; must be positive */
  -ms-flex-order: -1; /* IE 10 syntax */
  order: -1; /* new syntax */
} 

section[aria-pressed="true"] {
  /* Set order lower than 0 so it moves before other section elements,
      except old spec, where it must be positive.
   */
  -prefix-box-ordinal-group: 1;
  -ms-flex-order: -1;
  order: -1;

  -prefix-box-flex: 3;
  flex: 3;
  max-width: 370px; /* Stops it from getting too wide. */
}	
</pre>
<p>在旧的规范中，设置顺序（box-ordinal-group）属性值只接受一个正整数。因此，我们需要给每个元素设置order值为2，和选择的元素设置order为1。如果你想知道上面例子中aria-pressed="true"是什么？它是一个WAI-ARIR属性和属性值，当用户点击其中某个section元素时，就会自动加上。</p>
<p>这个属笥是用来告诉用户该元素激活了。如果你想了解更多有关于WAI-ARIA的信息，可以点击 Gez Lemon写的<a href="http://dev.opera.com/articles/view/introduction-to-wai-aria/">Introduction to WAI-ARIA</a>。因为当用点击后添加这个属性，在这个示例中需要一个简单的js文件才能正常工作，但flexbox本身并不需要它，它只是用来应用用户交互的。</p>
<p>但愿，这些能让给带来一些灵感，和足够的让你得到flexbox的入门知识，让你在你的设计中尝试使用flexbox。</p>
<h2>
	语法的变更</h2>
<p>正如你可能已经注意到了在这篇文章中，语法距离第一次实现flexbox已经有好几次的改变。下面的表格让我更好的了解各版本之间的变更。</p>
<h3>
	规范版本</h3>
<p><img alt="Flexbox制作CSS布局易如反掌" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/2013/flexbox-s4.jpg" style="padding:2px;border:1px solid rgb(204,204,204);width:680px"></p>
<h3>
	开启flexbox：让一个元素变成伸缩容器</h3>
<p><img alt="Flexbox制作CSS布局易如反掌" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/2013/flexbox-s5.jpg" style="padding:2px;border:1px solid rgb(204,204,204);width:680px"></p>
<h3>
	主轴对齐方式：指定伸缩项目沿主轴对齐方式</h3>
<p><img alt="Flexbox制作CSS布局易如反掌" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/2013/flexbox-s6.jpg" style="padding:2px;border:1px solid rgb(204,204,204);width:680px"></p>
<h3>
	侧轴对齐方式：指定伸缩项目沿侧轴对齐方式</h3>
<p><img alt="Flexbox制作CSS布局易如反掌" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/2013/flexbox-s7.jpg" style="padding:2px;border:1px solid rgb(204,204,204);width:680px"></p>
<h3>
	单个伸缩项目侧轴对齐方式</h3>
<p><img alt="Flexbox制作CSS布局易如反掌" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/2013/flexbox-s8.jpg" style="padding:2px;border:1px solid rgb(204,204,204);width:680px"></p>
<h3>
	伸缩项目行对齐方式：指定伸缩项目行在侧轴的对齐方式</h3>
<p><img alt="Flexbox制作CSS布局易如反掌" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/2013/flexbox-s9.jpg" style="padding:2px;border:1px solid rgb(204,204,204);width:680px"></p>
<p>这个只有伸缩项目有多行时才生效，这种情况只有伸缩容器设置了flex-wrap为wrap时，并且没有足够的空间把伸缩项目放在同一行中。这个将对每一行起作用而不是每一个伸缩项目。</p>
<h3>
	显示顺序：指定伸缩项目的顺序</h3>
<p><img alt="Flexbox制作CSS布局易如反掌" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/2013/flexbox-s10.jpg" style="padding:2px;border:1px solid rgb(204,204,204);width:680px"></p>
<h3>
	伸缩性：指定伸缩项目如何伸缩尺寸</h3>
<p><img alt="Flexbox制作CSS布局易如反掌" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/2013/flexbox-s11.jpg" style="padding:2px;border:1px solid rgb(204,204,204);width:680px"></p>
<p>flex属性在微软的草案与新标准或多或少不一样。主要的区别在于，它们都转换成标准缩写版本，他们的属性值为：flex-grow、flex-shrink和flex-basis。他们的值也使用同样的方式在速记。然而，flex-shrink（以前称为负flex）的默认值为1。这意味着伸缩项目默认不能收缩。以前，空间不足使用flex-shrink比例来收缩伸缩项目，但现在可以在flex-basis的基础上配合flex-shrink来收缩伸缩项目。</p>
<h3>
	伸缩流：指定伸缩容器主轴的伸缩流方向</h3>
<p><img alt="Flexbox制作CSS布局易如反掌" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/2013/flexbox-s12.jpg" style="padding:2px;border:1px solid rgb(204,204,204);width:680px"></p>
<p>在旧的版本规范中，使用box-direction属性设置为reverse和在新的规范版本中设置row-reverse或column-reverse得到的效果相同。如果你想要的效果是row或column你可以省略不设置，因为normal是默认的初始值。</p>
<p>当设置了direction为reverse,主轴就就翻转。意思是，当你使用“ltr”书写模式的，当你指定row-reverse时，所有伸缩项目会从右向左排列。类似的，column-reverse将会使所有伸缩项目从下向上排列，来代替从上往下排列。</p>
<p>在老的规范版本中，需要使用box-orient来设置书写模式的方向。当使用“ltr”模式时，horizontal可用在inline-axis，vertical可用block-axis。如果您使用的是一个自上而下的书写模式，如东亚那些传统的书写模式，这些值就会翻转。</p>
<h3>
	换行：指定伸缩项目是否沿着侧轴排列</h3>
<p><img alt="Flexbox制作CSS布局易如反掌" src="http://www.w3cplus.com/sites/default/files/styles/print_image/public/blogs/2013/flexbox-s13.jpg" style="padding:2px;border:1px solid rgb(204,204,204);width:680px"></p>
<p>wrap-reverse让伸缩项目在侧轴上进行start和end翻转，所以，如果伸缩项目在水平排列，伸缩项目翻转不会到一个新的线下面，他会翻转到一个新的线上面。（简单理解就是伸缩项目只是上下或前后翻转顺序）。</p>
<p>在写本文的时候，在Firefox中并不支持flex-wrap或者box-lines属笥。他不支持速记。</p>
<p>当前规范flex-flow是一个速记版本，他包括了换行flex-wrap和伸缩流flex-direction。在IE10中也支持这个版本规范。它目前还不支持Firefox浏览器，所以我建议避免使用它，仅使用flex-direction来指定伸缩流方向。</p>
<h2>
	总结</h2>
<p>嗯，这是一个伸缩容器。在这篇文章中，我介绍了一些flexbox所提供的功能。他具有源独立性，可以伸缩调整伸缩尺寸适合容器以及调整他的的对齐方式，我肯定你能找到方法在你的网站或应用程序使用flexbox功能。现在语法终于定下来了，在这里展示了所有的规范版本。所有主流浏览器都支持flexbox，至少最新版本的主流浏览器是这样。</p>
<p>虽然一些浏览器使用老的语法规范，Firefox看上去也在向新语法规范靠近和IE11将使用最新版本的语法规范。目前Safari还没有看到相关介绍，但这是一个显而易见的考虑，Chrome在和Blink与webkit分裂之前采用了最新的语法。目前，使用上面表格展示的各种语法，你会得到你需要的flexbox效果。</p>
<p>CSS布局正在变得越来越强大，Flexbox是第一个走出泥沼的。我们发现自己在这些年来，第一次使用提表格布局，然后是基于浮动的布局。IE10已经支持早期草案提出的网格布局规范，这有利于页面布局，还有Regions，他们将会彻底改变我们如何处理内容流布局。</p>
<p>如果你只需要支持现代浏览器或者提供一个优雅降级的方案，那么今天你就可以使用Flexbox。在不久的将来，Flexbox布局模块的各个属性都可以使用，这样我们可以使用最好的工具来完成工作。Flexbox正变成一个极好的工具。</p>
<h2>
	扩展阅读</h2>
<ul><li>
		“<a href="http://www.w3.org/TR/css3-flexbox/">CSS Flexible Box Layout Module</a>” (specification), W3C</li>
	<li>
		“<a href="http://caniuse.com/flexbox">Flexible Box Layout Module</a>,” Can I Use?</li>
	<li>
		“<a href="http://msdn.microsoft.com/en-us/library/ie/hh673531%28v=vs.85%29.aspx">Flexible box (‘Flexbox’) layout</a>,” Microsoft</li>
	<li>
		“<a href="https://developer.mozilla.org/en-US/docs/CSS/Tutorials/Using_CSS_flexible_boxes">Using CSS Flexible Boxes</a>,” Mozilla</li>
	<li>
		“<a href="http://css-tricks.com/old-flexbox-and-new-flexbox/">‘Old’ Flexbox and ‘New’ Flexbox</a>,” Chris Coyier</li>
</ul><blockquote>
	<h2>
		Flexbox中文教程</h2>
	<p>这段时间整理了一些国外有关于Flexbox的相关优秀教程，并将其转译成中文，放在了W3cplus上，如果您对Flexbox感兴趣，你可以花点时间阅读一下这些教程，如果您有更好的资源烦请在下面的评论中留言与我们一起分享：</p>
	<ol><li>
			<a href="http://www.w3cplus.com/css3/a-guide-to-flexbox.html">一个完整的Flexbox指南</a></li>
		<li>
			<a href="http://www.w3cplus.com/blog/666.html">深入了解 Flexbox 伸缩盒模型</a></li>
		<li>
			<a href="http://www.w3cplus.com/css3/ie10-flexbox-layout.html">IE10中的Flexible Box("Flexbox")布局</a></li>
		<li>
			<a href="http://www.w3cplus.com/css3/old-flexbox-and-new-flexbox.html">“老”的Flexbox和“新”的Flexbox</a></li>
		<li>
			<a href="http://www.w3cplus.com/css3/using-flexbox.html">使用Flexbox：新旧语法混用实现最佳浏览器兼容</a></li>
		<li>
			<a href="http://www.w3cplus.com/css3/advanced-cross-browser-flexbox.html">跨浏览器的Flexbox</a></li>
		<li>
			<a href="http://www.w3cplus.com/css3/a-look-on-whats-coming-up-css3-flexible-boxes.html">看看接下来会发生什么：CSS3 Flexible Boxes</a></li>
		<li>
			<a href="http://www.w3cplus.com/css3/responsive-design-of-the-future-with-flexbox.html">响应式设计的未来——Flexbox</a></li>
		<li>
			<a href="http://www.w3cplus.com/css3/css3-flexbox-layout.html">使用CSS3 Flexbox布局</a></li>
		<li>
			<a href="http://www.w3cplus.com/css3/flexbox-basics.html">Flexbox——快速布局神器</a></li>
		<li>
			<a href="http://www.w3cplus.com/css3/animating-flexboxes-the-lowdown.html">Flexbox中动画内幕</a></li>
		<li>
			<a href="http://www.w3cplus.com/codes/vertically-center-content-with-css3.html">CSS3实现水平垂直居中</a></li>
	</ol><p style="text-align:right">——大漠</p>
</blockquote>
<p><strong>译者手语：</strong>整个翻译依照原文线路进行，并在翻译过程略加了个人对技术的理解。如果翻译有不对之处，还烦请同行朋友指点。谢谢！</p>
<p>如需转载烦请注明出处：</p>
<p>英文原文：<a href="http://coding.smashingmagazine.com/2013/05/22/centering-elements-with-flexbox/">http://coding.smashingmagazine.com/2013/05/22/centering-elements-with-flexbox/</a></p>
<p>中文译文：<a href="http://www.w3cplus.com/css3/designing-css-layout-with-flexbox.html">http://www.w3cplus.com/css3/designing-css-layout-with-flexbox.html</a></p>
</div></div></div><div><ul><li><a href="http://www.w3cplus.com/blog/tags/11.html">css3</a></li></ul></div><div><ul><li><a href="http://www.w3cplus.com/blog/tags/157.html">flexbox</a></li><li><a href="http://www.w3cplus.com/blog/tags/180.html">layout</a></li><li><a href="http://www.w3cplus.com/blog/tags/131.html">垂直居中</a></li><li><a href="http://www.w3cplus.com/blog/tags/71.html">水平居中</a></li><li><a href="http://www.w3cplus.com/blog/tags/357.html">水平垂直居中</a></li></ul></div><div><div><div><div>
      <div>29</div>
                  <a href="http://www.w3cplus.com/vote/node/766/1/vote/alternate/tX1ff-_d92bC6av2jXKGjT-FnSMdL3x9ocY1JdAt99A/nojs" rel="nofollow">
                <div title="Vote up!"></div>
          <div>Vote up!</div>
              </a>
                </div>
</div></div></div><img src="http://www1.feedsky.com/t1/739259157/W3CPlus/feedsky/s.gif?r=http://www.w3cplus.com/css3/designing-css-layout-with-flexbox.html" border="0" height="0" width="0">