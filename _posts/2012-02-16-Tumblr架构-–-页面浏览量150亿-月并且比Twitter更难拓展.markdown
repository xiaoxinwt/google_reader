---
layout: post
title:  "Tumblr架构 – 页面浏览量150亿/月并且比Twitter更难拓展"
date:   2012-02-16 22:36:11
author: longhao
categories: program
---

## Tumblr架构 – 页面浏览量150亿/月并且比Twitter更难拓展
### by longhao
### at 2012-02-16 22:36:11
### original <http://www.udpwork.com/item/6851.html>

<p>
<br>
</p>
<div>    注：一些内容不熟悉，所以没有翻译。原文地址在<a href="http://highscalability.com/blog/2012/2/13/tumblr-architecture-15-billion-page-views-a-month-and-harder.html">这里</a></div>
<div>
<br>
</div>
<div>   <a href="http://Tumblr.com">Tumblr</a>每个月增长30% , 一天5亿网页浏览，40K/sec , 每天3TB的数据存储在1000+的服务器上。最开始只有4名工程师来处理所有事情，当有20多个工程师的时候，才有实力出一些有趣的解决方案。</div>
<div>    Tumblr最开始是典型的大型LAMP应用，现在的分布式服务模型使用了Scala, HBase, Redis,<a href="http://incubator.apache.org/kafka/design.html">Kafka</a> , Finagle等，现在在处理PHP应用的问题，开始走向面向服务的设计。</div>
<div>    分布式系统工程师 Blank Matheny讲述Tumblr的架构</div>
<div><h3>现状</h3>
</div>
<ul><li>每天5亿PV</li>
<li>~20 工程师</li>
<li>平均每秒4W请求</li>
<li>每天1+ TB 数据写入到Hadoop集群</li>
<li>每天更多TB的数据写入到 MySQL/HBase/Redis/Memcache</li>
<li>每月增长超过30%</li>
<li>~1000 硬件服务器</li>
<li>每个工程师每天分摊的PV将近10亿</li>
<li>每天提交 50GB . Follower列表更新每天产生2.7TB 的数据。</li>
<li>Dashboard 每秒百万的读取和每秒5万的写入, 并且还在增长.</li>
</ul>
<h2>软件</h2>
<ul><li>开发使用OS X , 服务器使用Linux (CentOS, Scientific)</li>
<li>Apache</li>
<li>PHP, Scala, Ruby</li>
<li>Redis, HBase, MySQL</li>
<li>Varnish, HA-Proxy, nginx,</li>
<li>Memcache, Gearman, Kafka, Kestrel, Finagle</li>
<li>Thrift, HTTP</li>
<li>Func – a secure, scriptable remote control framework and API</li>
<li>Git, Capistrano, Puppet, Jenkins</li>
</ul>
<h2>硬件</h2>
<ul><li>500 web servers</li>
<li>200 database servers (许多服务器是容灾的需要)<ul><li>47 pools</li>
<li>30 shards</li>
</ul>
</li>
<li>30 memcache servers</li>
<li>22 redis servers</li>
<li>15 varnish servers</li>
<li>25 haproxy nodes</li>
<li>8 nginx</li>
<li>14 job queue servers (kestrel + gearman)</li>
</ul>
<h2>架构设计</h2>
<ul><li>Tumblr 相对其他社会网络有不同的使用模式<ul><li>网状的分发结构，每天超过5000万的提交需要分发到用户的Follower , 很多用户都有数百个Follower ，百万Follower的用户也不是一两个。所以Tumblr的规模极具挑战性。</li>
<li>由于存在图片和视频，让用户停留时间比较长。</li>
<li>返回给用户的内容和用户的联系人有关，不是简单的数据流。</li>
<li>需要做大量的数据分析（用户数量，用户平均活动范围，用户提交的高质量的内容等）</li>
</ul>
</li>
<li>Tumblr 运行在托管服务器上 . 设计上需要考虑未来的地理分布。</li>
<li>Tumblr作为一个平台有2部分组成: 公共Tumblr日志 和面板
<br>
<ul><li>公共Tumblr日志是一个博客平台 . 容易缓存</li>
<li>控制面板类似 Twitter 时间表 . 需要实时更新用户follower的内容.<ul><li>cache不管用，尤其对活跃的用户</li>
<li>需要实时和一致，不应该显示陈旧数据，所以会有每天提交50GB，Follower列表更新需要2.7TB。（多媒体存储在S3上）</li>
</ul>
</li>
<li>多数用户把Tumblr作为内容消费工具, 70%浏览来自面板.</li>
<li>面板的可用性非常好了，Tumblr日志因为遗留的架构问题导致可用性不好。</li>
</ul>
</li>
</ul>
<h3>老的Tumblr</h3>
<ul><li>Tumblr公司开始托管在Rackspace ，他们给每个自定义域blog一个A记录（域名管理方面的内容），当迁出Rackspace的时候需要迁移大量用户，2007年前，使用 HAProxy and Varnish来负责均衡。许多像这样的遗留问题</li>
<li>一个传统的 LAMP 演进.<ul><li>历史原因，每个工程师都用PHP。</li>
<li>最开始阶段一台web服务器，一台数据库服务器，一台PHP应用服务器。</li>
<li>To scale they started using memcache, then put in front-end caching, then HAProxy in front of the caches, then MySQL sharding. MySQL sharding has been hugely helpful.</li>
<li>最开始使用memcache ，然后转到前端缓存，使用HAProxy 在缓存前，然后使用Mysql水平分区，Mysql水平分区非常有用。</li>
<li>最开始中心化，过去的几年中采用了2台用C语言写的后端服务器来做2件事情：<b>生成ID 和 Staircar</b> ，使用Redis来处理面板通知</li>
</ul>
</li>
<li>面板采用了 scatter-gather 的方式，当用户访问他们面板的时候采用事件驱动的方式，由于采用事件顺序，分片方案不是工作的很好。</li>
</ul>
<h3>新的Tumblr</h3>
<ul><li>因招聘和开发速度的原因，转向JVM。</li>
<li>目标是移除PHP的所有应用，采用服务的方式。所有应用是很薄的一层，通过访问服务来鉴权和呈现。</li>
<li>Scala and Finagle 的选择<ul><li>内部人员很多回Ruby和PHP，所以Scala语言比较容易接受</li>
<li><div>Finagle 是Scala写的，解决了大部分的分布式问题，同时它是免费的。</div>
</li>
<li> Finagle 提供了初始阶段所有想要的东西 (Thrift, ZooKeeper, etc).</li>
<li>Finagle 被 Foursquare和Twitter使用 . Meetup在使用Scala</li>
<li>喜欢Thrift的应用接口 ， 主要是高性能方面。</li>
<li>喜欢Netty, 但是不喜欢Java , Scala是个不错选择</li>
<li>选择 Finagle 可以通过更少的代码解决分布问题。 </li>
<li><div>Node.js不被选择是因为JVM平台更有优势，Node.js没有开发标准和好的开发策略，没有通过大型系统的代码验证。Scala 可以使用java代码，可拓展性好，5毫秒的响应速度，有许多大型java应用可以参考。</div>
</li>
</ul>
</li>
<li>内部服务从基于C/libevent转向基于 Scala/Finagle</li>
<li>使用非关系型数据库HBase和Redis ,  但是大半数据存储在Mysql集群，没有使用HBase替代MySQL。</li>
<li><div>HBase 被用来备份数十亿的短网址和历史数据分析，被用来解决高并发写的问题，例如：每秒百万写级别的面板重置。HBase 没有替代Mysql是因为项目经验少，不能打赌。</div>
</li>
<li>MySQL和shard(分片数据库）在时序数据上的问题是，一个分片总是热点块，并且由于在slave端的平行插入而导致读端的复制总是落后。
<br>
</li>
<li>Created a common services framework.<ul><li>Spent a lot of time upfront solving operations problem of how to manage a distributed system.</li>
<li>Built a kind of Rails scaffolding, but for services. A template is used to bootstrap services internally.</li>
<li>All services look identical from an operations perspective. Checking statistics, monitoring, starting and stopping all work the same way for all services.</li>
<li>Tooling is put around the build process in SBT (a Scala build tool) using plugins and helpers to take care of common activities like tagging things in git, publishing to the repository, etc. Most developers don’t have to get in the guts of the build system.</li>
</ul>
</li>
<li>前置层使用 HAProxy. Varnish ， 40台机器.</li>
<li>500 台web服务器上跑着Apache和PHP应用.</li>
<li>200台数据库服务器，部分服务器是容灾需要；成本考虑，硬件使用MTBF。</li>
<li>6种后端服务支撑PHP应用。有一个小组专门来开发这种服务，每2,3周都会推出一种新的服务。包括： Includes dashboard notifications, dashboard secondary index, URL shortener, and a memcache proxy to handle transparent sharding.</li>
<li>MySQL足够了，所以没有用MongoDB.</li>
<li><div>Gearman , 一个工作队列系统。用来跑长时间运行的或者无需人工干预的工作。</div>
</li>
<li>Availability is measured in terms of reach. Can a user reach custom domains or the dashboard? Also in terms of error rate.</li>
<li>历史最高需求已经被搞定。现在，如果一部分需求不能满足要求，我们会从用户角度和应用角度对错误的模型进行分析和系统的处理来达到成功的目的。</li>
<li>最初使用了Finagle的Actor模型，后来去掉了。使用了twitter的utility library的Futures接口来处理异步的需求。</li>
<li>Scala鼓励不适用共享状态。Finagle 在twitter的产品中得到了很好的测试验证，在机器上无状态运行，保证了开发人员不用去担心线程和锁。</li>
<li>22 Redis servers. Each server has 8 – 32 instances so 100s of Redis instances are used in production.<ul><li>Used for backend storage for dashboard notifications.</li>
<li>A notification is something  like a user liked your post. Notifications show up in a user’s dashboard to indicate actions other users have taken on their content.</li>
<li>High write ratio made MySQL a poor fit.  </li>
<li>Notifications are ephemeral so it wouldn’t be horrible if they were dropped, so Redis was an acceptable choice for this function.</li>
<li>Gave them a chance to learn about Redis and get familiar with how it works.</li>
<li>Redis has been completely problem free and the community is great.</li>
<li>A Scala futures based interface for Redis was created. This functionality is now moving into their Cell Architecture.</li>
<li>URL shortener uses Redis as the first level cache and HBase as permanent storage.</li>
<li>Dashboard’s secondary index is built around Redis.</li>
<li>Redis is used as Gearman’s persistence layer using a memcache proxy built using Finagle.</li>
<li>Slowly moving from memcache to Redis. Would like to eventually settle on just one caching service. Performance is on par with memcache.</li>
</ul>
</li>
</ul>
<h3>内部的Firehose(通信管道)</h3>
<ul><li>Internally applications need access to the activity stream. An activity steam is information about users creating/deleting posts, liking/unliking posts, etc.  A challenge is to distribute so much data in real-time. Wanted something that would scale internally and that an application ecosystem could reliably grow around. A central point of distribution was needed.</li>
<li>Previously this information was distributed using Scribe/Hadoop. Services would log into Scribe and begin tailing and then pipe that data into an app. This model stopped scaling almost immediately, especially at peak where people are creating 1000s of posts a second. Didn’t want people tailing files and piping to grep.</li>
<li>An internal firehose was created as a message bus. Services and applications talk to the firehose via Thrift.</li>
<li>LinkedIn’s Kafka is used to store messages. Internally consumers use an HTTP stream to read from the firehose. MySQL wasn’t used because the sharding implementation is changing frequently so hitting it with a huge data stream is not a good idea.</li>
<li>The firehose model is very flexible, not like Twitter’s firehose in which data is assumed to be lost.<ul><li>The firehose stream can be rewound in time. It retains a week of data. On connection it’s possible to specify the point in time to start reading.</li>
<li>Multiple clients can connect and each client won’t see duplicate data. Each client has a client ID. Kafka supports a consumer group idea. Each consumer in a consumer group gets its own messages and won’t see duplicates. Multiple clients can be created using the same consumer ID and clients won’t see duplicate data. This allows data to be processed independently and in parallel. Kafka uses ZooKeeper to periodically checkpoint how far a consumer has read.</li>
</ul>
</li>
</ul>
<h3>Cell Design for Dashboard Inbox(业务方面的实现，未翻译)</h3>
<ul><li>The current scatter-gather model for providing Dashboard functionality has very limited runway. It won’t last much longer.<ul><li>The solution is to move to an inbox model implemented using a Cell Based Architecture that is similar to Facebook Messages.</li>
<li>An inbox is the opposite of scatter-gather. A user’s dashboard, which is made up posts from followed users and actions taken by other users,  is logically stored together in time order.</li>
<li>Solves the scatter gather problem because it’s an inbox. You just ask what is in the inbox so it’s less expensive then going to each user a user follows. This will scale for a very long time.</li>
</ul>
</li>
<li>Rewriting the Dashboard is difficult. The data has a distributed nature, but it has a transactional quality, it’s not OK for users to get partial updates.<ul><li>The amount of data is incredible. Messages must be delivered to hundreds of different users on average which is a very different problem than Facebook faces. Large date + high distribution rate + multiple datacenters.</li>
<li>Spec’ed at a million writes a second and 50K reads a second. The data set size is 2.7TB of data growth with no replication or compression turned on. The million writes a second is from the 24 byte row key that indicates what content is in the inbox.</li>
<li>Doing this on an already popular application that has to be kept running.</li>
</ul>
</li>
<li>Cells<ul><li>A cell is a self-contained installation that has all the data for a range of users. All the data necessary to render a user’s Dashboard is in the cell.</li>
<li>Users are mapped into cells. Many cells exist per data center.</li>
<li>Each cell has an HBase cluster, service cluster, and Redis caching cluster.</li>
<li>Users are homed to a cell and all cells consume all posts via firehose updates.</li>
<li>Each cell is Finagle based and populates HBase via the firehose and service requests over Thrift.</li>
<li>A user comes into the Dashboard, users home to a particular cell, a service node reads their dashboard via HBase, and passes the data back.</li>
<li>Background tasks consume from the firehose to populate tables and process requests.</li>
<li>A Redis caching layer is used for posts inside a cell.</li>
</ul>
</li>
<li>Request flow: a user publishes a post, the post is written to the firehose, all of the cells consume the posts and write that post content to post database, the cells lookup to see if any of the followers of the post creator are in the cell, if so the follower inboxes are updated with the post ID.</li>
<li>Advantages of cell design:<ul><li>Massive scale requires parallelization and parallelization requires components be isolated from each other so there is no interaction. Cells provide a unit of parallelization that can be adjusted to any size as the user base grows.</li>
<li>Cells isolate failures. One cell failure does not impact other cells.</li>
<li>Cells enable nice things like the ability to test upgrades, implement rolling upgrades, and test different versions of software.</li>
</ul>
</li>
<li>The key idea that is easy to miss is:  all posts are replicated to all cells.<ul><li>Each cell stores a single copy of all posts. Each cell can completely satisfy a Dashboard rendering request. Applications don’t ask for all the post IDs and then ask for the posts for those IDs. It can return the dashboard content for the user. Every cell has all the data needed to fulfill a Dashboard request without doing any cross cell communication.</li>
<li>Two HBase tables are used: one that stores a copy of each post. That data is small compared to the other table which stores every post ID for every user within that cell. The second table tells what the user’s dashboard looks like which means they don’t have to go fetch all the users a user is following. It also means across clients they’ll know if you read a post and viewing a post on a different device won’t mean you read the same content twice. With the inbox model state can be kept on what you’ve read.</li>
<li>Posts are not put directly in the inbox because the size is too great. So the ID is put in the inbox and the post content is put in the cell just once. This model greatly reduces the storage needed while making it simple to return a time ordered view of an users inbox. The downside is each cell contains a complete copy of call posts. Surprisingly posts are smaller than the inbox mappings. Post growth per day is 50GB per cell, inbox grows at 2.7TB a day. Users consume more than they produce.</li>
<li>A user’s dashboard doesn’t contain the text of a post, just post IDs, and the majority of the growth is in the IDs.</li>
<li>As followers change the design is safe because all posts are already in the cell. If only follower posts were stored in a cell then cell would be out of date as the followers changed and some sort of back fill process would be needed.</li>
<li>An alternative design is to use a separate post cluster to store post text. The downside of this design is that if the cluster goes down it impacts the entire site.  Using the cell design and post replication to all cells creates a very robust architecture.</li>
</ul>
</li>
<li>A user having millions of followers who are really active is handled by selectively materializing user feeds by their access model (see Feeding Frenzy).<ul><li>Different users have different access models and distribution models that are appropriate. Two different distribution modes: one for popular users and one for everyone else.</li>
<li>Data is handled differently depending on the user type. Posts from active users wouldn’t actually be published, posts would selectively materialized.</li>
<li>Users who follow millions of users are treated similarly to users who have millions of followers.</li>
</ul>
</li>
<li>Cell size is hard to determine. The size of cell is the impact site of a failure. The number of users homed to a cell is the impact. There’s a tradeoff to make in what they are willing to accept for the user experience and how much it will cost.</li>
<li>Reading from the firehose is the biggest network issue. Within a cell the network traffic is manageable.</li>
<li>As more cells are added cells can be placed into a cell group that reads from the firehose and then replicates to all cells within the group. A hierarchical replication scheme. This will also aid in moving to multiple datacenters.</li>
</ul>
<h3>团队结构</h3>
<ul><li>Teams: 技术设施, 平台开发，架构, SRE （侧重可靠性和可拓展性方面的问题解决）, 产品, 测试, services（趋向于战略研究）.</li>
</ul>
<h3>软件部署</h3>
<ul><li>开发一套rsync脚本来部署PHP，当机器超过200台的时候出现各种状态问题。</li>
<li>接下来，使用Capistrano（一个开源工具，可以在多台服务器上运行脚本）在服务堆栈中构建部署进程（开发、分期、生产）。在几十台机器上部署可以正常工作，但当通过SSH部署到数百台服务器时，再次失败。</li>
<li>现在，所有的机器上运行一个协调软件。基于Redhat Func（一个安全的、脚本化的远程控制框架和接口）功能，一个轻量级的API用于向主机发送命令，以构建扩展性。</li>
<li>建立部署是在Func的基础上向主机发送命令，避免了使用SSH。比如，想在组A上部署软件，控制主机就可以找出隶属于组A的节点，并运行部署命令。</li>
<li>部署命令使用Capistrano来实现。使用http的方式从git从库上来检出和上传。</li>
<li>Func API用于返回状态报告，报告机器上的软件版本号</li>
<li>安全重启任何服务，因为他们先关闭连接，然后重启。</li>
<li>所有功能在激活前都运行dark mode下</li>
</ul>
<h3>开发</h3>
<ul><li>从哲学上将，任何人都可以使用自己想要的任意工具。但随着团队的发展壮大，这些工具出现了问题。新员工想要更好地融入团队，快速地解决问题，必须以他们为中心，建立操作的标准化。</li>
<li>过程类似于Scrum（一种敏捷管理框架），非常敏捷。</li>
<li>每个开发人员都有一台预配置的开发机器，并按照控制更新。</li>
<li>开发机会出现变化，测试，分期，乃至用于生产。</li>
<li>开发者使用VIM和TextMate。</li>
<li>测试是对PHP程序进行代码审核。</li>
<li>在服务方面，他们已经实现了一个与提交相挂钩的测试基础架构，接下来将继承并内建通知机制。
<br>
</li>
</ul>
<h3>招聘流程</h3>
<ul><li>面试通常避免数学、猜谜、脑筋急转弯等问题，看重实际工作技能。</li>
<li>编程能力是重中之重。</li>
<li>找到合适的人，不是比较人</li>
<li>挑战在于找到具有可用性、扩展性经验的人才，以应对Tumblr面临的网络拥塞。
<br>
<ul><li>Example, for a new ID generator they needed A JVM process to generate service responses in less the 1ms at a rate at 10K requests per second with a 500 MB RAM limit with High Availability. They found the serial collector gave the lowest latency for this particular work load. Spent a lot of time on JVM tuning.</li>
</ul>
</li>
</ul>
<h2>Lessons learned</h2>
<ul><li>无处不在的自动化。</li>
<li>MySQL 添加水平分区, 应用则不需要。</li>
<li>Redis是惊人的（是不是提醒大家用一下）。</li>
<li>Scala应用程序执行效率和出色。</li>
<li>取消不能工作的项目。</li>
<li>雇佣的人不要因为他们有对团队无用的技能，雇佣的人应该适应团队和工作。</li>
<li>找到帮你找到合适人的stack</li>
<li>围绕团队技能开展工作</li>
<li>阅读论文和博客</li>
<li>多与同行交流</li>
<li>技术追求需要循序渐进。在正式投入使用前，Tumblr用心研究HBase和Redis ， 这样可以降低线上服务风险。</li>
</ul>
<p>
<br>
</p>
<div>    </div>

			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/6851.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/6851.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">IT 牛人博客聚合网站(udpwork.com)</a> 聚合
					|
					<a href="http://www.udpwork.com/item/6851.html#reviews">评论: 0</a>
				</p>
			</div>