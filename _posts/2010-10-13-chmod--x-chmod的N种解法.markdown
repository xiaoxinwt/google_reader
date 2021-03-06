---
layout: post
title:  "chmod -x chmod的N种解法"
date:   2010-10-13 08:42:37
author: 酷壳 - CoolShell.cn
categories: program
---

## chmod -x chmod的N种解法
### by 酷壳 - CoolShell.cn
### at 2010-10-13 08:42:37
### original <http://coolshell.cn/articles/3136.html>

<a href="http://sr.ju690.cn/meme/item/86103">玩聚SR还知道：</a>
<blockquote>
<div style="background-color:#e1ecfe!important;width:auto">
	<div>还有<a href="http://sr.ju690.cn/people/EFanZh@GoogleReader?i=15520" title="EFanZh通过GoogleReader分享的其他文章"><img src="http://sr.ju690.cn/static/clogo/4.png?v=690" border="0/"> EFanZh</a>, <a href="http://sr.ju690.cn/people/cpy@GoogleReader?i=15438" title="cpy通过GoogleReader分享的其他文章"><img src="http://sr.ju690.cn/static/clogo/4.png?v=690" border="0/"> cpy</a>, <a href="http://sr.ju690.cn/people/Noe@GoogleReader?i=14796" title="Noe通过GoogleReader分享的其他文章"><img src="http://sr.ju690.cn/static/clogo/4.png?v=690" border="0/"> Noe</a>, 推荐，<a href="http://sr.ju690.cn/meme/item/86103"><b>查看全部 <big style="color:#ff7b7a;font-family:&#39;Georgia&#39;;font-size:16px">25</big> 个推荐 </b></a>
	</div>
</div></blockquote>

<div style="margin:0;padding:10px 0"><a href="http://sr.ju690.cn/author/%E9%85%B7%E5%A3%B3%20-%20CoolShell.cn"><b>酷壳 - CoolShell.cn</b></a>发表于2010-10-13 08:42:37</div>
<div style="margin:0;padding:5px 0"><p>在SlidesShare.net上有这么<a href="http://www.slideshare.net/cog/chmod-x-chmod">一个幻灯片</a>，其说了如下的一个面试题：</p>
<blockquote><p>如果某天你的Unix/Linux系统上的chomd命令被某人去掉了x属性（执行属行），<br>
那么，你如何恢复呢？</p></blockquote>
<p>下面是一些答案：</p>
<p><strong>1）重新安装</strong>。对于Debian的系统：</p>
<pre>sudo apt-get install --reinstall coreutils</pre>
<p><strong>2）使用语言级的chmod</strong>。</p>
<ul>
<li>Perl：perl-e ‘chmod 0755, “/bin/chmod”‘</li>
<li>Python：python -c “import os;os.chmod(‘/bin/chmod’, 0755)”</li>
<li>Node.js：require(“fs”).chmodSync(“/bin/chmod”, 0755);</li>
<li>C程序：</li>
</ul>
<pre>#include &lt;sys/types.h&gt;
#include&lt;sys/stat.h&gt;
void main()
{
chmod(&quot;/bin/chmod&quot;, 0000755);
}</pre>
<p><strong>3）使用已有的可执行文件。</strong></p>
<pre>
$cat - &gt; chmod.c
void main(){}
^D

$cc chmod.c
$cat /bin/chmod &gt; a.out
$./a.out 0755 /bin/chmod
</pre>
<pre>
$cp true &gt; new_chmod
$cat /bin/chmod &gt; new_chmod
$./new_chmod 0755 /bin/chmod
</pre>
<p><strong>4）使用GNU tar命令</strong></p>
<pre>$tar --mode 0755 -cf chmod.tar /bin/chmod
$tar xvf chmod.tar</pre>
<pre>tar --mode 755 -cvf - chmod | tar -xvf -</pre>
<p><strong>5）使用cpio</strong> （第19到24字节为file mode – <a href="http://4bxf.sl.pt">http://4bxf.sl.pt</a>）</p>
<pre>
echo chmod |
cpio -o |
perl -pe 's/^(.{21}).../${1}755/' |
cpio -i -u</pre>
<p><strong>6）使用hardcore</strong></p>
<pre>alias chmod='/lib/ld-2.11.1.so ./chmod'</pre>
<p><strong>7）使用Emacs</strong></p>
<blockquote><p>Ctrl+x b &gt; * scratch*<br>
(set-file-modes “/bin/chmod” (string-to-number “0755″ 8))<br>
Ctrl+j</p></blockquote>
<p>嗯，挺强大的，不过为什么不用install命令呢？</p>
<pre>install -m 755 /bin/chmod /tmp/chmod
mv /tmp/chmod /bin/chmod</pre>
<p>各位，你的方法呢？</p>
<p>（全文完）</p>
<h3>相关文章</h3><ul><li>2010年10月12日 -- <a href="http://coolshell.cn/articles/3125.html" title="主流文本编辑器学习曲线">主流文本编辑器学习曲线</a></li><li>2010年03月31日 -- <a href="http://coolshell.cn/articles/2271.html" title="Emacs配色在线生成器">Emacs配色在线生成器</a></li><li>2009年10月30日 -- <a href="http://coolshell.cn/articles/1640.html" title="文件备份的几个简单命令">文件备份的几个简单命令</a></li></ul></div>


<div style="padding-top:20px;border-top:1px solid #cbd9d9;padding-bottom:10px;FONT-SIZE:13px">
<strong>推荐相关的热文: </strong><br> 
	<div><img src="https://www.google.com/s2/favicons?domain=developers.solidot.org" border="0/"> <a title="22个所见即所得在线Web编辑器 | 22个推荐" href="http://sr.ju690.cn/meme/item/60237">22个所见即所得在线Web编辑器 | 22荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=twitter.com" border="0/"> <a title="rtmeme: RT @andy_jeet RT @virushuo: 我发现，对于只会骂妈逼那个人，只要你用和他一样的语言回骂，他立刻就跟你谈文明和素质，这得精神多分裂啊。这事情发生好几次了，被他骂过的可以试试看…灵啊。 | 5个推荐" href="http://sr.ju690.cn/meme/item/84297">rtmeme: RT @andy_jeet RT @virushuo: 我发现，.. | 5荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=www.gtdstudy.com" border="0/"> <a title="Emacs 普通人的编辑利器 | 20个推荐" href="http://sr.ju690.cn/meme/item/84940">Emacs 普通人的编辑利器 | 20荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=wowubuntu.com" border="0/"> <a title="最牛B的 Linux Shell 命令 系列连载（四） | 28个推荐" href="http://sr.ju690.cn/meme/item/79380">最牛B的 Linux Shell 命令 系列连载（四） | 28荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=li2z.cn" border="0/"> <a title="记录你的终端操作 ── script | 6个推荐" href="http://sr.ju690.cn/meme/item/53734">记录你的终端操作 ── script | 6荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=twitter.com" border="0/"> <a title="rtmeme: RT @wadeker RT @luoyonghao 致所有国内微博网站：不管你们删帖删的多狠，也不管你们是为了什么，今天你们扛住了没做“系统维护”就是臭牛逼。敬礼。 | 5个推荐" href="http://sr.ju690.cn/meme/item/85517">rtmeme: RT @wadeker RT @luoyonghao 致所有国内.. | 5荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=linuxtoy.org" border="0/"> <a title="SSH 使用技巧一则: 创建快捷方式 | 12个推荐" href="http://sr.ju690.cn/meme/item/20676">SSH 使用技巧一则: 创建快捷方式 | 12荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=cocre.com" border="0/"> <a title="《Vim Recipes》免费的Vim Cookbook | 7个推荐" href="http://sr.ju690.cn/meme/item/28659">《Vim Recipes》免费的Vim Cookbook | 7荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=coolshell.cn" border="0/"> <a title="一些非常有意思的杂项资源 | 49个推荐" href="http://sr.ju690.cn/meme/item/84239">一些非常有意思的杂项资源 | 49荐</a>
	</div> 
	<div><img src="https://www.google.com/s2/favicons?domain=initiative.yo2.cn" border="0/"> <a title="有图有真相：各种IDE/编辑器的学习曲线 | 4个推荐" href="http://sr.ju690.cn/meme/item/6052">有图有真相：各种IDE/编辑器的学习曲线 | 4荐</a>
	</div>
</div>
<div style="float:left;margin:2px 10px 2px 2px">
<a href="http://sr.ju690.cn" title="玩聚SR"><img src="http://sr.ju690.cn/static/images/fish.jpg" border="0" width="80px"></a>
</div>
<div><a href="http://sr.ju690.cn/">The readings, filtered by social network!</a><br>阅读<a href="http://sr.ju690.cn/tag/tech">科技</a>频道热文。手机请登录<a href="http://sr.ju690.cn/m">移动版</a>。</div>