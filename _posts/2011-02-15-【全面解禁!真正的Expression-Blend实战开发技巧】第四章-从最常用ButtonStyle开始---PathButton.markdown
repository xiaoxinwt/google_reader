---
layout: post
title:  "【全面解禁!真正的Expression Blend实战开发技巧】第四章 从最常用ButtonStyle开始 - PathButton"
date:   2011-02-15 18:06:00
author: 烤地瓜
categories: program
---

## 【全面解禁!真正的Expression Blend实战开发技巧】第四章 从最常用ButtonStyle开始 - PathButton
### by 烤地瓜
### at 2011-02-15 18:06:00
### original <http://www.cnblogs.com/kaodigua/archive/2011/02/15/1955472.html>

<p><p>　　上一篇我们介绍了TextButton，但为了追求界面的张力，时尚，仅仅使用系统的字体是不够的。在传媒领域中名片，报章，杂志，广告中的字体非常讲究。我们系统界面也是这样，一些很酷的 flash英文网站，为了追求最佳的效果，常常使用20种以上的字体。</p>
<p>　　我个人非常喜欢微软雅黑，但我无法保证每一个客户端都装有微软雅黑。在blend中可以很容易的嵌入字体包，供用户下载。但无奈中文字库太大了，一个微软雅黑就要20多M。好在微软提供了矢量图形，让我们中国的开发者可以有限度使用一些特殊的字体而无需下载字库，但这也带来了许多麻烦，许多情况下只能靠自己去摸索一条快速，有效的工作方式，根本无法在国外的网站中查找到解决方案。有些难题我要花几周时间才得以解决。随后又在N个月的实践中逐步完善。silverlight前端开发的过程中，更多的是许多零碎的知识点和经验的累积，也许每一个点拿出来分享，读者都会觉得这太easy了，但正如我签名中写的“所有真正杰出的设计一旦被设计好，看起来都是那么的简单和显而易见。但是在获得杰出设计的过程中，需要付出令人难以置信的努力”，在学习知识前，首先要尊重知识，知识才会尊重你。本章我就拿上一章的TextButtonStyle为基础，与大家分享，如何将特殊字体的文本转换为矢量图形，使用矢量图形做Button会遇到的难题，如何解决难题。</p>
<p>　　如果你看过上一章，并跟着我一步一步做出来。那么你应该得到一个使用了TextButtonStyle的Button，入下图所示</p>
<p>　　　　<img src="http://pic002.cnblogs.com/images/2011/223739/2011021517241324.jpg"></p>
<p>　　下面是代码</p>
<div style="width:1213px;height:91px">
<pre><div><span style="color:#0000ff">&lt;</span><span style="color:#800000">Button </span><span style="color:#ff0000">Content</span><span style="color:#0000ff">="I'm TextButon"</span><span style="color:#ff0000"> HorizontalAlignment</span><span style="color:#0000ff">="Left"</span><span style="color:#ff0000"> Margin</span><span style="color:#0000ff">="15,27,0,0"</span><span style="color:#ff0000"> </span></div><div><span style="color:#ff0000">Style</span><span style="color:#0000ff">="</span><span style="color:#808000">{StaticResource TextButtonStyle}</span><span style="color:#0000ff">" </span><span style="color:#ff0000"> VerticalAlignment</span><span style="color:#0000ff">="Top"</span><span style="color:#0000ff">/&gt;</span></div></pre>
</div>
<p>　　</p>
<p>　　大家可能注意到我在这里使用了一种特殊的字体，名为28 Days Later，既然我们打算不依赖于该字体，第一步就是将I'm TextButton矢量化。</p>
<p>　　添加一个TextBlock，指定字体为28 Days Later(这里你可以随意指定其他字体，比如黑体),然后右键点击Textblock,在弹出菜单中选择Path -&gt;Convert to Path</p>
<p>　　　　<img src="http://pic002.cnblogs.com/images/2011/223739/2011021517304327.jpg"></p>
<p>　　这样我们就成功得到了一个矢量图形 </p>
<p>　　　　<img src="http://pic002.cnblogs.com/images/2011/223739/2011021517321551.jpg"></p>
<p>　　在Blend左侧，Objects and timeline窗口中，拖拽Path到Button中。如果你成功了，那么此时界面结构应如下图所示</p>
<p>　　　　<img src="http://pic002.cnblogs.com/images/2011/223739/2011021517342017.jpg"></p>
<p>　　点击F5运行程序，此时你会遇到第一个难题。</p>
<p>　　　　<img src="http://pic002.cnblogs.com/images/2011/223739/2011021517370791.jpg"></p>
<p>　　请注意看上图，上一章我们在TextButtonStyle中，设置ContentControl的Cursor为Hand，所以当我们鼠标悬浮于文字上时，光标变成了"手指"的形状，但当你在按钮上运动时会发现一个恶心的事，为什么光标不停地在"箭头(arrow)"和"手指(hand)"中来回切换？这种体验显然很不好。随后，你在Button上慢慢移动鼠标，终于搞清楚了，原来只有光标停留在上图黑色部分时，才会变为"手指"，所有的白色部分，包括字母"O"中间的白色部分，光标都还是"箭头(arrow)"。要解决这个问题，首先要理解几个概念，在silverlight中，所有界面元素，都有三种状态。</p>
<p>　　　　<img src="http://pic002.cnblogs.com/images/2011/223739/2011021517432691.jpg"></p>
<p>　　第一种叫“存在，且看得见” 即Opactiy =1 ，Visibility = Visible ；</p>
<p>　　第二种叫“不存在，且看不见” 即Opactiy =1 ，Visibility = Collapsed (Collapsed时候控件的可视树根本不会加载，也不会消耗资源)；</p>
<p>　　第三种叫“存在，且看不见&quot;  即Opactiy =0 ，Visibility = Visible；</p>
<p>　　除了这三种外，我要告诉大家的是神秘的第四种，许多人不知道，但他真的非常有用；</p>
<p>　　第四种叫“存在，但是看不见”即Opactiy =1 ，Visibility = Visible ，Background=&quot;#00??????&quot; <span style="color:#00ccff">(重点是前两位00,后面的问号代表0~255间任意数值)；</span></p>
<p>　　要解决上面的问题就一定要使用第四种状态。首先右击Button，在弹出菜单中选择Edit Template -&gt; Edit Current,进入样式编辑状态</p>
<p>　　　　<img src="http://pic002.cnblogs.com/images/2011/223739/2011021517503382.jpg"></p>
<p>　　设置包裹ContentControl的Grid的Cursor=Hand,并为<span style="color:#00ccff">Grid随意指定一个背景颜色</span>，点击F5运行程序,鼠标移动到Button上你会发现，这次光标终于稳定了，完全变成hand状态。但唯一可惜的是背景的大红不是我们想要的。</p>
<p>　　　　<img src="http://pic002.cnblogs.com/images/2011/223739/2011021517550772.jpg"></p>
<p>　　不过不要紧，你只需要设置Button的Backgroun的阿尔法值为0，既可以完美解决这个问题。</p>
<p>　　　　<img src="http://pic002.cnblogs.com/images/2011/223739/2011021517573953.jpg"></p>
<p>　　再次运行程序，一个有着酷酷的字体的完美按钮出现在你眼前了。</p>
<p>　　　　<img src="http://pic002.cnblogs.com/images/2011/223739/2011021517370791.jpg"></p>
<p>　　不过对于一个程序原来说，这还没结束，从命名的角度讲我们目前的Style名为TextButtonStyle似乎有点不恰当，最好将它改为PathButtonStyle。点击Resource面板，展开Usercontrol节点，在TextButtonStyle处双击鼠标，就可以修改名称了。</p>
<p>　　　　<img src="http://pic002.cnblogs.com/images/2011/223739/2011021518014774.jpg"></p>
<p>　　这一讲就到这里，下一讲我会介绍一个有那么点特别的ImageButtonStyle。这种设计我保证你一定会遇到，而且会为此头痛。我会与大家分享最完美的解决方案，敬请期待!</p><img src="http://www.cnblogs.com/kaodigua/aggbug/1955472.html?type=1" width="1" height="1" alt=""><p>作者: <a href="http://www.cnblogs.com/kaodigua/">烤地瓜</a> 发表于 2011-02-15 18:06 <a href="http://www.cnblogs.com/kaodigua/archive/2011/02/15/1955472.html">原文链接</a></p><p>评论: 9　<a href="http://www.cnblogs.com/kaodigua/archive/2011/02/15/1955472.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/kaodigua/archive/2011/02/15/1955472.html#commentform">发表评论</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/91164/">卓越亚马逊总裁：2011年电子商务主拼无线应用</a><span style="color:gray">(2011-02-16 12:45)</span><br>· <a href="http://news.cnblogs.com/n/91163/">刘强东：京东要踩刹车 希望放缓订单增长量</a><span style="color:gray">(2011-02-16 12:43)</span><br>· <a href="http://news.cnblogs.com/n/91162/">金山词霸：暂不回应换标传闻</a><span style="color:gray">(2011-02-16 12:41)</span><br>· <a href="http://news.cnblogs.com/n/91161/">东西网“目前暂时无法访问”，“但我们确实备案了”</a><span style="color:gray">(2011-02-16 12:40)</span><br>· <a href="http://news.cnblogs.com/n/91160/">美国称将采取措施推动互联网自由</a><span style="color:gray">(2011-02-16 12:39)</span><br></p><p>编辑推荐：<a href="http://news.cnblogs.com/n/91108/">给想当程序员的大二学生的建议</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">我的园子</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://kb.cnblogs.com">知识库</a></p></p>