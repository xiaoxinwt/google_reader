---
layout: post
title:  "Riak对比HBase"
date:   2012-07-08 18:14:48
author: nosqlfan
categories: program
---

## Riak对比HBase
### by nosqlfan
### at 2012-07-08 18:14:48
### original <http://item.feedsky.com/~feedsky/nosqlfan/~8149226/706989117/6253001/1/item.html>

<p>文章来自<span><a href="http://blog.nosqlfan.com/tags/riak" title="查看 Riak 的全部文章">Riak</a></span>官方wiki，是一篇Riak与HBase的对比文章。Riak官方的对比通常都做得很中肯，并不刻意偏向自家产品。本文也是一样。</p>
<p>对比的Riak版本是1.1.x，HBase是0.94.x。</p>
<h4>大方面对比</h4>
<ul>
<li>Riak 与 HBase 都是基于 Apache 2.0 licensed 发布</li>
<li>Riak 的实现是基于 Amazon 的 Dynamo 论文，HBase 是基于 Google 的 BigTable</li>
<li>Riak 主要用 Erlang 写成，包括一部分的C，而 HBase 是用 Java 写的。</li>
</ul>
<h4>功能性对比</h4>
<table border="1" style="text-align:left">
<tbody>
<tr>
<th width="15%" style="text-align:center">功能点</th>
<th width="42%" style="text-align:center">Riak</th>
<th width="43%" style="text-align:center">HBase</th>
</tr>
<tr>
<td>数据模型</td>
<td>Riak 通过bucket作为命名空间，存储 Key-Value 信息
<ul>
<li><a href="http://wiki.basho.com/Concepts.html#Buckets%2C-Keys%2C-and-Values">Buckets, Keys, and Values</a></li>
</ul>
</td>
<td>HBase 按预先定义好的 column family 结构来存储数据（每一条数据有一个key以及若干个列属性值组成，每列的数据都有自己的版本信息）。HBase 中的数据是按列进行有序存储的（不像关系型数据库中按行存储）。
<ul>
<li><a href="http://hbase.apache.org/book/datamodel.html">HBase Data Model</a></li>
<li><a href="http://hbase.apache.org/book/supported.datatypes.html">Supported Data Types</a></li>
</ul>
</td>
</tr>
<tr>
<td>存储引擎</td>
<td>Riak 采用模块化的思想，将存储层作为引擎挂到系统上。你可以根据自己的需要选择不同的存储引擎。
<ul>
<li><a href="http://wiki.basho.com/Storage-Backends.html">Riak 支持的存储引擎</a></li>
</ul>
<p>你也可以甚至 Riak 的 <a href="http://wiki.basho.com/Backend-API.html">backend API</a> 实现你自己的存储引擎。</p></td>
<td>HBase 是存在于 HDFS之上的，其数据文件存在HDFS中。与BigTable 类似，数据存储分为内存中的 MemStore 和落地存储的 StoreFiles。其数据文件称为HFile，基于BigTable 的SSTable。可以直接使用JVM的 文件系统IO操作来对数据文件进行操作。
<ul>
<li><a href="http://hadoop.apache.org/hdfs/">HDFS</a></li>
<li><a href="http://hbase.apache.org/book/arch.hdfs.html">Hadoop Uses HDFS</a></li>
</ul>
</td>
</tr>
<tr>
<td>数据访问接口</td>
<td>除了直接使用 Erlang 之外，Riak 还提供了两种数据访问接口，REST方式和 Protocol Buffer :
<ul>
<li><a href="http://wiki.basho.com/HTTP-API.html">HTTP</a></li>
<li><a href="http://wiki.basho.com/PBC-API.html">Protocol Buffers</a></li>
</ul>
<p>Riak 的客户端都是基于上面的API来实现，目前对主流的语言支持很好。</p>
<ul>
<li><a href="http://wiki.basho.com/Client-Libraries.html">Client-Libraries</a></li>
<li><a href="http://wiki.basho.com/Community-Developed-Libraries-and-Projects.html">Community Developed Libraries and Projects</a></li>
</ul>
</td>
<td>HBase 的操作主要通过在JVM中直接执行代码。HBase 也提供了外部数据访问方式，包括REST方式以及Thrift协议的访问。
<ul>
<li><a href="http://hbase.apache.org/book/architecture.html">Java Interface</a></li>
<li><a href="http://wiki.apache.org/hadoop/Hbase/Stargate">REST</a></li>
<li><a href="http://thrift.apache.org/">Thrift</a></li>
</ul>
</td>
</tr>
<tr>
<td>数据操作方式</td>
<td>Riak中支持下面四种方式的操作
<ul>
<li>对主键进行直接操作 (GET, PUT, DELETE,UPDATE)</li>
<li><a href="http://wiki.basho.com/MapReduce.html">MapReduce 方式</a></li>
<li><a href="http://wiki.basho.com/Secondary-Indexes.html">Riak 还提供 Secondary Indexes</a></li>
<li><a href="http://wiki.basho.com/Riak-Search.html">Riak Search 插件</a></li>
<li><a href="http://wiki.basho.com/MapReduce-Search-2i-Comparison.html">上面几种方式的对比文章</a></li>
</ul>
</td>
<td>HBase 有两种方式的数据操作，通过对有序key值进行扫描查询，获取value值，或者借助强大的Hadoop来进行MapReduce查询
<ul>
<li><a href="http://hbase.apache.org/book/client.filter.html">Scanning</a></li>
<li><a href="http://hbase.apache.org/book/mapreduce.html">MapReduce</a></li>
<li><a href="http://hbase.apache.org/book/secondary.indexes.html">Secondary Indexes</a></li>
</ul>
</td>
</tr>
<tr>
<td>数据一致性问题</td>
<td>Riak 通过 vector clock的方式来维护数据版本，处理不一致的情况。同时你也可以不使用vector clock，而是采用基于时间戳的 “last-write-wins” 策略
<ul>
<li><a href="http://wiki.basho.com/Vector-Clocks.html">Vector Clocks</a></li>
<li><a href="http://basho.com/blog/technical/2010/01/29/why-vector-clocks-are-easy/">Why Vector Clocks Are Easy</a></li>
<li><a href="http://basho.com/blog/technical/2010/04/05/why-vector-clocks-are-hard/">Why Vector Clocks Are Hard</a></li>
</ul>
</td>
<td>HBase 采用了强一致性的读写保证。数据会在多个不同的region中进行保存。Column families 可以包含无限多个数据版本，每个版本可以有自己的TTL
<ul>
<li><a href="http://hbase.apache.org/book/architecture.html#arch.overview.nosql">Consistent Architecture</a></li>
<li><a href="http://wiki.basho.com/.html">Time to Live</a></li>
</ul>
</td>
</tr>
<tr>
<td>并发</td>
<td>Riak 集群中的所有节点都能同时进行读写操作，Riak只负责进行数据的写入操作（基于vector clock进行带版本控制的保存），在数据读取的时候再来定义数据冲突的处理逻辑。</td>
<td>HBase 通过行级锁来保证写操作的原子性，但是不支持多行写操作的事务性。数据扫描操作不保证一致性。
<ul>
<li><a href="http://hbase.apache.org/acid-semantics.html">Consistency Guarantees</a></li>
</ul>
</td>
</tr>
<tr>
<td>复制</td>
<td>Riak 的数据复制系统的理论来源主要是Dynamo 的论文和 Eric Brewer 博士的 CAP 理论。Riak 采用一致性hash对数据进行分片，同一份数据会在多个节点中保存备份。在一致性hash的理论支持下，Riak采用的是虚拟节点的方式来实现数据的复制并进行数据平衡分配的保证。引入虚拟节点使得数据与实际节点间能够保持松耦合
<ul>
<li><a href="http://wiki.basho.com/Replication.html">Replication</a></li>
<li><a href="http://wiki.basho.com/Concepts.html#Clustering">Clustering</a></li>
</ul>
<p>Riak 的API 提供在一致性和可用性之间的自由选择，你可以根据自己的应用场景选择不同策略。在最初存储数据到Riak中时，可以按bucket为单位进行复制方式的配置。在后续的读写操作中，每次都能够再进行复制份数的设置。</p>
<ul>
<li><a href="http://wiki.basho.com/Concepts.html#Reading%2C-Writing%2C-and-Updating-Data">Reading, Writing, and Updating Data</a></li>
</ul>
</td>
<td>HBase 是典型的最终一致性实现，数据复制通过 master向slave的推送来实现。最近HBase也添加了master-master 的实现。
<ul>
<li><a href="http://hbase.apache.org/replication.html">Replication</a></li>
</ul>
</td>
</tr>
<tr>
<td>扩展性</td>
<td>Riak 支持动态添加我删除节点，所有节点都对等，不存在主从的区别。当向Riak 中添加一个节点后，集群会通过gossiping 发现节点并分配对应的数据范围并进行数据迁移。移除节点的过程相反。Riak提供了一系列工具来完成节点的增删操作。
<ul>
<li><a href="http://wiki.basho.com/Adding-and-Removing-Nodes.html">Adding and Removing Nodes</a></li>
<li><a href="http://wiki.basho.com/Command-Line-Tools.html">Command Line Tools</a></li>
</ul>
</td>
<td>HBase 以regions为单位进行分片，region会分裂和合并，并自动在多个节点间分配。<a href="http://hbase.apache.org/book/regions.arch.html">Regions</a>
<ul>
<li><a href="http://hbase.apache.org/book/node.management.html">Node Management</a></li>
<li><a href="http://hbase.apache.org/book/architecture.html">HBase Architecture</a></li>
</ul>
</td>
</tr>
<tr>
<td>多数据中心的数据同步</td>
<td>只有Riak的企业版才支持多数据中心的部署方案，通常用户仅支持单数据中心的部署。
<ul>
<li><a href="http://basho.com/products/riak-enterprise/">Riak Enterprise</a></li>
</ul>
</td>
<td>HBase通过region来进行分片，天然就支持多数据中心的部署。
<ul>
<li><a href="http://hbase.apache.org/replication.html">Node Management</a></li>
</ul>
</td>
</tr>
<tr>
<td>图形化的监控管理工具</td>
<td>从Riak 1.1.x 开始，Riak 发布了 Riak Control，这是一个针对Riak的开源的图形化管理工具。
<ul>
<li><a href="http://wiki.basho.com/Riak-Control.html">Riak Control</a></li>
<li><a href="http://basho.com/blog/technical/2012/02/22/Riak-Control/">Introducing Riak Control</a></li>
</ul>
</td>
<td>HBase 有一些开源社区开发的图形化工具，同时也有一个命令行的控制终端能用。
<ul>
<li><a href="http://hbase.apache.org/book/ops_mgt.html#tools">Admin Console Tools</a></li>
<li><a href="http://wiki.apache.org/hadoop/Hbase/EclipseEnvironment">Eclipse Dev Plugin</a></li>
<li><a href="http://sourceforge.net/projects/hbasemanagergui/">HBase Manager</a></li>
<li><a href="https://github.com/zaharije/hbase-gui-admin">GUI Admin</a></li>
</ul>
</td>
</tr>
</tbody>
</table>
<p>来源：<a href="http://wiki.basho.com/Riak-Compared-to-HBase.html">wiki.basho.com</a>
<div style="margin-top:20px;margin-left:70px;line-height:24px;border:1px solid #ccc;text-align:center;width:545px;background:#fff">
<div style="font-size:16px;font-family:Verdana;background:#d20;color:#fff;float:left;border-radius:10px 0 10px 0;padding:3px 12px 4px;line-height:32px;margin-top:14px">42区 VPS</div>
<div>
42qu.com 云主机 , 卖给创业的你 。 <a href="http://vps.42qu.com/by/iammutex/rss" style="text-decoration:none;background:none;color:#02d">点击这里 , 查看详情</a>
</div>
</div>
<p style="margin:0;padding:0;height:1px;overflow:hidden">
    <a href="http://www.wumii.com/widget/relatedItems.htm" style="border:0"><img src="http://static.wumii.com/images/pixel.png" alt="无觅相关文章插件，快速提升流量" style="border:0;padding:0;margin:0"></a></p>
<table cellspacing="0" cellpadding="2" border="0" width="100%" style="clear:both">
    
    <tr>
        <td><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">相关文章：</font></b></td>
    </tr>
    
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.cn/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2936.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F4081.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">Riak系统介绍</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.cn/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F1845.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F4081.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">Cassandra、MongoDB、CouchDB、Redis、Riak、HBase比较</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.cn/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3723.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F4081.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">HBase Coprocessor的分析</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.cn/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3830.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F4081.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">Basho发布Riak CS，提供企业级云服务</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.cn/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3594.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F4081.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">Riaknostic：Riak的问题诊断医师</font>
                    </a>
                </td>
            </tr>
    
    <tr>
        <td align="right">
            <a style="text-decoration:none!important" href="http://www.wumii.com/widget/relatedItems" title="无觅相关文章插件">
                <font size="-1" color="#bbbbbb" style="display:block!important;font-family:arial!important;padding:5px 0!important;font-size:12px!important;color:#bbb!important">无觅</font>
            </a>
        </td>
    </tr>
</table><img src="http://www1.feedsky.com/t1/706989117/nosqlfan/feedsky/s.gif?r=http://item.feedsky.com/~feedsky/nosqlfan/~8149226/706989117/6253001/1/item.html" border="0" height="0" width="0"></p>