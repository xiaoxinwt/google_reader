---
layout: post
title:  "Hash Collision DoS 问题"
date:   2012-01-06 08:36:05
author: 陈皓
categories: program
---

## Hash Collision DoS 问题
### by 陈皓
### at 2012-01-06 08:36:05
### original <http://coolshell.cn/articles/6424.html>

<div style="clear:both;margin-top:5px;margin-bottom:5px"></div><p>最近，除了国内明文密码的安全事件，还有一个事是比较大的，那就是 Hash Collision DoS （Hash碰撞的拒绝式服务攻击），有恶意的人会通过这个安全弱点会让你的服务器运行巨慢无比。<strong>这个安全弱点利用了各语言的Hash算法的“非随机性”可以制造出N多的value不一样，但是key一样数据，然后让你的Hash表成为一张单向链表，而导致你的整个网站或是程序的运行性能以级数下降（可以很轻松的让你的CPU升到100%）</strong>。目前，这个问题出现于<a href="http://www.java.com/">Java</a>, <a href="http://jruby.org/">JRuby</a>, <a href="http://www.php.net/">PHP</a>, <a href="http://python.org/">Python</a>, <a href="http://rubini.us/">Rubinius</a>, <a href="http://www.ruby-lang.org/">Ruby</a>这些语言中，主要：</p>
<ul>
<li><a href="http://www.java.com">Java</a>, 所有版本</li>
<li><a href="http://jruby.org/">JRuby</a> &lt;= 1.6.5 （目前fix在 1.6.5.1）</li>
<li><a href="http://www.php.net/">PHP</a> &lt;= 5.3.8, &lt;= 5.4.0RC3 （目前fix在 5.3.9,  5.4.0RC4）</li>
<li><a href="http://python.org/">Python</a>, all versions</li>
<li><a href="http://rubini.us/">Rubinius</a>, all versions</li>
<li><a href="http://www.ruby-lang.org/">Ruby</a> &lt;= 1.8.7-p356 （目前fix在 1.8.7-p357, 1.9.x）</li>
<li><a href="http://geronimo.apache.org/">Apache Geronimo</a>, 所有版本</li>
<li><a href="http://tomcat.apache.org/">Apache Tomcat</a> &lt;= 5.5.34, &lt;= 6.0.34, &lt;= 7.0.22 （目前fix在 5.5.35,  6.0.35,  7.0.23）</li>
<li><a href="http://glassfish.java.net/">Oracle Glassfish</a> &lt;= 3.1.1 （目前fix在mainline）</li>
<li><a href="http://www.eclipse.org/jetty/">Jetty</a>, 所有版本</li>
<li><a href="http://plone.org/">Plone</a>, 所有版本</li>
<li><a href="http://rack.rubyforge.org/">Rack</a> &lt;= 1.3.5, &lt;= 1.2.4, &lt;= 1.1.2 （目前fix 在 1.4.0, 1.3.6, 1.2.5, 1.1.3）</li>
<li><a href="http://code.google.com/p/v8/">V8 JavaScript Engine</a>, 所有版本</li>
<li>ASP.NET 没有打MS11-100补丁</li>
</ul>
<p>注意，Perl没有这个问题，因为Perl在N年前就fix了这个问题了。关于这个列表的更新，请参看 <a href="http://www.ocert.org/advisories/ocert-2011-003.html">oCERT的2011-003报告</a>，比较坑爹的是，这个问题早在2003 年就在论文《<a href="http://www.cs.rice.edu/~scrosby/hash/CrosbyWallach_UsenixSec2003.pdf">通过算法复杂性进行拒绝式服务攻击</a>》中被报告了，但是好像没有引起注意，尤其是Java。</p>
<h4>弱点攻击解释</h4>
<p>你可以会觉得这个问题没有什么大不了的，因为黑客是看不到hash算法的，如果你这么认为，那么你就错了，这说明对Web编程的了解还不足够底层。</p>
<p><span></span></p>
<p>无论你用JSP，PHP，Python，Ruby来写后台网页的时候，在处理HTTP POST数据的时候，你的后台程序可以很容易地以访问表单字段名来访问表单值，就像下面这段程序一样：</p>
<pre>

$usrname = $_POST['username'];
$passwd = $_POST['password'];
</pre>
<p>这是怎么实现的呢？这后面的东西就是Hash Map啊，所以，我可以给你后台提交一个有10K字段的表单，这些字段名都被我精心地设计过，他们全是Hash Collision ，于是你的Web Server或语言处理这个表单的时候，就会建造这个hash map，于是在每插入一个表单字段的时候，都会先遍历一遍你所有已插入的字段，于是你的服务器的CPU一下就100%了，你会觉得这10K没什么，那么我就发很多个的请求，你的服务器一下就不行了。</p>
<p>举个例子，你可能更容易理解：</p>
<p>如果你有n个值—— v1, v2, v3, … vn，把他们放到hash表中应该是足够散列的，这样性能才高：</p>
<blockquote><p>0 -&gt; v2<br>
1 -&gt; v4<br>
2 -&gt; v1<br>
…<br>
…<br>
n -&gt; v(x)</p></blockquote>
<p>但是，这个攻击可以让我造出N个值——  dos1, dos2, …., dosn，他们的hash key都是一样的（也就是Hash Collision），导致你的hash表成了下面这个样子：</p>
<blockquote><p>0 – &gt; dos1 -&gt; dos2 -&gt; dos3 -&gt; …. -&gt;dosn<br>
1 -&gt; null<br>
2 -&gt; null<br>
…<br>
…<br>
n -&gt; null</p></blockquote>
<p>于是，单向链接就这样出现了。这样一来，O(1)的搜索算法复杂度就成了O(n)，而插入N个数据的算法复杂度就成了O(n^2)，你想想这是什么样的性能。</p>
<p>（关于Hash表的实现，如果你忘了，那就把大学时的《数据结构》一书拿出来看看）</p>
<h4>  Hash Collision DoS 详解</h4>
<p>StackOverflow.com是个好网站， 合格的程序员都应该知道这个网站。上去一查，就看到了这个贴子“<a title="Application vulnerability due to Non Random Hash Functions" href="http://stackoverflow.com/questions/8669946/application-vulnerability-due-to-non-random-hash-functions">Application vulnerability due to Non Random Hash Functions</a>”。我把这个贴子里的东西摘一些过来。</p>
<p>首先，这些语言使用的Hash算法都是“非随机的”，如下所示，这个是Java和Oracle使用的Hash函数：</p>
<pre>static int hash(int h)
{
h ^= (h &gt;&gt;&gt; 20) ^ (h &gt;&gt;&gt; 12);
return h ^ (h &gt;&gt;&gt; 7) ^ (h &gt;&gt;&gt; 4);
}</pre>
<p>所谓“非随机的” Hash算法，就可以猜。比如：</p>
<p>1）在Java里， Aa和BB这两个字符串的hash code(或hash key) 是一样的，也就是Collision 。</p>
<p>2）于是，我们就可以通过这两个种子生成更多的拥有同一个hash key的字符串。如：”AaAa”, “AaBB”, “BBAa”, “BBBB”。这是第一次迭代。其实就是一个排列组合，写个程序就搞定了。</p>
<p>3）然后，我们可以用这4个长度的字符串，构造8个长度的字符串，如下所示：</p>
<pre style="padding-left:30px"><code>"AaAaAaAa", "AaAaBBBB", "AaAaAaBB", "AaAaBBAa", 
"BBBBAaAa", "BBBBBBBB", "BBBBAaBB", "BBBBBBAa", 
"AaBBAaAa", "AaBBBBBB", "AaBBAaBB", "AaBBBBAa", 
"BBAaAaAa", "BBAaBBBB", "BBAaAaBB", "BBAaBBAa",</code></pre>
<p><code>4）同理，我们就可以生成16个长度的，以及256个长度的字符串，总之，很容易生成N多的这样的值。</code></p>
<p>在攻击时，我只需要把这些数据做成一个HTTP POST 表单，然后写一个无限循环的程序，不停地提交这个表单。你用你的浏览器就可以了。当然，如果做得更精妙一点的话，把你的这个表单做成一个跨站脚本，然后找一些网站的跨站漏洞，放上去，于是能过SNS的力量就可以找到N多个用户来帮你从不同的IP来攻击某服务器。</p>
<p> </p>
<h4>防守</h4>
<p>要防守这样的攻击，有下面几个招：</p>
<ul>
<li>打补丁，把hash算法改了。</li>
<li>限制POST的参数个数，限制POST的请求长度。</li>
<li>最好还有防火墙检测异常的请求。</li>
</ul>
<p>不过，对于更底层的或是其它形式的攻击，可能就有点麻烦了。</p>
<p>（全文完）</p>
<div style="float:left">
<div>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a></a>
<a href="http://www.jiathis.com/share?uid=1541368"></a>
<a></a>
</div>



</div><div style="clear:both;margin-top:5px;margin-bottom:5px"></div><table cellspacing="0" cellpadding="3" border="0" style="clear:both">
    
    <tr>
        <td colspan="5"><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">您可能也喜欢：</font></b></td>
    </tr>
    
        <tr>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important">
                    <a title="酷壳 – CoolShell.cn" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2F&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6424.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2012/02/03/14687960.jpg" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">酷壳 – CoolShell.cn</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="谈谈数据安全和云存储" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6976.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6424.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2012/04/09/19058579.jpg" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">谈谈数据安全和云存储</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="JAVA的字符串拼接与性能" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F2235.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6424.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/site_images/2011/06/21/13691753.png" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">JAVA的字符串拼接与性能</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Java构造时成员初始化的陷阱" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F1106.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6424.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/images/blogWidget/wordpress_default.gif" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Java构造时成员初始化的陷阱</font>
                    </a>
                </td>
                <td width="102" valign="top" style="padding:5px!important;margin:0!important;border-left:1px solid #dddddd!important">
                    <a title="Java如何取源文件中文件名和行号" style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fcoolshell.cn%2Farticles%2F611.html&amp;from=http%3A%2F%2Fcoolshell.cn%2Farticles%2F6424.html">
                        <img style="margin:0!important;padding:2px!important;border:1px solid #dddddd!important;width:96px!important;height:96px!important" src="http://static.wumii.com/images/blogWidget/wordpress_default.gif" width="96px" height="96px"><br>
                        <font size="-1" color="#333333" style="display:block!important;line-height:15px!important;width:102px!important;font:12px/15px arial!important;height:60px!important;margin:3px 0 0 0!important;padding:0!important;overflow:hidden!important">Java如何取源文件中文件名和行号</font>
                    </a>
                </td>
        </tr>
    
    <tr>
        <td colspan="5" align="right">
            <a style="text-decoration:none!important" href="http://www.wumii.com/widget/relatedItems" title="无觅相关文章插件">
                <font size="-1" color="#bbbbbb" style="display:block!important;font-family:arial!important;padding:5px 0!important;font-size:12px!important;color:#bbb!important">无觅</font>
            </a>
        </td>
    </tr>
</table><h3>相关文章</h3><ul><li>2012年04月09日 -- <a href="http://coolshell.cn/articles/6976.html" title="谈谈数据安全和云存储">谈谈数据安全和云存储</a></li><li>2012年01月05日 -- <a href="http://coolshell.cn/articles/6335.html" title="Resin服务器getResource揭秘">Resin服务器getResource揭秘</a></li><li>2011年12月28日 -- <a href="http://coolshell.cn/articles/6112.html" title="由一个问题到 Resin ClassLoader 的学习">由一个问题到 Resin ClassLoader 的学习</a></li><li>2011年12月22日 -- <a href="http://coolshell.cn/articles/6193.html" title="CSDN明文口令泄露的启示">CSDN明文口令泄露的启示</a></li><li>2011年12月07日 -- <a href="http://coolshell.cn/articles/6043.html" title="Web开发中需要了解的东西">Web开发中需要了解的东西</a></li><li>2011年11月28日 -- <a href="http://coolshell.cn/articles/5987.html" title="如何设计“找回用户帐号”功能">如何设计“找回用户帐号”功能</a></li><li>2011年11月25日 -- <a href="http://coolshell.cn/articles/5966.html" title="腾讯帐号申诉的用户体验">腾讯帐号申诉的用户体验</a></li></ul>