---
layout: post
title:  "Memcached二三事儿"
date:   2012-12-30 19:43:10
author: 老王
categories: program
---

## Memcached二三事儿
### by 老王
### at 2012-12-30 19:43:10
### original <http://www.udpwork.com/item/8946.html>

<p><a href="http://memcached.org/">Memcached</a>绝对称得上是NoSQL老兵！可惜随着时间的推移，Redis等后起之秀羽翼渐丰，Memcached相比之下已呈颓势。那我们还用不用学习它？答案是肯定的！毕竟仍然有很多项目依赖着它，如果忽视它，一旦出了问题就只有干瞪眼的份儿了。</p>
<p>网络上关于Memcached的资料可以说是浩如烟海，其中不乏一些精彩之作，比如说由爱好者翻译的「<a href="http://tech.idv2.com/2008/08/17/memcached-pdf/">Memcached全面剖析</a>」系列文章，在中文社区广为流传，虽然已经是几年前的文章了，但是即便现在读起来，依然感觉收获良多，推荐大家多看几遍：</p>
<ol><li><a href="http://tech.idv2.com/2008/07/10/memcached-001/">Memcached的基础</a></li>
<li><a href="http://tech.idv2.com/2008/07/11/memcached-002/">理解Memcached的内存存储</a></li>
<li><a href="http://tech.idv2.com/2008/07/16/memcached-003/">Memcached的删除机制和发展方向</a></li>
<li><a href="http://tech.idv2.com/2008/07/24/memcached-004/">Memcached的分布式算法</a></li>
<li><a href="http://tech.idv2.com/2008/07/31/memcached-005/">Memcached的应用和兼容程序</a></li>
</ol>
<p>当然，<a href="http://code.google.com/p/memcached/wiki/NewStart">官方Wiki</a>永远是最权威的资料，即便是里面的<a href="http://code.google.com/p/memcached/wiki/ReleaseNotes">ReleaseNotes</a>也不要放过。</p>
<p>实际应用Memcached时，我们遇到的很多问题都是因为不了解其内存分配机制所致，下面就让我们以此为开端来开始Memcached之旅吧！</p>
<p>为了规避内存碎片问题，Memcached采用了名为SlabAllocator的内存分配机制。内存以Page为单位来分配，每个Page分给一个特定长度的Slab来使用，每个Slab包含若干个特定长度的Chunk。实际保存数据时，会根据数据的大小选择一个最贴切的Slab，并把数据保存在对应的Chunk中。如果某个Slab没有剩余的Chunk了，系统便会给这个Slab分配一个新的Page以供使用，如果没有Page可用，系统就会触发<a href="http://en.wikipedia.org/wiki/LRU">LRU</a>机制，通过删除冷数据来为新数据腾出空间，这里有一点需要注意的是：LRU不是全局的，而是针对Slab而言的。</p>
<p>一个Slab可以有多个Page，这就好比在古代一个男人可以娶多个女人；一旦一个Page被分给某个Slab后，它便对Slab至死不渝，犹如古代那些贞洁的女人。但是女人的数量毕竟是有限的，所以一旦一些男人娶得多了，必然另一些男人就只剩下咽口水的份儿，这在很大程度上增加了社会的不稳定因素，于是乎我们要解放女性。</p>
<p>好在Memcached已经意识到解放女性的重要性，<a href="http://code.google.com/p/memcached/wiki/ReleaseNotes1411">新版本</a>中Page可以调配给其它的Slab：</p>
<pre>shell&gt; memcached -o slab_reassign,slab_automove</pre><p>换句话说：女人可以改嫁了！这方面，其实Memcached的儿子<a href="https://github.com/twitter/twemcache">Twemcache</a>革命得更彻底，他甚至写了一篇大字报，以事实为依据，痛斥老子的无能，有兴趣的可以继续阅读：<a href="https://github.com/twitter/twemcache/wiki/Random-Eviciton-vs-Slab-Automove">Random Eviciton vs Slab Automove</a>。</p>
<p>了解Memcached内存使用情况的最佳工具是：<a href="https://github.com/memcached/memcached/blob/master/scripts/memcached-tool">Memcached-tool</a>。如果我们发现某个Slab的Evicted不为零，则说明这个Slab已经出现了LRU的情况，这通常是个危险的信号，但也不能一概而论，需要结合Evict_Time来做进一步判断。</p>
<p>…</p>
<p>在Memcached的使用过程中，除了会遇到内存分配机制相关的问题，还有很多稀奇古怪的问题等着你呢，下面我选出几个有代表性的问题来逐一说明：</p>
<h2>Cache失效后的拥堵问题</h2>
<p>通常我们会为两种数据做Cache，一种是热数据，也就是说短时间内有很多人访问的数据；另一种是高成本的数据，也就说查询很很耗时的数据。当这些数据过期的瞬间，如果大量请求同时到达，那么它们会一起请求后端重建Cache，造成拥堵问题，就好象在北京上班做地铁似的，英文称之为：<a href="http://code.google.com/p/memcached/wiki/NewProgrammingTricks#Avoiding_stampeding_herd">stampeding herd</a>，老外这里的用词还是很形象的。</p>
<p>一般有如下几种解决思路可供选择：</p>
<p>首先，我们可以主动更新Cache。前端程序里不涉及重建Cache的职责，所有相关逻辑都由后端独立的程序（比如CRON脚本）来完成，但此方法并不适应所有的需求。</p>
<p>其次，我们可以通过加锁来解决问题。以PHP为例，伪代码大致如下：</p>
<pre>&lt;?php

function query()
{
    $data = $cache-&gt;get($key);

    if ($cache-&gt;getResultCode() == Memcached::RES_NOTFOUND) {
        if ($cache-&gt;add($lockKey, $lockData, $lockExpiration)) {
            $data = $db-&gt;query();
            $cache-&gt;add($key, $data, $expiration);
            $cache-&gt;delete($lockKey);
        } else {
            sleep($interval);
            $data = query();
        }
    }

    return $data;
}

?&gt;</pre><p>不过这里有一个问题，代码里用到了sleep，也就是说客户端会卡住一段时间，就拿PHP来说吧，即便这段时间非常短暂，也有可能堵塞所有的FPM进程，从而使服务中断。于是又有人想出了柔性过期的解决方案，所谓柔性过期，指的是设置一个相对较长的过期时间，或者干脆不再直接设置数据的过期时间，取而代之的是把真正的过期时间嵌入到数据中去，查询时再判断，如果数据过期就加锁重建，如果加锁失败，不再sleep，而是直接返回旧数据，以PHP为例，伪代码大致如下：</p>
<pre>&lt;?php

function query()
{
    $data = $cache-&gt;get($key);

    if (isset($data[&#39;expiration&#39;]) &amp;&amp; $data[&#39;expiration&#39;] &lt; $now) {
        if ($cache-&gt;add($lockKey, $lockData, $lockExpiration)) {
            $data = $db-&gt;query();
            $data[&#39;expiration&#39;] = $expiration;
            $cache-&gt;add($key, $data);
            $cache-&gt;delete($lockKey);
        }
    }

    return $data;
}

?&gt;</pre><p>问题到这里似乎已经圆满解决了，且慢！还有一些特殊情况没有考虑到：设想一下服务重启；或者某个Cache里原本没有的冷数据因为某些情况突然转换成热数据；又或者由于LRU机制导致某些键被意外删除，等等，这些情况都可能会让上面的方法失效，因为在这些情况里就不存在所谓的旧数据，等待用户的将是一个空页面。</p>
<p>好在我们还有<a href="http://gearman.org/">Gearman</a>这根救命稻草。当需要更新Cache的时候，我们不再直接查询数据库，而是把任务抛给Gearman来处理，当并发量比较大的时候，Gearman内部的优化可以保证相同的请求只查询一次后端数据库，以PHP为例，伪代码大致如下：</p>
<pre>&lt;?php

function query()
{
    $data = $cache-&gt;get($key);

    if ($cache-&gt;getResultCode() == Memcached::RES_NOTFOUND) {
        $data = $gearman-&gt;do($function, $workload, $unique);
        $cache-&gt;add($key, $data, $expiration);
    }

    return $data;
}

?&gt;</pre><p>说明：如果多个并发请求的$unique参数一样，那么实际上Gearman只会请求一次。</p>
<h2>Multiget的无底洞问题</h2>
<p>Facebook在Memcached的实际应用中，发现了<a href="http://highscalability.com/blog/2009/10/26/facebooks-memcached-multiget-hole-more-machines-more-capacit.html">Multiget无底洞</a>问题，具体表现为：出于效率的考虑，很多Memcached应用都已Multiget操作为主，随着访问量的增加，系统负载捉襟见肘，遇到此类问题，直觉通常都是通过增加服务器来提升系统性能，但是在实际操作中却发现问题并不简单，新加的服务器好像被扔到了无底洞里一样毫无效果。</p>
<p>为什么会这样？让我们来模拟一下案发经过，看看到底发生了什么：</p>
<p>我们使用Multiget一次性获取100个键对应的数据，系统最初只有一台Memcached服务器，随着访问量的增加，系统负载捉襟见肘，于是我们又增加了一台Memcached服务器，数据散列到两台服务器上，开始那100个键在两台服务器上各有50个，问题就在这里：原本只要访问一台服务器就能获取的数据，现在要访问两台服务器才能获取，服务器加的越多，需要访问的服务器就越多，所以问题不会改善，甚至还会恶化。</p>
<p>不过，作为被告方，Memcached官方开发人员对此进行了<a href="http://dormando.livejournal.com/521163.html">辩护</a>：</p>
<p>请求多台服务器并不是问题的症结，真正的原因在于客户端在请求多台服务器时是并行的还是串行的！问题是很多客户端，包括<a href="http://www.libmemcached.org/libMemcached.html">Libmemcached</a>在内，在处理Multiget多服务器请求时，使用的是串行的方式！也就是说，先请求一台服务器，然后等待响应结果，接着请求另一台，结果导致客户端操作时间累加，请求堆积，性能下降。</p>
<p>如何解决这个棘手的问题呢？只要保证Multiget中的键只出现在一台服务器上即可！比如说用户名字（user:foo:name），用户年龄（user:foo:age）等数据在散列到多台服务器上时，不应按照完整的键名（user:foo:name和user:foo:age）来散列的，而应按照特殊的键（foo）来散列的，这样就保证了相关的键只出现在一台服务器上。以PHP的 Memcached客户端为例，有<a href="http://www.php.net/manual/en/memcached.getmultibykey.php">getMultiByKey</a>和<a href="http://www.php.net/manual/en/memcached.setmultibykey.php">setMultiByKey</a>可供使用。</p>
<h2>Nagle和DelayedAcknowledgment的延迟问题</h2>
<p>老实说，这个问题和Memcached没有半毛钱关系，任何网络应用都有可能会碰到这个问题，但是鉴于很多人在写Memcached程序的时候会遇到这个<a href="http://www.searchtb.com/2012/09/libmemcached_and_memcached_max_buffer.html">问题</a>，所以还是拿出来聊一聊，在这之前我们先来看看<a href="http://en.wikipedia.org/wiki/Nagle&#39;s_algorithm">Nagle</a>和<a href="http://en.wikipedia.org/wiki/TCP_delayed_acknowledgment">DelayedAcknowledgment</a>的含义：</p>
<p>先看看Nagle：</p>
<p>假如需要频繁的发送一些小包数据，比如说1个字节，以IPv4为例的话，则每个包都要附带40字节的头，也就是说，总计41个字节的数据里，其中只有1个字节是我们需要的数据。为了解决这个问题，出现了Nagle算法。它规定：如果包的大小满足<a href="http://en.wikipedia.org/wiki/Maximum_segment_size">MSS</a>，那么可以立即发送，否则数据会被放到缓冲区，等到已经发送的包被确认了之后才能继续发送。通过这样的规定，可以降低网络里小包的数量，从而提升网络性能。</p>
<p>再看看DelayedAcknowledgment：</p>
<p>假如需要确认每一个包的话，那么网络中将会充斥着数不胜数的ACK，从而降低了网络性能。为了解决这个问题，DelayedAcknowledgment规定：不再针对单个包发送ACK，而是一次确认两个包，或者在发送响应数据的同时捎带着发送ACK，又或者触发超时时间后再发送ACK。通过这样的规定，可以降低网络里ACK的数量，从而提升网络性能。</p>
<p>Nagle和DelayedAcknowledgment虽然都是好心，但是它们在一起的时候却会办坏事。下面我们举例说说Nagle和DelayedAcknowledgment是如何产生延迟问题的：</p>
<p>客户端需要向服务端传输数据，传输前数据被分为ABCD四个包，其中ABC三个包的大小都是MSS，而D的大小则小于MSS，交互过程如下：</p>
<p>首先，因为客户端的ABC三个包的大小都是MSS，所以它们可以耗无障碍的发送，服务端由于DelayedAcknowledgment的存在，会把AB两个包放在一起来发送ACK，但是却不会单独为C包发送ACK。</p>
<p>接着，因为客户端的D包小于MSS，并且C包尚未被确认，所以D包不会立即发送，而被放到缓冲区里延迟发送。</p>
<p>最后，服务端触发了超时阈值，终于为C包发送了ACK，因为不存在未被确认的包了，所以即便D包小于MSS，也总算熬出头了，可以发送了，服务端在收到了所有的包之后就可以发送响应数据了。</p>
<p>说到这里，假如你认为自己已经理解了这个问题的来龙去脉，那么我们尝试改变一下前提条件：传输前数据被分为ABCDE五个包，其中ABCD四个包的大小都是MSS，而E的大小则小于MSS。换句话说，满足MSS的完整包的个数是偶数个，而不是前面所说的奇数个，此时又会出现什么情况呢？答案我就不说了，留给大家自己思考。</p>
<p>知道了问题的原委，解决起来就简单了：我们只要设置socket选项为TCP_NODELAY即可，这样就可以禁用Nagle，如此一来，少了一个巴掌，问题自然就拍不响了。</p>
<p>如果大家意犹未尽，可以继续浏览：<a href="http://www.stuartcheshire.org/papers/NagleDelayedAck/">TCP Performance problems caused by interaction between Nagle’s Algorithm and Delayed ACK</a>。</p>
<p>…</p>
<p>希望本文能让大家在使用Memcached的过程中少走一些弯路。相对于Memcached，其实我更喜欢Redis，从功能上看，Redis可以说是Memcached的超集，不过Memcached自有它存在的价值，即便已呈颓势，但是：老兵永远不死，只是慢慢凋零。</p>

			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/8946.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/8946.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">IT 牛人博客聚合网站(udpwork.com)</a> 聚合
					|
					<a href="http://www.udpwork.com/item/8946.html#reviews">评论: 0</a>
					|
					<a href="http://book.benegg.com/tag/%E7%BC%96%E7%A8%8B?from=udpwork-feed">10000+ 本编程/Linux PDF/CHM 电子书下载</a>
				</p>
			</div>