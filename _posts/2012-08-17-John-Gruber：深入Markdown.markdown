---
layout: post
title:  "John Gruber：深入Markdown"
date:   2012-08-17 09:12:04
author: 张 重骐
categories: program
---

## John Gruber：深入Markdown
### by 张 重骐
### at 2012-08-17 09:12:04
### original <http://blog.jobbole.com/25449/?utm_source=rss&utm_medium=rss&utm_campaign=%25e6%25b7%25b1%25e5%2585%25a5markdown>

<p>“有时候一件事情的真相，不是来自于对它的思考，而是来自于对它的感觉。” ——STANLEY KUBRICK</p>
<p><strong>要点1</strong></p>
<p>这儿有个问题：你最近一次听到足以改变你内心想法的论述是什么时候？不仅仅是关于那些你没怎么想过的问题，也包括在听到这个论述之前你原本相当确信的观念。<span></span></p>
<p>也就是说，你最近一次认识到自己在某个观念上彻底错了是什么时候？</p>
<p>如果你的回答是“从来没有”，或者是“很久以前”，那么这是否就意味着你总是正确的？</p>
<p>这里有我的一个故事。</p>
<p>一月份的时候，关于Postel法则在解析XML聚合信息流（比如<a href="http://blogs.law.harvard.edu/tech/rss">RSS</a>和<a href="http://www.atomenabled.org/">Atom</a>这样的格式）的软件中的应用，曾经有过一次争论。这次争论简而言之，就是<a href="http://www.ibiblio.org/pub/docs/rfc/rfc793.txt">Postel法则认为</a>：“自己做的时候要谨慎，对别人的东西要宽容”，而XML规范则明确说明：“一旦发现致命错误，解析器就不能再继续正常的解析流程了（比如，绝不能继续将字符数据和文档结构描述信息还用标准方式传递给应用程序）。”</p>
<p>那么，解析XML聚合信息流的软件该如何抉择呢？是依照Postel法则，包容遇到的错误？还是依照XML标准，一遇到严重错误就中端处理？</p>
<p><a href="http://inessential.com/?comments=1&amp;postid=2770">Brent Simmons</a>（Mac系统上的主流新闻聚合阅读器NetNewsWire的开发者）和<a href="http://nick.typepad.com/blog/2004/01/feeddemon_and_w_1.html">NIck Bradbury</a>（Windows系统上主流新闻聚合阅读器FeedDemon的开发者）都认为在处理Atom和XML信息流时，他们的软件应该采取严格的标准。很多牛人都同意他们的看法。</p>
<p>“客户端的标准应该严格”的论点，我觉得可以归结为一下几点：</p>
<p>1、正确、规整的XML要好于有语法错误的XML；</p>
<p>2、要写出能生成正确、规整的XML的软件并不是那么难（就像<a href="http://www.tbray.org/ongoing/When/200x/2004/01/11/PostelPilgrim">Tim Bray说的</a>，“任何不能用规整的XML写聚合信息流的人都是无能的蠢货”）。</p>
<p>3、如果主流的XML信息流阅读软件都要求正确、规整的XML数据，那么就会促使生成信息流的应用不再输出糟糕的XML。</p>
<p>这三点我都同意，因此，我坚定地站在“客户端解析时应该有严格标准”的阵营中。</p>
<p>但之后我读了Mark Pilgrim的“<a href="http://diveintomark.org/archives/2004/01/14/thought_experiment">思想实验</a>”。Pilgrim不仅是信息流解析应该宽松的倡议者，而且他公开了自己的所有代码，还写出了著名的开源信息流解析器<a href="http://diveintomark.org/projects/feed_parser/">Universal Feed Parser</a></p>
<p>Pilgrim的观点，至少在我看来是这样的：如果你在做处理XML信息流的软件，而你的解析器非常严格，那么你的用户在遇到异常数据的时候就会遭殃。事实上，你的用户终究是会遇到异常数据的；这种情况发生的话，也许错在造成数据异常的人，但最终受害的却是你的用户，就因为客户端被强加的严格检查。</p>
<p>读过之后，我认真思考了他的思想实验，最终意识到我之前彻底错了，他是对的。（Simmons也改变了他自己的想法，看以到<a href="http://inessential.com/?comments=1&amp;postid=2785">这里</a>和<a href="http://inessential.com/?comments=1&amp;postid=2786">这里</a>看后续的故事。）</p>
<p>Pilgrim仅凭借他在“<a href="http://diveintomark.org/archives/2004/01/14/thought_experiment">思想实验</a>”中的论述，就让我认识到自己错了。但有意思的是，他说服我的时候，<strong>并没有反驳任何一个让我一开始站在“严格解析”阵营的事实</strong>。</p>
<p>这就是我要说的第一点：当我最终改变某个观点的时候，通常并不是因为我掌握的事实有误，而是因为我没有选对事实。</p>
<p><a href="http://blog.jobbole.com/wp-content/uploads/2012/08/John-Gruber-Markdown.jpg" rel="lightbox[25449]" title="John Gruber - Markdown"><img title="John Gruber - Markdown" src="http://blog.jobbole.com/wp-content/uploads/2012/08/John-Gruber-Markdown.jpg" alt="" width="458" height="333"></a></p>
<p><strong>要点2</strong></p>
<p>回头来看，所有博客软件背后的基本理念，其核心似乎都很简单。你是在管理一系列的文章，而不是有一系列网页的网站，博客软件会帮你将文章转换成网页。</p>
<p>博客软件对一般人的吸引力很容易理解。没有博客软件，他们就没法发布站点。</p>
<p>但博客软件对行家们——就是完全有能力用HTML手写出一个博客网站的专家们——的吸引力在哪儿呢？当然了，确实有<a href="http://zeldman.com/">一些人坚持手写</a>。但是对大多数人，甚至包括世界上<a href="http://simplebits.com/">最博学</a>和<a href="http://stopdesign.com/">最著名</a>的web工程师，都在用博客软件包（或者是<a href="http://waferbaby.com/">开发自己的</a>博客发布程序）。</p>
<p>答案是方便，灵活。博客软件免去了更新站点的过程中绝大多数的单调劳动。我必须承认——直到我2002年发布Daring Fireball的几个月前才明白这点。我能手写一整个网站出来而且还觉得写代码很容易，这样的事实让我忽视了在这个过程中有很多很多机械重复性的工作。</p>
<p>下面是我每次往Daring Fireball上发布一篇新文章实际上会发生的事：</p>
<p>1、新文章出现在首页的顶部（同时从首页移去最旧的文章）；</p>
<p>2、新文章有一个单独的永久页面；</p>
<p>3、在我的RSS信息流中更新这篇文章；</p>
<p>4、标题和新页面的链接被添加到我的<a href="http://daringfireball.net/archives">存档</a>页面；</p>
<p>5、新页面的标题和链接作为“下一篇”被添加到上一篇文章中。</p>
<p>我只需要一个操作——发布一篇文章——然后Movable Type就会创建一个新文件，并且更新其他四项。这些任务都不<em>难</em>手动完成，其实很大程度上就是拷贝-粘贴的事儿。这些工作确实很无聊——而且即使确实不<em>难</em>，我仍然很可能出错。我会犯错，因为我可能烦了，或是忘了操作其中哪步。单调的重复性工作正是计算机所擅长，而不适合人类的。</p>
<p>另外，博客软件的这种封装也显得很自然。写一篇文章——或是对现有文章做出修改——感觉上就应该是一项任务。我要编辑的是文章，而不是文章所出现的页面。</p>
<p>因此，我要说的第二点是：不能仅仅因为一件事情不难，就认为它应该这样做。</p>
<p> </p>
<p><strong>下面我要试着将要点1和要点2结合起来，说说Markdown的事儿。</strong></p>
<p>让我们先回退一步。之前我说了，博客软件让你将站点当作文章的集合来管理，而不是页面的集合。</p>
<p>那么什么是一篇<em>文章</em>？</p>
<p>常见的说法是，一篇文章就是一个HTML代码段。不是完整的HTML文档，仅仅是一段HTML格式的文本——博客软件负责生成完整的HTML（或者XML）文档。你的博客模板里有关于文档结构的所有标签——&lt;html&gt;, &lt;head&gt;, &lt;body&gt;, 等等——以及给你文章预留的位置。一旦你发布，你的博客软件就把你的文章当作HTML代码短插入你的HTML模板中。</p>
<p>在我写Daring Fireball的第一年里，即从2002年8月到2003年8月的整整一年，我非常认可“文章就是HTML代码段”的说法。我甚至从来没有考虑过别的可能性。我在Daring Fireball中写的每一篇文章，都是标准的HTML格式。（其实是XHTML，但这种区别现在不重要。）</p>
<p>当然，除了一点之外，那就是HTML<em>代码段</em>不能被作正确性验证，因为HTML是一种文件类型。你可以写出<em>规整的</em>HTML代码——确保关闭每一个标签，转换每一个&amp;和&lt;&gt;符号——但你却不能将HTML代码段放到<a href="http://validator.w3.org/">W3C HTML Validator</a>上做检查，也不能用BBEdit的HTML语法检查工具。</p>
<p>我期望的工作流程是：</p>
<p>1、在BBEdit中写文章，编辑，修订；</p>
<p>2、搞定后，登录MT的web界面，将文章粘贴进去，然后发布。</p>
<p>但我的实际工作流程看起来却是这样的：</p>
<p>1、在BBEdit中写文章；</p>
<p>2、在<span><a href="http://blog.jobbole.com/12749/" title="浏览器">浏览器</a></span>中预览：</p>
<p>3、切换到BBEdit中修订；</p>
<p>4、重复上面的步骤，直到搞定；</p>
<p>5、登录MT，粘贴文章，然后发布。</p>
<p>最终，我领悟到：<em>这太愚蠢了</em>。用电脑写文章的最大优势就是编辑起来比较直接。写、读、修订，都在同一个窗口中、同一种模式下完成。</p>
<p>用HTML格式直接写文章的理由——我用了很多年的理由——是HTML并不难。这点<em>我仍然认同</em>。HTML很容易学会，而且一旦学会，就能马上用起来。专业的web开发？那确实很难。但HTML的基本tag和规则——足够能让你用HTML代码直接撰写博客的HTML知识——是很简单的。</p>
<p>但像<a href="http://lynx.browser.org/">Lynx</a>这样的纯文本浏览器也不直接给你显示HTML源代码，是有原因的。因为HTML本身就不是要作为一种可读格式存在的。用一种没有可读性的格式来写作，你不觉得很奇怪吗？突然间，我感到自己很荒唐。</p>
<p>要点1的应用：我没说写HTML源代码很难，而且仍然同意它很容易。我在论述完全不相关的另一个观点——我们所说的容易，是指讲文章标记上各种标签很容易，而不是阅读和排版容易。</p>
<p>要点2的应用：即使你仍然坚持认为，用HTML源代码写文章很容易，但难道这样不是很繁琐吗？要写”AT&amp;amp;T”而不是直接写”AT&amp;T”，这难道不无聊吗？（更别提要把URL的&amp;符号进行编码。）</p>
<p>现在是2004年了！你的电脑难道不应该有能力判断你的文章哪里是分段，哪里是副标题吗？</p>
<p>别跟我说Movable Type的“转换分段符”功能可以实现这个效果。2.661版MT的“转换分段符”功能将会把输入的如下两行：</p>
<pre>&lt;h2&gt;This is a header.&lt;/h2&gt;
This is a paragraph</pre>
<p>转换成：</p>
<pre>&lt;p&gt;&lt;h2&gt;This is a header.&lt;/h2&gt;
&lt;/p&gt;&lt;p&gt;This is a paragraph.&lt;/p&gt;</pre>
<p>这效果真是够矬的。TypePad显然能做到不在块级别的HTML标签外再嵌套一层没用的&lt;p&gt;标签——因此MT3.0大概也能做到——但这仍然无法改变其所生成HTML代码的冗余和难看。</p>
<p>为什么桌面版的博客编辑器需要提供“预览”模式呢？你在发送一封电子邮件之前，不需要“预览”——您写完邮件，回过头读一下，然后编辑修改，就行了。</p>
<p>实际上，我很喜欢写电子邮件。电子邮件是我最喜欢的写作媒介。在过去的5年里，我发送的邮件超过16,000封。电子邮件的纯文本传统让我能清楚、准确地表达我自己， 中途不会有别的东西来干扰我。</p>
<p>就这样，Markdown诞生了。电子邮件风格的web写作方式。</p>
<p>其他多数的“文本-HTML”转换器都基于这样的假设，即HTML的标签很难用，所以他们用自己的标签来替代HTML的标签，结果是相较于HTML既不“更容易”也不“更可读“。而且，最终的结果是当用户遇到问题时，很难自如翻阅手册，不得不再次捡起HTML来。</p>
<p>其他的转换器都意在替换HTML，而Markdown则考虑的是别的东西。它希望能最有效解决问题，既能让人在必要的时候很容易地使用原生的HTML，也能让你在只需要写字的时候可以专心地写纯文本。</p>
<p>多数博客应用提供的一些标签快捷按钮——斜体、加粗、链接、图片、引用——并不是你一开始就需要考虑的。把插入这些标签做的这么容易，并不会让写作更容易，相反，会让你的文章结构更难被辨识。</p>
<p>但当你真的需要使用内嵌的HTML源码时——比如说，要用自定义的类属性来创建一个特殊样式的有序列表——你应该能直接就写HTML。不用做字符转义，不用特殊的模式转换标记，就直接用标签好了。Markdown让你能这样做，因为它是专门被设计为只作为HTML预处理器的。</p>
<p>（如果你确实需要将Markdown格式的文档转换为非HTML格式，只需要先将它转换为HTML，然后使用现有的HTML转换器就行了。）</p>
<p>尽管我确实认为HTML很简单，但在一个特定领域它真的很棘手（如果不能说难的话）：用HTML标记语言来写关于HTML标记的东西实在很让人头痛。当你写<em>有关</em>代码的东西的时候，你应该只用关注示例代码本身——而不是每一个涉及&lt;, &amp;, &amp;lt; 以及&amp;amp;符号的转义问题。</p>
<p>除了让在文档中添加内嵌HTML变得容易之外，Markdown也让在代码段中添加<em>示例</em> HTML标签变得容易了。</p>
<div style="text-align:center">
<dl style="width:296px">
<dt><a href="http://blog.jobbole.com/wp-content/uploads/2012/08/dive-into-markdown.jpeg" rel="lightbox[25449]" title="深入Markdown"><img title="深入Markdown" src="http://blog.jobbole.com/wp-content/uploads/2012/08/dive-into-markdown.jpeg" alt="" width="286" height="176"></a></dt>
<dd>深入Markdown</dd>
</dl>
</div>
<p> </p>
<p><strong>感觉 vs 思考</strong></p>
<p>纯文本在印刷上的局限性——单一的字体，单一的字号，没有斜体和加粗——跟打字机的局限性非常类似。设想有个不错的人给你买了个礼物：一个用原始打字机打印出来的一本经典小说的手稿，就比如说是Fitzgerald的“了不起的盖茨比”吧。你可以坐下来静静读这本手稿，从头到尾，这样获得的阅读体验，与你阅读一本精心排版和包装后的书的体验几乎是一样的。没错，那种感觉贯穿在打字机充满油污的、等宽Courier式的字体中，以及用下划线代替斜体的习惯，等等。——但文字依旧流畅，从纸面飞跃到你的心中，就像Fitzgerald所期望的那样。</p>
<p>我在文章开头引用的Stanley Kubrick的名言是我最喜欢的话之一。当你读或者写用HTML标签标记的文字时，这些标记在强迫你集中注意力去思考他们。而我希望Markdown格式的文本所传达的是一种感觉。</p>
<p> </p>
<p>英文原文：<a href="http://daringfireball.net/2004/03/dive_into_markdown">dive into markdown</a>   编译：张重骐（<a href="http://weibo.com/n/candyhorse">@candyhorse</a>）</p>
<p> </p>
<h2>相关文章</h2><ul><li><a href="http://blog.jobbole.com/21741/" title="Linux中最受欢迎的邮件传输代理（MTA）">Linux中最受欢迎的邮件传输代理（MTA）</a></li><li><a href="http://blog.jobbole.com/22158/" title="Internet大事记，1969-1974">Internet大事记，1969-1974</a></li><li><a href="http://blog.jobbole.com/22060/" title="为什么到今天还要坚持写博客">为什么到今天还要坚持写博客</a></li><li><a href="http://blog.jobbole.com/18251/" title="开发者拒绝写技术博客的常见理由">开发者拒绝写技术博客的常见理由</a></li><li><a href="http://blog.jobbole.com/8934/" title="设计师需磨练口头和写作技能的5个理由">设计师需磨练口头和写作技能的5个理由</a></li><li><a href="http://blog.jobbole.com/8079/" title="2011年美国25大最具价值博客 ">2011年美国25大最具价值博客 </a></li><li><a href="http://blog.jobbole.com/1504/" title="40年前的10月发出了第一封电子邮件">40年前的10月发出了第一封电子邮件</a></li><li><a href="http://blog.jobbole.com/1074/" title="创业公司的博客经营之道">创业公司的博客经营之道</a></li><li><a href="http://blog.jobbole.com/1051/" title="WordPress与Tumblr：传统博客与轻博客之争">WordPress与Tumblr：传统博客与轻博客之争</a></li><li><a href="http://blog.jobbole.com/973/" title="40岁的Email，生日快乐！">40岁的Email，生日快乐！</a></li></ul>