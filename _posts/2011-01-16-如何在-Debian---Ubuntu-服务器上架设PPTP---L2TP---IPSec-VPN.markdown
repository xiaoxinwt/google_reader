---
layout: post
title:  "如何在 Debian / Ubuntu 服务器上架设PPTP / L2TP / IPSec VPN"
date:   2011-01-16 10:29:42
author: iGFW
categories: program
---

## 如何在 Debian / Ubuntu 服务器上架设PPTP / L2TP / IPSec VPN
### by iGFW
### at 2011-01-16 10:29:42
### original <http://igfw.tk/archives/1759>

<p><strong><big>第一部分：如何用 Debian / Ubuntu 服务器上架设 PPTP VPN服务器</big></strong></p>
<p>如果你有一台linux主机，通过<a href="http://www.chiark.greenend.org.uk/%7Esgtatham/putty/download.html">putty.exe</a> 或苹果电脑的teminal用SSH登录到主机后要用文本编辑器，若是上传下载编辑好的配置文件就太麻烦了。 我先简单介绍一下VI编辑器的用法，学会VI了就不且上传下载编辑好的配置文件了：</p>
<blockquote><p><span style="color:#009900">vi /etc/ilovezola.conf</span> 这是进入编辑ilovezola.conf的模式，然后hjkl四个键是方向键，分别是左下上右，按字母x可删除一个字符。按a进入插入模式，hjkl就 不能当方向键用了。退出插入模式按ESC键，保存文件输入命令冒号加字母:w  退出输入命令:q 退出并保存就是 :wq</p></blockquote>
<p><span></span>有了这些基础就可以安装PPTP VPN服务器了。下面<span style="color:#009900">绿色的字就</span>是可以复制到命令行里执行的，<span style="color:#3333ff">蓝色的字是需要你对比或复制到文件中的</span>，懒人办事效率高，复制吧，不会错的。<br>
先登录你的主机：<br>
<span style="color:#33cc00">ssh root@fuckgfw.zuola.com</span><br>
<span style="color:#009900">vi /etc/apt/sources.list</span><br>
然后删除所有deb前面的＃号 ，你按照<a href="http://blog.cuoluo.net/2009/12/install-pptp-vpn-in-linode-vps/">这里</a>操作就行，我直接引用，这不算抄袭：</p>
<blockquote><p>接着用下面的命令更新你的软件包数据库<br>
<span style="color:#009900">apt-get update</span><br>
现在我们可以正式安装VPN server了。这里我们选择pptp(vpn 协议的一种),因为简单，一条命令搞定。剩下的无非是一些配置。<br>
<span style="color:#006600">apt-get install pptpd</span><br>
pptpd安装完成后，编辑这个文件，<br>
<span style="color:#009900">vi /etc/pptpd.conf</span><br>
去掉下面两行的注释或者直接添加这两行(在文件的最后).这一步是配置ip地址的范围。<br>
<span style="color:#3333ff">localip 192.168.0.1<br>
remoteip 192.168.0.234-238,192.168.0.245</span><br>
然后在编辑这个文件，<br>
<span style="color:#009900">vi /etc/ppp/chap-secrets</span><br>
在这个文件中添加vpn用户，按照下面的格式,每个用户一行。<br>
<span style="color:#3333ff">username pptpd password *<br>
fuck * gfw *<br>
zhoushuguang pptpd fuckgfw *</span><br>
为了让你的用户连上VPN后能够正常地解析域名，我们需要手动设置DNS. <span style="color:#009900">vi /etc/ppp/options</span>，找到ms-dns这一项，设置你的DNS.这里我推荐的是Google 最近发布的Public DNS,原因是因为好记。<br>
<span style="color:#3333ff">ms-dns 8.8.8.8<br>
ms-dns 8.8.4.4</span><br>
vi /etc/sysctl.conf文件，找到”net.ipv4.ip_forward=1″这一行，去掉前面的注释。<br>
<span style="color:#3333ff">net.ipv4.ip_forward=1</span><br>
运行下面的命令让配置生效。<br>
<span style="color:#009900">sysctl -p</span><br>
重启pptpd服务<br>
<span style="color:#009900">/etc/init.d/pptpd restart</span><br>
最后开启iptables转发<br>
<span style="color:#009900">/sbin/iptables -t nat -A POSTROUTING -s 192.168.0.0/24 -o eth0 -j MASQUERADE</span><br>
ok,安装完毕。</p></blockquote>
<p>附：使用PPTP VPN的方法：</p>
<ol>
<li>如何在 Windows XP 中配置虚拟专用网络 (VPN) 连接 <a href="http://support.microsoft.com/kb/314076/zh-cn">http://support.microsoft.com/kb/314076/zh-cn</a></li>
<li>How to configure a connection to a virtual private network (VPN) in Windows XP <a href="http://support.microsoft.com/kb/314076/en">http://support.microsoft.com/kb/314076/en</a></li>
<li>iOS：设置 VPN <a href="http://support.apple.com/kb/HT1424?viewlocale=zh_CN">http://support.apple.com/kb/HT1424?viewlocale=zh_CN</a></li>
<li>iOS: Setting up VPN <a href="http://support.apple.com/kb/HT1424?viewlocale=en_US">http://support.apple.com/kb/HT1424?viewlocale=en_US</a></li>
<li>Mac OS X 10.6 Help 建立一个到虚拟专用网络的连接<a href="http://docs.info.apple.com/article.html?path=Mac/10.6/zh/9010.html">http://docs.info.apple.com/article.html?path=Mac/10.6/zh/9010.html</a></li>
<li>设定高级 VPN 选项 <a href="http://docs.info.apple.com/article.html?path=Mac/10.6/zh/11941.html">http://docs.info.apple.com/article.html?path=Mac/10.6/zh/11941.html</a></li>
<li>Ubuntu下使用VPN <a href="http://wiki.ubuntu.org.cn/%C3%A5%C2%A6%C2%82%C3%A4%C2%BD%C2%95%C3%A4%C2%BD%C2%BF%C3%A7%C2%94%C2%A8UbuntuVPN">http://wiki.ubuntu.org.cn/如何使用UbuntuVPN</a></li>
<li>Installing personalVPN-PPTP on Android Mobile Phone <a href="http://wiki.witopia.net/wiki/Installing_personalVPN-PPTP_on_Android_Mobile_Phone">http://wiki.witopia.net/wiki/Installing_personalVPN-PPTP_on_Android_Mobile_Phone</a></li>
</ol>
<p><big><strong>第二部分：如何用 Debian / Ubuntu 服务器安装成L2TP / IPSec VPN 服务器</strong></big></p>
<p>Ubuntu 10.04 LTS（Lucid）自带的OpenSwan有NAT的BUG。10.10  openswan无此问题（但是10.10的xl2tpd有问题,不分配IP,需要手动指定,解决方案见下文,本文都是指使用Ubuntu 10.04  LTS）可以从OpenSWAN下载最新版本（2.6.24+）才可以。也可以使用非官方PPA源<br>
<span style="color:#009900">sudo apt-get install python-software-properties<br>
sudo add-apt-repository ppa:openswan/ppa<br>
sudo apt-get update</span><br>
然后查查看是否是已经跟更新到最新<br>
<span style="color:#009900">apt-cache policy openswan</span><br>
<span style="color:#3366ff">openswan:<br>
Installed: 1:2.6.23+dfsg-1ubuntu1<br>
Candidate: 1:2.6.31-1xelerance1<br>
Version table:<br>
1:2.6.31-1xelerance1 0<br>
500 http://ppa.launchpad.net/openswan/ppa/ubuntu/ lucid/main Packages<br>
*** 1:2.6.23+dfsg-1ubuntu1 0<br>
500 http://us.archive.ubuntu.com/ubuntu/ lucid/universe Packages<br>
100 /var/lib/dpkg/status</span><br>
可见源里面已经获得2.6.31+的版本<br>
<span style="color:#009900">apt-get install openswan</span>就可以可以开始配置了<br>
安装的时候一律回车即可<br>
<span style="color:#009900">vi /etc/ipsec.conf</span>，改成这样：<br>
<span style="color:#3333ff">version 2.0<br>
config setup<br>
nat_traversal=yes<br>
virtual_private=%v4:10.0.0.0/8,%v4:192.168.0.0/16,%v4:172.16.0.0/12<br>
oe=off<br>
protostack=netkey</span></p>
<p><span style="color:#3333ff">conn L2TP-PSK-NAT<br>
rightsubnet=vhost:%priv<br>
also=L2TP-PSK-noNAT</span></p>
<p><span style="color:#3333ff">conn L2TP-PSK-noNAT<br>
authby=secret<br>
pfs=no<br>
auto=add<br>
keyingtries=3<br>
rekey=no<br>
ikelifetime=8h<br>
keylife=1h<br>
type=transport<br>
left=<span style="color:#ff0000">YOUR.SERVER.IP.ADDRESS</span><br>
leftprotoport=17/1701<br>
right=%any<br>
rightprotoport=17/%any</span><br>
<span style="color:#009900">vi /etc/ipsec.secrets</span>，改成这样：<br>
<span style="color:#3333ff"><span style="color:#ff0000">YOUR.SERVER.IP.ADDRESS</span> %any:  PSK “zhimakaimen”</span><br>
（别忘了把红色的「<span style="color:#ff0000">YOUR.SERVER.IP.ADDRESS</span>」这部分换成你的服务器的 IP 地址，把「zhimakaimen」部分换成随便一个字串，例如你喜欢的一句话如 ilovemoney，等等。）</p>
<p>运行以下命令,这下有点复杂了，前面的知识不够用了，继续补课，先<br>
<span style="color:#009900">vi run.sh</span><br>
然后复制下面的内容到run.sh并保存<br>
<span style="color:#3333ff">for each in /proc/sys/net/ipv4/conf/*<br>
do<br>
echo 0 &gt; $each/accept_redirects<br>
echo 0 &gt; $each/send_redirects<br>
done</span><br>
然后<br>
<span style="color:#009900">./run.sh</span> 这样就执行上面的循环命令了。<br>
检查一下 IPSec 能否正常工作：<br>
<span style="color:#009900">sudo ipsec verify</span><br>
如果在结果中看到「Opportunistic Encryption Support」被禁用（disable）了，没关系，其他项 OK 即可。</p>
<p>重启 openswan:<br>
<span style="color:#009900">sudo /etc/init.d/ipsec restart</span><br>
安装 L2TP。常用的 L2TP 后台软件包是 xl2tpd，它和 openswan 是同一帮人写的。<br>
运行以下命令：<br>
<span style="color:#009900">sudo aptitude install xl2tpd</span><br>
<span style="color:#009900">vi /etc/xl2tpd/xl2tpd.conf</span>，改成下面这样,或复制进去就好了<br>
<span style="color:#3333ff">[global]<br>
ipsec saref = yes</span></p>
<p><span style="color:#3333ff">[lns default]<br>
ip range = 10.1.2.2-10.1.2.255<br>
local ip = 10.1.2.1<br>
;require chap = yes<br>
refuse chap = yes<br>
refuse pap = yes<br>
require authentication = yes<br>
ppp debug = yes<br>
pppoptfile = /etc/ppp/options.xl2tpd<br>
length bit = yes</span><br>
这里要注意的是 ip range 一项里的 IP 地址不能和你正在用的 IP 地址重合，也不可与网络上的其他 IP 地址冲突。你照抄就不会错了。</p>
<p>安装 ppp。这是用来管理 VPN 用户的。<br>
<span style="color:#009900">sudo aptitude install ppp</span><br>
检查一下 /etc/ppp 目录里有没有 options.xl2tpd 这个文件，没有的话就建一个<br>
<span style="color:#009900">vi </span><span style="color:#009900">/etc/ppp/options.xl2tpd</span><br>
文件内容如下,把下面的内容复制进来 ：<br>
<span style="color:#3333ff">require-mschap-v2<br>
ms-dns 208.67.222.222<br>
ms-dns 208.67.220.220<br>
asyncmap 0<br>
auth<br>
crtscts<br>
lock<br>
hide-password<br>
modem<br>
debug<br>
name l2tpd<br>
proxyarp<br>
lcp-echo-interval 30<br>
lcp-echo-failure 4</span><br>
注意 ms-dns 两行我填的是 OpenDNS。如果你想用其他的 DNS 服务器（例如谷歌的公共 DNS  8.8.8.8   8.8.4.4  ），请自行更换。<br>
现在可以添加一个 VPN 用户了。用文字编辑器打开chap-secrets 文件<br>
<span style="color:#009900">vi /etc/ppp/chap-secrets</span><br>
<span style="color:#3333ff"># user      server      password            ip<br>
test        l2tpd       testpassword        *<br>
</span><span style="color:#3333ff">fuck * gfw *</span><br>
如果你之前设置过 PPTP VPN，chap-secrets 文件里可能已经有了其他用户的列表，如上面那个密码为<span style="color:#3333ff">gfw</span>的<span style="color:#3333ff">fuck</span>用户，就既可登录PPTP VPN,也可以登录l2tp的VPN，因为他的server没有指定为l2tp，而是用星号代替了。你只要把    test l2tpd testpassword * 这样加到后面即可。记得要有空格。</p>
<p>重启 xl2tpd<br>
<span style="color:#009900">sudo /etc/init.d/xl2tpd restart</span><br>
设置 iptables 的数据包转发，下面是两行命令哦：<br>
<span style="color:#009900">iptables –table nat –append POSTROUTING –jump MASQUERADE</span><br>
<span style="color:#009900">echo 1 &gt; /proc/sys/net/ipv4/ip_forward</span><br>
因为某种原因，openswan 在服务器重启后无法正常自动，所以我们可以在 /etc/rc.local 文件里写入如下语句：<br>
<span style="color:#009900">vi /etc/rc.local</span><br>
<span style="color:#3333ff">iptables –table nat –append POSTROUTING –jump MASQUERADE<br>
echo 1 &gt; /proc/sys/net/ipv4/ip_forward<br>
for each in /proc/sys/net/ipv4/conf/*<br>
do<br>
echo 0 &gt; $each/accept_redirects<br>
echo 0 &gt; $each/send_redirects<br>
done<br>
/etc/init.d/ipsec restart</span><br>
到这里，设置工作已经基本完成。你可以用 iPhone 或 iPad 试着连一下。记得在「Secret」中填入你在上述第三步里填的 <span style="color:#3333ff">zhimakaimen</span>。</p>
<p>如果连接成功，上网也没问题的话，恭喜你，大功告成。我就是这么在linode的Ubuntu 10.04 LTS上搞定VPN的，我之前走了些弯路是因为<span style="color:#3366ff">openswan</span>没安装到最新版，Apple4.us的文章提供的安装方法失败，还好有<a href="http://ihipop.info/2010/12/1906.html">另一篇文章</a>提供了安装<span style="color:#3366ff">openswan</span>到最新版的方法。所以合并经验，让其他人少走弯路，可以节约学习的时间哦。</p>
<p>L2TP的VPN的使用方法：</p>
<ol>
<li>苹果电脑上使用l2tp VPN <a href="http://support.apple.com/kb/HT1288?viewlocale=zh_CN">http://support.apple.com/kb/HT1288?viewlocale=zh_CN</a></li>
</ol>
<p>我参考了以下文献，综合了一下，写出以上修正版，由于 Apple4.us 保留一切权利，所以我等他们通知我删除这篇派生的文章吧，因为他们仍然保留让我派生的权利，也保留不让我基于他们的文章派生作品的权利。</p>
<ol>
<li>在Linode VPS上安装pptp VPN<a href="http://blog.cuoluo.net/2009/12/install-pptp-vpn-in-linode-vps/">
<p>http://blog.cuoluo.net/2009/12/install-pptp-vpn-in-linode-vps/</p></a></li>
<li>如何在 Debian / Ubuntu 服务器上架设 L2TP / IPSec VPN<a href="https://apple4.us/2010/05/setting-up-l2tp-vpn-on-debian-ubuntu.html">
<p>https://apple4.us/2010/05/setting-up-l2tp-vpn-on-debian-ubuntu.html</p></a></li>
<li>L2TP Over IPSec On Ubuntu(Debian,CentOS) With OpenSwan<a href="http://ihipop.info/wap/index-wap2.php?p=1906"><br>
</a><a href="http://ihipop.info/2010/12/1906.html">http://ihipop.info/2010/12/1906.html</a></li>
</ol>
<p>来源：<a href="https://www.zuola.com/">周曙光的网络日志 </a> 原文：<a href="https://www.zuola.com/weblog/?p=1658">如何在 Debian / Ubuntu 服务器上架设PPTP / L2TP / IPSec VPN</a></p>