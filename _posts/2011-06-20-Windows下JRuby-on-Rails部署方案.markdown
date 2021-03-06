---
layout: post
title:  "Windows下JRuby on Rails部署方案"
date:   2011-06-20 10:43:47
author: redouble
categories: program
---

## Windows下JRuby on Rails部署方案
### by redouble
### at 2011-06-20 10:43:47
### original <http://redouble.iteye.com/blog/1098279>

<p>显然，在Windows下面进行Rails的部署不是最佳的解决方案。</p>
<p>但是，做过项目的人都清楚，很多时候，我们无法自由的选择应用部署所在的环境。其中最常见的场景是下面两个：</p>
<p> </p>
<ol>
<li>客户方有严格的IT管理政策，我们除了遵守，别无选择。</li>
<li>我们不得不将应用部署于一台共享的服务器上面，该服务器上面同时运行着多个其他应用。</li>
</ol>
<p>遇到这样的情况，我们不得不考虑如何在Windows下面部署Rails应用。目前看，有两种较好的方式：</p>
<p> </p>
<ol>
<li>在Windows主机上运行一个Linux虚拟机，将Rails应用部署于Linux虚拟机上面。</li>
<li>在Windows上面使用JRuby环境。</li>
</ol>
<p>今天讨论后者。（<span style="color:#339966">后者还有一个小小的好处：对于某些客户，你可以声称正在开发一个标准的java应用。;-)</span>   ）</p>
<h2>一、部署JRuby环境</h2>
<p>在这里，我偷了一点懒，没有使用JRuby安装包，而是用了BitNami JRubyStack（第三方的安装包，该包将自动安装：JavaSDK, Tomcat, MySQL, JRuby, Rails, Glassfish gem, Subversion等等）</p>
<p> </p>
<h3>BitNami JrubyStack</h3>
<p>1.下载BitNami JRubyStack</p>
<p><a href="http://bitnami.org/stack/jrubystack">http://bitnami.org/stack/jrubystack</a></p>
<p>2.Install BitNami JRubyStack</p>
<p>3.Use BitNami JRubyStack （见开始菜单）</p>
<p><span style="color:#ff0000">以后所有的终端命令都应该在该窗口下执行。</span></p>
<p> </p>
<h3>更改mysql默认编码</h3>
<p>mysql的默认编码为latin1。</p>
<p>进入mysql，查看默认编码：</p>
<p> </p>
<pre name="code">$ mysql -u root -p
$ show variables like 'character%';</pre>
 
<p>显示字符编码：</p>
<p> </p>
<pre name="code">+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | latin1                     |
| character_set_connection | latin1                     |
| character_set_database   | latin1                     |
| character_set_filesystem | binary                     |
| character_set_results    | latin1                     |
| character_set_server     | latin1                     |
| character_set_system     | utf8                       |
| character_sets_dir       | C:\PROGRA~1\BITNAM~1\mysql\share\charsets\ |
+--------------------------+----------------------------+</pre>
 
<p>先暂停jrubystackmysql服务：</p>
<p>修改 C:\Program Files\BitNami JRubyStack\mysql\my.ini</p>
<p>在[client]下面加上：</p>
<p> </p>
<pre name="code">default-character-set=utf8  </pre>
 
<p>在[mysqld]下面加上：</p>
<p> </p>
<pre name="code">character-set-server=utf8  
init_connect='SET NAMES utf8'  </pre>
 
<p>保存，退出</p>
<p> </p>
<p>重启jrubystackmysql服务：</p>
<p> </p>
<p>再次查看数据库编码</p>
<p> </p>
<pre name="code">$ mysql -u root -p
$ show variables like 'character%';</pre>
 
<p>显示字符编码：</p>
<p> </p>
<pre name="code">+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | utf8                       |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | utf8                       |
| character_set_system     | utf8                       |
| character_sets_dir       | C:\PROGRA~1\BITNAM~1\mysql\share\charsets\ |
+--------------------------+----------------------------+</pre>
 
<p>启动mysql</p>
<p> </p>
<pre name="code">$ cd c:\program files\bitnami jrubystack\mysql\scripts
$ servicerun.bat START</pre>
 
<p>停止服务</p>
<p> </p>
<pre name="code">$ service.bat STOP</pre>
 
<p>将mysql安装为服务</p>
<p> </p>
<pre name="code">$ serviceinstall.bat</pre>
 
<p> </p>
<h3>Install Rails gems</h3>
<p> </p>
<pre name="code">C:\Documents and Settings\Administrator\BitNami JRubyStack projects&gt;jruby -S gem install activerecord-jdbcsqlite3-adapter ruby-debug</pre>
 
<h2>二、Setup Rails App</h2>
<p> </p>
<pre name="code">$ cd C:\Documents and Settings\UserName\BitNami JRubyStack projects</pre>
<pre name="code">$ jruby -S rails myappname</pre>
 
<p>更改 config/database.yml</p>
<p> </p>
<pre name="code">development:  
  adapter: jdbcsqlite3  </pre>
 
<p>试运行</p>
<p> </p>
<pre name="code">$ jruby script/server</pre>
 
<p> </p>
<p>更改 config/database.yml</p>
<p> </p>
<pre name="code">production:   
  adapter: jdbcmysql  </pre>
 
<p>创建mysql数据库</p>
<p> </p>
<pre name="code">$ jruby -S rake db:migrate RAILS_ENV=production
$ jruby -S rake db:seed RAILS_ENV=production</pre>
<pre name="code">$ jruby script/server -e production</pre>
 
<h2>三、部署Rails应用到产品环境</h2>
<p>产品环境的部署有以下几种方式：</p>
<p>发布war包，使用glassfish gem，使用trinidad gem，使用jetty-rails gem，使用<span><a style="text-decoration:underline;color:#0b59b2" href="http://torquebox.org/">Torquebox</a></span></p>
<p>下面依次讨论</p>
<p> </p>
<h3>I. Tomcat + war</h3>
<p><em><span style="color:#339966">使用这种方式，你将部署一个“纯”java应用</span></em></p>
<p>打开BitNami终端</p>
<p> </p>
<pre name="code">$ cd myappname</pre>
 
<p>使用Warbler gem生成 config/warble.rb 文件</p>
<p> </p>
<pre name="code">$ jruby -S warble config</pre>
 
<p>这个文件中包含了将要生成的war文件所需的配置信息。如果你正在使用第三方的gem，则需要将他们的名字加入到config.gems数组中。</p>
<p> </p>
<p>生成war文件，并将它copy到tomcat的webapps目录</p>
<p> </p>
<pre name="code">$ jruby -S warble war
$ copy jrubystack.war c:\Program Files\BitNami JRubyStack\apache-tomcat\webapps</pre>
<p> </p>
<p>启动Tomcat</p>
<p> </p>
<div> 写道</div>
<div>在终端中进入BitNami主目录 <br>cd C:\Program Files\BitNami JRubyStack\<br>启动服务<br>servicerun.bat START<br>停止服务<br>servicerun.bat STOP</div>
 
<p>访问如下地址</p>
<p>http://localhost:8080/myappname</p>
<p>（Tomcat可能需要20秒钟的启动时间）</p>
<p> </p>
<h3>II. Glassfish gem</h3>
<p>不推荐这种部署方式。（我有一个在Oracle工作的哥们儿告诉我：不要再对Glassfish抱什么希望了⋯⋯）</p>
<p> </p>
<h3>III. Trinidad gem + Apache</h3>
<p>这种方式是基于Tomcat的。</p>
<p> </p>
<p> </p>
<pre name="code">$ jruby -S gem install trinidad
$ jruby -S trinidad -e production</pre>
 
<p>Trinidad配置文件</p>
<p>myappname\config.yml</p>
<p> </p>
<pre name="code">---  
  port: 8088  # port where trinidad is running  
  ajp:            # ajp configuration section  
    port: 8099  
    allowTrace: true&lt;/code&gt;  
  
  environment: production  # environment for rails applications  </pre>
 
<p>可以将trinidad安装为系统服务 </p>
<p><span style="color:#ff0000">（目前这一步总是出现这样那样的问题，还没有实验成功过。美中不足。）</span></p>
<p> </p>
<pre name="code">$ jruby -S gem install trinidad_daemon
$ jruby -S trinidad_daemon_install</pre>
 
<p><em><span style="color:#339966">另一个插件 trinidad_daemon_extension 不支持windows系统</span></em></p>
<p> </p>
<h5>配置Apache</h5>
<p>1.安装APACHE</p>
<p>2.更改配置文件</p>
<p>用编辑工具打开Apache2.2目录下面的conf/httpd.conf，需要取消如下模块的注释：</p>
<p> </p>
<pre name="code">LoadModule proxy_module modules/mod_proxy.so    
LoadModule proxy_balancer_module modules/mod_proxy_balancer.so    
LoadModule proxy_ajp_module modules/mod_proxy_ajp.so  </pre>
 
<p>如果你希望对页面输出使用压缩，也需要取消如下模块的注释：</p>
<p> </p>
<pre name="code">LoadModule deflate_module modules/mod_deflate.so    </pre>
 
<p>然后按如下内容配置基于HTTP代理的负载均衡：</p>
<p>在httpd.conf文件的最后加入 xml 代码</p>
<p> </p>
<pre name="code">ProxyRequests Off       
&lt;Proxy balancer://trini&gt;  
  BalancerMember ajp://127.0.0.1:8099  
&lt;/Proxy&gt;  
          
&lt;VirtualHost *:8080&gt;      
  ServerName localhost       
  DocumentRoot &quot;C:\Documents and Settings\Administrator\BitNami JRubyStack projects\myappname\public&quot;      
  ProxyPass /images !       
  ProxyPass /stylesheets !       
  ProxyPass /javascripts !       
  ProxyPass / balancer://trini/       
  ProxyPassReverse / balancer://trini/       
  ProxyPreserveHost on       
&lt;/VirtualHost&gt;  
</pre>
 
<p>trini定义了群集中的每个trinidad应用服务器节点。</p>
<p>ProxyPass /images !指明该URL开始的请求不代理给trinidad群集，而由Apache自己处理。</p>
<div><br></div>
<div>
<h3>IV. Jetty-rails（推荐）</h3>
<p>这种方式是基于jetty服务器的</p>
<div>1.安装</div>
<div>
<pre name="code">$ jruby -S gem install jetty-rails</pre>
 </div>
<div>2.运行</div>
<div>
<pre name="code">$ cd myrailsapp
$ jruby -S jetty_rails</pre>
 </div>
<div>3.配置文件</div>
<div>
<pre name="code">$ jruby -S jetty_rails -c path/to/config.yml</pre>
 </div>
<div>如果没有 -c 参数，jetty_rails将默认使用config/jetty_rails.yml文件。</div>
<div><br></div>
<div>在 config/environment.rb 文件中加入如下代码（必须）</div>
<pre name="code">ActionController::AbstractRequest.relative_url_root = "/testA"</pre>
<div>   </div>
<div>JRuby 配置</div>
<div>
<pre name="code">jruby_min_runtimes: 1  
jruby_max_runtimes: 2  </pre>
 </div>
<div>Jetty 配置</div>
<div>
<pre name="code">thread_pool_max: 40  
thread_pool_min: 1  </pre>
<br>
</div>
<div>The acceptor size is the number of acceptor threads available for that server's channel connector.</div>
</div>
<div>
<pre name="code">acceptor_size: 20</pre>
</div>
<p> </p>
<h5>Apache</h5>
<div><span style="color:#ff0000">jetty-rails不需要与Apache进行集成！</span></div>
<div>详见： <a href="http://wiki.eclipse.org/Jetty/Tutorial/Apache">http://wiki.eclipse.org/Jetty/Tutorial/Apache</a>
</div>
<div><br></div>
<h3>V. Torquebox</h3>
<div>Torquebox是基于jboss的完整解决方案。但是由于安装包巨大（300M），目前还没有机会尝试。</div>
          
          <br><br>
          <span style="color:red">
            <a href="http://redouble.iteye.com/blog/1098279#comments" style="color:red">已有 <strong>0</strong> 人发表留言，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">ITeye推荐</span>
<br>
<ul><li><a href="http://www.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>