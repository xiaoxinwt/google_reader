---
layout: post
title:  "KISSY深入研究（3）——loader.js"
date:   2010-10-24 21:39:36
author: ghSky
categories: program
---

## KISSY深入研究（3）——loader.js
### by ghSky
### at 2010-10-24 21:39:36
### original <http://ghsky.com/2010/10/kissy-research-part-three-loader.html>

<p>由于国庆大假的原因，加之最近工作上时间比较零散，没有相对较为完整的时间学习，因此KISSY源码分析停滞了很久，今天要分析的是在<a href="http://github.com/kissyteam/kissy/blob/master/CHANGELOG.md">KISSY 1.1.5</a>中才正式引入的loader概念。</p>
<h3>Loader 背景</h3>
<p>首先需要了解一些JavaScript Loader的相关知识，Loader这个概念主要是出现于页面加载时对于JavaScript代码的加载管理、依赖关系处理等。如果本身页面较为简单，可能Loader就并不适用于此。豆瓣的这篇文章<a href="http://site.douban.com/widget/notes/22456/note/87598595/">《攻城利器之微型框架 - Do》</a>正是介绍了它们的微型框架对于页面JavaScript代码的加载管理，这个框架的主要用途并不像普通JavaScript框架，它的主要用途是页面中JavaScript代码的管理者和组织者，具体介绍可以详细看原文已经整个框架代码。同时还有这篇文章<a href="http://www.uedmagazine.com/ued/index.php?entry=entry100814-104521">《KISSY loader 的设计 》</a>，是KISSY loader机制的开发者之一<a href="http://www.uedmagazine.com/ued/static.php?page=static090823-042648">@拔赤</a>介绍的相关设计理念，强烈推荐仔细阅读。</p>
<h3>Loader 使用</h3>
<p>有了对于Loader的基本概念以及相关的设计理念后，我们先从比较直观的方面介绍一下Loader。有关Loader的具体API文档可以<a href="http://kissyteam.github.com/kissy/docs/kissy/loader.html">猛击这里</a>。</p>
<p>我们从一个实例具体理解一下Loader的使用吧。在<a href="http://www.taobao.com/">淘宝网</a>首页中存在如下一段JavaScript代码：
<pre>&lt;script src=&quot;http://a.tbcdn.cn/??s/kissy/1.1.3/kissy-min.js,p/header/header-v8-min.js?t=20100908.js&quot;&gt;&lt;/script&gt; 
&lt;script&gt; 
KISSY.app(&#39;FP&#39;);
FP.add({
    &#39;direct-promo&#39;: {
        fullpath: &#39;http://a.tbcdn.cn/p/fp/2010c/js/fp-direct-promo-min.js&#39;
    },
    &#39;fp-mods&#39;: {
        fullpath: &#39;http://a.tbcdn.cn/??s/kissy/1.1.3/suggest/suggest-pkg-min.js,s/kissy/1.1.3/datalazyload/datalazyload-pkg-min.js,s/kissy/1.1.3/switchable/switchable-pkg-min.js,s/kissy/1.1.3/flash/flash-pkg-min.js,p/fp/2010c/js/fp-alimama-ecpm-min.js,p/fp/2010c/js/fp-alimama-ecpm-min.js,p/fp/2010c/js/fp-hubble-monitor-min.js,p/fp/2010c/js/fp-p4p-min.js,p/fp/2010c/js/fp-init-min.js?t=20100915.js&#39;
    }
});
FP.use(&#39;direct-promo&#39;, function(F) {
    F.DirectPromo.request([48, 49, 75, 51, 52, 53, 68, 73, 74], 1);
});
&lt;/script&gt; </pre>
<p>可以很清楚地看出这里使用了Loader技术。根据前面学习到的知识，在第二段JavaScript代码块中第一行首先是创建了一个FP的应用。之后我们往FP这个应用上添加了两个模块'direct-promo'以及'fp-mods'，而且这两个模块的添加方式都是使用文件引用，当然我们也可以直接将代码写在这里，同时还可以加上一些配置，具体写法还是参照<a href="http://kissyteam.github.com/kissy/docs/kissy/loader.html">API文档</a>的说明。接下来就开始在FP这个应用是使用我们添加的模块做事儿了，这里指的就是use这个方法。</p>
<p>那么这种写法（用法）有什么好处呢？与最原始的方式又有什么不同呢？我想通过背景里面的两篇文章大家一定都能有一些概念了，下面我们看下这样加载方式的Timeline</p>
<p><a href="http://ghsky.com/wp-content/uploads/2010/10/taobao-index-loader.png"><img title="taobao-index-loader" alt="taobao-index-loader" src="http://ghsky.com/wp-content/uploads/2010/10/taobao-index-loader_thumb.png" width="520" height="188"></a></p>
<p>是的，我们只需预先把种子脚本载入（种子脚本相对较小，但是因为首页具体情况，这里没有使用seed.js种子脚本），然后其后再按需add模块代码，use模块，就能保证我们这里的业务脚本无阻加载，这样不仅仅使代码管理起来更加集中、规范，同时也能大大提高页面性能。有时我们没办法做到不加载，但是通过延时加载，合理分配任务，让繁重工作分时完成，同样能够达到提高页面性能、用户响应的目的，增强网站可访问性。</p>
<h3>Loader 概述</h3>
<p>既然已经对Loader有了大致了解，并且通过一个例子也算是比较直观地感受了Loader带来的好处，那么我们下面就深入到loader.js源码中具体研究研究这种设计是如何实现的。</p>
<p>我们用一段典型代码来说明整个Loader 的实现过程：
<pre>// Add mod1 module for KISSY
KISSY.add('mod1', {
    fullpath: 'http://ghsky.com/labs/foo.js'
});

// Add mod2 module for KISSY
KISSY.add('mod2', function() {
    // some code here
}, {
    requires: ['mod1']
});

// Use the mod2 to do something
KISSY.use('mod2', function() {
    // callback here
});</pre>
<p>对于模块主要分为add以及use两个阶段，add阶段主要是添加模块相关配置信息到模块宿主环境中保存，以下是一张典型的宿主环境的模块存储结构：</p>
<p><a href="http://ghsky.com/wp-content/uploads/2010/10/KISSY.Env_.png"><img title="KISSY.Env" alt="KISSY.Env" src="http://ghsky.com/wp-content/uploads/2010/10/KISSY.Env_thumb.png" width="520" height="193"></a></p>
<p>同时添加模块的方式主要有两种，一种是直接以代码形式添加（代码模块），例如上面示例代码的'mod2'，另外一种就是以文件资源形式添加（资源模块），例如上面示例代码的'mod1'。由此我们引出模块的5种主要状态，0（初始状态），LOADING(1)，LOADED(2)，ERROR(3)，ATTACHED(4)。</p>
<p>0和LOADING状态只有资源模块会存在，而所有的代码的初始状态均为LOADED。处于LOADED状态的模块均表示外部资源已经读取或者不需要外部资源，此时与ATTACHED状态的唯一差别就是模块自身的代码栈未执行，这里提到模块自身的代码栈，主要指的还是代码模块，当然有些资源模块添加之后同样还可以为其添加一些代码，这样也就成为他们自身的代码栈了。只有当每个模块的依赖模块都加载完毕才会执行其自身代码栈，当然这些工作都完成后，模块也就是ATTACHED（可用）了！</p>
<p>可能你还注意到宿主环境里面还有一个_loadQueue的对象，其作用主要是用于保存资源文件的加载队列，后面会详细介绍。下面我们就大致看一下Loader模块的具体组成：</p>
<p><a href="http://ghsky.com/wp-content/uploads/2010/10/loader.png"><img title="loader" alt="loader" src="http://ghsky.com/wp-content/uploads/2010/10/loader_thumb.png" width="520" height="286"></a></p>
<p>暴露的公共API就是三个，getScript, add, use，三个方法的具体说明可以<a href="http://kissyteam.github.com/kissy/docs/kissy/loader.html">猛击这里查看</a>。其他的方法均为内部使用，因此才去了私有方法的命名方式，这里先说一下，__mixMod &amp; __mixMods 两个方法设计到了一些全局loader的高级的特性，因此这里就不具体研究。下面我们主要从add以及use两个方法入手来具体了解loader的实现机制。</p>
<h3>Loader add()</h3>
<p>正如上面示例所示，add方法仅为宿主环境添加所需的执行代码（或者资源文件）以及模块的配置信息，添加之后的模块只有在use时才能体现出其作用，因此要注意理解add与use的区别。首先大致了解一下add方法的处理流程：</p>
<p><a href="http://ghsky.com/wp-content/uploads/2010/10/Loader.add_.png"><img title="Loader.add" alt="Loader.add" src="http://ghsky.com/wp-content/uploads/2010/10/Loader.add_thumb.png" width="297" height="734"></a></p>
<p>具体解释一下上述的流程，add首先根据传递的参数进行规格化处理，然后判断添加的模块类型（代码模块或是资源模块）。若是资源模块，则很简单，目前只需简单地将模块的相关配置信息规格化后加入到宿主模块环境中；若是代码模块，则首先也要进行相关配置的规格化处理，之后需要判断模块是否有依赖模块，若有的话则检查各个依赖模块是否均已ATTACHED，若是的话就可以执行自身的代码栈，否则需要将代码压入代码栈等待后续执行，至此整个add过程就已经结束了。这里主要涉及到了__isAttached以及__attachMod两个方法。</p>
<p>__isAttached方法顾名思义，就是检查模块是否已经可用了（ATTACHED），其判断依据便是每个模块均有的状态标记，之前的“概述”里面已经提到了模块的状态。</p>
<p>__attachMod方法则是attach模块，也就是执行模块自身代码栈的方法。该方法的执行条件是依赖模块均已可用，这时候便可以执行模块自身的代码栈，顺利执行之后即可将模块的状态设置为ATTACHED（可用）。因此__attachMod方法可以保证代码模块是在依赖模块加载后才执行，因此可以保证代码执行安全。</p>
<p>正如示例所示，我们以外部资源方式添加了'mod1'，以代码形式添加了'mod2'，此时add方法对于'mod1'，只简单规格化配置后就将其添加至宿主模块环境中，只是目前其状态字段为undefined，其所需的外部资源也未读取。对于'mod2'，以代码形式添加，但是由于其依赖模块'mod1'在__isAttached检测中未通过，也就是'mod2'的代码还暂时无法执行，故只能将其压入其代码栈等待后续执行。</p>
<h3>Loader use()</h3>
<p>use方法相对add就要复杂一些，下面先大致看下基本流程：</p>
<p><a href="http://ghsky.com/wp-content/uploads/2010/10/Loader.use_.png"><img title="Loader.use" alt="Loader.use" src="http://ghsky.com/wp-content/uploads/2010/10/Loader.use_thumb.png" width="463" height="748"></a></p>
<p>这么一看大致流程估计也够晕的，那么下面就顺序解释一下。首先声明一下，use的参数中可以传递一个global的参数，但由于其使用较少，笔者基本没有使用经验，所以这里就避而不谈了。use方法支持同时加载多个模块，其方式是以“,”分隔各模块名。方法首先将需要use的模块建立成数组形式，然后调用__isAttached方法检查是否均已可用，若都加载完毕可用，那么就直接执行回调函数退出即可。对于只要存在一个不可用的模块，则比较对其加载，因此需要遍历这个模块数组，排除那些已经可用的模块，只用处理还未可用的模块，同时use方法支持按序执行，也就是如果需要use多个模块，且需要按序加载他们，这里use方法便会利用一个小技巧，将当前模块之前的一个模块（也就是保证按序加载）放入到当前模块的依赖中，这样由于依赖模块均在当前模块前加载可用，因此变相实现了按序加载，对于这种方式会破坏原始的依赖模块数组因此需要做一个备份，当前模块加载可用后再做一次还原即可。同时按序加载中还需要防止循环依赖的情况出现。</p>
<p>目前已经将当前模块的配置处理完毕，包括可能出现的按序加载处理，之后便需要调用__attach方法加载当前模块。这个方法也是利用回调机制，执行完毕后回调处理函数。我们这里的回调处理函数实际就相当于要执行use方法的回调，只是这里需要保证的是整个use列表的模块都已经加载完毕之后才可执行，因此这里需要简单的判断。进入__attach方法，如果存在依赖模块则需要递归地先对依赖模块加载，之后如果当前模块为资源模块的话便需要处理资源加载的问题，首先需要建立资源的绝对路径（处理模块配置中使用的相对路径，同时这里如果存在模块的样式资源文件引用，也需要处理其加载路径），完成后在模块上形成一个'fullpath'（可能还存在'csspath'）这样的属性指定加载资源的绝对路径。资源文件路径都准备好了之后就需要进入__load方法，读取模块资源。</p>
<p>在__load方法中，还记得我们最开始说到的在KISSY这个宿主环境中有一个_loadQueue的对象么，这里我们用其来缓存每一个需要加载的外部资源。首先获取_loadQueue中当前资源的信息，然后初始化当前模块的状态（注意对于那些代码模块，在add方法中我们已经初始化过其模块状态为LOADED，这里便不会覆盖），同样对于已经存在于_loadQueue中的资源同样，判断其是正在加载或者已经加载完毕来初始化当前模块的状态。如果存在css资源加载的话，直接通过getScript方法加载资源即可，同样，对于那些还未加载过的资源，设置模块状态为LOADING，然后使用getScript进行加载并注册相关回调来修改模块状态，对于那些已经被之前模块申请加载中的模块，只需要再注册上一个回调函数即可。内嵌代码则直接执行__attach中注册的回调函数。</p>
<p><a href="http://ghsky.com/wp-content/uploads/2010/10/loader_callbacks.png"><img title="loader_callbacks" alt="loader_callbacks" src="http://ghsky.com/wp-content/uploads/2010/10/loader_callbacks_thumb.png" width="459" height="419"></a></p>
<p>刚刚提到的几个方法都存在比较复杂的回调关系，从上图可以重新理清一遍思路。这个use方法使用回调机制于包括加载模块、读取资源、资源载入几个部分有机地联系起来。</p>
<p>最后再啰嗦一下，前面我们一直提到宿主环境这个词，前面也讲到多次，那么到底什么算一个宿主环境呢？其实简单说就是KISSY以及用KISSY创建的APP都算一个宿主环境，在其上都存在add, use等方法，且对于各个宿主环境的模块是彼此独立的，因此在宿主环境的模块对象中是彼此相互不同的，但是对于_loadQueue这个对象比较特殊，因为__load方法对其的操作均是在KISSY这个宿主环境中，而非其衍生的宿主环境。为什么这么处理呢？因为刚刚提到每个宿主环境的模块是彼此独立管理的，但是可能某些不同的模块使用了同样的外部JavaScript资源，那么如果我们不用_loadQueue进行管理便可会导致外部JavaScript资源重复载入两遍，因为JavaScript每次载入均会执行，因此可能导致未知异常，需要将其异步载入的节点加入_loadQueue中进行状态判定，但是对于CSS样式资源文件变不会存在这种执行问题且其载入也是同步的，因此不用在_loadQueue中保存载入的节点，只需记录其读取状态为LOADED即可（当然若此时由于一些未知因此导致的载入失败，状态也是无法判定的）。</p>
<p>getScript方法是典型的异步脚本载入方式，实现也是很简单的，同时其还支持CSS资源的载入，原理也是大同小异，这里也就不赘述了。</p>
<h3>结束</h3>
<p>至此从Loader机制的设计理念到具体实现方式已经大致介绍完毕，从篇幅上便可知其复杂程度，主要是由于其函数间依赖关系比较复杂，因此需要理清思路才能顺利理解。其实很多问题也都是在实现或者使用过程中才遇到的，从源代码中可见开发者是基于实际开发经验以及众多类库相关机制理解后才能够实现得如此周密。因此对于我们个人学习或练习实践中大可在思路理清之后形成一个大致程序框架，明确相关关系，而具体可能的细节问题不用在构架中过于纠结，逐渐尝试和调整，总之不可能一步登天，做到如此周详的考虑。</p>
<p>- EOF -</p><img src="http://www1.feedsky.com/t1/708404304/ghsky/feedsky/s.gif?r=http://ghsky.com/2010/10/kissy-research-part-three-loader.html" border="0" height="0" width="0"></p></p>