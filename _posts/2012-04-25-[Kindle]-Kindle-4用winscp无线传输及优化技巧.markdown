---
layout: post
title:  "[Kindle] Kindle 4用winscp无线传输及优化技巧"
date:   2012-04-25 20:52:01
author: ibuffalo
categories: program
---

## [Kindle] Kindle 4用winscp无线传输及优化技巧
### by ibuffalo
### at 2012-04-25 20:52:01
### original <http://www.v2ex.com/t/33572#reply0>

在豆瓣kindle小组发布过，但是没人看。也不想发到多看论坛去，那里帖子太乱。<br><br>一、Kindle 4实现wifi传输。（略） <br>具体参考这篇文章：《Kindle 4 通过Wifi进行SSH登录》 <br><a href="http://miaoo.in/kindle4-ssh-via-wifi.html">http://miaoo.in/kindle4-ssh-via-wifi.html</a> <br><br>二、使用winscp进行wifi传输。（略） <br>具体参考这篇文章第6部分：《kindle 3实现SFTP连接，摆脱USB传送文件》 <br><a href="http://www.wincold.com/archives/115.html">http://www.wincold.com/archives/115.html</a> <br><br>三、优化winscp的wifi传输。 <br>问题有三个：如何解决中文乱码、如何实现文件拷入后kindle刷新文件列表、如何简化刷新。 <br><br>1、用sftp-server解决中文乱码。 <br>到这个地址下载sftp-server： <br><a href="http://www.mobileread.com/forums/showpost.php?p=1125427&amp;postcount=1">http://www.mobileread.com/forums/showpost.php?p=1125427&amp;postcount=1</a> <br>解压，然后把sftp-server拷贝到/mnt/us目录。 <br>接下来，使用sftp登陆即可。如何登陆，具体参考这篇文章的9.3.3部分：http://bbs.duokan.com/forum/thread-59157-1-1.html <br><br>2、刷新。 <br>用wifi拷入新文件后，可用如下办法手动刷新。 <br>在/mnt/us下新建一个文件（随便起名如refresh），进行编辑，写入&quot;dbus-send --system /default com.lab126.powerd.resuming int32:1&quot;（不要输入双引号，其后加一回车），执行这个文件即可刷新文件列表。 <br><br>3、简化刷新。 <br>可利用winscp“自定义命令按钮”功能设置一个快捷按钮，名字随便起，如k4_refresh，命令填/mnt/us/refresh（或你自己在步骤2中新建立的文件名）即可。拷入文件后，点击此按钮即自动刷新。 <br><br>四、小结。 <br>以上办法是剽窃网上优化kindle 3无线传输的思路，经实践，其方案同样适用于kindle 4 non touch。 <br><br>另外，kindle 4的/mnt/us目录和/mnt/base-us目录实际是一回事。