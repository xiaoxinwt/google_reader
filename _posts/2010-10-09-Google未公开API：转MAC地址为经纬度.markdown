---
layout: post
title:  "Google未公开API：转MAC地址为经纬度"
date:   2010-10-09 15:28:13
author: 陈皓
categories: program
---

## Google未公开API：转MAC地址为经纬度
### by 陈皓
### at 2010-10-09 15:28:13
### original <http://coolshell.cn/articles/3089.html>

<p>这里有一个POC（Proof of Concept）可以通过你Web浏览器后面的路由器XSS攻击得到一个准确的GPS坐标。注意：路由器和Web浏览器以及IP地址并不包含任和地理信息。其方法是使用了一个Google未公开的API。大约方法如下：</p>
<ol>
<li>访问一个网页，这个网页隐藏了一个基于你WiFi路由器的XSS（ 参见： <a href="http://samy.pl/vzwfios/">XSS  Verizon FiOS router</a>）</li>
<li>通过这个XSS 可以获得路由器的MAC 地址。</li>
<li>然后通过 Google Location Services我们可以把这个MAC地址映射到GPS坐标。Googel的这个服务是基于HTTP的服务。这并不是一个Google正式发布的API，而是通过 <a href="http://www.mozilla.com/en-US/firefox/geolocation/">Firefox’s Location-Aware Browsing</a> 发现的。</li>
</ol>
<p>演示地点在这里：<a href="http://samy.pl/mapxss/">http://samy.pl/mapxss/</a></p>
<p>我试了一下，无论无线和有线都可以准确定位我的位置。很强大，你也试试看。</p>
<h3>相关文章</h3><ul><li>2010年10月12日 -- <a href="http://coolshell.cn/articles/3156.html" title="Go语言的”Issue 9″ Closed!">Go语言的”Issue 9″ Closed!</a></li><li>2010年07月13日 -- <a href="http://coolshell.cn/articles/2608.html" title="Google App Inventor ">Google App Inventor </a></li><li>2010年05月22日 -- <a href="http://coolshell.cn/articles/2466.html" title="Google吃豆游戏Logo的源码">Google吃豆游戏Logo的源码</a></li><li>2010年04月22日 -- <a href="http://coolshell.cn/articles/2367.html" title="谷歌Chrome取消”http://”">谷歌Chrome取消”http://”</a></li><li>2010年03月14日 -- <a href="http://coolshell.cn/articles/2179.html" title="中国仍然是一个很穷的国家">中国仍然是一个很穷的国家</a></li><li>2010年02月07日 -- <a href="http://coolshell.cn/articles/2091.html" title="分享：我是如何使用Google Reader的">分享：我是如何使用Google Reader的</a></li><li>2009年12月28日 -- <a href="http://coolshell.cn/articles/2015.html" title="google的免费dns服务器">google的免费dns服务器</a></li></ul>