---
layout: post
title:  "iPhone OS SDK的这些事[安装、下载、版本、实例、脱机文档等资料汇总]"
date:   2010-12-15 16:14:00
author: 2012
categories: program
---

## iPhone OS SDK的这些事[安装、下载、版本、实例、脱机文档等资料汇总]
### by 2012
### at 2010-12-15 16:14:00
### original <http://www.cnblogs.com/2018/archive/2010/12/15/1906912.html>

<p><p>第一次使用iPhone SDK是，如果不清楚版本和操作系统等的关系，会浪费很多的时间进行下载和安装。</p>
<p>以下汇总了一些常见可以尽快使用的方法和参考。</p>
<h2>SDK安装</h2>
<p>切记选择和自己的mac os系统吻合的版本，下载包都很大，国内需要很长的时间下载，如果选择错误，安装不了,如最新的ios sdk 4.2需要mac os的版本是10.6.4</p>
<p> </p>
<p>Mac OS的升级策略</p>
<p>小版本号的产品更新可以免费升级，如10.6.*系列的可以免费，直接在系统的主菜单，软件升级，即可自动下载和更新</p>
<p>但大的版本需要收费升级，如10.5升级到10.6系列，是不能直接升级的，需要交费后升级</p>
<p> </p>
<p>一个办法【手工修改系统版本号】<a href="http://jimychen.javaeye.com/blog/798487">http://jimychen.javaeye.com/blog/798487</a> </p>
<p>有些情况下不太好用</p>
<p>Root用户</p>
<p>vi /System/Library/CoreServices/SystemVersion.plist </p>
<p>修改这个文件中的ProductUserVisibleVersion  ProductVersion</p>
<p> </p>
<h2>SDK下载地址</h2>
<p><a href="http://developer.apple.com">http://developer.apple.com</a>的官方地址仅列出了最新的SDK下载地址</p>
<p> </p>
<p>旧的SDK下载地址</p>
<p><a href="http://sheng.javaeye.com/blog/764174">http://sheng.javaeye.com/blog/764174</a></p>
<p>列出2.x ~ 3.x的版本的下载列表</p>
<p> </p>
<p><a href="http://dev.enekoalonso.com/2010/08/03/iphone-sdk-old-versions/">http://dev.enekoalonso.com/2010/08/03/iphone-sdk-old-versions/</a></p>
<p>这个地址列出了一些以前的版本的下载地址，如下：</p>
<p><a href="http://developer.apple.com/iphone/download.action?path=/iphone/iphone_sdk_3.1__final/iphone_sdk_3.1_with_xcode_3.2_final__snow_leopard__10a432.dmg">iPhone SDK 3.1 with XCode 3.2.1 for Snow Leopard (10.6.0)</a> </p>
<p><a href="http://developer.apple.com/iphone/download.action?path=/iphone/iphone_sdk_3.1.3__final/iphone_sdk_3.1.3_with_xcode_3.2.1__snow_leopard__10m2003a.dmg">iPhone SDK 3.1.3 with XCode 3.2.1 for Snow Leopard (10.6.0)</a></p>
<p><a href="http://developer.apple.com/iphone/download.action?path=/iphone/iphone_sdk_3.2__final/xcode_3.2.2_and_iphone_sdk_3.2_final.dmg">iPhone SDK 3.2 Final with Xcode 3.2.2 for Snow Leopard (10.6.0)</a> </p>
<p><a href="http://developer.apple.com/iphone/download.action?path=/iphone/iphone_sdk_4_gm_seed/xcode_3.2.3_and_iphone_sdk_4_gm_seed.dmg">Xcode 3.2.3 and iPhone SDK 4 GM seed for Snow Leopard (10.6.2)</a> </p>
<p><a href="http://developer.apple.com/iphone/download.action?path=/iphone/iphone_sdk_4_gm_seed/xcode_3.2.3_and_iphone_sdk_4_gm_seed.dmg">http://developer.apple.com/iphone/download.action?path=/iphone/iphone_sdk_4_gm_seed/xcode_3.2.3_and_iphone_sdk_4_gm_seed.dmg</a></p>
<p> </p>
<p><a href="http://developer.apple.com/iphone/download.action?path=%2Fios%2Fios_sdk_4.0.1__final%2Fxcode_3.2.3_and_ios_sdk_4.0.1.dmg">Xcode 3.2.3 and iOS SDK 4.0.1 for Snow Leopard</a> </p>
<p><a href="http://developer.apple.com/iphone/download.action?path=%2Fios%2Fios_sdk_4.0.1__final%2Fxcode_3.2.3_and_ios_sdk_4.0.1.dmg">http://developer.apple.com/iphone/download.action?path=%2Fios%2Fios_sdk_4.0.1__final%2Fxcode_3.2.3_and_ios_sdk_4.0.1.dmg</a></p>
<p> </p>
<p><a href="http://developer.apple.com/iphone/download.action?path=%2Fios%2Fios_sdk_4.0.2__final%2Fxcode_3.2.3_and_ios_sdk_4.0.2.dmg">Xcode 3.2.3 and iOS SDK 4.0.2 for Snow Leopard</a></p>
<p><a href="http://developer.apple.com/iphone/download.action?path=%2Fios%2Fios_sdk_4.0.2__final%2Fxcode_3.2.3_and_ios_sdk_4.0.2.dmg">http://developer.apple.com/iphone/download.action?path=%2Fios%2Fios_sdk_4.0.2__final%2Fxcode_3.2.3_and_ios_sdk_4.0.2.dmg</a></p>
<p> </p>
<p>最近的sdk版本</p>
<p>Xcode 3.2.4 and iOS SDK 4.1</p>
<p>http://adcdownload.apple.com/ios/ios_sdk_4.1__final/xcode_3.2.4_and_ios_sdk_4.1.dmg</p>
<p>Mac OS X 10.6.2 (Snow Leopard) or later</p>
<p> </p>
<p>Xcode 3.2.5 and iOS SDK 4.2</p>
<p>https://developer.apple.com/ios/download.action?path=/ios/ios_sdk_4.2__final/xcode_3.2.5_and_ios_sdk_4.2_final.dmg</p>
<p>Mac OS X Snow Leopard version 10.6.4 or later.</p>
<p> </p>
<p>如果不发布软件到appstore或开发阶段，而且不使用sdk的最新新增功能，可以临时不用最新的sdk，否则sdk比较快速的升级是比较麻烦的</p>
<p> </p>
<h2>SDK版本选择</h2>
<p><a href="http://stackoverflow.com/questions/4264894/which-version-of-the-ios-sdk-should-i-use">http://stackoverflow.com/questions/4264894/which-version-of-the-ios-sdk-should-i-use</a></p>
<p>which version of the iOS SDK i should use</p>
<p>AppStore只审查最近两个版本的软件，因此不是我们想选择哪个SDK的问题</p>
<p> </p>
<h2>实例</h2>
<p>http://www.appsamuck.com</p>
<p>31个例子程序</p>
<p> </p>
<p>http://appsamuck.com/blog/index.php/2009/11/24/download-the-source-code-from-all-of-our-31-days-of-iphone-sdk-apps/</p>
<p>http://appsamuck.com/blog/index.php/2008/11/01/full-list-of-31-days-of-iphone-sdk-apps/ </p>
<p> </p>
<p>http://stackoverflow.com/questions/1353130/where-can-i-find-sample-iphone-code</p>
<p>iPhone的例子地址收集</p>
<p> </p>
<p>http://developer.apple.com/library/ios/navigation/#section=Resource%20Types&amp;topic=Sample%20Code</p>
<p> </p>
<p>Apple的官方实例代码地址，http://www.devdiv.net/bbs/thread-19294-1-1.html 这儿有具体的一些解释</p>
<p><a href="http://sxsoul.blog.163.com/blog/static/42849160201010114330802/">http://sxsoul.blog.163.com/blog/static/42849160201010114330802/</a></p>
<p>官方例子的下载地址 </p>
<p> </p>
<p>http://groupaware.mobi/iphone/index.html#_Samples</p>
<p>一些界面元素的显示效果</p>
<h2>文档脱机查看</h2>
<p>Xcode中的文档查看时是连接apple的站点进行查看，速度比较慢，因此最好是把文档下载下来查看更方便一些。</p>
<p>官方的更新超慢，参考<a href="http://www.cocoachina.com/iphonedev/sdk/2010/1102/2281.html">http://www.cocoachina.com/iphonedev/sdk/2010/1102/2281.html</a> 这个的方法下载</p>
<p>具体为：</p>
<p>不同版本的API reference and conceptual documentation for iOS</p>
<p><a href="http://developer.apple.com/rss/com.apple.adc.documentation.AppleiPhone3_1.atom">http://developer.apple.com/rss/com.apple.adc.documentation.AppleiPhone3_1.atom</a></p>
<p><a href="http://developer.apple.com/rss/com.apple.adc.documentation.AppleiPhone4_0.atom">http://developer.apple.com/rss/com.apple.adc.documentation.AppleiPhone4_0.atom</a></p>
<p><a href="http://developer.apple.com/rss/com.apple.adc.documentation.AppleiPhone4_2.atom">http://developer.apple.com/rss/com.apple.adc.documentation.AppleiPhone4_2.atom</a></p>
<p> </p>
<p> </p>
<p>Mac OS X 10.6 Core Library / Mac OS X Java Library / Mac OS X Legacy Library</p>
<p><a href="http://developer.apple.com/rss/com.apple.adc.documentation.ApplesnowLeopard.atom">http://developer.apple.com/rss/com.apple.adc.documentation.ApplesnowLeopard.atom</a></p>
<p> </p>
<p>分析以上的源内容，选择需要的文档用迅雷下载，</p>
<p>然后使用如下的命令解压到Xcode的目录下，即可在Xcode中查看</p>
<p>sudo xar -xf 下载的文件名.xar </p>
<p>将其解压，然后使用:</p>
<p>sudo chown -R -P devdocs 解压后的文件名.docset </p>
<p>将文档的所有者改为devdocs。打开Xcode就可以离线浏览了。</p>
<p> </p>
<p> </p><img src="http://www.cnblogs.com/2018/aggbug/1906912.html?type=1" width="1" height="1" alt=""><p>作者: <a href="http://www.cnblogs.com/2018/">2012</a> 发表于 2010-12-15 16:14 <a href="http://www.cnblogs.com/2018/archive/2010/12/15/1906912.html">原文链接</a></p><p>评论: 0　<a href="http://www.cnblogs.com/2018/archive/2010/12/15/1906912.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/2018/archive/2010/12/15/1906912.html#commentform">发表评论</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/84751/">在线比价搜索引擎Shop.com出售 盖茨曾投资</a><span style="color:gray">(2010-12-16 08:54)</span><br>· <a href="http://news.cnblogs.com/n/84749/">邓元鋆离职背后：诺基亚中国腹背受敌</a><span style="color:gray">(2010-12-16 08:53)</span><br>· <a href="http://news.cnblogs.com/n/84748/">央行：超级网银收费将降低</a><span style="color:gray">(2010-12-16 08:52)</span><br>· <a href="http://news.cnblogs.com/n/84747/">Android和iPhone平台2010年度最佳软件和游戏榜单出炉</a><span style="color:gray">(2010-12-16 08:50)</span><br>· <a href="http://news.cnblogs.com/n/84746/">京东遭遇出版社集体逼宫 今日恢复原价改返券</a><span style="color:gray">(2010-12-16 08:48)</span><br></p><p>编辑推荐：<a href="http://news.cnblogs.com/n/84722/">Google首席Java架构师访谈：API对设计流程的影响</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">我的园子</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://kb.cnblogs.com">知识库</a></p></p>