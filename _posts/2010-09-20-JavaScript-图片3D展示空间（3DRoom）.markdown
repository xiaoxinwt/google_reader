---
layout: post
title:  "JavaScript 图片3D展示空间（3DRoom）"
date:   2010-09-20 09:59:21
author: 
categories: program
---

## JavaScript 图片3D展示空间（3DRoom）
### by 
### at 2010-09-20 09:59:21
### original <http://www.javaeye.com/topic/767846>

一般的平面效果，通过改变水平和垂直坐标就能实现，再加上深度，就能在视觉上的产生3D（三维）的效果。
<br>程序就是模拟这样一个三维空间，里面的图片会根据三维坐标显示在这个空间。
<br>很久以前就看过一个<a href="http://www.dhteumeuleu.com/mandatory-upgrade/">3DRoom</a>效果，是用复杂的计算实现的。
<br>在上一篇<a href="http://www.cnblogs.com/cloudgamer/archive/2010/08/16/ImageTrans.html">图片变换</a>研究过css3的transform之后，就想到一个更简单的方法来实现。
<br>
<br><img src="http://dl.javaeye.com/upload/attachment/313672/37e8b05a-83fb-31c0-aef3-eb2eb66473a5.jpg">
<br>
<br><img src="http://dl.javaeye.com/upload/attachment/313674/8f6f5244-0638-36a7-af4b-3e6b5ad4eb0d.jpg">
<br>
<br><a href="http://www.cnblogs.com/cloudgamer/archive/2010/09/20/Image3D.html"><strong><span style="color:Red">在线效果预览</span></strong></a>
<br>
<br><a href="http://files.cnblogs.com/cloudgamer/Image3D.rar"><strong>完整实例下载</strong></a>
<br>
<br>
<br><strong>程序说明</strong>
<br>
<br><strong>【实现原理】</strong>
<br>
<br>3D效果的关键，是深度的实现。
<br>把3D容器看成一个由多个不同深度的层组成的空间，这些层的尺寸默认跟容器一样。
<br>层里面放了该深度的图片，并且各个层会根据深度的变化做缩放变换，从视觉上产生深度差。
<br>缩放变换的比例按照最近点为1，最远点为0，逐渐变化。
<br>关键的地方是层里面图片的尺寸和坐标必须跟着层同时变换，这个通过css3的transform很简单就能实现。
<br>这样图片只需设置好尺寸再相对层定好位就行了，避免了随深度变化要不断调整图片尺寸和定位的麻烦。
<br>
<br>
<br><strong>【图片加载】</strong>
<br>
<br>在程序初始化之后，就可以调用add方法来添加图片。
<br>add方法有两个参数：图片地址和参数对象，还会返回一个图片操作对象。
<br>操作对象包含以下属性和方法，方便对图片进行操作：
<br>img: 图片元素
<br>src: 图片地址
<br>options: 参数对象
<br>show: 显示图片方法
<br>remove: 移除图片方法
<br>其中options可以设置如下属性：
<br>属性:    默认值//说明
<br>x:		0,//水平位移
<br>y:		0,//垂直位移
<br>z:		0,//深度
<br>width:	0,//宽度
<br>height:	0,//高度
<br>scaleW:	1,//宽度缩放比例
<br>scaleH:	1//高度缩放比例
<br>其中x、y分别是水平和垂直坐标的位移参数，坐标原点在容器底部中间，水平坐标向右，纵坐标向上，单位是px。
<br>而z是深度，用于比例的计算，方向由近点到原点。
<br>坐标系如下图：
<br><img src="http://images.cnblogs.com/cnblogs_com/cloudgamer/169629/o_coordinate.gif">
<br>
<br>图片加载成功后，就会执行_load图片加载程序。
<br>首先根据参数设置图片样式：
<br><pre name="code">img.style.cssText = "position:absolute;border:0;padding:0;margin:0;-ms-interpolation-mode:nearest-neighbor;"
	+ "z-index:" + (99999 - z) + ";width:" + width + "px;height:" + height + "px;"
	+ "left:" + (((clientWidth - width) / 2 + opt.x) / clientWidth * 100).toFixed(5) + "%;"
	+ "top:" + ((clientHeight - height - opt.y) / clientHeight * 100).toFixed(5) + "%;";</pre>绝对定位是必须的，宽度和高度根据参数设置就行。
<br>left和top根据坐标参数计算，这里需要用百分比的形式表示，后面再详细说明。
<br>还要给图片增加一个_z属性记录深度，方便调用。
<br>最后插入对应z的层，并重新显示该层。
<br>
<br>
<br><strong>【层变换】</strong>
<br>
<br>图片加载后，会用_insertLayer程序把图片插入到对应的层中。
<br>_insertLayer有两个参数：图片元素和z深度。
<br>程序用_layers对象，以z为关键字记录对应的层元素。
<br>如果在该深度还没有创建层，会自动创建一个：
<br><pre name="code">layer = document.createElement("div");
layer.style.cssText = "position:absolute;border:0;padding:0;margin:0;left:0;top:0;visibility:hidden;background:transparent;width:" + this._clientWidth + "px;height:" + this._clientHeight + "px;";</pre>层的坐标和尺寸要跟容器一致，因为插入图片的坐标是相对容器来定义的，这样使用起来比较方便。
<br>还会添加一个_count属性，记录层包含的图片数，最后插入到容器并记录到_layers对象中。
<br>获取层对象后，就把图片插入层中，并把_count计数加1。
<br>
<br>接着就可以通过_showLayer程序根据深度显示对应的层。
<br>程序包含三个坐标属性：_x、_y、_z，表示容器的三维坐标的偏移量。
<br>首先通过_getScale获取比例方法得到z深度的缩放比例scale。
<br>比例大于1，说明图片在视觉深度的后面，理论上应该看不到，所以隐藏；小于0，就是小到看不到了也隐藏。
<br>
<br>而_x和_y偏移量也需要根据深度来重新计算，程序有两种偏移方式：远点固定和近点固定。
<br>远点固定的意思是平面位移偏移量随着深度逐渐变小，产生以最远点为固定点移动方向的效果，近点固定就刚好相反。
<br>要实现这个效果，只要位移偏移量也跟着比例变化就行了，即远点固定时偏移量跟比例成正比，远点固定时是反比：
<br><pre name="code">var moveScale = this.fixedFar ? scale : (1 - scale);</pre>
<br>
<br>然后把这些参数交给_show程序来处理，并显示效果。
<br>
<br>为了最大限度地利用层元素，程序会在_remove图片移除程序中，把没有图片的层放到_invalid废弃层集合中，在需要插入层时，优先从_invalid中获取。
<br>
<br>
<br><strong>【缩放比例】</strong>
<br>
<br>上面已经说了，缩放比例应该按照最近点为1，最远点为0，逐渐变化。
<br>程序默认是通过下面的公式计算：
<br><pre name="code">function(z){ return 1 - z / 1000; }</pre>
<br>但用这个公式实现3DRoom效果的时候，会发现比例变化太急速，并不像这个<a href="http://www.dhteumeuleu.com/mandatory-upgrade/">3DRoom</a>那样平稳。
<br>
<br>研究代码后发现，原来它用的公式是这样的：
<br><pre name="code">this.r = FL / (FL + (z * Z));</pre>
<br>其中FL和Z是一个常量来的，即公式可表示成：
<br><pre name="code">function(z){ return 1/(1+z/常量); }</pre>
<br>那按照这个公式，深度为0时比例为1，深度为常量时比例为0.5，深度为无穷大时比例为0。
<br>
<br>变化效果可以参考这里的<a href="http://www.cnblogs.com/cloudgamer/archive/2010/09/20/Image3D.html">公式演示程序</a>。
<br>可以看出，缩放比例在默认公式是均匀变化的，而3DRoom公式是先快后慢，而且是逐渐变慢，所以有那种平稳的感觉。
<br>那按照实际，还可以自己设计适合的公式，只要符合从1到0变化就行。
<br>
<br>
<br><strong>【css3模式】</strong>
<br>
<br>程序中有三种缩放变换方式：css3、zoom和base，模式的程序结构跟上一篇<a href="http://www.cnblogs.com/cloudgamer/archive/2010/08/16/ImageTrans.html">图片变换</a>类似。
<br>缩放变换的目的是根据传递过来的比例和位置偏移量，把缩放效果显示出来，实现最终的3D效果。
<br>
<br>css3模式使用的是css3的transform，在上一篇已经介绍过用transform的matrix做缩放和旋转，这次还需要后面两个参数做位置变换。
<br>后面两个参数要注意单位的设置，在MDC的<a href="https://developer.mozilla.org/en/CSS/-moz-transform">-moz-transform</a>有说明：
<br>Gecko (Firefox) accepts a &lt;length&gt; value for tx and ty.
<br>Safari (WebKit) and Opera currently support a unitless &lt;number&gt; for tx and ty.
<br>意思是位移参数tx和ty，在Firefox需要带单位，而WebKit和Opera只需要数字（不带单位，默认px）。
<br>程序会根据浏览器设置单位。
<br>
<br>使用css3模式，还可以通过修改_r弧度属性进行旋转。
<br>最后设置matrix实现变换：
<br><pre name="code">layer.style[ css3Transform ] = "matrix("
	+ ( Cos * scale).toFixed(5) + "," + (Sin * scale).toFixed(5) + ","
	+ (-Sin * scale).toFixed(5) + "," + (Cos * scale).toFixed(5) + ", "
	+ Math.round(x) + unit + ", " + Math.round(y) + unit + ")";</pre>
<br>
<br>这里还要注意一个问题，计算得到的比例可能是一个很长的小数，在拼字符时会出问题。
<br>例如执行：alert(0.0000001)，会得到“1e-7”，js会用这个结果来拼字符，得到错误的结果。
<br>所以在做数字和字符的拼接时，能用整数的应该先转成整数，小数的话也要用toFixed转换一下。
<br>
<br>
<br><strong>【zoom模式】</strong>
<br>
<br>ie还不支持transform，但有一个zoom样式能实现类似的效果。
<br>由于zoom后，尺寸会发生变化，所以需要修正left和top移动到正确的位置。
<br>
<br>除了ie，webkit(chrome/safari)也支持zoom，不过ie6/7、ie8和webkit的实现并不完全相同。
<br>测试以下代码：
<br><pre name="code">&lt;style&gt;
.inner{ width:100px; height:100px; position:absolute; background:#0CF; zoom:0.5; top:50px; left:50px;}
.inner div{ width:50px; height:50px;position:absolute; left:25px;background:#CCC;}
&lt;/style&gt;
&lt;div style=&quot;width:150px;height:150px; border:1px solid #000; position:relative;&quot;&gt;
&lt;div class=&quot;inner&quot; id=&quot;t&quot;&gt;&lt;div&gt;test&lt;/div&gt;&lt;/div&gt;
&lt;/div&gt;</pre>
<br>在ie6/7实现了想要的效果，但在webkit显示的位置错了。
<br>原因是使用zoom后，元素的left和top也会随着缩放，那只要按比例重新计算就行。
<br>像上面的例子，只要把left和top改成50/0.5，即100就正确了。
<br>
<br>ie8就更麻烦，里面的内容是按zoom缩放了，但left和top还是原来的大小。
<br>被这个问题困扰了很久，最后发现通过用百分比定位就可以解决，在图片加载时left和top要用百分比就是这个原因。
<br>例如在例子中，修正left和top，并把最里面的div的left改成25%就可以了。
<br>在ie8还看到一个问题，在zoom后，内容是缩小了，容器和内部元素的尺寸却没有变化，还好这不会影响到图片的显示，定位也要用left和top，免得麻烦。
<br>还有，如果zoom的元素的尺寸用百分比设置，那元素的尺寸就不会根据zoom缩放了。
<br>
<br>在计算时还要注意一个问题，上面提到在webkit和ie8，left和top都需要除以scale来修正，当scale接近0到一定程度，结果会变成Infinity(无穷大)。
<br>用Infinity进行运算会出错，需要修正这个问题：
<br><pre name="code">left = Math.min(MAX, Math.max( -MAX, left )) | 0;
top = Math.min(MAX, Math.max( -MAX, top )) | 0;</pre>
<br>其中MAX是Number.MAX_VALUE(js能表达的最大数)。
<br>
<br>
<br><strong>【base模式】</strong>
<br>
<br>还有兼容全部浏览器的base模式，用的是传统的方法，即根据缩放比例，计算并设置每个图片的尺寸和位置。
<br>每次显示时，历遍层里面的图片，再逐个计算设置。
<br>计算需要图片的原始位置和尺寸，在第一次计算时会把数据保存在_original属性中：
<br><pre name="code">var original = img._original = img._original || {
	width: img.offsetWidth,	height: img.offsetHeight,
	left: img.offsetLeft, 	top: img.offsetTop
};</pre>
<br>尺寸只要根据比例缩放就行，位置除了计算相对层的缩放还要加上相对容器的位移，这个跟zoom模式的计算是一样的。
<br>理解了层变换的方式后，再理解这个就不难了。
<br>
<br>
<br><strong>【zIndex】</strong>
<br>
<br>深度除了要缩放和定位，还需要合理的前后遮盖。
<br>前后遮盖需要用<a href="http://www.w3.org/TR/WD-positioning-970131#&#39;z-index">zIndex</a>来实现，可以在图片或层上设置。
<br>
<br>首先最简单的方法是在层上设置：
<br><pre name="code">&lt;style&gt;
div,img{width:200px;height:200px;position:absolute;left:0;top:0;}
img{width:150px;height:150px;}
&lt;/style&gt;
&lt;div style=&quot;z-index:300;&quot;&gt;
	&lt;img style=&quot;background:#0C9;&quot; alt=&quot;300&quot; onclick=&quot;alert(300)&quot;&gt;
&lt;/div&gt;
&lt;div style=&quot;z-index:100;&quot;&gt;
	&lt;img style=&quot;background:#396;left:50px;top:50px;&quot; alt=&quot;100&quot; onclick=&quot;alert(100)&quot;&gt;
&lt;/div&gt;</pre>
<br>实现一般的3D效果可以这样设置。
<br>但点击测试，在ff和webkit前面的能触发后面的不能触发，而ie和opera就前后都可以触发。
<br>ps：如果img换成div，那么ie和opera后面的元素也不能触发，原因还不清楚。
<br>这样要想像3DRoom那样触发图片事件的话就不能在层设置zIndex。
<br>
<br>还可以在图片上设置：
<br><pre name="code">&lt;style&gt;
div,img{width:200px;height:200px;position:absolute;left:0;top:0;}
img{width:150px;height:150px;}
&lt;/style&gt;
&lt;div&gt;
	&lt;img style=&quot;background:#0C9;z-index:300;&quot; alt=&quot;300&quot; onclick=&quot;alert(300)&quot;&gt;
&lt;/div&gt;
&lt;div&gt;
	&lt;img style=&quot;background:#396;left:50px;top:50px;z-index:100;&quot; alt=&quot;100&quot; onclick=&quot;alert(100)&quot;&gt;
&lt;/div&gt;</pre>
<br>这样图片在所有浏览器都能正常触发，但在ie6/7层叠的效果失效了,看来在ie6/7只能在层用zIndex。
<br>还有一个问题，如果给div加上变换效果：
<br><pre name="code">div{-moz-transform:scale(1);-webkit-transform:scale(1);-o-transform:scale(1);}</pre>
<br>那图片上的zIndex就会失效，那css3模式就只能在层设置zIndex了。
<br>
<br>总结一下：
<br>在css3模式肯定要在层设置zIndex，但图片也不能触发事件。
<br>在zoom和base模式，应该在图片设置zIndex，但在ie6/7就要在层设置。
<br>这样至少在base模式层叠和图片触发事件都是正常的。
<br>
<br>
<br><strong>【msInterpolationMode】</strong>
<br>
<br>开始做的时候，效果在ie8下会很卡，但这个<a href="http://www.dhteumeuleu.com/mandatory-upgrade/">3DRoom</a>却不会卡，最后发现是使用了-ms-interpolation-mode。
<br>这个东西在<a href="http://www.aoao.org.cn/blog/2009/03/img-ms-interpolation-mode/">aoao</a>的文章中看过，但没想到可以用在这里。
<br>
<br>在MSDN有<a href="http://msdn.microsoft.com/en-us/library/ms530822(VS.85).aspx">msInterpolationMode</a>的介绍：
<br>Gets or sets the interpolation (resampling) method used to stretch images.
<br>即获取或设置用于拉伸图像的插值（重采样）方法。
<br>它有两个值：
<br>nearest-neighbor：使用近邻插值模式。
<br>bicubic：使用高品质的双三次插值模式。
<br>这些名词比较专业，我们只要知道使用nearest-neighbor效率高但效果差，而bicubic效果好效率低就够了。
<br>程序把它设为nearest-neighbor提高效率，这样在ie8中也不会卡了。
<br>
<br>
<br><strong>【拖动方向变换/滚轮深度变换】</strong>
<br>
<br>程序扩展了拖动视觉变换和滚轮深度变换。
<br>拖动和滚动的做法跟上一个的做法差不多，这里拖动是实现方向的变换，滚轮是实现深度的变换。
<br>移动是通过修改_x和_y属性来实现，缩放是通过修改_z来实现。
<br>修改属性之后再调用show方法显示效果。
<br>
<br>
<br><strong>使用技巧</strong>
<br>
<br><strong>【3DRoom】</strong>
<br>
<br>在3DRoom效果中，因为要实现图片的触发事件，所以不能用css3模式，原因是上面提到的层叠问题。
<br>上面也提到在ie8被zoom的元素尺寸不会改变，导致触发范围错误，所以也不用zoom模式。
<br>使用base模式就不会有问题了。
<br>
<br>在点击图片时，视觉会移动到图片上面，这个通过点击图片后根据本身的三维参数修改_x/_y/_z来实现：
<br><pre name="code">img.onclick = function(){
	i3D._z = -options.z | 0;
	i3D._x = -options.x | 0;
	i3D._y = options.y | 0;
	i3D.show();
}</pre>
<br>
<br>图片在mouseover时会显示一个边框，为了让图片加边框后不发生位移，加了一个"-1px"的margin，mouseout时再去掉。
<br>这里3DRoom跟参考的效果还是有差距，本文主要还是对3D效果的实现和研究。
<br>
<br><strong>【模式选择】</strong>
<br>
<br>css3模式稳定，大部分浏览器都支持，除了ie。
<br>zoom模式兼容性不好，但ie支持。
<br>base最慢，但兼容性好，而且没有bug。
<br>一般情况下应优先使用css3模式，然后是zoom，最后base，但像3DRoom那样的情况就要按实际选择了。
<br>设计的时候，ie是打算用Matrix滤镜的，但开发中发现一些问题，效率又太低，就不考虑了。
<br>
<br>
<br><strong>使用说明</strong>
<br>
<br>实例化时，必须有容器作为参数：
<br><pre name="code">var i3D = new Image3D( container, options );</pre>
<br>然后调用i3D方法添加图片：
<br><pre name="code">i3D.add( src, options );</pre>
<br>
<br>可选参数用来设置系统的默认属性，包括：
<br>属性:   	 默认值//说明
<br>mode:		"css3|zoom|base",//模式
<br>x:			0,//水平偏移值
<br>y:			0,//垂直偏移值
<br>z:			0,//深度偏移值
<br>r:			0,//旋转角度(css3支持)
<br>fixedFar:	false,//是否远点固定
<br>getScale:	function(z){ return 1 - z / 1000; },//获取比例方法
<br>onError:	function(err){}//出错时执行
<br>
<br>add方法的可选参数在图片加载中已经说明。
<br>
<br>还提供了以下方法：
<br>add：添加图片；
<br>show：显示效果；
<br>reset：重置默认状态；
<br>dispose：销毁程序。
<br>
<br>加入拖动方向变换或滚轮深度变换扩展后，可通过设置相关参数定义变换范围。
          
          <br><br>
          作者: <a href="http://cloudgamer.javaeye.com">cloudgamer</a> 
          <br>
          声明: 本文系JavaEye网站发布的原创文章，未经作者书面许可，严禁任何网站转载本文，否则必将追究法律责任！
          <br><br>
          <span style="color:red">
            <a href="http://www.javaeye.com/topic/767846" style="color:red">已有 <strong>0</strong> 人发表回复，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">JavaEye推荐</span>
<br>
<ul><li></li><li><a href="http://www.iteye.com/clicks/138"><span style="color:red;font-weight:bold">上海：高薪诚聘php开发人员</span></a></li><li><a href="http://www.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>