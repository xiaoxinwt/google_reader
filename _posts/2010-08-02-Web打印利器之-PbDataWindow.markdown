---
layout: post
title:  "Web打印利器之-PbDataWindow"
date:   2010-08-02 08:07:00
author: Vincent.Q
categories: program
---

## Web打印利器之-PbDataWindow
### by Vincent.Q
### at 2010-08-02 08:07:00
### original <http://www.cnblogs.com/xiyang1011/archive/2010/08/02/1790141.html>

<p>作者: <a href="http://www.cnblogs.com/xiyang1011/">Vincent.Q</a> 发表于 2010-08-02 08:07 <a href="http://www.cnblogs.com/xiyang1011/archive/2010/08/02/1790141.html">原文链接</a> 阅读: 1120 评论: 9</p><h3>业务场景</h3>
<p>前段时间公司要把PB的东东搞到Asp.Net项目里,并实现打印.考虑过使用水晶报表,这样PB的DataWindow(数据窗口)就用不上了,这个很心痛,而且水晶报表相当于是个鸡肋.(书中暗表,PB的DataWindow相当相当的强大,不用确实可惜).经过多方打探和印证,PB的DataWindow有个active的web控件,叫active datawindow.其实sybase本身推出了datawindow.net,但个人感觉,使用起来没有active datawindow好用.</p>
<p>active datawindow的强大之外在于</p>
<p>l 可以以字符串方式加载数据</p>
<p>l Asp.net所涉及的样式表,js代码一律不需要,我们只需要在后台将数据加载至dataset或datatable对象里即可,调用一个转为字符串的方法,直接加载即可</p>
<p>l PB中可以用Create方式在DataWindow中创建一个对象,同样的语法,直接可以在active datawindow调用.这样就最大程度的解决了PB和Net间共用问题.</p>
<p>好了,废话不多说了.先看一下整体效果图,如图-1所示.本篇贴子就是详细讲解一下如何使用这个控件实现web打印.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/xiyang1011/WindowsLiveWriter/WebPbDataWindow_720C/clip_image002_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="clip_image002" border="0" alt="clip_image002" src="http://images.cnblogs.com/cnblogs_com/xiyang1011/255890/o_%E5%9B%BE-1.jpg" width="600" height="337"></a></p>
<p>图-1</p>
<p>怎么样,效果还可以吧!展示区部分,使用的就是active datawindow控件.如果使用拼接html字符串的方式,可能要拼上一段时间吧,而且html的样式也可拼接进去.至少对于我来说,我会疯掉.现在web开发流行这样一句话:行为和结构分开!</p>
<h4>实现环境</h4>
<p>首先,要装个pb11.5,原因是需要其中的dll和cab文件,只要有这些dll和cab包之外,再加上pb必备的dll文件,也可以不装pb11.5的(所有的dll和cab文件,均可以下载).文件列表如下所示,如图-2和图-3所示</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/xiyang1011/WindowsLiveWriter/WebPbDataWindow_720C/clip_image003_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="clip_image003" border="0" alt="clip_image003" src="http://images.cnblogs.com/cnblogs_com/xiyang1011/255890/o_%E5%9B%BE-2.jpg" width="381" height="301"></a></p>
<p>图-2</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/xiyang1011/WindowsLiveWriter/WebPbDataWindow_720C/clip_image004_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="clip_image004" border="0" alt="clip_image004" src="http://images.cnblogs.com/cnblogs_com/xiyang1011/255890/o_%E5%9B%BE-3.jpg" width="164" height="43"></a></p>
<p>图-3</p>
<p>若已安装pb11.5的</p>
<p>cab文件在\Sybase\Shared\PowerBuilder文件夹</p>
<p>dll文件在\Sybase\PowerBuilder 11.5\DotNET\bin文件夹.还有一部分dll文件是pb必备的,在系统盘的/window/system32/文件夹里可以找到,pb开头的</p>
<h4>实现步骤</h4>
<p>下面我们通过一个完整的实例来展现它的强大</p>
<p>第1步,搭建环境,新建一个web项目解决方案,将我们之前安装好的cab和dll文件放进来,整体布局如图-4所示</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/xiyang1011/WindowsLiveWriter/WebPbDataWindow_720C/clip_image005_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="clip_image005" border="0" alt="clip_image005" src="http://images.cnblogs.com/cnblogs_com/xiyang1011/255890/o_%E5%9B%BE-4.jpg" width="223" height="307"></a></p>
<p>图-4</p>
<p>在此说明下</p>
<p>App_Code是针对这个控件封装的一些类</p>
<p>Cab是控件安装包</p>
<p>Pbl是数据窗口展示部分,一个页面的样式,布局,非数据部分都在这里存储</p>
<p>Public下面的js文件,也是针对这个控件封装的js前端类</p>
<p>这里要注意一下,由于此控件加载数据是通过ajax方法从后端取数据加载的,因此前后端交互所产生的数据量会比较大,需要在web.config加上如下代码,如图-5所示</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/xiyang1011/WindowsLiveWriter/WebPbDataWindow_720C/clip_image006_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="clip_image006" border="0" alt="clip_image006" src="http://images.cnblogs.com/cnblogs_com/xiyang1011/255890/o_%E5%9B%BE-5.jpg" width="500" height="133"></a></p>
<p>图-5</p>
<p>其次,我们需要布局一个页面,比如样式,文字大小和颜色等,这里我使用的是PB11.5,目前没有什么好工具可以替代PB来画数据窗口,如图-6所示,我们画好了一个页面</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/xiyang1011/WindowsLiveWriter/WebPbDataWindow_720C/clip_image008_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="clip_image008" border="0" alt="clip_image008" src="http://images.cnblogs.com/cnblogs_com/xiyang1011/255890/o_%E5%9B%BE-6.jpg" width="616" height="540"></a></p>
<p>图-6</p>
<p>里面对于文字的大小,字体,颜色等都可以调整.而且还可以把数据窗口导出至字符串文件,如图-7所示</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/xiyang1011/WindowsLiveWriter/WebPbDataWindow_720C/clip_image010_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="clip_image010" border="0" alt="clip_image010" src="http://images.cnblogs.com/cnblogs_com/xiyang1011/255890/o_%E5%9B%BE-7.jpg" width="669" height="514"></a></p>
<p>图-7</p>
<p>保存成字符串的文件后,好处多多啊!</p>
<p>第2步,在页面中引用此控件,如图-8所示</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/xiyang1011/WindowsLiveWriter/WebPbDataWindow_720C/clip_image012_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="clip_image012" border="0" alt="clip_image012" src="http://images.cnblogs.com/cnblogs_com/xiyang1011/255890/o_%E5%9B%BE-8.jpg" width="638" height="238"></a></p>
<p>图-8</p>
<p>引入之后,所有准备工作都以完成,接下来就直接编写代码了</p>
<p>第3步,编程实现数据加载,采用的是ajax从后台取数据,在前台加载,我们先看看后台代码,如图-9所示</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/xiyang1011/WindowsLiveWriter/WebPbDataWindow_720C/clip_image013_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="clip_image013" border="0" alt="clip_image013" src="http://images.cnblogs.com/cnblogs_com/xiyang1011/255890/o_%E5%9B%BE-9.jpg" width="550" height="425"></a></p>
<p>图-9</p>
<p>我们说下代码的思路</p>
<p>1. 首先,创建一个datastore对象,相当于net里的dataset/datatable,即数据集,创建它时,要指定它是所属哪个数据窗口(相当于数据源,sql语句是啥)</p>
<p>2. 其次,将要加载至此控件中的数据加载至dataset或datatable对象中,然后通过ds_print.Retrieve(table_data)即可实现绑定,看就这一句话,就实现了数据的加载,加载好以后,通过ajax方法传至前台页面,前台页面接收到值以后,按图-10所示代码即可实现加载</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/xiyang1011/WindowsLiveWriter/WebPbDataWindow_720C/clip_image014_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="clip_image014" border="0" alt="clip_image014" src="http://images.cnblogs.com/cnblogs_com/xiyang1011/255890/r_%E5%9B%BE-10.jpg" width="548" height="185"></a></p>
<p>图-10</p>
<p>至此,加载数据过程结束,让我们看看最终效果图,如图-11所示</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/xiyang1011/WindowsLiveWriter/WebPbDataWindow_720C/clip_image016_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="clip_image016" border="0" alt="clip_image016" src="http://images.cnblogs.com/cnblogs_com/xiyang1011/255890/r_%E5%9B%BE-11.jpg" width="616" height="399"></a></p>
<p>图-11</p>
<p>提供的示例项目中有详细代码,在此不再赘述.</p>
<h4>开发结论</h4>
<p>1. 整个开发过程中,最为复杂的环节属于画页面的过程,加载数据只需加载至dataset/datatable对象中即可</p>
<p>2. 动态实现控件赋值,如图-12所示.同时我们还可以动态的创建控件,删除控件,控件包括图片,计算域均可</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/xiyang1011/WindowsLiveWriter/WebPbDataWindow_720C/clip_image017_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="clip_image017" border="0" alt="clip_image017" src="http://images.cnblogs.com/cnblogs_com/xiyang1011/255890/o_%E5%9B%BE-12.jpg" width="539" height="363"></a></p>
<p>图-12</p>
<p>3. 较为复杂的报表,比如主明细,有表头和表尾,套打之类的,都没有问题.</p>
<p>结论</p>
<p>在bs软件中实现cs方式的打印,实在是bs软件打印功能的利器,在此强烈推荐!</p>
<h4>FAQ</h4>
<p>1. 问:执行打印事件时没有响应?</p>
<p>答:需要指定一个打印机名称,在前台代码中指定即可,如图-13</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/xiyang1011/WindowsLiveWriter/WebPbDataWindow_720C/clip_image018_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="clip_image018" border="0" alt="clip_image018" src="http://images.cnblogs.com/cnblogs_com/xiyang1011/255890/o_%E5%9B%BE-13.jpg" width="531" height="137"></a></p>
<p>图-13</p>
<p>2. 问:所有控件都可以动态创建吗?</p>
<p>答:可以,请参考PbDwModelWebUI类方法</p>
<p>3. 问:有表头,表体和表尾的报表如何处理?</p>
<p>答:制作数据窗口的时候,制作为复合式数据窗口,分别对这个数据窗口中的子数据窗口加载数据即可,详细代码请参考PbDwModelWebUI类</p>
<p>4. 问:数据窗口中栏目类型是下拉菜单如何加载其数据源?</p>
<p>答:将下拉菜单的数据源看成是datatable即可,也相当于子数据窗口,详细代码请参考PbDwModelWebUI类</p>
<p>5. 问:关于此控件的帮助文档在哪可以找到?</p>
<p>答:PB11.5编程环境中即可找到,如图-14所示.browser页面内,选ole页面即可</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/xiyang1011/WindowsLiveWriter/WebPbDataWindow_720C/clip_image019_2.jpg"><img style="border-bottom:0px;border-left:0px;display:inline;border-top:0px;border-right:0px" title="clip_image019" border="0" alt="clip_image019" src="http://images.cnblogs.com/cnblogs_com/xiyang1011/255890/o_%E5%9B%BE-14.jpg" width="546" height="479"></a></p>
<p>图-14</p>
<p>6. 问:提示控件安装失败或者没有提示安装</p>
<p>答:在ie选项-高级里,关于active控件的选项,置为提示或启用,如果仍然不可以,再在ie选项-&gt;安全-&gt;自定义级别里,将active控件的选项,置为提示或启用即可</p>
<p>最后,祝大家使用此控件愉快,同时,本人免费提供该控件技术支持!</p>
<p> </p>
<p>源码<a title="WebPbDataWindow" href="http://files.cnblogs.com/xiyang1011/WebPbDataWindow.rar">WebPbDataWindow</a></p>
<p> </p>
<p> </p><img src="http://www.cnblogs.com/xiyang1011/aggbug/1790141.html?type=1" width="1" height="1" alt=""><p>评论: 9　<a href="http://www.cnblogs.com/xiyang1011/archive/2010/08/02/1790141.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/xiyang1011/archive/2010/08/02/1790141.html#commentform">发表评论</a></p><p><a href="http://job.cnblogs.com/">程序员找工作，就在博客园</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/69823/">韩国启动1G宽带服务试点 已超我国250倍</a><span style="color:gray">(2010-08-02 13:40)</span><br>· <a href="http://news.cnblogs.com/n/69822/">姚欣笑谈PPLive吸引陶闯加盟：盖茨也需要鲍尔默</a><span style="color:gray">(2010-08-02 13:26)</span><br>· <a href="http://news.cnblogs.com/n/69819/">三星的Windows Phone 7手机将支持视频通话</a><span style="color:gray">(2010-08-02 12:35)</span><br>· <a href="http://news.cnblogs.com/n/69817/">Tor开发者被美国警方短暂拘留，追问Wikileaks</a><span style="color:gray">(2010-08-02 12:28)</span><br>· <a href="http://news.cnblogs.com/n/69816/">微软发布Windows Phone 7设计模板</a><span style="color:gray">(2010-08-02 12:12)</span><br></p><p>编辑推荐：<a href="http://www.cnblogs.com/cmt/archive/2010/07/30/1788463.html">博客园上海俱乐部八月份活动通知（2010-8-7）</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">个人主页</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://space.cnblogs.com">社区</a>  <a href="http://kb.cnblogs.com">知识库</a></p>