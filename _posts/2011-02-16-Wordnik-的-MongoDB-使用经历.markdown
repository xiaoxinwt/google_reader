---
layout: post
title:  "Wordnik 的 MongoDB 使用经历"
date:   2011-02-16 10:31:37
author: nosqlfan
categories: program
---

## Wordnik 的 MongoDB 使用经历
### by nosqlfan
### at 2011-02-16 10:31:37
### original <http://blog.nosqlfan.com/html/1139.html>

<p><a href="http://www.wordnik.com/">Wordnik</a> 是一个在线辞典和语言库，他提供Web页面并开放有相应的API接口，在他们的官方博客中，记录了他们的一些技术实践及相应的效果，他们后端存储经历了从 MySQL 到 MongoDB 的转变，下面是相关的文章：</p>
<h3><a rel="bookmark" href="http://blog.wordnik.com/what-has-technology-done-for-words-lately">What has technology done for words lately?</a> 最近的技术改进</h3>
<ul>
<li><strong>在某些方面仅保证最终一致性</strong></li>
<li><strong>使用文档型存储-这里指的是 MongoDB</strong></li>
<li><strong> </strong></li>
</ul>
<h3><a rel="bookmark" href="http://blog.wordnik.com/12-months-with-mongodb">12 Months with MongoDB</a> 使用 MongoDB 12个月的经历</h3>
<p>主要讲述了 MongoDB 在Wordnik 系统中的高性能</p>
<ul>
<li>选择将存储迁移到 MongoDB 的原因是性能，MySQL 的性能已经不能满足应用需求。</li>
<li>MongoDB 平均每小时处理50w请求，高峰时期可能达到200w/h.</li>
<li>超过 120亿的文档存储在MongoDB中，每个Mongo 结点存储大约3TB的数据</li>
<li>MongoDB 可轻松应对每秒 8k 条的灵气插入，峰值可达5w 条/s</li>
<li>一个java客户端对MongoDB进行读操作，可以在千兆网卡上达到10MB/s的吞吐，四个客户端大概能达到40MB/s</li>
<li>相比MySQL 条种操作都有了很大的性能提高:
<ul>
<li>数据获取操作的时间从400ms 降低到 60ms</li>
<li>dictionary entries 从 20ms 降低到 1ms</li>
<li>document metadata 从 30ms 降低到 0.1ms</li>
<li>拼写的suggest提示响应时间 从 10ms 降低到 1.2ms</li>
</ul>
</li>
<li>MongoDB 内部的 Cache 系统使我们不再使用memcached层，这导致响应速度提高了大概1-2ms</li>
</ul>
<h3>下面是一个Wordnik的同学做的一个PPT，讲述了Wordnik从MySQL 迁移到 MongoDB 的一些心得：</h3>
<div style="width:600px"><strong><a title="Migrating from MySQL to MongoDB at Wordnik" href="http://www.slideshare.net/fehguy/migrating-from-mysql-to-mongodb-at-wordnik">Migrating from MySQL to MongoDB at Wordnik</a></strong><iframe src="http://reader.googleusercontent.com/reader/embediframe?src=http://static.slidesharecdn.com/swf/ssplayer2.swf?doc%3Dwordnikmongosf-100430175142-phpapp02%26stripped_title%3Dmigrating-from-mysql-to-mongodb-at-wordnik%26userName%3Dfehguy&amp;width=600&amp;height=500" width="600" height="500"></iframe>
<div style="padding:5px 0 12px">View more <a href="http://www.slideshare.net/">presentations</a> from <a href="http://www.slideshare.net/fehguy">fehguy</a>.</div>
</div>
<h3>下面这个PPT是Wordnik的MongoDB运维经验</h3>
<div style="width:600px"><strong><a title="Keeping the Lights On with MongoDB" href="http://www.slideshare.net/fehguy/mongo-sv-tony-tam">Keeping the Lights On with MongoDB</a></strong><iframe src="http://reader.googleusercontent.com/reader/embediframe?src=http://static.slidesharecdn.com/swf/ssplayer2.swf?doc%3Dmongosvtonytam-101203104529-phpapp02%26stripped_title%3Dmongo-sv-tony-tam%26userName%3Dfehguy&amp;width=600&amp;height=500" width="600" height="500"></iframe>
<div style="padding:5px 0 12px">View more <a href="http://www.slideshare.net/">presentations</a> from <a href="http://www.slideshare.net/fehguy">fehguy</a>.</div>
</div>
<h3><a href="http://blog.wordnik.com/mongoutils">MongoDB Admin Tools</a></h3>
<p>Wordnik使用的MongoDB管理工具</p>
<h3><a href="http://www.cloudera.com/blog/2011/02/wordnik-bypasses-processing-bottleneck-with-hadoop/">Wordnik Bypasses Processing Bottleneck with Hadoop</a></h3>
<p>这是 cloudera 关于 Wordnik 使用 Hadoop 解决性能问题的文章</p>
<p>同时，infoq上赵姐夫的一篇文章也不错：<a href="http://www.infoq.com/cn/news/2010/11/wordnik-mongodb">Wordnik的MongoDB使用经验</a></p>

<span><a href="http://addthis.org.cn/share/" title="收藏-分享"><img src="http://blog.nosqlfan.com/wp-content/plugins/addthischina/a1.gif" alt="分享家:Addthis中国"></a></span>
	<br><br><h4>Related posts</h4>
	<ul>
	<li><a href="http://blog.nosqlfan.com/html/1158.html" title="夜说mongodb (2011年02月17日)">夜说mongodb</a> </li>
	<li><a href="http://blog.nosqlfan.com/html/332.html" title="NoSQL数据库笔谈v2 (2010年07月24日)">NoSQL数据库笔谈v2</a> </li>
	<li><a href="http://blog.nosqlfan.com/html/1155.html" title="视觉中国的NoSQL之路：从MySQL到MongoDB (2011年02月17日)">视觉中国的NoSQL之路：从MySQL到MongoDB</a> </li>
	<li><a href="http://blog.nosqlfan.com/html/219.html" title="简单的MongoDB (2010年05月20日)">简单的MongoDB</a> </li>
	<li><a href="http://blog.nosqlfan.com/html/1022.html" title="监控你的MongoDB（MongoSV 精华摘录） (2010年12月30日)">监控你的MongoDB（MongoSV 精华摘录）</a> </li>
</ul><img src="http://www1.feedsky.com/t1/477642324/nosqlfan/feedsky/s.gif?r=http://blog.nosqlfan.com/html/1139.html" border="0" height="0" width="0"><p><a href="http://www1.feedsky.com/r/l/feedsky/nosqlfan/477642324/art01.html"><img border="0" ismap src="http://www1.feedsky.com/r/i/feedsky/nosqlfan/477642324/art01.gif"></a></p>