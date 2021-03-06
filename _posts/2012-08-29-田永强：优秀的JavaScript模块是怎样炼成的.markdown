---
layout: post
title:  "田永强：优秀的JavaScript模块是怎样炼成的"
date:   2012-08-29 06:51:05
author: 
categories: program
---

## 田永强：优秀的JavaScript模块是怎样炼成的
### by 
### at 2012-08-29 06:51:05
### original <http://blog.jobbole.com/26101/?utm_source=rss&utm_medium=rss&utm_campaign=%25e7%2594%25b0%25e6%25b0%25b8%25e5%25bc%25ba%25ef%25bc%259a%25e4%25bc%2598%25e7%25a7%2580%25e7%259a%2584javascript%25e6%25a8%25a1%25e5%259d%2597%25e6%2598%25af%25e6%2580%258e%25e6%25a0%25b7%25e7%2582%25bc%25e6%2588%2590%25e7%259a%2584>

<p>引言：如今的JavaScript已经是Web上最流行的语言，没有之一。从<span><a href="http://blog.jobbole.com/6492/" title="GitHub如何运作：时间并不决定一切">GitHub</a></span>上的语言排行榜<a href="https://github.com/languages">https://github.com/languages</a>上即可看出，也是如今最为活跃的开源社区。随着Node的加入，JavaScript开枝散叶进入服务器领域，为这个语言榜的占比，也贡献了几分热度。尽管经历了Web2.0的洗礼 ，但在国内谈及开源，开源人士似乎都当这门语言并不存在，这也意味着国内的开发中坚阶层，并没有改变JavaScript以及前端过去二流形象的认识，也没意识到JavaScript如今真正的价值。历史原因造就如今的局面，但是你并不能就此否认，一个出身不好，小时候还挺调皮的孩子，他长大后就是没有出息。本文将介绍一个优秀的JavaScript模块应该是怎样炼成的，以期望未来国内的开源社区能够涌现出更多的优秀模块。</p>
<p>引起我写这篇文章冲动的是近期接触到moment模块时的震惊。用JavaScript写程序，通常要经历两个阶段：第一个阶段是采用一些模块来擦除JavaScript语言（跨平台）自身的问题；第二个阶段是自己写一些方法来擦除引入的模块的问题。第三个阶段才会真正进入业务开发中，前两个阶段俗称“擦屁股”。时常在第三个阶段时，我会陷回到第二个阶段，和各种模块碰撞，往往会引起一些心情上的不舒爽。而在接触到moment时，这些烦恼瞬间消散，我在心底知道为何会如此释怀，如碰到心仪的女神。这也让我回头反思过去在使用和写模块过程中遇到的挫折和收获，这落差让我产生了如此的冲动，此文算是一个总结，期望能在汲取优秀模块的经验中，国内开源社区能够成长起来。</p>
<p><strong>为什么是模块，而不是库或者框架</strong></p>
<p>在过去几年做前端的同学多半谈论的是库，或是框架，提及库则是Prototypejs和jQuery，框架则是YUI3，Dojo、Extjs等。关于库或者框架的，对应的比喻则是大教堂和集市。在最早的时间，大教堂和集市的建设都在同步行进，具有代表意义的是YUI3和jQuery，前者Yahoo!投入许多精力，历时两代完成，后者则是集市建造的典范，由John Resig创建，社区参与。最后看看后续的反馈：</p>
<p><strong>大教堂未必优质</strong></p>
<p>大教堂模式产出的作品，让享用者可以有一步到位的服务，无需其他。但是由于大教堂的建设过程中承担着解决所有问题的使命，这导致它逐渐变得庞大，尽管它有着宏大而美妙的结构，像一首长长的赞美诗，任何一段都可以被唱诗班演唱得动听。但是，弱点依旧暴露出来：</p>
<p>1、局部的优良程度未必是最好的；</p>
<p>2、庞大的结构导致灵活度下降，升级困难。</p>
<p>3、任何一条龙服务，都不能保证每个环节都是优异的。</p>
<p><strong>小集市钻石闪烁，沙砾良多</strong></p>
<p>小集市的特点是开放式建设、周期短、成本低。大多数创建出来的集市是功能简陋、品质平庸的。jQuery是一个值得玩味的现象，品质极高，但是它带来的插件市场，却体现了小集市的另一面，大多数的jQuery插件的质量却十分低下。</p>
<p>可以说小集市模式创建出来的作品，在单点上，是能够超越教堂模式作品中对应的那部分。前者缺乏一个宏伟的结构，但是在微观上，它是完善的，可以随意挪移的。后者虽然具备优良的结构，但在单点上并非优秀，这也容易让人怀疑是否其他点上也并不优秀，而且教堂式作品的一个特点则是，局部是不可移植的，非独立性的，这在YUI3中表现十分明显。</p>
<p>上述对比很容易让人联想到，如果一个架构的特性具备单点可移植，可拆卸，自身极其轻量，汲取了大教堂和小集市的优点。那必将是一个全新的时代，那是一个可以DIY的时代。原有的大教堂模式由于缺失了灵活性，在迭代迅速的开发中，必将淘汰。而小集市尽管良莠不齐，好在预留了选择权利给用户，并且他的开放性也预示着它的可成长性，所以还有未来可期。</p>
<p>没错，如今这个时代已经到来。库通常是一个比框架小一个粒度的单元，模块则是比库更小一个粒度的单元。一个库可能由几个模块组成，框架则可能是在几个库的基础上构建。不再谈论库和框架的原因是将库和框架的架构部分还给开发者，以便开发者可以根据实际业务去构建合适的解决方案。任何框架或库都只具备解决某一方面的能力，不具备普适性。当粒度降低到模块级别的时候，构建任何上层业务，可以实现按需使用。由于模块的粒度更小，所以相对库而言，更加专注，变优秀的成本更低。类比孩子在充满沙砾的海边奔跑，但很容易装满一口袋的贝壳。对于稍具慧眼的开发者而言，挑选一堆适合的模块来解决业务的问题，比使用教堂式作品或者更高效。</p>
<p>这种方案因为CommonJS模块规范的影响，已经在既定事实中成型。在Node中，NPM平台（<a href="https://npmjs.org/">https://npmjs.org/</a>）上13000+的模块数量可以说明这个问题。前端中由于受到历史原因的影响，进展较慢，国内玉伯的SeaJS在推动此事，Arale是在SeaJS基础上创建了一些模块，这类模块可以非常容易迁移到其他符合CommonJS模块规范的环境中。除此之外腾讯的JX也具备SeaJS的特性，可以轻松将别的库应用到JX中，但是还不够规范。</p>
<p>如果非得给这种模块提供方式一个名称，我觉得该叫做小教堂模式，具备小集市的小，意味着这个教堂可能只提供祈祷服务，如果注册结婚，则需要换另一家小教堂。但是这类小教堂的服务是最优质的。创建这类小教堂只比创建一个优异的小集市略费成本，换言之，优异的小集市就是小教堂，它的创建方式是沿袭小集市的开放、透明的、有既定目标的成长性的。</p>
<p>另一个考虑是，在大部分的情况下，我们是不需要大教堂的，现实中为了整体环境，我们宁愿在大教堂中祈祷，但是程序设计中，我们不会因为喜欢一棵树木，而买下整块山头，我们几乎不喜欢任何看起来多余的部分。同时我们也不需要小集市，我们需要的是一个品质优良的商场。开发者是采购者，采购模块的过程既是架构的设计的过程。</p>
<p>前文我也提到这种小教堂的模式依然存在不理想的功能重叠、功能多余、功能缺陷、功能冲撞等问题。可谓说完美的小教堂是可遇不可求的，但一旦它完美，将再难被替换。目前阶段的JavaScript模块开发还存在着这些问题。是故，如果开发者了解如何去打造一个优秀的JavaScript模块，并乐于贡献到开源社区，这将大幅提升社区JavaScript水平，后续的开发者在做业务架构时，将具备更优质的材料来DIY更优异的产品。</p>
<p><strong>炼成优秀模块的最佳实践</strong></p>
<p>其实这并不算是精确的最佳实践，只是从别的模块哪里学习到的和自己过去的一些经验，仅做一定的总结。欢迎补充和讨论。</p>
<p><strong>模块自身的素质要求</strong></p>
<p>要写出一个优秀的模块，模块自身的素质十分重要，如果自身条件太差，成为优秀模块的概率是极小的。这些自身素质包括美好的愿景、专注的定位、名字、API设计、文档、测试等。</p>
<p><strong>合理的愿景：设定既定目标</strong></p>
<p>如果你打算写作一个模块，并贡献到开源社区中，并期望它是优异的，并被许多人使用的，那么为它设定一个既定目标是首要的。如果这个目标是没有意义，没有趣味的，那多半没有人使用，甚至自己开发到一半都没有兴趣继续写下去。没有理想的屌丝，注定不能成为高富帅。</p>
<p>对于具备众多坑爹问题的JavaScript语言而言，找到一个目标并非难事。典型的例子如：jQuery专注解决DOM操作和Ajax、Underscore专注对象和集合的操作、QUnit和Jasmine专注BDD和TDD的单元测试、moment模块专注解决从Java那里学过来的Date的问题；拿近一些的例子，玉伯的SeaJS专注模块加载，老赵的Wind.js专注异步编程同步化来解决流程控制问题；拿一个有趣的例子，PNGDrive<a href="https://github.com/MadeInHaus/PNGDrive">https://github.com/MadeInHaus/PNGDrive</a>这个项目虽然没有什么实际用处，但是将文件编码为图片显示出来的方式足够有趣。</p>
<p>另外这个目标必须是既定的。也就意味着饼不用画为无限的，这个目标一定是可以完成的。如果目标太大，也就意味着模块自身会变复杂。jQuery兼容各种<span><a href="http://blog.jobbole.com/12749/" title="浏览器">浏览器</a></span>的DOM操作这个目标，在移动平台上变得没有意义，所以存在着Zepto.js这样的项目。更小的目标意味着模块自身简洁，且能够更专注，目标更容易抵达。一旦抵达目标，该模块就是稳定的，未来被替换的机会极小。</p>
<p><strong>为模块或项目起一个贴切的名字</strong></p>
<p>模块需要一个贴切而好记的名字。这个名字何以帮助用户最直观地感受模块。SeaJS在起名上算是一个表率，让人很容易有海纳百川的联想，这也正是SeaJS的行为。一个好的名字可以使得模块的后期推广事半功倍，而且一旦开始推广，尽量不要换名字。</p>
<p><strong>不做逾越的事</strong></p>
<p>并非每个使用者都喜欢买一送一的感觉，因为后面这个一，对于使用者而言，并非是期望的，所以它的优良无法直观的判定好坏。无关的方法一定不要提供。评判一个模块是否完美，不是可以添加API，而是无法再减少API了。</p>
<p><strong>不污染公共环境</strong></p>
<p>每个人都不喜欢公共环境被人污染。破窗效应揭示，如果一辆汽车的门窗稍有损坏，不立即修复，那么很快整辆车就会被破坏甚至偷走。 在JavaScript，公共环境包括全局变量，原型链等。jQuery和Underscore为了代码的写作方便，占用了$和_两个符号，尽管他们都提供了noConflict方法来避免冲突，但是抱怨者还是大有人在。所幸这两个库太过于知名，几乎没有再有的库来使用这两个变量名。另一个例子是Prototype.js库对对象进行扩展时，直接在Array、Object等原生对象的原型链上添加方法，尽管它看起来不影响，但是总有冲突的一天，并且使用者并不一定知晓原型链被改动，在他的默认上下文中，难保他也不去修改原型链。相比Prototype.js的做法，Underscore提供的方法则优雅许多，另行提供API来处理操作，而不是修改共有的原型链。</p>
<p>在Node和浏览器中，global和window是全局对象，如果随意放置变量到全局对象上，也容易遭到他人修改或者覆盖你变量的事情。<br>
CommonJS提供的require、exports则十分优雅解决这个问题。谁使用，谁引入。而不是通过全局变量。在前端没有AMD或者CommonJS环境下，则是采用命名空间和闭包来解决这个问题。</p>
<p>不污染公共环境是模块与模块之间互相不影响的基本保证。如果引入你的模块，导致其他模块失效的事情，多半是不招人待见的。</p>
<p><strong>抵制墨菲效应</strong></p>
<p>有可能变糟糕的事情，它变糟糕的可能性就会变大。模块在升级，迭代的过程中，如何避免这种糟糕的事情发生呢？答案是单元测试。当单元测试覆盖了你认为会出问题的地方，可以避免相同的错误再次发生。这是模块稳定迭代的基本保证。当我发现一个仅仅为了解决日期操作的moment模块，它为数不多的API竟然具有多达7000+的断言时，十分惊讶。</p>
<p>过去JavaScript只做简单的事情，地位低下，所以对于JavaScript的质量保证也极少。这个思维需要改变，一个用户在评估采用你的模块时，如果单元测试都无法看到，心里该是有多不踏实。</p>
<p>除此之外，还有性能测试，性能测试结果可以横向比较，也可以纵向比较，有利于感知模块的具体性能表现。</p>
<p>数据通常容易打动理性的人。</p>
<p><strong>保持简洁</strong></p>
<p>对于任意看起来复杂的事物，用户均会觉得它很复杂。老赵的Wind.js（前身是Jscex）是一个想法独特的模块，但在提供的API上由于eval，以及需要引入的模块较多，让它看起来比较复杂，让用户感觉潜意识里复杂，这种复杂的信号很容易变成它有问题的信号，让人心生疏远。</p>
<p>将能不暴露给用户看到的东西，尽量隐藏，过多的步骤只会让用户觉得麻烦和不靠谱。</p>
<p><strong>职责单一</strong></p>
<p>这里的反面例子来自于Require.js。如果你用过RequireJS，可以看到require方法是一个变态的设置，参数为’a’、[‘a’]、[‘a.js’]、{}他们的行为都是不一致的。这种参数类型可以随意变化是JavaScript的灵活的地方，但是函数的行为如果也发生变化，这会让人产生迷惑。适当的重载并不意味这行为也要完全不同。</p>
<p>功能过多，带来的问题的可能性也会变大，使用者在调用过程中也会增加无形的压力。分离功能可以保持方法职责单一会是你API优秀的一部分。</p>
<p><strong>编码风格</strong></p>
<p>编码风格一定需要统一，而且编码风格一定不要显得外行。如果你的用户发现你的编码风格是PHP或者C#的，他们可能会产生你不是专业的这个感觉。推荐贴近JavaScript社区，采用JSLint或JSHint来矫正编码风格。这有利于源代码的阅读，在不同的人之间传递不会有不换了一门语言的感觉。</p>
<p><strong>API接口漂亮</strong></p>
<p>API接口漂亮包含几个方面：</p>
<p><strong>命名</strong></p>
<p>对外API的命名需要谨慎对待，方法名太长、方法名不直观、方法名大小写不对、方法名单词太复杂都会影响到使用者的直观感受。由于国内的英文水平高低不一，使用者遇见不认识的单词，都会造成障碍。</p>
<p>jQuery在方法命名上十分优秀。简短，直观，优雅。</p>
<p><strong>调用</strong></p>
<p>实参的传入也是考验API设计者的地方。如果需要调用者传入的参数较多，则该反思该API是否适合暴露给调用者。如果调用参数确实较多，并且支持可选项，则传入一个对象作为参数较为合适。由于对象带有明确的key，获取参数也无需一个一个检测。</p>
<p>$.ajax()是一个典型的例子，它支持的参数非常多，并且大多可选。所以暴露的API为$.ajax(url, obj)或$.ajax(obj)。</p>
<p>在JavaScript中，链式调用也是让用户较为喜爱的一点。Underscore除了提供普通的API外，还支持包装对象之后进行链式调用。这让熟悉函数式编程的人，顿生亲切。</p>
<p><strong>习惯</strong></p>
<p>Zepto.js是一个经典的案例，它提供了与jQuery几乎完全兼容的API，为的是照顾用户对于jQuery的熟悉。这让它可以零成本被应用到移动浏览器上。<br>
jQuery的另一件反面例子则是它的each方法，与原生数组的forEach方法的回调传入值次序不同，这与习惯不同的接口，会造成一定反感心理。 在设计API的过程中，尽量寻找贴合用户习惯的已有形式，这会让用户易于接受。</p>
<p><strong>可扩展性</strong></p>
<p>模块在开发的过程中，可以包括有限的部分和无限的部分。有限的部分将会通过项目迭代，臻于完美。如果模块存在无限的部分，并且在有限的部分留出扩展来衍生无限，这对于模块的成长，这是一个大大的加分项。</p>
<p>jQuery留出$.fn来供用户扩展它，形成的影响是大量的jQuery插件涌现了出来。尽管大多数情况没有被正确使用，但不能掩盖它是一个漂亮的设计。</p>
<p>moment模块在它的lang部分，也提供了优雅的扩展它的部分。使得不同地区的用户可以自定义语言的显示。</p>
<p>扩展性的存在，使得开源社区能够参与，能够起到抛砖引玉的效果，反过来会增进模块的功能。</p>
<p><strong>使用合适的<span><a href="http://www.amazon.cn/gp/product/B001130JN8/ref=as_li_qf_sp_asin_il_tl?ie=UTF8&amp;tag=vastwork-23&amp;linkCode=as2&amp;camp=536&amp;creative=3200&amp;creativeASIN=B001130JN8" title="设计模式:可复用面向对象软件的基础" rel="nofollow">设计模式</a></span></strong></p>
<p>合适的设计模式可以让模块自身无瑕。不合适的设计模式则会适得其反。</p>
<p>这里的正反例子都与jQuery相关。正例子是Deferred的应用。过去ajax操作success和fail回调都必须写到$.ajax(obj)的参数对象中，但是Deferred对象使得调用更加自然：</p>
<pre>$.get(&quot;test.php&quot;).done(function() {
  alert(&quot;$.get succeeded&quot;);
});</pre>
<p>LABjs的设计模式也十分优秀，script和wait两个用于加载和阻塞的API，通过链式调用，其乐融融：</p>
<pre>&lt;script&gt;
   $LAB
   .script(&quot;framework.js&quot;).wait()
   .script(&quot;plugin.framework.js&quot;)
   .script(&quot;myplugin.framework.js&quot;).wait()
   .script(&quot;init.js&quot;).wait();
&lt;/script&gt;</pre>
<p>反例子则是来自jQuery插件。jQuery.fn不失为一个好的扩展点，但是有大量的jQuery插件操作的并非DOM，却生搬硬套将方法挂靠在jQuery.fn上。另一个例子则来自jQuery社区得意的UI组件。</p>
<pre>$(foo).dialog(&#39;open&#39;)</pre>
<p>这类通过传入参数，又不能得到期望的返回值的场景，并不适合操作这个组件对象，API的参数传递更是显得不伦不类。如果是直接操作组件对象，则更友好一些。相比jQuery UI，YUI3的组件则优秀太多，API漂亮，组件的层次结构分明，易于扩展和自定义，jQuery UI通过插件方法的调用方式，自定义组件的代价极大。</p>
<p><strong>合理的目录结构</strong></p>
<p>开发方式多半会影响到后续的使用方式。尽管前端脚本多半都是提供一个文件给用户，但是在开发过程中，合理地组织自己项目的目录结构是值得注意的。jQuery的源代码目录中，各个功能点都分别写在各自的文件中，使得开发过程中编写代码方便。</p>
<p>另一方面，CommonJS的包规范还定义了以下目录和文件：</p>
<pre>&lt;span style=&quot;color: #888888;&quot;&gt;bin doc test lib package.json&lt;/span&gt;</pre>
<p>分别用于存放二进制文件、项目文档、单元测试用例、源代码。package.json文件则用于描述该包的一些包括包名、版本号、依赖等的信息，详情见<a href="http://wiki.commonjs.org/wiki/Packages/1.0">http://wiki.commonjs.org/wiki/Packages/1.0</a>。遵循规范的目录结构通常更好一点，因为大家都有同一个准则来参考，彼此更熟悉。</p>
<p><strong>巨细的注解</strong></p>
<p>通常用户真正需要去阅读你的代码的时候，是出现问题的时候。在开源社区，如何让发现你问题的人刚你改进代码，注释的作用功不可没。</p>
<p>另外，当一个用户真正是来欣赏你的代码时，如果看到Underscore这样密度的注解时（<a href="http://documentcloud.github.com/underscore/docs/underscore.html">http://documentcloud.github.com/underscore/docs/underscore.html</a>），还有拒绝它的勇气吗？</p>
<p><strong>清晰明了API的文档</strong></p>
<p>优秀的模块不仅仅体现在代码写得好上，更多的体现在如何让用户使用时更舒适。API文档必不可少。我心目中的API文档应该详细描述方法作用、参数、返回值的。甚至还应该有demo代码伴随。</p>
<p>API文档可以通过jsdoc之类的注解文档来生成。也可以另起文档来写。</p>
<p>API文档的一个特点是应该能方便查找，能在一个页面中展现完成的，尽量不要页面跳转或翻页。</p>
<p>API文档最大的作用让用户精确理解API，使得不造成误解，和清楚输入输出。</p>
<p>Underscore的API文档（<a href="http://documentcloud.github.com/underscore/">http://documentcloud.github.com/underscore/</a>）借用模版生成了漂亮的页面，使得查阅方便。</p>
<p><strong>一见钟情的demo</strong></p>
<p>相比Node，前端JavaScript模块更擅长做这件事情，尤其是UI库。男女之间首次见面的第一印象，很大程度可以决定某两个人是否会谈恋爱的概率。demo提供的形式可以影响到用户的直观体验，一般而言，用户觉得越炫，但是旁边提示的示例代码越简单，越会引发用户的好奇心。如果只有代码，或者只有demo，都会在表现性上打折扣。</p>
<p>对于Node的模块而言，由于没有live demo的感觉，尽量展示sample代码，让用户了解到他的目标是否与模块的目标一致。不要让用户错过你的模块，也不要让你的模块错过了它的用户。</p>
<p><strong>README.md</strong></p>
<p>README文件承担的作用仅次于demo，无需让用户产生心动的感觉，但是一定要引导用户更深度的了解你的模块，详细阅读你README的人，多半是有兴趣使用你的模块的人在做实地考察了。一个geting start入门是必不可少的，到API文档的链接也应该有。如果空落落的README，会立马有生疏感的。后期用户的心得体会等相关文章，也记得更新到README中。</p>
<p><strong>模块的社区打造</strong></p>
<p>话说酒香也怕巷子深。在满足成为优秀模块的基本素质要求后的首要事情是如何将模块推向开源社区，积极到社区中宣传。</p>
<p><strong>放到Github开源：不仅仅是代码</strong></p>
<p>每一个<span><a href="http://blog.jobbole.com/821/" title="程序员的本质">程序员</a></span>都应该有属于自己的Github帐号。如果你没有，不是Github的遗憾，而是你的遗憾。关于Github有什么，可以参看这篇文章：如何高效利用GitHub （<a href="http://www.yangzhiping.com/tech/github.html">http://www.yangzhiping.com/tech/github.html</a>）。 排除掉文化上带来的好处。Github可以帮我们托管代码，帮我们解决版本控制的问题。它可以提供一个wiki，帮我们存放文档。它提供Issues页面，使得别人可以为模块提出意见和反馈。提供Pull Requests页面，使得别人可以帮我们修改代码后，供我们合并修改。</p>
<p><strong>交流平台的创建</strong></p>
<p>交流平台主要用于讨论、答疑，使得模块作者和用户之间可以产生思维的碰撞。交流过程可以不断完善模块自身的不足，形成文档。主要的形式有如下：</p>
<p><strong>邮件列表</strong></p>
<p>国外的社区大多采用Google Groups的邮件列表来交流。邮件列表可以将讨论发送到每一个关注它的人手中。</p>
<p><strong>实时交流</strong></p>
<p>国外多采用IRC频道来进行此事。国内的情况，可以选择合适的QQ群来进行交流。</p>
<p><strong>留言版</strong></p>
<p>留言版的功能Github的issue具备了该功能。但是如果具备单独的介绍页面或者站点，集成Disqus来收集用户的反馈会是个不错的选择，因为这更符合普通用户的习惯。</p>
<p><strong>版本控制</strong></p>
<p>这里的版本控制不是git的版本控制。而是模块的整体版本。标注好模块的版本是分场重要的，如果用户需要升级模块，它能够得到明确的指导。前端模块中通常是在文件名上，或者文件内部写明版本。对于Node而言，写在package.json文件中。</p>
<p>最好能够在大版本的发布时，通过正式的新闻页面，或是邮件列表发出新闻通知，以显得版本发布的正式。</p>
<p><strong>悠扬的历史：Change Log</strong></p>
<p>不要小看Change log的作用，长长的，细碎的Change log可以给用户该模块历史悠扬的感觉，也能让用户体会到写作该模块的过程细节。每次迭代发布的过程中，展现给用户看当前的change log也能让用户知道改动的影响范围。如果碰巧发布了一个用户需要的方法，他会有收到礼物的感觉。</p>
<p><strong>选择合适的License</strong></p>
<p>国内的环境中，也许大家不太关心License的问题。事实上，License的选择，会影响到用户的评估。目前只有BSD和MIT两种License可以让人放心使用。对于商业公司而言，如果不是这两种License，他们需要投入更多的精力和时间周期来评估这个模块是否可用。</p>
<p>但是对于JavaScript社区而言，通常采用的是MIT，所以也基本没有问题。选好License，并在明显且不重要的位置表明该模块在什么License下发行。</p>
<p><strong>发布到NPM中</strong></p>
<p>在Node中，搞定代码后，发布到NPM中是最应该做的事。</p>
<p><strong>易于获得的感觉</strong></p>
<p><code>npm install module</code>或是一个显眼又不失素雅的Download按钮会在潜意识中让用户觉得易于获得，容易集成到现有系统中。不要做了各种分享介绍之后，让用户找不到获取模块的地方。</p>
<p><strong>持续集成</strong></p>
<p>一个随时都能拿出钥匙，开出跑车的青年，必将被认为是高富帅。支支吾吾不能随时给出结果的人，基本是屌丝无疑。</p>
<p>推荐注册你的项目到travis-ci，并运行它。绿色的Passing时刻昭示该项目的可靠指数、稳定指数较高。这个小图标也能帮助你检测迭代是否出现问题。</p>
<p><strong>漂亮的站点</strong></p>
<p>注册一个模块名字的org域名，一套简洁明了的UI，清晰的导航，简单的demo等等。细节在前后的实践中都有提及。</p>
<p><strong>标致的Logo</strong></p>
<p>如果你没有任何视觉设计的天赋，厚着脸皮找你的视觉设计师同事要一枚吧，尽管它是一个锦上添花的事情，但是Logo在品牌认知上的功劳是不用质疑的。</p>
<p><strong>线下分享交流</strong></p>
<p>开发完模块后，一定记得分享给团队的同事，他们应该是模块的首批用户，他们给予的反馈也是最宝贵的。</p>
<p>另外，还可以在线下社区分享，将你的模块的推广范围从身边延续到这个城市。尽管在线下社区分享模块的机会不多，但是一旦有，不要忘记介绍自己得意的模块给他人。</p>
<p>分享带来的收益是明显的，有利于自己梳理对模块的认识，也能收到用户的直观反馈。</p>
<p><strong>及时响应反馈</strong></p>
<p>也许你的模块已经很久没有更新，但是用户还是会发来反馈或提出问题，甚至提交pull request，请及时响应需求。如果是提出问题，说明你的文档还不够完善。如果是提出需求，则说明你最初设定的目标还没有完成。</p>
<p><strong>善意营销：赏金猎人</strong></p>
<p>如果有用户指出你的低级bug，或是帮你写作了模块的体验文章，这些事情都是值得模块作者有所表示的时候，因为这些用户是你的忠实用户，他们在帮你的模块成长。有所表示并不意味着要给多少钱，一些有意义的奖品或纪念品更适合拉近这些用户与你之间的距离。</p>
<p><strong>与兄弟社区的互动</strong></p>
<p>一个社区如果过小，需要到隔壁的社区中发布一些信息让大家知晓。如果可以，无论国内还是国外的的兄弟社区的协助推广，将会对社区运作有很大的帮助。友情链接是一个典型的方式。</p>
<p><strong>模块开发者的自我修炼</strong></p>
<p>模块自身的优秀加上社区的打造可以保证模块拥有不错质量和口碑。但是决定模块能否走得远，模块开发者的自身素质也十分重要，这其实模块的另一个软素质。经历了开发阶段和社区运作阶段后，越发展到后期，开发者的自身修炼的影响越会凸显。</p>
<p><strong>忍得住寂寞&amp;持续坚持</strong></p>
<p>典型的例子是老赵对于Wind.js的坚持。在对Wind.js的开发和推广上，可谓是寂寞的。略呈偏门的异步同步化、eval、编译等工序，被误解和排斥的多次。这是需要忍受的，如果没有持续坚持和忍住寂寞，模块就没有明天。作者的热情一旦消散，用户的热情也必然消散。</p>
<p><strong>简单专注</strong></p>
<p>前文的愿景部分提及到了简单专注。简单专注，不仅仅是在项目初期保持，在长久发展中，也应当如此，只有简单专注方能保证小教堂的服务质量是最顶尖的。</p>
<p><strong>奉献精神</strong></p>
<p>模块开发和推广事实上是没有直接的经济收入的，而且还会耗费大量的时间和精力。但是开源事业的收益，实际上是无法用金钱进行衡量的。并且这个过程也是自愿自发的，没有人会主动来推动你。</p>
<p>如果确实对经济造成影响，可以在页面上加上donate的链接。优秀的模块不会让这个donate链接的功能白费的。</p>
<p>幸运的是开源社区中不会缺乏具有奉献精神的人，是他们在推动社区和技术发展。</p>
<p><strong>演讲能力</strong></p>
<p>前文提到线下分享，这对于开发者的演讲能力有更多的要求。演讲能力的高低，是在另一个层面上提升模块的表现力，尽管这种能力不需要运行在CPU中。</p>
<p><strong>文笔</strong></p>
<p>模块开发者的文笔在文档的影响上是能够直观反映的。除此之外在社区推广中，文案的品质也有一定的影响。如果开发者文笔能够好到用优美的文章去传递模块的思想，这是令人愉悦和容易接受的。</p>
<p><strong>人格魅力</strong></p>
<p>模块的开发者应当具备良好的人格魅力，这包括人际关系、交流能力等。一个具备人格魅力和一个并不为人所知需要进一步了解的开发者，他们展现在大家面前的收效是并不相同的。后者更容易吸引他人，为模块带来更多的用户，这些人是你的用户，也是你的合作者。他们的涌入，会帮助改进模块。 关于人格魅力这个软素质，这里不再多说，相信都了解他的重要性的。</p>
<p><strong>总结</strong></p>
<p>通过对最佳实践的罗列，我自己相当于重温了一遍JavaScript开源的一系列文化。而这些最佳实践部分其实都是必要条件，通过这些最佳实践，未必保证会有优秀的JavaScript模块产出。但是观察如今的那些优秀模块，他们基本上都具备这些特征。最后，JavaScript社区虽然活跃，佼佼者不在少数，但是整体水平的提升，还需时日，希望此文能够帮助到一些开发者并欢迎讨论。</p>
<p><strong>关于作者</strong></p>
<p>田永强，新浪微博<a href="http://weibo.com/shyvo">@朴灵</a>，前端工程师，曾就职于SAP，现就职于淘宝，花名朴灵，致力于NodeJS和Mobile Web App方面的研发工作。双修前后端JavaScript，寄望将NodeJS引荐给更多的工程师。兴趣：读万卷书，行万里路。个人Github地址：<a href="http://github.com/JacksonTian">http://github.com/JacksonTian</a>。</p>
<h2>相关文章</h2><ul><li><a href="http://blog.jobbole.com/25390/" title="一种隐藏javascript代码的技术">一种隐藏javascript代码的技术</a></li><li><a href="http://blog.jobbole.com/24878/" title="JavaScript宝座：八大框架论剑">JavaScript宝座：八大框架论剑</a></li><li><a href="http://blog.jobbole.com/24602/" title="向非程序员解释 JavaScript">向非程序员解释 JavaScript</a></li><li><a href="http://blog.jobbole.com/24329/" title="了解 JavaScript 中的内置对象">了解 JavaScript 中的内置对象</a></li><li><a href="http://blog.jobbole.com/23563/" title="Javascript定义类（class）的三种方法">Javascript定义类（class）的三种方法</a></li><li><a href="http://blog.jobbole.com/23517/" title="Dojo与jQuery综合比较分析">Dojo与jQuery综合比较分析</a></li><li><a href="http://blog.jobbole.com/21962/" title="高性能JavaScript模板引擎原理解析">高性能JavaScript模板引擎原理解析</a></li><li><a href="http://blog.jobbole.com/21419/" title="pffp（一个JavaScript公共函数接口规范）的简介">pffp（一个JavaScript公共函数接口规范）的简介</a></li><li><a href="http://blog.jobbole.com/21136/" title="10 个 jQuery 图表插件推荐">10 个 jQuery 图表插件推荐</a></li><li><a href="http://blog.jobbole.com/20427/" title="8 个效果惊人的 WebGL/JavaScript 演示">8 个效果惊人的 WebGL/JavaScript 演示</a></li></ul>