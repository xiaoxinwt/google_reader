---
layout: post
title:  "cross domain request"
date:   2011-09-29 18:39:55
author: 
categories: program
---

## cross domain request
### by 
### at 2011-09-29 18:39:55
### original <http://yiminghe.iteye.com/blog/1183425>

<h1>场景</h1>
<p>跨域请求是随着应用服务化而越来越多遇到的问题，大体分为两类</p>
<p> </p>
<p>1. 子域间通信：a.t.com 要和 b.t.com 通信</p>
<p> </p>
<p>2. 完全不同域间通信：t.com 要和 m.com 通信</p>
<p> </p>
<p>1 可以看做是 2 的特例，不过解决方法更简单点.</p>
<h1>解决方案</h1>
<h2>完全不同域间通信</h2>
<p>w3c 很早就考虑到了这种需求，提出了 <a href="http://www.w3.org/TR/cors/">Cross-Origin Resource Sharing</a> 标准，通过定义一系列请求头和响应头，可以在客户端透明（或者经过很少的修改）得支持跨源的 xmlhttprequest。</p>
<p> </p>
<p>服务器通过返回响应头进行权限控制，例如</p>
<p> </p>
<p>Access-Control-Allow-Origin 控制那些外部请求可以访问该资源</p>
<p> </p>
<p>Access-Control-Allow-Credentials 结合客户端 xmlhttprequest 的 withCredentials 属性可以控制是否发送 cookie 等验证信息</p>
<p> </p>
<p>Access-Control-Allow-Headers 控制客户端可以发送的额外头部信息.</p>
<p> </p>
<p> </p>
<p>那么只要 b.t.com  的响应设置合适的头部信息，最好情况下 a.t.com 可以不经过任何修改就可以向 b.t.com 发请求.</p>
<h3>ie 的例外</h3>
<p>不出预料，ie 不完全支持此规范：</p>
<h4>ie&gt;=8</h4>
<p>有自己的一套跨域请求机制 <a href="http://msdn.microsoft.com/en-us/library/cc288060(v=vs.85).aspx">XDomainRequest</a> ，通过替换 XmlHttpRequest 为 XDomainRequest 也可以往外部域发请求，但服务器端控制就少点，只能设置</p>
<p> </p>
<p>Access-Control-Allow-Origin 控制那些外部请求可以访问该资源</p>
<p> </p>
<p>也就意味着：不能发送 cookie 信息， 不能设置额外请求头。</p>
<h4>ie&lt;8 </h4>
<p>则是完全不支持，流行的做法是<a href="http://www.xml.com/pub/a/2006/06/28/flashxmlhttprequest-proxy-to-the-rescue.html"> flash 模拟</a>，安全则交给 crossdomain.xml 控制，目前看来， ie&lt;8 且不装 flash 的确实很少，可行！凑巧的是 flash 在 ie 下携带 cookie 信息，在其他浏览器下则不携带 cookie 信息，如果一定要附带 cookie 信息，那 ie 全平台都要用 flash 方案了。</p>
<h3>另一种思路：</h3>
<p>jsonp 不多说，最简单的一种，虽然控制少点（但服务器通过 refer 仍然可以限制来源请求）以及不能 post 数据（写操作受限），也算一个不错的选择。</p>
<h2>子域间请求</h2>
<p>子域访问作为跨域访问的特例，上述方法的任意一种都可行，但由于请求双方间共享一个主域，因而存在另外一种方案：</p>
<p> </p>
<p><strong>cross sub domain xmlhttprequest using proxy page</strong> </p>
<h3>场景：</h3>
<p>a.t.com 希望发请求给 b.t.com 的资源地址，但 b.t.com 的资源实际上只能通过 b.t.com 下的请求才能访问，而我们知道通过设置</p>
<p> </p>
<pre name="code">document.domain = "t.com" ;</pre>
<p> </p>
<p>那么 a.t.com 就可以操作 b.t.com 的文档以及 window 对象。</p>
<p> </p>
<p>具体做法为 引入 b.t.com/proxy.htm，内容为</p>
<p> </p>
<pre name="code">&lt;script&gt;
    document.domain=&quot;t.com&quot;
&lt;/script&gt;</pre>
<p> </p>
<p>将其作为 iframe 嵌入到欲发请求的 a.t.com 页面，a.t.com 通过操纵 b.t.com/prxoy.html 自身的 xmlhttprequest 向 b.t.com 发请求，这样子就可以绕过 a.t.com 自己的 xmlhttprequest 同源限制了.</p>
<h3>问题</h3>
<p>domain 设置是<a href="http://yiminghe.iteye.com/blog/812181">不可逆的</a>，一旦主页面设置了 domain，那么其包含的iframe除非设置和主页面相同的 domain，否则就不能再和主页面通信，会导致大量的已有代码修改。</p>
<p> </p>
<p>推荐：一开始进行全局总体设计时就规定所有子域页面统统设置 domain 为主域（全局脚本统一设置？）。</p>
<h1>All In One</h1>
<p>通过统一的接口，实际上可以做出透明的请求发送客户端，调用者不需要考虑以上细节，只要指定请求地址，通过工厂模式系统自动生成合适的客户端进行处理.</p>
<p> </p>
<p> </p>
<p><img src="http://dl.iteye.com/upload/attachment/562691/18ca3d1e-af67-3d5e-a8d5-2627c193ebc3.jpg" alt="" width="400" height="620"></p>
<p> </p>
<p> </p>
<p> </p>
              
              <br><br>
              <span style="color:red">
                <a href="http://yiminghe.iteye.com/blog/1183425#comments" style="color:red">已有 <strong>0</strong> 人发表留言，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
              </span>
              <br><br><br>
<span style="color:#e28822">ITeye推荐</span>
<br>
<ul><li><a href="http://yiminghe.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>