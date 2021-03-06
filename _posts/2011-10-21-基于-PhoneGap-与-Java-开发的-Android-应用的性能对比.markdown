---
layout: post
title:  "基于 PhoneGap 与 Java 开发的 Android 应用的性能对比"
date:   2011-10-21 19:07:33
author: editor
categories: program
---

## 基于 PhoneGap 与 Java 开发的 Android 应用的性能对比
### by editor
### at 2011-10-21 19:07:33
### original <http://stblog.baidu-tech.com/?p=993>

<p><a href="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=21318225335.bmp&amp;type=image%2Fbmp&amp;width=1010&amp;height=787"></a><a href="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=2011-10-11-13-48-291318312130.jpg&amp;type=image%2Fjpeg&amp;width=866&amp;height=730"></a><a href="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=2011-10-11-13-49-441318312216.jpg&amp;type=image%2Fjpeg&amp;width=867&amp;height=186"></a><a href="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=2011-10-11-13-49-441318312296.jpg&amp;type=image%2Fjpeg&amp;width=876&amp;height=1198"></a><a href="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=2011-10-11-14-43-161318315412.jpg&amp;type=image%2Fjpeg&amp;width=851&amp;height=696"></a><a href="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=2011-10-11-14-44-181318315471.jpg&amp;type=image%2Fjpeg&amp;width=845&amp;height=695"></a></p>
<p>此次的调研的重点是针对一个Android应用的基础需求，用phonegap与Java实现的应用在性能及开发成本等方面的对比。</p>
<p>开发一个应用的最基本需求应该是浏览性需求，而在Android开发中ListView比较常用的控件，广泛被用于数据列表的展现上，而且也比较灵活。所以本次选择用phonegap和Java各自实现一个ListView的内容展现功能的应用；同时引入另外一个常用组件GridView来实现图片浏览的功能应用。</p>
<h2>Delicious书签订阅应用</h2>
<h3>一、应用功能描述</h3>
<p>Delicious书签订阅应用。进入应用首先展现订阅的书签源列表，点击书签源项，进入书签源书签列表页，共展现最新的20条书签。</p>
<p>数据来源由Delicious提供的JSON格式的数据。参考地址（<a href="http://www.delicious.com/help/json">http://www.delicious.com/help/json</a>）  <span></span></p>
<h3>二、应用界面截图</h3>
<h4>1、PhoneGap delicious bookmark</h4>
<p><a href="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=21318225335.bmp&amp;type=image%2Fbmp&amp;width=1010&amp;height=787"><img title="2" src="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=21318225335.bmp&amp;type=image%2Fbmp&amp;width=1010&amp;height=787" alt="" width="1010" height="787"></a></p>
<p><a href="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=21318225335.bmp&amp;type=image%2Fbmp&amp;width=1010&amp;height=787"></a> <a href="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=2011-10-11-11-36-351318304275.jpg&amp;type=image%2Fjpeg&amp;width=864&amp;height=728"></a></p>
<h4>2、Java delicious bookmark</h4>
<p><a href="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=2011-10-11-11-36-351318304275.jpg&amp;type=image%2Fjpeg&amp;width=864&amp;height=728"><img title="2011-10-11 11-36-35" src="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=2011-10-11-11-36-351318304275.jpg&amp;type=image%2Fjpeg&amp;width=864&amp;height=728" alt="" width="864" height="728"></a></p>
<h3>
三、功能测试对比</h3>
<p>【测试机器为 Google Nexus One G5】</p>
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td width="121" valign="top"><strong>Android</strong><strong>应用类型</strong></td>
<td width="217" valign="top"><strong>Html5 (phonegap)</strong></td>
<td width="230" valign="top"><strong>Java</strong></td>
</tr>
<tr>
<td width="121" valign="top"><strong>功能实现</strong></td>
<td width="217" valign="top">Html + jQuery基础库</td>
<td width="230" valign="top">ListView组件</td>
</tr>
<tr>
<td width="121" valign="top"><strong>文件大小</strong></td>
<td width="217" valign="top">159KB</td>
<td width="230" valign="top">23KB（只用了系统的原生界面）</td>
</tr>
<tr>
<td width="121" valign="top"><strong>内存占用</strong></td>
<td width="217" valign="top">45.37MB(RSS)</td>
<td width="230" valign="top">27.02MB(RSS)</td>
</tr>
<tr>
<td width="121" valign="top"><strong>数据通信</strong></td>
<td width="217" valign="top">Ajax</td>
<td width="230" valign="top">Apache http Java功能包</td>
</tr>
<tr>
<td width="121" valign="top"><strong>启动速度</strong></td>
<td width="217" valign="top">打开相同订阅源2.7秒</td>
<td width="230" valign="top">打开相同订阅源2.3秒</td>
</tr>
<tr>
<td width="121" valign="top"><strong>操作响应速度</strong></td>
<td width="217" valign="top">正常操作速度流畅，频繁操作响应会变慢</td>
<td width="230" valign="top">操作速度流畅</td>
</tr>
<tr>
<td width="121" valign="top"><strong>Monkey</strong><strong>注入5</strong><strong>万个事件测试结果</strong></td>
<td width="217" valign="top">Events injected: 50000:Dropped: keys=17 pointers=43 trackballs=0 flips=0     ## Network stats: elapsed time=1108130ms (0ms mobile, 1099670ms wifi, 8460ms not connected)
<p>// Monkey finished</p></td>
<td width="230" valign="top">Events injected: 50000:Dropped: keys=28 pointers=53 trackballs=0 flips=0     ## Network stats: elapsed time=1216977ms (0ms mobile, 1216977ms wifi, 0ms not connected)
<p>// Monkey finished</p></td>
</tr>
<tr>
<td width="121" valign="top"><strong>稳定性</strong></td>
<td width="217" valign="top">在Monkey测试注入大约4万个事件时，整个应用已经处于空白无响应状态。有连接超时情况发生。手动频繁操作会引起，响应速度变慢，webkit的WebView不能很好释放内存，甚至会引起应用的crash。</td>
<td width="230" valign="top">能较好处理Activity切换延时等待。运行较为流畅。Monkey测试时书签列表页切换时有时候会出现黑色背景，然后再载入列表，比正常速度稍慢。能够比较好的释放内存，没有出现过应用crash的情况。</td>
</tr>
<tr>
<td width="121" valign="top"><strong>资源占用</strong></td>
<td width="217" valign="top">对于频繁操作时，内存释放不够理想，导致内存占用上升。</td>
<td width="230" valign="top">内存占用相对比较稳定。</td>
</tr>
<tr>
<td width="121" valign="top"><strong>开发成本</strong></td>
<td width="217" valign="top">运用html +  css  + javascript开发，适合前端人员开发。由于webkit在不同的终端机发行版本不一样，所以需要针对不同的机型进行适配。同时对于不同屏幕大小在适配上也只能通过Javascript进行控制实现。</td>
<td width="230" valign="top">适合有Java开发经验的程序员，可以充分利用Android提供的组件进行开发。但是开发学习成本较高。</td>
</tr>
<tr>
<td width="121" valign="top"><strong>开发难度</strong></td>
<td width="217" valign="top">目前phonegap只使用一个WebView，开发时需要使用OPOA(One Page, One Application)的模式，对代码的组织方式及开发方式有较高要求。同时介于手机的资源有限，对如何管理和分配内存提出了要求。目前phonegap可以在控制台输出简单的JS调试日志，但是并不方便。</td>
<td width="230" valign="top">需要有Java开发经验，同时对Android开发体系有较深入的了解。</td>
</tr>
<tr>
<td width="121" valign="top"><strong>多人协作</strong></td>
<td width="217" valign="top">OPOA模式并不利于多人协作并行开发，只能通过基础的javascript的设计模式来解决多人协作的问题。</td>
<td width="230" valign="top">比较方便支持多人协作并行开发。</td>
</tr>
<tr>
<td width="121" valign="top"><strong>其它问题</strong></td>
<td width="217" valign="top">1、需要通过Javascript来管理操作的历史记录，并保证返回键能够正常使用；同样也包括选项键。2、不提供调用新的WebView，不能把现有的wap版的内容直接包装到应用当中。</td>
<td width="230" valign="top"></td>
</tr>
</tbody>
</table>
<h2>空间图片浏览应用</h2>
<h3>一、应用功能描述</h3>
<p>在应用中创建一个gridview方式展示的图片列表。 图片总数 48， 16行3列。 原生app使用gridview布局和渲染。webapp使用了jquery和jquery.mobile（后者依赖前者）</p>
<h3><span style="font-size:12pt;font-family:宋体">二、应用界面截图</span></h3>
<p style="margin:0cm 0cm 0pt;text-align:left">
<h4><span style="font-size:10.5pt;font-family:&#39;Calibri&#39;,&#39;sans-serif&#39;" lang="EN-US">1</span><span style="font-size:10.5pt;font-family:宋体">、</span><span style="font-size:10.5pt;font-family:&#39;Calibri&#39;,&#39;sans-serif&#39;" lang="EN-US">PhoneGap mm100 photo viewer</span></h4>
<p><a href="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=2011-10-11-14-43-161318315412.jpg&amp;type=image%2Fjpeg&amp;width=851&amp;height=696"><img title="2011-10-11 14-43-16" src="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=2011-10-11-14-43-161318315412.jpg&amp;type=image%2Fjpeg&amp;width=851&amp;height=696" alt="" width="851" height="696"></a></p>
<h4>2、Android Java  mm100 photo viewer</h4>
<p><a href="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=2011-10-11-14-44-181318315471.jpg&amp;type=image%2Fjpeg&amp;width=845&amp;height=695"><img title="2011-10-11 14-44-18" src="http://stblog.baidu-tech.com/wp-content/uploads/wp-display-data.php?filename=2011-10-11-14-44-181318315471.jpg&amp;type=image%2Fjpeg&amp;width=845&amp;height=695" alt="" width="845" height="695"></a></p>
<h3>三、功能测试对比</h3>
<p>【测试机器为 Google Nexus One G5】</p>
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td width="121" valign="top"><strong>Android</strong><strong>应用类型</strong></td>
<td width="217" valign="top"><strong>Html5 (phonegap)</strong></td>
<td width="230" valign="top"><strong>Java</strong></td>
</tr>
<tr>
<td width="121" valign="top"><strong>功能实现</strong></td>
<td width="217" valign="top">jQuery与jQuery Mobile基础库</td>
<td width="230" valign="top">GridView组件</td>
</tr>
<tr>
<td width="121" valign="top"><strong>文件大小</strong></td>
<td width="217" valign="top">220KB</td>
<td width="230" valign="top">72KB</td>
</tr>
<tr>
<td width="121" valign="top"><strong>内存占用</strong></td>
<td width="217" valign="top">40.6MB(RSS)</td>
<td width="230" valign="top">29.9MB(RSS)</td>
</tr>
<tr>
<td width="121" valign="top"><strong>启动速度</strong></td>
<td width="217" valign="top">约7秒（js大小会直接影响速度）</td>
<td width="230" valign="top">约3秒</td>
</tr>
<tr>
<td width="121" valign="top"><strong>操作响应速度</strong></td>
<td width="217" valign="top">在内容比较多的时候，都不是很流畅。调用外部交互：弹窗提示为例，会比原生大约有1s的延迟。</td>
<td width="230" valign="top">在内容比较多的时候，都不是很流畅。调用外部交互：原生app基本上瞬间响应。</td>
</tr>
<tr>
<td width="121" valign="top"><strong>Monkey</strong><strong>注入5</strong><strong>万个事件测试结果</strong></td>
<td width="217" valign="top">Events injected: 50000:Dropped: keys=20 pointers=72 trackballs=0 flips=0     ## Network stats: elapsed time=1460305ms (0ms mobile, 1440448ms wifi, 19857ms not connected)</td></tr></tbody></table></p>
<p>// Monkey finished
<table><td width="230" valign="top">Events injected: 30002:Dropped: keys=7 pointers=10 trackballs=0 flips=0     ## Network stats: elapsed time=230436ms (0ms mobile, 230436ms wifi, 0ms not connected)</td></table></p>
<p>** System appears to have crashed at event 30002 of 50000 using seed 0

<table><tr>
<td width="121" valign="top"><strong>稳定性</strong></td>
<td width="217" valign="top">由于交互深度较浅，所以整个Monkey测试过程较为流畅，但是还测试完毕后还是存在WebView内存无法释放的情况。</td>
<td width="230" valign="top">整个Monkey测试过程较流畅。</td>
</tr>
<tr>
<td width="121" valign="top"><strong>资源占用</strong></td>
<td width="217" valign="top">Webapp占用内存约40.6M，占用应该主要是由于webapp是基于浏览器的，并且加载了一个java库所致。所以资源占用应该不是线性的。</td>
<td width="230" valign="top">占用内存约29.9M，内存占用相对比较稳定。</td>
</tr>
<tr>
<td width="121" valign="top"><strong>开发难度</strong></td>
<td colspan="2" width="448" valign="top">对于比较简单的应用，在同样具备完善基础库的条件下，两者开发难度基本一致。</td>
</tr>
<tr>
<td width="121" valign="top"><strong>其它问题</strong></td>
<td colspan="2" width="448" valign="top">1.内存优化：webapp因为是基于浏览器的，而浏览器自身是进行了相应的优化的，所以在图片显示上很不错。     原生app如果在一页中显示比较多的图片的时候，必须比较细致完善的进行内存优化工作，否则极易出现因为图片资源过大而引起的崩溃问题。</td></tr></table></p>
<p>2.图片缩放裁切 webapp一般情况下通过js和css来进行缩放裁切。在进行图片动态缩放的时候，页面显示情况不是很正常（抖动，跳跃）。最好的办法是后端服务器对图片处理后再发送给手机端。</p>
<p>原生app可以直接通过java来对图片进行处理。</p>
<p>3.布局 原生app可以利用android提供的特殊技术方案，来自动适应多种分辨率的屏幕。如9png 和 多drawable目录。 相当简单方面。 但是在交互方面，原生app的开发量会比较大。</p>
<p>webapp就比较杯具一些了，需要开发者比较多的关注。 可以通过js来动态的获取屏幕尺寸进行资源调整和加载（开发几套不同的ui，然后根据分辨率js动态加载），这个会花费比较多的时间。</p>
<p>4.调试</p>
<p>webapp js调试不太方便，特别是调用外部应用的时候。如果是本应用内部，可以通过firebug进行调试。



<p style="margin:0cm 0cm 0pt;text-align:left">
<p style="margin:0cm 0cm 0pt;text-align:left">
<h2 style="margin:0cm 0cm 0pt;text-align:left"><strong>总结</strong><strong> </strong></h2>
<p>此次对比主要集中在对大量数据通信下web app UI性能。通过与Java app相比较，web app的UI性能会比Java app的UI性能差。主要原因是依赖webkit浏览器内核的渲染解析能力。同时在只有一个WebView的情况下，如何控制内存的上涨速度以无法释放内存的情况无缝地重新启动WebView从而不影响用户体验，是一个现实待解决问题。</p>
<p>在非大数据量且不需要频繁更新UI的情况下，基于wekit浏览器phonegap模式还是可以满足Android开发应用的需求。同时应用的实现的效率还依赖于OPOA开发模式的Javascript基础架构是否强大和高效。</p>
<p>对于不同分辨率的屏幕，需要通过JS或者通过要集成的框架封装来解决适配的问题。同时由于不同版本的Android所集成的webkit的版本不同，同样也需要处理不同版本的在JavaScript和CSS支持上不同的兼容性问题。还有解决开发时多人协作及方便的调试工具集成，也是进行html5 app开发的重要前提条件。<span> </span></p>
<p style="margin:0cm 0cm 0pt;text-align:left"><span style="font-size:small"><span style="font-family:宋体">此次对比主要集中在对大量数据通信下</span><span lang="EN-US"><span style="font-family:Calibri">web app UI</span></span><span style="font-family:宋体">性能。通过与</span><span lang="EN-US"><span style="font-family:Calibri">Java app</span></span><span style="font-family:宋体">相比较，</span><span lang="EN-US"><span style="font-family:Calibri">web app</span></span><span style="font-family:宋体">的</span><span lang="EN-US"><span style="font-family:Calibri">UI</span></span><span style="font-family:宋体">性能会比</span><span lang="EN-US"><span style="font-family:Calibri">Java app</span></span><span style="font-family:宋体">的</span><span lang="EN-US"><span style="font-family:Calibri">UI</span></span><span style="font-family:宋体">性能差。主要原因是依赖</span><span lang="EN-US"><span style="font-family:Calibri">webkit</span></span><span style="font-family:宋体">浏览器内核的渲染解析能力。同时在只有一个</span><span lang="EN-US"><span style="font-family:Calibri">WebView</span></span><span style="font-family:宋体">的情况下，如何控制内存的上涨速度以无法释放内存的情况无缝地重新启动</span><span lang="EN-US"><span style="font-family:Calibri">WebView</span></span><span style="font-family:宋体">从而不影响用户体验，是一个现实待解决问题。</span><span lang="EN-US"> </span></span></p>
<p style="margin:0cm 0cm 0pt;text-align:left"><span style="font-size:small"><span lang="EN-US"><span><span style="font-family:Calibri"> </span></span></span><span style="font-family:宋体">在非大数据量且不需要频繁更新</span><span lang="EN-US"><span style="font-family:Calibri">UI</span></span><span style="font-family:宋体">的情况下，基于</span><span lang="EN-US"><span style="font-family:Calibri">wekit</span></span><span style="font-family:宋体">浏览器</span><span lang="EN-US"><span style="font-family:Calibri">phonegap</span></span><span style="font-family:宋体">模式还是可以满足</span><span lang="EN-US"><span style="font-family:Calibri">Android</span></span><span style="font-family:宋体">开发应用的需求。同时应用的实现的效率还依赖于</span><span lang="EN-US"><span style="font-family:Calibri">OPOA</span></span><span style="font-family:宋体">开发模式的</span><span lang="EN-US"><span style="font-family:Calibri">Javascript</span></span><span style="font-family:宋体">基础架构是否强大和高效。</span></span></p>
<p><span style="font-size:10.5pt;font-family:&#39;Calibri&#39;,&#39;sans-serif&#39;" lang="EN-US"><span> </span></span><span style="font-size:10.5pt;font-family:宋体">对于不同分辨率的屏幕，需要通过</span><span style="font-size:10.5pt;font-family:&#39;Calibri&#39;,&#39;sans-serif&#39;" lang="EN-US">JS</span><span style="font-size:10.5pt;font-family:宋体">或者通过要集成的框架封装来解决适配的问题。同时由于不同版本的</span><span style="font-size:10.5pt;font-family:&#39;Calibri&#39;,&#39;sans-serif&#39;" lang="EN-US">Android</span><span style="font-size:10.5pt;font-family:宋体">所集成的</span><span style="font-size:10.5pt;font-family:&#39;Calibri&#39;,&#39;sans-serif&#39;" lang="EN-US">webkit</span><span style="font-size:10.5pt;font-family:宋体">的版本不同，同样也需要处理不同版本的在</span><span style="font-size:10.5pt;font-family:&#39;Calibri&#39;,&#39;sans-serif&#39;" lang="EN-US">JavaScript</span><span style="font-size:10.5pt;font-family:宋体">和</span><span style="font-size:10.5pt;font-family:&#39;Calibri&#39;,&#39;sans-serif&#39;" lang="EN-US">CSS</span><span style="font-size:10.5pt;font-family:宋体">支持上不同的兼容性问题。还有解决开发时多人协作及方便的调试工具集成，也是进行</span><span style="font-size:10.5pt;font-family:&#39;Calibri&#39;,&#39;sans-serif&#39;" lang="EN-US">html5 app</span><span style="font-size:10.5pt;font-family:宋体">开发的重要前提条件。</span></p>
<p style="margin:0cm 0cm 0pt;text-align:left">
<h2></h2></p></p></p></p>