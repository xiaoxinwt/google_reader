---
layout: post
title:  "通过eclipse调试MapReduce任务"
date:   2012-06-08 09:16:11
author: dafu
categories: program
---

## 通过eclipse调试MapReduce任务
### by dafu
### at 2012-06-08 09:16:11
### original <http://rdc.taobao.com/team/jm/archives/1801>

<div>
<p>利用MapReduce利器作开发，不论从思想上还是技能上都是新的体验。以下罗列下如何利用eclipse调试MR任务。</p>
<p>(本人环境：<a href="http://www.apache.org/dyn/closer.cgi/hadoop/common/">hadoop 1.0.2</a>，部署在linux上，本地windows开发)</p>
<p>1、安装hadoop。</p>
<p>先在linux上安装好hadoop，为更接近线上环进，我的是<a href="http://hadoop.apache.org/common/docs/current/hadoop-yarn/hadoop-yarn-site/ClusterSetup.html">安装成Cluster</a></p>
<p>注意要远程访问相关端口，conf/mapred-site.xml中localhost:9001中需要换成ip加端口。</p>
<p>sh bin/start-all.sh启动，先按文档命令行跑一下example的wordcount。</p>
<p>我安装好运行example遇到了XML解析的错(原因应该是${java.home}/lib/jaxp.properties没有设置，见:javax.xml.xpath.XPathFactory.newInstance(uri)的注释)，后来手动加了xalan-j_2.7.0.jar、xercesImpl-2.7.1.jar、xml-apis-2.7.1.jar、xmlenc-0.52.jar、serializer-2.7.1.jar几个包解决。</p>
<p>2、下载<a href="http://code.google.com/p/hadoop-eclipse-plugin/downloads/list">hadoop eclipse plugin</a> (最新只有0.20.3，不过也能使用)，将jar放到eclipse/plugins下，启动eclipse</p>
<p>这个插件比较简陋，只有一个设置项:Window-&gt;Preferences-&gt;Hadoop Map/Reduce，设置Hadoop的安装路径，解压一份跟运行hadoop同版本的到本机并指向就行了(我的使用smb映射)</p>
<p>建ecplise工程，把WordCount的示例代码复制一份吧，有些hadoop相关的jar需要引用。</p>
<p>Run As-&gt;Run On Hadoop设置Map/Reduce Master的IP，端口为9000, DFS Master端口为9001</p>
<p>这样，不出意外的话就能在eclipse里运行Map/Reduce程序了。</p>
<p>3、打开debug。</p>
<p>因为Map/Reduce程序会被打成一个jar并拷到TaskTracker机器上(单独的进程)并开一个Child进程来跑JVM的，配置加到bin/下的脚本都是针对hadoop本身的进程，正确的配置如下：</p>
<p>在conf/mapred-site.xml中添加以下配置项：</p>
<blockquote><p>        &lt;property&gt;<br>
                &lt;name&gt;mapred.child.java.opts&lt;/name&gt;<br>
                &lt;!–&lt;value&gt;-Xmx200m -Xdebug -Xrunjdwp:transport=dt_socket,address=7788,server=y,suspend=y&lt;/value&gt;–&gt;<br>
                &lt;value&gt;-Xmx200m&lt;/value&gt;<br>
        &lt;/property&gt;</p>
<p>        &lt;property&gt;<br>
                &lt;name&gt;mapred.tasktracker.map.tasks.maximum&lt;/name&gt;<br>
                &lt;value&gt;1&lt;/value&gt;<br>
                &lt;description&gt;tasktracker的map任务上限&lt;/description&gt;<br>
        &lt;/property&gt;</p>
<p>        &lt;property&gt;<br>
                &lt;name&gt;mapred.tasktracker.reduce.tasks.maximum&lt;/name&gt;<br>
                &lt;value&gt;1&lt;/value&gt;<br>
                &lt;description&gt;tasktracker的reduce任务上限&lt;/description&gt;<br>
        &lt;/property&gt;</p>
<p>        &lt;property&gt;<br>
                &lt;name&gt;mapred.task.timeout&lt;/name&gt;<br>
                &lt;value&gt;100000000&lt;/value&gt;<br>
        &lt;/property&gt;</p>
</blockquote>
<p>其中的mapred.child.java.opts会在启动Child进程时设为启动参数。suspend=y取y/n分别表示是否挂起进程等待debug连进来。当然，这个选项还可以方便加其它JVM参数。</p>
<p>重启hadoop，这样运行示例，便可以看到7788端口被监听了。以下几条命令可能会用到：</p>
<blockquote><p>./bin/stop-all.sh<br>
sleep 1s<br>
./bin/start-all.sh<br>
sleep 1s<br>
./bin/hadoop dfsadmin -safemode leave<br>
#杀掉hadoop进程或某些异常情况下hadoop会安全模式中不退出，HDFS的文件是不能被修改的(运行示例会无任何输出，但从日志中可以看到异常)这个命令强制退出。<br>
./bin/hadoop dfs -rmr /user/dafu/out.txt<br>
echo done</p>
<p> </p>
</blockquote>
<p>以上只在linux的命令行运行有效。eclipse插件其实是自己作为JobClient在运行，从eclipse中运行时会自己生成一个hadoop-site.xml文件(新版hadoop拆分为3个了，不过不影响执行)，而且，在插件GUI设置界面，居然找不到mapred.child.java.opts等配置项，手工打开：workspace/.metadata\.plugins\org.apache.hadoop.eclipse，可以看到这里存放了插件帮我们打好的jar，在hadoop-conf-132297972833171678里可以找到执行的配置。打开目发locations里边的xml文件（hadoop-site.xml的模板文件），搜索mapred.child.java.opts并修改为前面设置的值。</p>
<p>接下来先 Run As-&gt;Run On Hadoop，再用eclipse远程连7788端口进行debug即可。</p>
<p>注：多个TaskTracker，可以断多台，应该是一样。</p>
</div>