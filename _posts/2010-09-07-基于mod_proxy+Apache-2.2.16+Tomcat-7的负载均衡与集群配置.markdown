---
layout: post
title:  "基于mod_proxy+Apache 2.2.16+Tomcat 7的负载均衡与集群配置"
date:   2010-09-07 07:25:32
author: 
categories: program
---

## 基于mod_proxy+Apache 2.2.16+Tomcat 7的负载均衡与集群配置
### by 
### at 2010-09-07 07:25:32
### original <http://www.javaeye.com/topic/757125>

<div style="text-align:center">基于mod_proxy+Apache 2.2.16+Tomcat 7的负载均衡与集群配置
<br>Peter Wei</div>
<br>
<br>周日晚和GF的老乡们喝了五粮液，导致周一起不了床，只好请假在家睡觉。白天睡了半天，晚上自然睡不着觉。正好现在的项目中也用到了负载均衡和集群的东西，虽然有新手贴的危险，但还是手痒，决定写点东西，以备不时之需。也希望能对大家有所帮助。
<br>
<br>第一章.	背景简介
<br>对于大多数企业应用，都希望能做到7*24小时不间断运行。要保持如此高的可用性并非易事，比较常见的做法是将系统部署到多台机器上，每台机器都对外提供同样的功能，这就是集群。系统变为集群时，除了要求系统能够支持水平伸缩外，还要解决两个问题：
<br>1，	如何均衡地访问到提供业务功能的机器。
<br>2，	如何保证当机器出现问题时，用户能自动跳转到另外的机器，不影响使用。
<br>常用的负载均衡技术有硬件和软件两种，本示例常用软件的技术实现。软件也有很多实现技术，如基于apache的mod_jk以及mod_proxy等。基于mod_jk的文章有不少，本文演示一下用mod_proxy的方式。
<br>	实现集群的应用最重要的是处理用户Session的问题，一般有三种策略：
<br>1，	Session复制
<br>2，	Session Sticky
<br>3，	基于Cache的集中式Session
<br>本文使用的是Tomcat 7.0.2应用服务器，用的方法是Session复制。
<br>
<br>第二章.	配置环境
<br>1，	JDK1.6,请自行下载安装,搞Java的一般都装有的吧，哈哈。
<br>2，	Apache 2.2.16, (released 2010-07-25),现在为止应该是最新的稳定版本，下载地址: http://httpd.apache.org/download.cgi
<br>3，	Tomcat 7.0.2,目前也是最新的版本。Minimum Java Version1.6.下载地址：http://tomcat.apache.org/download-70.cgi
<br>4，	安装过程略
<br>
<br>第三章.	部署图
<br>
<br><img src="http://dl.javaeye.com/upload/attachment/304560/5ca77deb-7e0e-3871-8c22-ff58a047bbab.jpg">
<br>
<br> 
<br>第四章.	Tomcat7集群配置
<br>一、	就地取材，复制tomcat7/webapps下的examples,重命名为cluster应用,以后就用cluster做测试。
<br>二、	详细配置参照tomcat7 \webapps\docs\cluster-howto.html 或者http://tomcat.apache.org/tomcat-7.0-doc/cluster-howto.html
<br>三、	为了在Tomcat7中实现session复制，以下必需完成：
<br>•	<span style="color:red">所有session属性必需实现 java.io.Serializable </span>
<br>•	Uncomment the Cluster element in server.xml。把Cluster元素的注释去掉。参照四
<br>•	If you have defined custom cluster valves, make sure you have the ReplicationValve defined as well under the Cluster element in server.xml 。参照四
<br>•	If your Tomcat instances are running on the same machine, make sure the tcpListenPort attribute is unique for each instance, in most cases Tomcat is smart enough to resolve this on it&#39;s own by autodetecting available ports in the range 4000-4100。参照四&lt; Receiver&gt;中的注释
<br>•	Make sure your web.xml has the &lt;distributable/&gt; element or set at your &lt;Context distributable=&quot;true&quot; /&gt; 参照四.2
<br>•	If you are using mod_jk, make sure that jvmRoute attribute is set at your Engine &lt;Engine name=&quot;Catalina&quot; jvmRoute=&quot;node01&quot; &gt; and that the jvmRoute attribute value matches your worker name in workers.properties .用mod_jk的情况，我们可以不管。
<br>•	Make sure that all nodes have the same time and sync with NTP service! 当使用多台机器时，要保证不同机器时间的同步。原因为tomcat session复制的一些机制。具体原因看文档。
<br>•	Make sure that your loadbalancer is configured for sticky session mode. 保证负载均衡软件设置为session sticky模式。
<br>四、	详细配置：
<br>1.	修改tomcat7_a/conf/server.xml, 我们采用的是默认的配置，在&lt;Engine&gt;节点下添加：
<br>
<br><pre name="code">&lt;Cluster className=&quot;org.apache.catalina.ha.tcp.SimpleTcpCluster&quot;
                 channelSendOptions=&quot;8&quot;&gt;

          &lt;Manager className=&quot;org.apache.catalina.ha.session.DeltaManager&quot;
                   expireSessionsOnShutdown=&quot;false&quot;
                   notifyListenersOnReplication=&quot;true&quot;/&gt;

          &lt;Channel className=&quot;org.apache.catalina.tribes.group.GroupChannel&quot;&gt;
            &lt;Membership className=&quot;org.apache.catalina.tribes.membership.McastService&quot;
                        address=&quot;228.0.0.4&quot;
                        port=&quot;45564&quot;
                        frequency=&quot;500&quot;
                        dropTime=&quot;3000&quot;/&gt;
            &lt;Receiver className=&quot;org.apache.catalina.tribes.transport.nio.NioReceiver&quot;
                      address=&quot;auto&quot;
&lt;!—tcpListenPort如果是同一机器部署两个tomcat7应用，则修改tomcat7_b为4001，以免冲突. 不同机器下，不用更改此项。—&gt;
                      [color=red]port=&quot;4000&quot;[/color]
                      autoBind=&quot;100&quot;
                      selectorTimeout=&quot;5000&quot;
                      maxThreads=&quot;6&quot;/&gt;

            &lt;Sender className=&quot;org.apache.catalina.tribes.transport.ReplicationTransmitter&quot;&gt;
              &lt;Transport className=&quot;org.apache.catalina.tribes.transport.nio.PooledParallelSender&quot;/&gt;
            &lt;/Sender&gt;
            &lt;Interceptor className=&quot;org.apache.catalina.tribes.group.interceptors.TcpFailureDetector&quot;/&gt;
            &lt;Interceptor className=&quot;org.apache.catalina.tribes.group.interceptors.MessageDispatch15Interceptor&quot;/&gt;
          &lt;/Channel&gt;

          &lt;Valve className=&quot;org.apache.catalina.ha.tcp.ReplicationValve&quot;
                 filter=&quot;&quot;/&gt;
          &lt;Valve className=&quot;org.apache.catalina.ha.session.JvmRouteBinderValve&quot;/&gt;

          &lt;Deployer className=&quot;org.apache.catalina.ha.deploy.FarmWarDeployer&quot;
                    tempDir=&quot;/tmp/war-temp/&quot;
                    deployDir=&quot;/tmp/war-deploy/&quot;
                    watchDir=&quot;/tmp/war-listen/&quot;
                    watchEnabled=&quot;false&quot;/&gt;

          &lt;ClusterListener className=&quot;org.apache.catalina.ha.session.JvmRouteSessionIDBinderListener&quot;/&gt;
          &lt;ClusterListener className=&quot;org.apache.catalina.ha.session.ClusterSessionListener&quot;/&gt;
        &lt;/Cluster&gt;</pre>
<br>2.	&lt;Connector <span style="color:red">port="8081"</span> protocol=&quot;HTTP/1.1&quot; connectionTimeout=&quot;20000&quot; redirectPort=&quot;8443&quot; /&gt; 修改，仅为了调试方便。
<br>3.	tomcat7_a\webapps\cluster\WEB-INF\web.xml中加入<span style="color:red">&lt;distributable/&gt;</span>
<br>4.	&lt;Engine name=&quot;Catalina&quot; defaultHost=&quot;localhost&quot; <span style="color:red">jvmRoute=&quot;tomcat7_a&quot;&gt;,</span>添加jvmRoute属性，此项为后面apache负载均衡用到。
<br>
<br>五、	复制一份tomcat7_a应用,改名为tomcat7_b.只是为了图方便，实际应该复制的是前面的cluster工程。注意以下几点配置就ok.
<br>1.	修改tomcat7_a/conf/server.xml 中的Server port属性&lt;Server <span style="color:red">port="8006"</span> shutdown=&quot;SHUTDOWN&quot;&gt;,因为是同一台机器两个tomcat应用，所以改一下。
<br>2.	修改&lt;connector <span style="color:red">port="8082"</span> protocol=&quot;HTTP/1.1&quot; onnectionTimeout=&quot;20000&quot; edirectPort=&quot;8443&quot; /&gt;,同理，为了避免同一台机器端口号冲突。部置在不同的机器是不用管的。
<br>3.	修改&lt;Engine name=&quot;Catalina&quot; defaultHost=&quot;localhost&quot; <span style="color:red">jvmRoute=&quot;tomcat7_b&quot;&gt;，</span>此项为后面apache负载均衡用到。
<br>4.	修改&lt;Receiver className=&quot;org.apache.catalina.tribes.transport.nio.NioReceiver&quot; address=&quot;auto&quot; <span style="color:red">port="4001"</span> autoBind=&quot;100&quot; selectorTimeout=&quot;5000&quot; maxThreads=&quot;6&quot;/&gt;
<br>5.	tomcat7_b\ebapps\cluster\WEB-INF\web.xml中加入<span style="color:red">&lt;distributable/&gt;</span>
<br>到此集群配置完成.
<br>第五章.	集群测试
<br>1.	tomcat7_a和tomcat7_b的cluster工程中分别添加测试文件:testCluster.jsp
<br><pre name="code">&lt;%@ page contentType=&quot;text/html; charset=GBK&quot; %&gt;
&lt;%@ page import=&quot;java.util.*&quot; %&gt;
&lt;html&gt;&lt;head&gt;&lt;title&gt;Cluster Test&lt;/title&gt;&lt;/head&gt;
&lt;body&gt;
&lt;%
  //HttpSession session = request.getSession(true);
  System.out.println(session.getId());
  out.println(&quot;&lt;br&gt; SESSION ID:&quot; + session.getId()+&quot;&lt;br&gt;&quot;);  
  // 如果有新的请求，则添加session属性
  String name = request.getParameter(&quot;name&quot;);
  if (name != null &amp;&amp; name.length() &gt; 0) {
     String value = request.getParameter(&quot;value&quot;);
     session.setAttribute(name, value);
  }  
	out.print(&quot;&lt;b&gt;Session List:&lt;/b&gt;&quot;);  
	Enumeration&lt;String&gt; names = session.getAttributeNames();
	while (names.hasMoreElements()) {
		String sname = names.nextElement(); 
		String value = session.getAttribute(sname).toString();
		out.println( sname + &quot; = &quot; + value+&quot;&lt;br&gt;&quot;);
        System.out.println( sname + &quot; = &quot; + value);
   }
%&gt;
  &lt;form action=&quot;testCluster.jsp&quot; method=&quot;post&quot;&gt;
    名称:&lt;input type=text size=20 name=&quot;name&quot;&gt;
     &lt;br&gt;
    值:&lt;input type=text size=20 name=&quot;value&quot;&gt;
     &lt;br&gt;
    &lt;input type=submit value=&quot;提交&quot;&gt;
   &lt;/form&gt;
&lt;/body&gt;
&lt;/html&gt;</pre>
<br>2.	启动tomcat7_a,启动完毕后，启动tomcat7_b
<br>3.	进入http://localhost:8081/cluster/testCluster.jsp 对应tomcat7_a(8081)，登录几次，可看到
<br> 
<br><img src="http://dl.javaeye.com/upload/attachment/304562/f30aa269-6e86-3f1f-97f2-bd207cd96aa3.jpg">
<br>
<br>4.	另外打开一个浏览器，进入http://localhost:8082/cluster/testCluster.jsp对应tomcat7_b(8082)，登录name:tomcat_b，value:b value可看到
<br>
<br><img src="http://dl.javaeye.com/upload/attachment/304564/9e033009-378b-3d7c-9d95-360c8db88398.jpg">
<br> 
<br>
<br>5.	刷新tomcat7_a(8081)相关页面,可以看到从tomcat7_b提交的值session同步过来了，说明集群成功。
<br> 
<br><img src="http://dl.javaeye.com/upload/attachment/304566/19b3e53e-34bc-30d9-9429-b5c97f37f4e2.jpg">
<br>
<br>
<br><img src="http://dl.javaeye.com/upload/attachment/304568/ff86eead-8ea8-3d25-9fc5-d52c03936d3a.jpg">
<br>
<br> 
<br>
<br>第六章.	Session集群工作步骤
<br>参照tomcat7 doc:
<br>To make it easy to understand how clustering works, We are gonna take you through a series of scenarios. In the scenario we only plan to use two tomcat instances TomcatA and TomcatB. We will cover the following sequence of events:
<br>1.	TomcatA starts up 
<br>2.	TomcatB starts up (Wait that TomcatA start is complete) 
<br>3.	TomcatA receives a request, a session S1 is created. 
<br>4.	TomcatA crashes 
<br>5.	TomcatB receives a request for session S1 
<br>6.	TomcatA starts up 
<br>7.	TomcatA receives a request, invalidate is called on the session (S1) 
<br>8.	TomcatB receives a request, for a new session (S2) 
<br>9.	TomcatA The session S2 expires due to inactivity. 
<br>
<br>第七章.	负载均衡配置
<br>Tomcat有两种负载均衡的方式：
<br>1.	使用 JK1.2.x native connector
<br>2.	使用Apache HTTP Server 2.x with mod_proxy
<br>我们使用的是<span style="color:red">mod_proxy</span>,在Apache Http Server2.2以上版本已经自动带有：
<br>Mod_proxy supports either HTTP or AJP load balancing. 我们通过ajp方式。
<br>
<br>详细查看：tomcat7\webapps\docs\balancer-howto.html
<br>1.	首先,监听8000端口, 在Apache安装目录下找到conf/httpd.conf文件
<br>稍前面加上
<br><pre name="code"># 监听端口和监听地址
Listen 8000</pre>
<br>2.	conf/httpd.conf,去掉以下文本前的注释符(#)以便让Apache在启动时自动加载代理(proxy)模块｡
<br><pre name="code">LoadModule proxy_module modules/mod_proxy.so  
LoadModule proxy_ajp_module modules/mod_proxy_ajp.so  
LoadModule proxy_balancer_module modules/mod_proxy_balancer.so  
LoadModule proxy_connect_module modules/mod_proxy_connect.so  
LoadModule proxy_ftp_module modules/mod_proxy_ftp.so  
LoadModule proxy_http_module modules/mod_proxy_http.so </pre>
<br>
<br>3.	conf/httpd.conf文件最后加上:
<br><pre name="code">#虚拟机配置,负载均衡配置
&lt;VirtualHost *:8000&gt;
	ServerAdmin weigbo@163.com
	ServerName localhost
	ServerAlias localhost
	#小心，有些地方要有空格，要不然会出错哈哈。
	ProxyPass / balancer://cluster/ stickysession=JSESSIONID|jsessionid nofailover=On
	ProxyPassReverse / balancer://cluster/
	#ErrorLog &quot;logs/error.log&quot;
	#CustomLog &quot;logs/access.log&quot; common
&lt;/VirtualHost&gt; 

#The ProxyRequests directive should usually be set off when using ProxyPass.
ProxyRequests Off
&lt;proxy balancer://cluster&gt;
	BalancerMember ajp://localhost:8009 loadfactor=1 route=tomcat7_a  smax=5 max=20 ttl=120 retry=300 timeout=15
	BalancerMember ajp://localhost:9009 loadfactor=1 route=tomcat7_b  smax=5 max=20 ttl=120 retry=300 timeout=15
	# status=+H为配置热备，当所有机器都over时，才会请求该机器
	#BalancerMember http://192.168.1.218:8009 status=+H
	ProxySet lbmethod=bytraffic
&lt;/proxy&gt;</pre>4.	Tomcat7配置(server.xml):
<br>因为是同一机器，两个应用，所以配不同的端口，不同机器则不用配，要和前面的ajp对应上。
<br>&lt;Connector <span style="color:red">port="8009"</span> protocol=&quot;AJP/1.3&quot; redirectPort=&quot;8443&quot; /&gt;
<br>&lt;Connector <span style="color:red">port="9009"</span> protocol=&quot;AJP/1.3&quot; redirectPort=&quot;8443&quot; /&gt;
<br>
<br>第八章.	负载均衡测试
<br>Tomcat7_a的cluster工程的测试文件:testCluster.jsp加上html代码:
<br>&lt;b&gt;负载均衡测试：此为：Tomcat7_a上的文件，&lt;font color=red&gt;aaaaaaaaaaaaaaaaaa&lt;/font&gt;&lt;b&gt;
<br>
<br>Tomcat7_b的cluster工程的测试文件:testCluster.jsp加上html代码
<br>&lt;b&gt;负载均衡测试：此为：Tomcat7_b上的文件，&lt;font color=red&gt;bbbbbbbbbbbbbbbbbb&lt;/font&gt;&lt;b&gt;
<br>
<br>打开浏览器，进入地址：http://localhost:8000/cluster/testCluster.jsp
<br>多刷新几次，会看到页面值会在aaaaa和bbbbb间切换，说明负载均衡配置成功。如图：
<br> 
<br><img src="http://dl.javaeye.com/upload/attachment/304570/acdf49ab-a282-38bb-a8d8-555f6c4cddfe.jpg">
<br>
<br>
<br><img src="http://dl.javaeye.com/upload/attachment/304572/80ea0957-e041-37cd-9a3a-5c497cec6782.jpg">
<br>
<br> 
<br>
<br>第九章.	Mod_proxy负载均衡算法
<br>目前mod_proxy有3种负载均衡算法：
<br>1.	Request Counting(我猜是Round-robin), lbmethod=byrequests
<br>2.	Weighted Traffic Counting(这个是按权重，此例也是用此算法), lbmethod=bytraffic
<br>3.	Pending Request Counting(从apche文档来看，应该是按负载量，也就是往负载少的派发新请求). lbmethod=bytraffic
<br>它们通过lbmethod值设置。
<br>第十章.	参考文档：
<br>Tomcat 7 doc文档
<br>http://httpd.apache.org/docs/2.2/mod/mod_proxy_balancer.html
<br>http://httpd.apache.org/docs/2.2/mod/mod_proxy.html
<br>
<br>
<br>附件中是一些相关的配置文件，大家可以参考：
<br>
<br>
          
  <br><br>
  <ul>
    本文附件下载:
    
      <li><a href="http://dl.javaeye.com/topics/download/3115cf18-19b3-31a0-9b64-b05dfddeb86a">cluster_loadbalance.rar</a> (15.7 KB)</li>
    
  </ul>

          <br><br>
          作者: <a href="http://peterwei.javaeye.com">peterwei</a> 
          <br>
          声明: 本文系JavaEye网站发布的原创文章，未经作者书面许可，严禁任何网站转载本文，否则必将追究法律责任！
          <br><br>
          <span style="color:red">
            <a href="http://www.javaeye.com/topic/757125" style="color:red">已有 <strong>0</strong> 人发表回复，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">JavaEye推荐</span>
<br>
<ul><li><a href="http://www.iteye.com/clicks/138"><span style="color:red;font-weight:bold">上海：高薪诚聘Python开发人员</span></a></li><li><a href="http://www.iteye.com/clicks/269"><span style="color:red;font-weight:bold">北京：手机之家网站诚聘PHP程序员</span></a></li></ul>
<br><br><br>