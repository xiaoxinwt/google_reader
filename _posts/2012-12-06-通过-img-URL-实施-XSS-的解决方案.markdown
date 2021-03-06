---
layout: post
title:  "通过 img URL 实施 XSS 的解决方案"
date:   2012-12-06 11:47:00
author: sofish
categories: program
---

## 通过 img URL 实施 XSS 的解决方案
### by sofish
### at 2012-12-06 11:47:00
### original <http://sofish.de/2138>

<p>今天在整理一些 js，9月份的时候给百姓网支持了 markdown，但 UGC 的自由意味着我们要做一些保持措施，比如防 XSS 攻击。翻到了9月初写的一篇邮件。当时的背景是，当天上了 markdown 支持，晚上觉得似乎不太对，结果 23 点后开始研究 XSS 攻击。其中有一个难点，如何防止通过外部图片链接进行 XSS。我的解决方案在邮件原文做了简单的介绍：</p>
<p>通过 XSS 的方法好多啊，特别是 image 的方式，而我们今天发布的 viewad markdown 支持是可以使用 img.src 进行外链的，因此需要考虑这样的问题，今晚 11 点多回来研究了一下，主要可以归类为以下几种：</p>
<p><span></span></p>
<ol>
<li><code>src=&quot;javascript:alert(1)&quot;</code></li>
<li><code>src=&quot;jav ascript:alert(2)&quot;</code></li>
<li><code>src=&quot;java&amp;#x0script:alert(3)&quot;</code></li>
<li><code>src=&quot;&amp;#000 …….&quot;</code></li>
<li><code>src=&quot;上面4中的变种“</code></li>
<li><code>src=&quot;外部执行脚本链接&quot;</code></li>
</ol>
<p>Google 了很久，没有现成所工具，而且对于第6种的解法都很浪费资源，只能自己通过 <a href="http://sofish.de/">http://ha.ckers.org/xss.html</a> 上的总结，分析得出了上面几种类型，在 Markdown parser 中进行 xss 类型检测支持。还真是多亏了有 ha.ckers 的总结，这个函数可以写得非常简单：</p>
<pre>
function imageXSS($img){
    return preg_match(&#39;/(?:javascript|jav\s+ascript|\&amp;#\d+|\&amp;#x)/i&#39;, $img);
}
</pre>
<p>而解除第6种XSS方法，判断外部资源是最麻烦的。</p>
<p>Google 得来的结论：总的来说是通过 <code>get_headers</code> 和 <code>stream_get_meta_data</code> 等取到 <code>content-Type</code> 的方式来做，需要我们的服务器进行请求，并且需要分析来源，再根据 <code>content-Type</code> 决定，这样做会有一些问题：后端渲染的数据要等 <code>header</code> 取过来，即加载图片后（可能有多个并且可能非常大），会阻碍起来导致渲染非常慢不缓存且每个图片都请求，浪费服务器资源。即使是一个 flickr 上 500px 的图，在我 20M 的网络下加载也要 49 ms。可能一不小心就会导致服务器挂了（如果图片多和访问的人多的话，这个是不缓存图片 url 的）。</p>
<p>搞了很久，最终想到 preload image 的方式，根据测试看来，在浏览器中只有加载的内容是真正的 image 才会触发图片的 <code>onload</code> 事件，那么其实我们可以利用 <code>onload</code> 来解决这样的问题。这样我们可以把请求分布给每一个用户，而不需要我们任何一点资源，并且这种方式还会进行并行加载，甚至更提升了 viewad 的速度。大概需要做下面几步：</p>
<p>默认情况下不加载 <code>img</code> 的 <code>src</code>，而是设置 <code>data-xssimg=&quot;图片地址&quot;</code>，检测图片的 <code>onload</code> 事件，如果没有触发onload 则不显示，不过当 src 为空的时候，可能在一些浏览器会影响网站的渲染速度，所以在 <code>error</code> 触发的时候引用了一个永久缓存的图片：<code>http://static.baixing.net/images/nopic_small.png</code>。</p>
<p>而这些代码看起来如下（已经变成一个 jQuery 组件），目前这段代码已经放在 github 上，你可以在这里查看：<a href="https://github.com/sofish/imageXSS.js">imagesXSS.js</a>：</p>
<pre>
~function ($) {
    $.fn.imageXSS = function () {
        this.each(function () {
            var that = $(this),
                url = that.data('mdimg'),
                img = document.createElement('img');

            $(img).on('load', function () {
                that.attr('src', url);
            })

            $(img).on('error', function () {
                that.attr('src', 'http://static.baixing.net/images/nopic_small.png');
            })

            img.src = url;
        })
    }
    $('[data-mdimg]').imageXSS();
}(jQuery);
</pre>
<p>目前也只能想到这个点上。基本上测试过的都没有问题。线上目前已经支持这个版本。周一回去再提交一个版本。</p>