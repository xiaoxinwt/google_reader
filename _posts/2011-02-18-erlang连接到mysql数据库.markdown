---
layout: post
title:  "erlang连接到mysql数据库"
date:   2011-02-18 14:11:11
author: 
categories: program
---

## erlang连接到mysql数据库
### by 
### at 2011-02-18 14:11:11
### original <http://www.javaeye.com/topic/910030>

<p><strong>erlang连接到mysql数据库</strong></p>
<p> </p>
<p> </p>
<p><span lang="EN-US"><strong>一，</strong></span><span><strong>驱动的下载与安装</strong></span></p>
<p style="margin-left:21.0pt;text-indent:0cm"><span lang="EN-US"> </span></p>
<p><span><span style="font-size:12.0pt" lang="EN-US"><span>1.<span style="font:7.0pt">       
</span></span></span></span><span>下载</span><span style="font-size:12.0pt" lang="EN-US">e</span><span><span lang="EN-US">rlang-mysql-driver</span></span><span></span></p>
<p style="margin-left:42.0pt;text-indent:0cm"><span><span lang="EN-US"><span>     </span></span></span><span><span>下载地址如下：</span></span><span></span></p>
<p style="margin-left:42.0pt;text-indent:0cm"><span><span lang="EN-US"><span>     </span></span></span><span><span lang="EN-US">svn checkout http://erlang-mysql-driver.googlecode.com/svn/trunk/
<span>    </span>mysql</span></span></p>
<p style="margin-left:42.0pt;text-indent:0cm"><span><span lang="EN-US"> <span style="color:#339966"><strong>(如果你没有安装SVN，附件中提供源码下载)</strong></span></span></span></p>
<p><span><span lang="EN-US"><span>2.<span style="font:7.0pt">    
</span></span></span></span><span><span lang="EN-US"><span>      </span></span></span><span><span>对</span></span><span><span lang="EN-US">src</span></span><span><span>下面的所有源码进行编译</span></span><span></span></p>
<p style="margin-left:42.0pt;text-indent:0cm"><span><span lang="EN-US"><span>      </span>erlc *.erl</span></span></p>
<p style="margin-left:21.0pt;text-indent:0cm"><span><span lang="EN-US"> </span></span></p>
<p><span><span lang="EN-US"><span>3.<span style="font:7.0pt">    
</span></span></span></span><span><span lang="EN-US"><span>      </span></span></span><span><span>然后在</span></span><span><span lang="EN-US">mysql </span></span><span><span>下面创建</span></span><span><span lang="EN-US">ebin</span></span><span><span>，并将</span></span><span><span lang="EN-US">src </span></span><span><span>下面的所有</span></span><span><span lang="EN-US">*.beam</span></span><span><span>拷贝到</span></span><span><span lang="EN-US">ebin<span>     </span></span></span><span><span>下面</span></span><span></span></p>
<p><span><span lang="EN-US"><span>4.<span style="font:7.0pt">    
</span></span></span></span><span><span lang="EN-US"><span>      </span></span></span><span><span>将</span></span><span><span lang="EN-US">mysql</span></span><span><span>整个文件夹拷贝到</span></span><span><span lang="EN-US">erlang/lib</span></span><span><span>下面</span></span><span></span></p>
<p style="margin-left:21.0pt;text-indent:0cm"><span><span lang="EN-US"> </span></span></p>
<p><span><span lang="EN-US"><strong>二，</strong><span style="font:7.0pt"><strong>
</strong></span></span></span><span><span><strong>操作数据库</strong></span></span><span></span></p>
<p><span><span lang="EN-US"><span>1<span style="font:7.0pt">               
</span></span></span></span><span><span><strong>连接</strong></span></span><span><span lang="EN-US"><strong>mysql:</strong></span></span></p>
<p style="margin-left:63.0pt;text-indent:0cm"><span><span lang="EN-US">mysql:start_link(</span></span><span lang="EN-US">PoolId</span><span><span lang="EN-US">,
"localhost", "root", "password", "databasename").</span></span></p>
<p style="margin-left:63.0pt;text-indent:0cm"><span lang="EN-US"> </span></p>
<p><span lang="EN-US"><span style="white-space:pre">	</span>2<span>          </span>mysql:connect(PoolId,
Host, ?PORT, User, Password, Database, undefined,</span></p>
<p style="margin-left:21.0pt;text-indent:0cm"><span lang="EN-US"><span>         </span><span>   
</span>Reconnect).<strong>%%</strong></span><span><strong>建立一个连接</strong></span></p>
<p style="margin-left:21.0pt;text-indent:0cm"><span lang="EN-US"> </span></p>
<p style="margin-left:21.0pt"><span lang="EN-US">3<span>           </span></span><span><strong>数据操作</strong></span></p>
<p style="margin-left:21.0pt"><span lang="EN-US"><span>                   </span></span><span><strong>添加：</strong></span></p>
<p style="margin-left:63.0pt;text-indent:0cm"><span lang="EN-US">mysql:fetch(PoolId,&lt;&lt;”insert into….”&gt;&gt;).</span></p>
<p style="margin-left:63.0pt;text-indent:0cm"><span>   <strong>查询：</strong></span></p>
<p style="margin-left:63.0pt;text-indent:0cm"><span lang="EN-US">mysql:fetch(p1,&lt;&lt;&quot;select * from
user&quot;&gt;&gt;).</span></p>
<p style="margin-left:63.0pt;text-indent:0cm"> </p>
<p style="margin-left:63.0pt;text-indent:0cm">
</p>
<p style="margin-left:21pt;text-indent:0cm;text-align:left"><span lang="EN-US"><strong>4                </strong></span><span><strong>接收到数据后怎么处理</strong></span></p>
<p style="margin-left:21pt;text-align:left"><span lang="EN-US"><span>                   </span>fetch(PoolId,
&quot;select * from user&quot;) -&gt; Result</span></p>
<p style="margin-left:21pt;text-align:left"><span lang="EN-US"><span>                   </span>%%%<span>     </span>Result = {data, MySQLRes} | {updated,
MySQLRes} |</span></p>
<p style="margin-left:21pt;text-indent:0cm;text-align:left"><span lang="EN-US"><span>                            </span>%%%<span>     </span>{error, MySQLRes}</span></p>
<p style="margin-left:21pt;text-indent:0cm;text-align:left"><span lang="EN-US"> </span></p>
<p><span style="font-family:Wingdings" lang="EN-US"><span>n<span style="font:7.0pt &quot;Times New Roman&quot;">  </span></span></span><span><strong>查询</strong>：</span><span lang="EN-US">{data,
MySQLRes} = fetch(PoolId, &lt;&lt;&quot;select * from user&quot;&gt;&gt;).</span></p>
<p><span style="font-family:Wingdings" lang="EN-US"><span>Ø<span style="font:7.0pt &quot;Times New Roman&quot;">  </span></span></span><span>获取字段名称信息</span><span lang="EN-US">:</span></p>
<p style="margin-left:42pt;text-indent:0cm;text-align:left"><span lang="EN-US"><span>         </span><span>  
</span>FieldInfo = mysql:get_result_field_info(MysqlRes).</span></p>
<p><span style="font-family:Wingdings" lang="EN-US"><span>Ø<span style="font:7.0pt &quot;Times New Roman&quot;">  </span></span></span><span>获取字段值：</span></p>
<p style="margin-left:42pt;text-indent:0cm;text-align:left"><span lang="EN-US"><span>         </span><span>  
</span>AllRows<span>   </span>=
mysql:get_result_rows(MysqlRes)</span></p>
<p style="margin-left:21pt;text-indent:0cm;text-align:left"><span lang="EN-US"><span>                   </span></span></p>
<p><span style="font-family:Wingdings" lang="EN-US"><span>n<span style="font:7.0pt &quot;Times New Roman&quot;">  </span></span></span><span><strong>更新</strong></span><span lang="EN-US"><strong>:</strong></span></p>
<p style="margin-left:78.15pt;text-indent:0cm;text-align:left"><span lang="EN-US">{updated,UD} = mysql: fetch(PoolId, &lt;&lt;&quot;update
<span> </span>* from user&quot;&gt;&gt;).</span></p>
<p style="margin-left:21pt;text-indent:0cm;text-align:left"><span lang="EN-US"><span>         </span><span>  
</span><span>  </span><span>   </span>AffectedRow = mysql:get_result_affected_rows(UD).%%</span><span>获得影响的记录数</span></p>
<p style="margin-left:21pt;text-indent:0cm;text-align:left"><span lang="EN-US"> </span></p>
<p><span style="font-family:Wingdings" lang="EN-US"><span>n<span style="font:7.0pt &quot;Times New Roman&quot;"> <strong> </strong></span></span></span><span><strong>出错</strong></span></p>
<p style="margin-left:78.15pt;text-indent:0cm;text-align:left"><span lang="EN-US">{error,Err} = fetch(PoolId, &lt;&lt;&quot;update
<span> </span>* from user&quot;&gt;&gt;).</span></p>
<p style="margin-left:78.15pt;text-indent:0cm;text-align:left"><span lang="EN-US">Reason<span>    </span>= mysql:get_result_reason(MysqlRes).%%</span><span>出错原因</span></p>

          
  <br><br>
  <ul>
    本文附件下载:
    
      <li><a href="http://dl.javaeye.com/topics/download/800eb6be-6370-3bcf-b942-d128713d0855">erlang-mysql-driver-read-only.rar</a> (46.4 KB)</li>
    
  </ul>

          <br><br>
          作者: <a href="http://wolfdream.javaeye.com">dxm1986</a> 
          <br>
          声明: 本文系JavaEye网站发布的原创文章，未经作者书面许可，严禁任何网站转载本文，否则必将追究法律责任！
          <br><br>
          <span style="color:red">
            <a href="http://www.javaeye.com/topic/910030" style="color:red">已有 <strong>0</strong> 人发表回复，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">JavaEye推荐</span>
<br>
<ul><li><a href="http://www.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>