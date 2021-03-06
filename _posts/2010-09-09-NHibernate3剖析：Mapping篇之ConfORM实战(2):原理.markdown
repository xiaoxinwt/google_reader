---
layout: post
title:  "NHibernate3剖析：Mapping篇之ConfORM实战(2):原理"
date:   2010-09-09 08:55:00
author: 李永京
categories: program
---

## NHibernate3剖析：Mapping篇之ConfORM实战(2):原理
### by 李永京
### at 2010-09-09 08:55:00
### original <http://www.cnblogs.com/lyj/archive/2010/09/09/inside-nh3-conform-theory.html>

<p><a href="http://www.cnblogs.com/lyj/"><img src="http://pic.cnblogs.com/face/u20012.jpg" alt="" border="0"></a><br>作者: <a href="http://www.cnblogs.com/lyj/">李永京</a> 发表于 2010-09-09 08:55 <a href="http://www.cnblogs.com/lyj/archive/2010/09/09/inside-nh3-conform-theory.html">原文链接</a> 阅读: 1020 评论: 7</p><p><em><strong>本节内容</strong></em></p>
<ul>
    <li><a href="http://www.cnblogs.com/rss#insidenh3">系列引入</a></li>
    <li><a href="http://www.cnblogs.com/rss#introduction">ConfORM概述</a></li>
	<li><a href="http://www.cnblogs.com/rss#interface">ConfORM重要接口</a></li>
	<li><a href="http://www.cnblogs.com/rss#pattern">ConfORM重要模式</a></li>
	<li><a href="http://www.cnblogs.com/rss#summary">结语</a></li>
	<li><a href="http://www.cnblogs.com/rss#reference">参考资料</a></li>
</ul>
<h1><a name="insidenh3">系列引入</a></h1>
<p><strong>NHibernate3剖析系列</strong>分别从Configuration篇、Mapping篇、Session篇、Core篇、Tool篇、Practice篇、Extension篇等方面全面揭示NHibernate3版本内容、特性及其应用，完全基于NHibernte3版本。</p>
<ul>
	<li>NHibernate专题：<a href="http://kb.cnblogs.com/zt/nhibernate/">http://kb.cnblogs.com/zt/nhibernate/</a></li>
	<li>NHibernate官方站点：<a href="http://nhforge.org/">http://nhforge.org/</a></li>
	<li>NHibernate参考文档：<a href="http://nhforge.org/doc/nh/en/">http://nhforge.org/doc/nh/en/</a></li>
	<li>获取NHibernate地址：<a href="http://sourceforge.net/projects/nhibernate/files/">http://sourceforge.net/projects/nhibernate/files/</a> </li>
</ul>
<h1><a name="introduction">ConfORM概述</a></h1>
<p>在<a href="http://www.cnblogs.com/lyj/archive/2010/04/21/inside-nh3-conform-introduction.html">上一节</a>中，我用一个简单的例子描述了ConfORM简单使用。留下了很多疑问，大家不解为何使用ConfORM以及怎么使用ConfORM，其内部原理是什么。这节，我们先注重了解一些ConfORM的原理。</p>
<p>你可以到<a href="http://code.google.com/p/codeconform/">http://code.google.com/p/codeconform/</a> 
获取ConfORM</p>
<h1><a name="interface">ConfORM重要接口</a></h1>
<p>ConfORM的核心就是实例化一个ObjectRelationalMapper对象和Mapper对象，配置Domain对象，调用Mapper对象的CompileMappingFor()方法生成HbmMapping。即<a href="http://www.cnblogs.com/lyj/archive/2010/04/21/inside-nh3-conform-introduction.html">上一节</a>中我们所写的：</p>
<pre><span><span style="color:green">//Code Snippets Copyright http://lyj.cnblogs.com/</span>
<span style="color:blue">public static </span><span style="color:#2b91af">HbmMapping </span>GetMapping()
{
    <span style="color:green">//初始化ObjectRelationalMapper类</span>
    <span style="color:blue">var </span>orm = <span style="color:blue">new </span><span style="color:#2b91af">ObjectRelationalMapper</span>();
    <span style="color:green">//配置Domain为TablePerClass</span>
    orm.TablePerClass&lt;<span style="color:#2b91af">Domain</span>&gt;();
    <span style="color:green">//在这里可以调用ObjectRelationalMapper类一些方法配置Domain语义</span>
    <span style="color:green">//使用orm参数初始化Mapper类</span>
    <span style="color:blue">var </span>mapper = <span style="color:blue">new </span><span style="color:#2b91af">Mapper</span>(orm);
    <span style="color:green">//在这里可以调用Mapper类一些方法配置Domain的Mapping</span>
    <span style="color:green">//调用Mapper类的CompileMappingFor方法编译生成HbmMapping对象</span>
    <span style="color:blue">return </span>mapper.CompileMappingFor(<span style="color:blue">new</span>[] { <span style="color:blue">typeof</span>(<span style="color:#2b91af">Domain</span>) });
}</span></pre>
<p>在了解这段代码之前，先看看ConfORM的重要接口：</p>
<h2>IDomainInspector接口</h2>
<p>IDomainInspector接口用来描述我们的领域模型，按照ORM术语定义。是ConfORM的切入点，同时也是Mapping类的驱动。由ObjectRelationalMapper类实现这个接口。</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/lyj/NH3/ConfOrm-IDomainInspector.png">
<img alt="IDomainInspector" src="http://images.cnblogs.com/cnblogs_com/lyj/NH3/ConfOrm-IDomainInspector.png"></a></p>
<h2>IObjectRelationalMapper接口</h2>
<p>首先回顾下一些ORM术语：</p>
<p>面向对象的三种继承策略：</p>
<ul>
	<li>TablePerClass：每个类一张表映射策略</li>
	<li>TablePerClassHierarchy：每个类分层结构一张表映射策略</li>
	<li>TablePerConcreteClass：每个具体类一张表映射策略</li>
</ul>
<p>在Entity模型属性中，主要有主键属性、持久化属性、非持久化属性。</p>
<p>IObjectRelationalMapper接口使用ORM术语来描述Domain模型，包含了三种继承策略和Entity模型的各种属性设置方法：</p>
<ul>
	<li>主键(Poid)：每个实体都有自己的状态和生命周期，在数据库中的记录需要一个主键来识别。</li>
	<li>持久化属性：一般有基本属性，还有版本号属性(VersionProperty)、NaturalId属性、各种集合属性(Set、Bag、List、Array、Dictionary、Complex、HeterogeneousAssociation)、各种关联关系属性(ManyToMany、ManyToOne、OneToOne)。</li>
	<li>非持久化属性：不是所有的Domain、属性都需要做持久化，当我们不需要映射类或者属性时可以使用Exclude、ExcludeProperty排除。</li>
</ul>
<p>另外附加一个关联集合的级联方法：Cascade</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/lyj/NH3/ConfOrm-IObjectRelationalMapper.png">
<img alt="IObjectRelationalMapper" src="http://images.cnblogs.com/cnblogs_com/lyj/NH3/ConfOrm-IObjectRelationalMapper.png"></a></p>
<h2>IExplicitDeclarationsHolder接口</h2>
<p>显式声明Domain语义接口，使用IObjectRelationalMapper接口方法显式声明自定义Domain语义，ConfORM会添加到相应集合中。</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/lyj/NH3/ConfOrm-IExplicitDeclarationsHolder.png">
<img alt="IExplicitDeclarationsHolder" src="http://images.cnblogs.com/cnblogs_com/lyj/NH3/ConfOrm-IExplicitDeclarationsHolder.png"></a></p>
<h2>IPatternsHolder接口</h2>
<p>用于隐式声明Domain语义接口，主要原理是其默认实现类(DefaultNHibernatePatternsHolder类)中在各个集合里默认定义了相匹配的模式供我们来匹配其成员。</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/lyj/NH3/ConfOrm-IPatternsHolder.png">
<img alt="IPatternsHolder" src="http://images.cnblogs.com/cnblogs_com/lyj/NH3/ConfOrm-IPatternsHolder.png"></a></p>
<p>例如：实体主键Poid默认使用PoIdPattern：如果实体成员的名称是&quot;id&quot;或者&quot;poid&quot;或者&quot;oid&quot;(不区分大小写)的话，就认为是实体的主键。</p>
<h2>ObjectRelationalMapper类</h2>
<p>ObjectRelationalMapper类实现IDomainInspector接口和IObjectRelationalMapper接口。</p>
<p>IObjectRelationalMapper接口实现方法：用于把自定义的Domain语义配置到IExplicitDeclarationsHolder接口的相应集合中。</p>
<p>IDomainInspector接口实现方法：用于验证Domain语义，它从两种角度去验证Domain语义：</p>
<ul>
	<li>其一是在IExplicitDeclarationsHolder接口默认实现(ExplicitDeclarationsHolder类)中相关集合中显式匹配。</li>
	<li>其二是在IPatternsHolder接口的默认实现(DefaultNHibernatePatternsHolder类)中相关集合中隐式匹配。</li>
</ul>
<h2>Mapping类</h2>
<p>Mapping类是ObjectRelationalMapper和NHibernate映射的桥梁。在ConfOrm.NH命名空间下，Mapping类通过IDomainInspector接口来分析Domain模型语义，我们通过CompileMappingFor()方法或者CompileMappingForEach()方法根据这些语义把程序中的Domain模型编译并转换为NHibernate使用的HbmMapping对象。</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/lyj/NH3/ConfOrm-Mapper.png">
<img alt="Mapper" src="http://images.cnblogs.com/cnblogs_com/lyj/NH3/ConfOrm-Mapper.png"></a></p>
<h1><a name="pattern">ConfORM重要模式</a></h1>
<p>Mapping类除了CompileMappingFor()、CompileMappingForEach()方法之外，还为我们引入了三个重要模式，分别为：模式适配器(pattern-applier)模式、通用定制化(generic-customizer)模式、特定定制化(specific-customizer)模式。</p>
<h2>模式适配器(pattern-applier)</h2>
<p>模式适配器(pattern-applier)，顾名思义，就是Domain模型按模式匹配，如果符合这个模式就进行相应操作。</p>
<p>ConfORM默认提供了很多pattern-applier(即DefaultPatternsAppliersHolder类)。我们可以通过Mapping类的PatternsAppliers属性查看。也可以通过AddXXXPattern()方法增加模式适配器(pattern-applier)。</p>
<h2>通用定制化(generic-customizer)</h2>
<p>通用定制化(generic-customizer)，就是指对通用的类型实现定制化。 </p>
<p>我们不需要准确的知道这个类型的真实映射。如果要定制化一个类，我们不需要知道这个类最终被映射为class,subclass,joined-subclass,union-subclass还是component，只是把这个类设置一些属性。同理，你定制化一个集合不需要知道这个集合将映射为bag,set,array,list还是map(dictionary)。</p>
<p>通用定制化由Mapping类的Customize()方法提供。</p>
<h2>特定定制化(specific-customizer)</h2>
<p>特定定制化(specific-customizer)，就是对特定的类或者集合实现定制化。</p>
<p>与通用定制化恰恰相反，我们对特定的类或者特定的某个集合设置一些定制化属性，这个定制化仅对当前你定制的对象有用。</p>
<p>特定定制化由Mapping类的Class()、Subclass()、JoinedSubclass()、UnionSubclass()、Component()方法提供。</p>
<h1><a name="summary">结语</a></h1>
<p>这篇文章了解一些ConfORM的原理，以后的文章都是以这篇文章为基础展示ConfORM各种应用。</p>
<h1><a name="reference">参考资料</a></h1>
<p>Fabio Maulo：<a href="http://fabiomaulo.blogspot.com/2010/02/conform-nhibernate-un-mapping.html">ConfORM: NHibernate un-Mapping</a></p><img src="http://www.cnblogs.com/lyj/aggbug/1821933.html?type=1" width="1" height="1" alt=""><p>评论: 7　<a href="http://www.cnblogs.com/lyj/archive/2010/09/09/inside-nh3-conform-theory.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/lyj/archive/2010/09/09/inside-nh3-conform-theory.html#commentform">发表评论</a></p><p><a href="http://job.cnblogs.com/">程序员找工作，就在博客园</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/73732/">腾讯发力企业通讯业务 RTX与QQ打通是未来趋势</a><span style="color:gray">(2010-09-09 17:51)</span><br>· <a href="http://news.cnblogs.com/n/73730/">腾讯将于11月10日公布第三季财报</a><span style="color:gray">(2010-09-09 17:48)</span><br>· <a href="http://news.cnblogs.com/n/73728/">电影行业以暴制暴：雇佣黑客攻击盗版网站</a><span style="color:gray">(2010-09-09 17:42)</span><br>· <a href="http://news.cnblogs.com/n/73725/">360安全中心：仅一成网民36秒内完成开机</a><span style="color:gray">(2010-09-09 17:19)</span><br>· <a href="http://news.cnblogs.com/n/73726/">百度的矛和360的盾</a><span style="color:gray">(2010-09-09 17:18)</span><br></p><p>编辑推荐：<a href="http://www.cnblogs.com/KnightsWarrior/archive/2010/09/08/1821098.html">云计算之旅5—实战第一个云程序</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">个人主页</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://space.cnblogs.com">社区</a>  <a href="http://kb.cnblogs.com">知识库</a></p>