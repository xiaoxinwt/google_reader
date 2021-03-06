---
layout: post
title:  "Ruby Web API Server小评测"
date:   2013-05-27 00:44:45
author: robbin
categories: program
---

## Ruby Web API Server小评测
### by robbin
### at 2013-05-27 00:44:45
### original <http://www.udpwork.com/item/9922.html>

<p>前几天看到5月份杭州Ruby活动上黄志敏的Topic<a href="http://yedingding.com/2013/05/14/ruby-event-5-11.html">构建异步的API服务</a>，挺有收获的，对Fiber方式运行Web API Server比较有兴趣。可惜的是作者测试的是单进程并发对比Fiber并发的数据，没有测试多线程对比Fiber并发，所以周末我写了个简单的测试案例，做了一下评测，评测方案有4个，分别是：</p>
<ul><li><p><a href="https://github.com/robbin/ruby_framework_bench/tree/master/grape_on_goliath">grape_on_goliath</a></p>
<p>Goliath有点类似于Rack，但是提供了fiber并发的封装，Grape是类似于Sinatra的Ruby轻量级框架，专门为写API设计的框架。</p>
</li>
<li><p><a href="https://github.com/robbin/ruby_framework_bench/tree/master/grape_on_rainbows">grape_on_rainbows</a></p>
<p>Rainbows是Ruby的多线程服务器，Grape也可以以多线程的方式运行，用来和fiber并发做对比。</p>
</li>
<li><p><a href="https://github.com/robbin/ruby_framework_bench/tree/master/sinatra_on_rainbows">sinatra_on_rainbows</a></p>
<p>Sinatra以多线程方式运行在Rainbows上，可以和Grape多线程模式做下对比。</p>
</li>
<li><p><a href="https://github.com/robbin/ruby_framework_bench/tree/master/sinatra_on_thin">sinatra_on_thin</a></p>
<p><a href="https://github.com/kyledrake/sinatra-synchrony">sinatra_synchrony</a>可以让Sinatra框架以fiber并发的方式运行在Thin上，这样测试可以用来对比多线程Sinatra并发性能，以及对比Grape的fiber并发。</p>
</li>
</ul>
<p>对于IO并发来说，无论是多线程并发，还是fiber并发，只有当IO操作占比例比较高的时候，并发的优势才能体现出来，而测试案例访问的数据库非常简单，数据量又太少，所以我在测试里面设置一个WAIT_TIME参数，用来控制访问数据库的时长，最多设置到500ms，模拟真实环境的数据库IO比例。</p>
<p>在我的MacbookPro上做的测试结果在：<a href="https://github.com/robbin/ruby_framework_bench">ruby_framework_bench</a>，大家有兴趣和耐心，可以自己测试一下。测试原始数据比较多，我也懒得一一整理了，直接上结论吧：</p>
<h2>Fiber vs Multi-thread</h2>
<p>fiber并发和多线程并发的原理其实差不多，都是当前执行线程(纤程)在执行到外部IO调用的时候，放弃CPU控制权，让另一个线程(纤程)来获取CPU。主要差异在于fiber并发只占用1个操作系统线程，由应用程序来调度纤程；而多线程并发占用n个操作系统线程，由Ruby VM来调度线程。</p>
<p>因此两者的性能差异主要是调度方式带来的：纤程的场景切换非常轻量级，而多线程的场景切换代价高于纤程，因此理论上来说fiber并发性能会更好，实际测试结果也表明了这一点：</p>
<ol><li>Running状态的并发线程/纤程不太多的情况下，多线程和fiber并发的性能差异很小，不明显。</li>
<li>Running状态的并发线程/纤程很高的情况下，比方说超过50个running的线程和纤程，性能差异可以明显的看出来，fiber并发CPU的消耗明显低于线程并发10-20%。并发越高，性能差异越大。</li>
</ol>
<p>运行fiber并发有两个常见的方案：</p>
<ol><li><p><a href="https://github.com/kyledrake/sinatra-synchrony">sinatra_synchrony</a></p>
<p>这是Kyle Drake写的一个Sinatra扩展，在支持EventMachine的Ruby Web Server(例如Thin)运行。当Web请求到达的时候，调用rack fiber_pool中间件，创建一个fiber，封装当前执行场景，请求执行完毕，释放fiber。sinatra_synchrony还hack了ruby内置的TCPSocket，所以向外发起HTTP请求，也不会被阻塞，也会让当前fiber释放CPU控制权。</p>
</li>
<li><p><a href="https://github.com/postrank-labs/goliath">goliath</a></p>
<p>Goliath是一个底层的框架，相当于实现了一个fiber并发版本的Rack框架，简单的项目，可以直接用Goliath自己的API写，也可以使用Grape在Goliath上面运行。Sinatra做一些额外的处理也可以跑在Goliath上，但没有用sinatra_synchrony更加方便。</p>
</li>
</ol>
<p>在我的测试当中，sinatra_on_thin 和 grape_on_goliath 没有表现出明显的性能差异，sinatra_on_thin性能表现的稍微好一点点。</p>
<h2>Sinatra vs Grape</h2>
<p>sinatra和grape都是Ruby的轻量级框架，在同样跑Rainbows多线程的评测对比下，也没有表现出明显的性能差异，两者主要区别还是在功能方面：</p>
<ul><li>Grape是一个纯API框架，提供了非常多写json/xml API很方便的设施，但不提供任何view模板功能</li>
<li>Sinatra是一个通用的框架，提供了主流的各种view模板功能，但写纯json/xml API，没有Grape方便</li>
</ul>
<p>所以如果写纯json/xml API的话，用Grape更方便；如果需要模板渲染，那么就用Sinatra。</p>
<h2>Sinatra on Thin的配置</h2>
<p>用Sinatra写Web项目，如果应用的IO并发请求非常高，那么用Thin跑fiber并发，无疑是一个非常好的选择，我是强烈推荐用sinatra_synchrony的。但是fiber并发对驱动和IO库的兼容性要求非常高，目前只有很少的驱动和库能够良好的支持fiber并发，<a href="https://github.com/igrigorik/em-synchrony">em-synchrony</a>提供了常用的fiber并发IO支持库，我们开发web项目，可能涉及到的有：</p>
<ol><li>数据库，例如mysql，postgresql，mongodb等等，目前em-synchrony可以良好的支持mysql和mongodb，其他数据库尚不支持。</li>
<li>缓存服务器，例如redis和memcached，目前redis-rb可以提供良好的支持，memcached也可以用。</li>
<li>向外发起HTTP请求，例如调用其他外部服务，目前sinatra-synchrony内置支持，faraday也提供了良好的支持。</li>
</ol>
<p>我写了一个简单的示例：<a href="https://github.com/robbin/sinatra_synchrony_template">sinatra_synchrony_template</a>，相比标准的sinatra项目，fiber并发需要修改如下配置：</p>
<p>Gemfile里面<a href="https://github.com/robbin/sinatra_synchrony_template/blob/master/Gemfile">相关配置</a>：</p>
<pre>gem &#39;sinatra-synchrony&#39;, :require =&gt; &#39;sinatra/synchrony&#39;
gem &#39;em-synchrony&#39;, :require =&gt; [&#39;em-synchrony&#39;, &#39;em-synchrony/mysql2&#39;, &#39;em-synchrony/activerecord&#39;]
</pre><p>数据库的配置文件database.yml需要如下修改：</p>
<pre>adapter: em_mysql2
</pre><p>如果需要使用redis做缓存，配置如下<a href="https://github.com/robbin/sinatra_synchrony_template/blob/master/application.rb">application.rb</a></p>
<pre>CACHE = EventMachine::Synchrony::ConnectionPool.new(size: 100) do
  ActiveSupport::Cache.lookup_store :redis_store, { :host =&gt; &quot;localhost&quot;, :port =&gt; &quot;6379&quot;, :driver =&gt; :synchrony, :expires_in =&gt; 1.week }
end
</pre><p>每个fiber会分配一个redis连接。</p>
<p>如果需要使用memcached做缓存也可以，memcached的ruby client：dalli并不原生支持fiber，所以当一个fiber访问memcached的时候，fiber并不会放弃CPU控制权。不过因为memcached访问速度非常快，一般只有0.1ms左右，所以并不会造成fiber堵塞的问题，可以直接配置：</p>
<pre>CACHE = ActiveSupport::Cache::DalliStore.new(&quot;127.0.0.1&quot;)
</pre><p>如果使用faraday访问外部服务，配置如下：</p>
<pre>  conn = Faraday.new(:url =&gt; &#39;http://www.facebook.com&#39;) do |faraday|
    faraday.request  :url_encoded             # form-encode POST params
    faraday.response :logger                  # log requests to STDOUT
    faraday.adapter  :em_synchrony            # fiber aware http client
  end    
</pre><p>当访问外部服务的时候，当前fiber就会放弃CPU控制权。</p>
<p>最后提醒一点：无论多线程还是fiber并发，都只能利用单颗CPU内核，在多核服务器上，应该启动多个进程，有几个CPU内核，就启动几个进程，这样可以充分利用服务器的CPU资源。目前无论是支持多线程的Rainbows，还是支持fiber并发的Thin，都内置了cluster的方式，可以配置和控制多个进程。</p>

			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/9922.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/9922.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">udpwork.com</a> 聚合
					|
					<a href="http://www.udpwork.com/item/9922.html#reviews">评论: 0</a>
					|
					<a href="http://www.jikenow.com/">要! 要! 即刻! Now!</a>
				</p>
			</div>