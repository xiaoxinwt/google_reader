---
layout: post
title:  "Nginx走HttpProxy连JBoss在上传文件时的一个bug,及其解决"
date:   2011-09-10 17:34:27
author: jm
categories: program
---

## Nginx走HttpProxy连JBoss在上传文件时的一个bug,及其解决
### by jm
### at 2011-09-10 17:34:27
### original <http://rdc.taobao.com/team/jm/archives/1124>

<div>
<p>这是帮其他团队的同学解决的一个问题.这里做个记录.</p>
<p>问题描述:</p>
<ul>
<li>Nginx作为前端HttpServer</li>
<li>使用HttpProxy与JBoss进行连接</li>
<li>在上传比较大的文件时,JBoss已经返回结果,但是Nginx不立即将结果返回给浏览器,直到Nginx的HttpProxy与JBoss因为超时而断开后才返回结果给浏览器</li>
<li>这样导致的结果就是用户觉得上传文件需要很长时间.</li>
</ul>
<div>问题原因分析:</div>
<div>
<ul>
<li>Nginx HttpProxy转发的请求是Http 1.0的,就是说是不支持Keep-Alive的,那么也就是说只有当JBoss主动断开与HttpProxy的连接,或是超时被动断开,Nginx才认为这个请求已经完结了.</li>
<li>在看一下业务代码,因为在上传处理逻辑中会先进行一些前置判断,一旦判断失败,会不读取上传的文件然后直接返回一个Failed的Response回去,而这个时候就会出现上述的问题.</li>
<li>用Wireshark抓包分析HttpProxy与JBoss之间的通信发现,当JBoss一返回Response之后Nginx就会立马停止向JBoss Post数据,但是JBoss这个时候却不会断开与Nginx的连接.</li>
<li>再分析一下JBoss(即Tomcat)的对于endRequest的源码:</li>
<ul>
<li>在当Tomcat处理完servlet后在org.apache.coyote.http11.Http11AprProcessor#endRequest的过程当中org.apache.coyote.http11.filters.IdentityInputFilter#end 会去读完所有的Content-Length这个长度的请求后才会Close连接,但是这个时候Nginx已经停止Post了,那么org.apache.coyote.http11.filters.IdentityInputFilter#end 取不到要取的数据就只有等待soTimeout(即配置的connectionTimeout)之后才超时断开连接,这个时候Nginx才会返回response给真正的Client</li>
</ul>
<li>原因就是因为Nginx的处理和JBoss的处理不兼容引起的</li>
<ul>
<li>Nginx认为JBoss已经返回Response了,就没有必要再需要继续Post数据了</li>
<li>JBoss比较死板,就算你在Servlet中没有全读完整个Request,也会在Servlet之后的org.apache.coyote.http11.Http11AprProcessor#endRequest里读完整个Content-Length长度的请求</li>
</ul>
</ul>
<div>问题解决:</div>
<div>
<ul>
<li>需要在Servlet里面读完整个Request,或用一个Filter来读完也可以</li>
</ul>
</div>
</div></div>