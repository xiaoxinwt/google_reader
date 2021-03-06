---
layout: post
title:  "HTML 中表格table 的相关知识"
date:   2010-08-11 14:37:00
author: 钢钢
categories: program
---

## HTML 中表格table 的相关知识
### by 钢钢
### at 2010-08-11 14:37:00
### original <http://www.cnblogs.com/xugang/archive/2010/08/11/1797305.html>

<p><a href="http://www.cnblogs.com/xugang/"><img src="http://pic.cnblogs.com/face/u24048.jpg" alt="" border="0"></a><br>作者: <a href="http://www.cnblogs.com/xugang/">钢钢</a> 发表于 2010-08-11 14:37 <a href="http://www.cnblogs.com/xugang/archive/2010/08/11/1797305.html">原文链接</a> 阅读: 1460 评论: 17</p><p style="line-height:20px">  </p>
<p> </p>
<p><font color="#ff0000" size="5">&lt;table&gt;标签中比较少见的属性和子标签：</font></p>
<p><font size="4"><strong>summary 属性：</strong>用于概括整个表格的内容。它对于搜索引擎的机器人记录信息十分重要。</font></p>
<p><font size="4"><strong>bordercolor 属性：</strong>用来设置表格边框的颜色。但它在不同的浏览器下显示的效果不一致。</font></p>
<p><font size="4">                            （不推荐使用bordercolor 属性，而推荐使用CSS 样式表的border 属性来进行设置）</font></p>
<p><font size="4"><strong>cellspacing 属性：</strong>用来设置表格的单元格之间的间距。</font></p>
<p><font size="4">                           （推荐使用CSS 样式表的border-collapse 属性来进行设置）</font></p>
<p><font size="4"><strong>&lt;caption&gt; 标记：</strong>表示表格的大标题，该标记可以出现在&lt;table&gt; 之间的任意位置。</font></p>
<p><font size="4">                           它对于搜索引擎的机器人记录信息十分重要。</font></p>
<p><font size="4"><strong>&lt;th&gt; 标记：</strong>用于表示表格的行或者列的名称。</font></p>
<p><font size="4"><strong>&lt;th&gt; 标记的scope 属性</strong>：专门用来区分行名称和列名称。如：&lt;th  scope=&#39;row&#39;&gt; 或 &lt;th  scope=&#39;col&#39;&gt; </font></p>
<p><font size="4"><strong>&lt;table&gt;里还包含：&lt;thead&gt; 、&lt;tbody&gt; 、&lt;tfoot&gt; 标记。</strong>它们分别表示表格的表头，表正文，表脚。<br>
             在打印网页内容的时候，如果表格很大，一页打印不完，&lt;thead&gt;和&lt;tfoot&gt;将在每一页出现。</font></p>
<p><font size="4">            （注意：在IE 中无效，但在 Firefox 有效）</font></p>
<p style="line-height:20px">  </p>
<p> </p>
<p>经典的表格代码如下：</p>
<div style="border:1px solid #999999;padding-left:18px;font-size:12px;width:700px;line-height:14px;font-family:courier new;background-color:#eeeeee">
<table style="border:0px none;padding:0px;width:100%" cellspacing="0">
     <tbody>
         <tr>
             <td><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">html</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">head</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">title</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">财政报表</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/title</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000;background-color:#ffff00">&lt;style type=&quot;text/css&quot;&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#8b0000">&lt;!--</span></td>
         </tr>
         <tr>
             <td><span style="color:#8b0000">.datalist</span><span style="color:#8b0000">{</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">border</span><span style="color:#0000ff">:</span><span style="color:#0000ff">1px</span><span style="color:#0000ff"> </span><span style="color:#0000ff">solid</span><span style="color:#0000ff"> </span><span style="color:#0000ff">#429fff</span><span style="color:#0000ff">;</span><span style="color:#ff0000">    </span><span style="color:#008000">/*</span><span style="color:#008000"> </span><span style="color:#008000">表格边框</span><span style="color:#008000"> </span><span style="color:#008000">*/</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">font-family</span><span style="color:#0000ff">:</span><span style="color:#0000ff">Arial</span><span style="color:#0000ff">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">border-collapse</span><span style="color:#0000ff">:</span><span style="color:#0000ff">collapse</span><span style="color:#0000ff">;</span><span style="color:#ff0000">    </span><span style="color:#008000">/*</span><span style="color:#008000"> </span><span style="color:#008000">边框重叠</span><span style="color:#008000"> </span><span style="color:#008000">*/</span></td>
         </tr>
         <tr>
             <td><span style="color:#8b0000">}</span></td>
         </tr>
         <tr>
             <td><span style="color:#8b0000">.datalist</span><span style="color:#8b0000"> </span><span style="color:#8b0000">tr:hover</span><span style="color:#8b0000">{</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000"> </span><span style="color:#ff0000"> </span><span style="color:#ff0000"> </span><span style="color:#ff0000"> </span><span style="color:#ff0000">background-color</span><span style="color:#0000ff">:</span><span style="color:#0000ff">#c4e4ff</span><span style="color:#0000ff">;</span><span style="color:#ff0000"> </span><span style="color:#ff0000"> </span><span style="color:#ff0000"> </span><span style="color:#008000">/*</span><span style="color:#008000"> </span><span style="color:#008000">动态变色,IE6下无效！</span><span style="color:#008000">*/</span></td>
         </tr>
         <tr>
             <td><span style="color:#8b0000">}</span></td>
         </tr>
         <tr>
             <td><span style="color:#8b0000">.datalist</span><span style="color:#8b0000"> </span><span style="color:#8b0000">caption</span><span style="color:#8b0000">{</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">padding-top</span><span style="color:#0000ff">:</span><span style="color:#0000ff">3px</span><span style="color:#0000ff">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">padding-bottom</span><span style="color:#0000ff">:</span><span style="color:#0000ff">2px</span><span style="color:#0000ff">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">font</span><span style="color:#0000ff">:</span><span style="color:#0000ff">bold</span><span style="color:#0000ff"> </span><span style="color:#0000ff">1.1em</span><span style="color:#0000ff">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">background-color</span><span style="color:#0000ff">:</span><span style="color:#0000ff">#f0f7ff</span><span style="color:#0000ff">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">border</span><span style="color:#0000ff">:</span><span style="color:#0000ff">1px</span><span style="color:#0000ff"> </span><span style="color:#0000ff">solid</span><span style="color:#0000ff"> </span><span style="color:#0000ff">#429fff</span><span style="color:#0000ff">;</span><span style="color:#ff0000">    </span><span style="color:#008000">/*</span><span style="color:#008000"> </span><span style="color:#008000">表格标题边框</span><span style="color:#008000"> </span><span style="color:#008000">*/</span></td>
         </tr>
         <tr>
             <td><span style="color:#8b0000">}</span></td>
         </tr>
         <tr>
             <td><span style="color:#8b0000">.datalist</span><span style="color:#8b0000"> </span><span style="color:#8b0000">th</span><span style="color:#8b0000">{</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">border</span><span style="color:#0000ff">:</span><span style="color:#0000ff">1px</span><span style="color:#0000ff"> </span><span style="color:#0000ff">solid</span><span style="color:#0000ff"> </span><span style="color:#0000ff">#429fff</span><span style="color:#0000ff">;</span><span style="color:#ff0000">    </span><span style="color:#008000">/*</span><span style="color:#008000"> </span><span style="color:#008000">行、列名称边框</span><span style="color:#008000"> </span><span style="color:#008000">*/</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">background-color</span><span style="color:#0000ff">:</span><span style="color:#0000ff">#d2e8ff</span><span style="color:#0000ff">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">font-weight</span><span style="color:#0000ff">:</span><span style="color:#0000ff">bold</span><span style="color:#0000ff">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">padding-top</span><span style="color:#0000ff">:</span><span style="color:#0000ff">4px</span><span style="color:#0000ff">;</span><span style="color:#ff0000"> </span><span style="color:#ff0000">padding-bottom</span><span style="color:#0000ff">:</span><span style="color:#0000ff">4px</span><span style="color:#0000ff">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">padding-left</span><span style="color:#0000ff">:</span><span style="color:#0000ff">10px</span><span style="color:#0000ff">;</span><span style="color:#ff0000"> </span><span style="color:#ff0000">padding-right</span><span style="color:#0000ff">:</span><span style="color:#0000ff">10px</span><span style="color:#0000ff">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">text-align</span><span style="color:#0000ff">:</span><span style="color:#0000ff">center</span><span style="color:#0000ff">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#8b0000">}</span></td>
         </tr>
         <tr>
             <td><span style="color:#8b0000">.datalist</span><span style="color:#8b0000"> </span><span style="color:#8b0000">td</span><span style="color:#8b0000">{</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">border</span><span style="color:#0000ff">:</span><span style="color:#0000ff">1px</span><span style="color:#0000ff"> </span><span style="color:#0000ff">solid</span><span style="color:#0000ff"> </span><span style="color:#0000ff">#429fff</span><span style="color:#0000ff">;</span><span style="color:#ff0000">    </span><span style="color:#008000">/*</span><span style="color:#008000"> </span><span style="color:#008000">单元格边框</span><span style="color:#008000"> </span><span style="color:#008000">*/</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">text-align</span><span style="color:#0000ff">:</span><span style="color:#0000ff">right</span><span style="color:#0000ff">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">    </span><span style="color:#ff0000">padding</span><span style="color:#0000ff">:</span><span style="color:#0000ff">4px</span><span style="color:#0000ff">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#8b0000">}</span></td>
         </tr>
         <tr>
             <td><span style="color:#8b0000">--&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000;background-color:#ffff00">&lt;/style&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/head</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">body</span><span style="color:#0000ff">&gt;</span><span style="color:#000000"> </span></td>
         </tr>
         <tr>
             <td><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">table</span><span style="color:#ff0000"> class</span><span style="color:#8b0000">=</span><span style="color:#0000ff">"</span><span style="color:#0000ff">datalist</span><span style="color:#0000ff">"</span><span style="color:#ff0000"> summary</span><span style="color:#8b0000">=</span><span style="color:#0000ff">"</span><span style="color:#0000ff">财政报表</span><span style="color:#0000ff">"</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">caption</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">财政报表</span><span style="color:#000000"> </span><span style="color:#000000">2005</span><span style="color:#000000"> </span><span style="color:#000000">-</span><span style="color:#000000"> </span><span style="color:#000000">2008</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/caption</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">thead</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">tr</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">th</span><span style="color:#0000ff">&gt;</span><strong><span style="color:#ff0000">&amp;nbsp;</span></strong><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/th</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">th</span><span style="color:#ff0000"> scope</span><span style="color:#8b0000">=</span><span style="color:#0000ff">"</span><span style="color:#0000ff">col</span><span style="color:#0000ff">"</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">2005</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/th</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">th</span><span style="color:#ff0000"> scope</span><span style="color:#8b0000">=</span><span style="color:#0000ff">"</span><span style="color:#0000ff">col</span><span style="color:#0000ff">"</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">2006</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/th</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">th</span><span style="color:#ff0000"> scope</span><span style="color:#8b0000">=</span><span style="color:#0000ff">"</span><span style="color:#0000ff">col</span><span style="color:#0000ff">"</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">2007</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/th</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">th</span><span style="color:#ff0000"> scope</span><span style="color:#8b0000">=</span><span style="color:#0000ff">"</span><span style="color:#0000ff">col</span><span style="color:#0000ff">"</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">2008</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/th</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/tr</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000"> </span><span style="color:#000000"> </span><span style="color:#000000"> </span><span style="color:#000000"> </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/thead</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">tbody</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">tr</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">th</span><span style="color:#ff0000"> scope</span><span style="color:#8b0000">=</span><span style="color:#0000ff">"</span><span style="color:#0000ff">row</span><span style="color:#0000ff">"</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">拨款</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/th</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">11,980</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">12,650</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">9,700</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">10,600</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/tr</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">tr</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">th</span><span style="color:#ff0000"> scope</span><span style="color:#8b0000">=</span><span style="color:#0000ff">"</span><span style="color:#0000ff">row</span><span style="color:#0000ff">"</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">捐款</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/th</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">4,780</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">4,989</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">6,700</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">6,590</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/tr</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">tr</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">th</span><span style="color:#ff0000"> scope</span><span style="color:#8b0000">=</span><span style="color:#0000ff">"</span><span style="color:#0000ff">row</span><span style="color:#0000ff">"</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">投资</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/th</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">8,000</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">8,100</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">8,760</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">8,490</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/tr</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">tr</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">th</span><span style="color:#ff0000"> scope</span><span style="color:#8b0000">=</span><span style="color:#0000ff">"</span><span style="color:#0000ff">row</span><span style="color:#0000ff">"</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">募捐</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/th</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">3,200</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">3,120</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">3,700</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">4,210</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/tr</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000"> </span><span style="color:#000000"> </span><span style="color:#000000"> </span><span style="color:#000000"> </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/tbody</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">tfoot</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">tr</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000"> </span><span style="color:#000000"> </span><span style="color:#000000"> </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">td</span><span style="color:#ff0000"> colspan</span><span style="color:#8b0000">=</span><span style="color:#0000ff">"</span><span style="color:#0000ff">5</span><span style="color:#0000ff">"</span><span style="color:#0000ff">&gt;</span><span style="color:#000000">2008</span><span style="color:#000000"> </span><span style="color:#000000">年统计</span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/td</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/tr</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/tfoot</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/table</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/body</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#0000ff">&lt;</span><span style="color:#8b0000">/html</span><span style="color:#0000ff">&gt;</span></td>
         </tr>
     </tbody>
</table>
</div>
<p>显示效果如下：</p>

<table>
     <caption>财政报表 2005 - 2008</caption>
     <thead>
         <tr>
             <th> </th> <th>2005</th> <th>2006</th> <th>2007</th> <th>2008</th>
         </tr>
     </thead>
     <tbody>
         <tr>
             <th>拨款</th>
             <td>11,980</td>
             <td>12,650</td>
             <td>9,700</td>
             <td>10,600</td>
         </tr>
         <tr>
             <th>捐款</th>
             <td>4,780</td>
             <td>4,989</td>
             <td>6,700</td>
             <td>6,590</td>
         </tr>
         <tr>
             <th>投资</th>
             <td>8,000</td>
             <td>8,100</td>
             <td>8,760</td>
             <td>8,490</td>
         </tr>
         <tr>
             <th>募捐</th>
             <td>3,200</td>
             <td>3,120</td>
             <td>3,700</td>
             <td>4,210</td>
         </tr>
         <tr>
             <th>销售</th>
             <td>28,400</td>
             <td>27,100</td>
             <td>27,950</td>
             <td>29,050</td>
         </tr>
         <tr>
             <th>杂费</th>
             <td>2,100</td>
             <td>1,900</td>
             <td>1,300</td>
             <td>1,760</td>
         </tr>
         <tr>
             <th>总计</th>
             <td>58,460</td>
             <td>57,859</td>
             <td>58,110</td>
             <td>60,700</td>
         </tr>
     </tbody>
     <tfoot>
     <tr>
         <td colspan="5">2008 年统计</td>
     </tr>
     </tfoot>
</table>
<p><font color="#ff0000">注意：</font></p>
<p>IE6 只有&lt;a&gt;标记支持:hover 伪类，其余标签都不支持！</p>
<p>并且&lt;a&gt;标记的伪类有顺序：a:link -&gt; a:visited -&gt; a:hover -&gt; a:active</p>
<p style="line-height:40px"><br>
</p>
<p> </p>
<p><font color="#ff0000" size="5">利用DOM 的方法和属性实现对表格的动态操作</font></p>
<p style="line-height:20px">  </p>
<p> </p>
<p style="line-height:20px"><font color="#0000ff" size="4">A  利用DOM 动态添加一行</font> </p>
<div style="border:1px solid #999999;padding-left:18px;font-size:12px;width:700px;line-height:14px;font-family:courier new;background-color:#eeeeee">
<table style="border:0px none;padding:0px;width:100%" cellspacing="0">
     <tbody>
         <tr>
             <td><span style="color:#ff0000">&lt;</span><span style="color:#000000">script</span><span style="color:#000000"> </span><span style="color:#ff00ff">language</span><span style="color:#ff0000">=</span><span style="color:#4682b4">"</span><span style="color:#4682b4">javascript</span><span style="color:#4682b4">"</span><span style="color:#ff0000">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff00ff">window</span><span style="color:#ff0000">.</span><span style="color:#ff00ff">onload</span><span style="color:#ff0000">=</span><strong><span style="color:#0000ff">function</span></strong><span style="color:#ff0000">(</span><span style="color:#ff0000">)</span><span style="color:#000000">{</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000"> </span><span style="color:#000000"> </span><span style="color:#000000"> </span><span style="color:#000000"> </span><span style="color:#008000">//</span><span style="color:#008000">插入一行</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><strong><span style="color:#0000ff">var</span></strong><span style="color:#000000"> </span><span style="color:#000000">oTr</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#ff00ff">document</span><span style="color:#ff0000">.</span><span style="color:#000000">getElementById</span><span style="color:#ff0000">(</span><span style="color:#4682b4">"</span><span style="color:#4682b4">mytable</span><span style="color:#4682b4">"</span><span style="color:#ff0000">)</span><span style="color:#ff0000">.</span><span style="color:#000000">insertRow</span><span style="color:#ff0000">(</span><strong><span style="color:#008080">2</span></strong><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><strong><span style="color:#0000ff">var</span></strong><span style="color:#000000"> </span><span style="color:#000000">aText</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#ff0000">new</span><span style="color:#000000"> </span><strong><span style="color:#00008b">Array</span></strong><span style="color:#ff0000">(</span><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">aText</span><span style="color:#ff0000">[</span><strong><span style="color:#008080">0</span></strong><span style="color:#ff0000">]</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#ff00ff">document</span><span style="color:#ff0000">.</span><span style="color:#000000">createTextNode</span><span style="color:#ff0000">(</span><span style="color:#4682b4">"</span><span style="color:#4682b4">cell1的内容</span><span style="color:#4682b4">"</span><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">aText</span><span style="color:#ff0000">[</span><strong><span style="color:#008080">1</span></strong><span style="color:#ff0000">]</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#ff00ff">document</span><span style="color:#ff0000">.</span><span style="color:#000000">createTextNode</span><span style="color:#ff0000">(</span><span style="color:#4682b4">"</span><span style="color:#4682b4">cell2的内容</span><span style="color:#4682b4">"</span><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">aText</span><span style="color:#ff0000">[</span><strong><span style="color:#008080">2</span></strong><span style="color:#ff0000">]</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#ff00ff">document</span><span style="color:#ff0000">.</span><span style="color:#000000">createTextNode</span><span style="color:#ff0000">(</span><span style="color:#4682b4">"</span><span style="color:#4682b4">cell3的内容</span><span style="color:#4682b4">"</span><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">aText</span><span style="color:#ff0000">[</span><strong><span style="color:#008080">3</span></strong><span style="color:#ff0000">]</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#ff00ff">document</span><span style="color:#ff0000">.</span><span style="color:#000000">createTextNode</span><span style="color:#ff0000">(</span><span style="color:#4682b4">"</span><span style="color:#4682b4">cell4的内容</span><span style="color:#4682b4">"</span><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">aText</span><span style="color:#ff0000">[</span><strong><span style="color:#008080">4</span></strong><span style="color:#ff0000">]</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#ff00ff">document</span><span style="color:#ff0000">.</span><span style="color:#000000">createTextNode</span><span style="color:#ff0000">(</span><span style="color:#4682b4">"</span><span style="color:#4682b4">cell5的内容</span><span style="color:#4682b4">"</span><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><strong><span style="color:#0000ff">for</span></strong><span style="color:#ff0000">(</span><strong><span style="color:#0000ff">var</span></strong><span style="color:#000000"> </span><span style="color:#000000">i</span><span style="color:#ff0000">=</span><strong><span style="color:#008080">0</span></strong><span style="color:#ff0000">;</span><span style="color:#000000">i</span><span style="color:#ff0000">&lt;</span><span style="color:#000000">aText</span><span style="color:#ff0000">.</span><span style="color:#ff00ff">length</span><span style="color:#ff0000">;</span><span style="color:#000000">i</span><span style="color:#ff0000">+</span><span style="color:#ff0000">+</span><span style="color:#ff0000">)</span><span style="color:#000000">{</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><strong><span style="color:#0000ff">var</span></strong><span style="color:#000000"> </span><span style="color:#000000">oTd</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#000000">oTr</span><span style="color:#ff0000">.</span><span style="color:#000000">insertCell</span><span style="color:#ff0000">(</span><span style="color:#000000">i</span><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#000000">oTd</span><span style="color:#ff0000">.</span><span style="color:#000000">appendChild</span><span style="color:#ff0000">(</span><span style="color:#000000">aText</span><span style="color:#ff0000">[</span><span style="color:#000000">i</span><span style="color:#ff0000">]</span><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">}</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">}</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">&lt;</span><span style="color:#ff0000">/</span><span style="color:#000000">script</span><span style="color:#ff0000">&gt;</span></td>
         </tr>
     </tbody>
</table>
</div>
<p style="line-height:20px">  </p>
<p> </p>
<p><font color="#0000ff" size="4">B  利用DOM 修改单元格内容</font></p>
<div style="border:1px solid #999999;padding-left:18px;font-size:12px;width:700px;line-height:14px;font-family:courier new;background-color:#eeeeee">
<table style="border:0px none;padding:0px;width:100%" cellspacing="0">
     <tbody>
         <tr>
             <td><span style="color:#ff0000">&lt;</span><span style="color:#000000">script</span><span style="color:#000000"> </span><span style="color:#ff00ff">language</span><span style="color:#ff0000">=</span><span style="color:#4682b4">"</span><span style="color:#4682b4">javascript</span><span style="color:#4682b4">"</span><span style="color:#ff0000">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff00ff">window</span><span style="color:#ff0000">.</span><span style="color:#ff00ff">onload</span><span style="color:#ff0000">=</span><strong><span style="color:#0000ff">function</span></strong><span style="color:#ff0000">(</span><span style="color:#ff0000">)</span><span style="color:#000000">{</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><strong><span style="color:#0000ff">var</span></strong><span style="color:#000000"> </span><span style="color:#000000">oTable</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#ff00ff">document</span><span style="color:#ff0000">.</span><span style="color:#000000">getElementById</span><span style="color:#ff0000">(</span><span style="color:#4682b4">"</span><span style="color:#4682b4">mytable</span><span style="color:#4682b4">"</span><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#008000">//</span><span style="color:#008000">修改单元格内容</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">oTable</span><span style="color:#ff0000">.</span><span style="color:#000000">rows</span><span style="color:#ff0000">[</span><strong><span style="color:#008080">3</span></strong><span style="color:#ff0000">]</span><span style="color:#ff0000">.</span><span style="color:#000000">cells</span><span style="color:#ff0000">[</span><strong><span style="color:#008080">4</span></strong><span style="color:#ff0000">]</span><span style="color:#ff0000">.</span><span style="color:#ff00ff">innerHTML</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#4682b4">"</span><span style="color:#4682b4">更改的内容</span><span style="color:#4682b4">"</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">}</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">&lt;</span><span style="color:#ff0000">/</span><span style="color:#000000">script</span><span style="color:#ff0000">&gt;</span></td>
         </tr>
     </tbody>
</table>
</div>
<p style="line-height:20px">  </p>
<p> </p>
<p><font color="#0000ff" size="4">C  利用DOM 删除一个单元格或一行</font></p>
<div style="border:1px solid #999999;padding-left:18px;font-size:12px;width:700px;line-height:14px;font-family:courier new;background-color:#eeeeee">
<table style="border:0px none;padding:0px;width:100%" cellspacing="0">
     <tbody>
         <tr>
             <td><span style="color:#ff0000">&lt;</span><span style="color:#000000">script</span><span style="color:#000000"> </span><span style="color:#ff00ff">language</span><span style="color:#ff0000">=</span><span style="color:#4682b4">"</span><span style="color:#4682b4">javascript</span><span style="color:#4682b4">"</span><span style="color:#ff0000">&gt;</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff00ff">window</span><span style="color:#ff0000">.</span><span style="color:#ff00ff">onload</span><span style="color:#ff0000">=</span><strong><span style="color:#0000ff">function</span></strong><span style="color:#ff0000">(</span><span style="color:#ff0000">)</span><span style="color:#000000">{</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><strong><span style="color:#0000ff">var</span></strong><span style="color:#000000"> </span><span style="color:#000000">oTable</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#ff00ff">document</span><span style="color:#ff0000">.</span><span style="color:#000000">getElementById</span><span style="color:#ff0000">(</span><span style="color:#4682b4">"</span><span style="color:#4682b4">mytable</span><span style="color:#4682b4">"</span><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#008000">//</span><span style="color:#008000">删除一行，后面的行号自动补齐</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">oTable</span><span style="color:#ff0000">.</span><span style="color:#000000">deleteRow</span><span style="color:#ff0000">(</span><strong><span style="color:#008080">2</span></strong><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#008000">//</span><span style="color:#008000">删除一个单元格，后面的也自动补齐</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">oTable</span><span style="color:#ff0000">.</span><span style="color:#000000">rows</span><span style="color:#ff0000">[</span><strong><span style="color:#008080">2</span></strong><span style="color:#ff0000">]</span><span style="color:#ff0000">.</span><span style="color:#000000">deleteCell</span><span style="color:#ff0000">(</span><strong><span style="color:#008080">1</span></strong><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">}</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">&lt;</span><span style="color:#ff0000">/</span><span style="color:#000000">script</span><span style="color:#ff0000">&gt;</span></td>
         </tr>
     </tbody>
</table>
</div>
<p style="line-height:20px">  </p>
<p> </p>
<p><font color="#0000ff" size="4">D  利用DOM 动态添加一列，并动态删除某行</font></p>
<div style="border:1px solid #999999;padding-left:18px;font-size:12px;width:700px;line-height:14px;font-family:courier new;background-color:#eeeeee">
<table style="border:0px none;padding:0px;width:100%" cellspacing="0">
     <tbody>
         <tr>
             <td><span style="color:#ff0000">&lt;</span><span style="color:#000000">script</span><span style="color:#000000"> </span><span style="color:#ff00ff">language</span><span style="color:#ff0000">=</span><span style="color:#4682b4">"</span><span style="color:#4682b4">javascript</span><span style="color:#4682b4">"</span><span style="color:#ff0000">&gt;</span></td>
         </tr>
         <tr>
             <td><strong><span style="color:#0000ff">function</span></strong><span style="color:#000000"> </span><span style="color:#000000">myDelete</span><span style="color:#ff0000">(</span><span style="color:#ff0000">)</span><span style="color:#000000">{</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><strong><span style="color:#0000ff">var</span></strong><span style="color:#000000"> </span><span style="color:#000000">oTable</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#ff00ff">document</span><span style="color:#ff0000">.</span><span style="color:#000000">getElementById</span><span style="color:#ff0000">(</span><span style="color:#4682b4">"</span><span style="color:#4682b4">mytable</span><span style="color:#4682b4">"</span><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#008000">//</span><span style="color:#008000">删除该行</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><strong><span style="color:#0000ff">this</span></strong><span style="color:#ff0000">.</span><span style="color:#000000">parentNode</span><span style="color:#ff0000">.</span><span style="color:#000000">parentNode</span><span style="color:#ff0000">.</span><span style="color:#000000">parentNode</span><span style="color:#ff0000">.</span><span style="color:#000000">removeChild</span><span style="color:#ff0000">(</span><strong><span style="color:#0000ff">this</span></strong><span style="color:#ff0000">.</span><span style="color:#000000">parentNode</span><span style="color:#ff0000">.</span><span style="color:#000000">parentNode</span><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">}</span></td>
         </tr>
         <tr>
             <td> </td>
         </tr>
         <tr>
             <td><span style="color:#ff00ff">window</span><span style="color:#ff0000">.</span><span style="color:#ff00ff">onload</span><span style="color:#ff0000">=</span><strong><span style="color:#0000ff">function</span></strong><span style="color:#ff0000">(</span><span style="color:#ff0000">)</span><span style="color:#000000">{</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><strong><span style="color:#0000ff">var</span></strong><span style="color:#000000"> </span><span style="color:#000000">oTable</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#ff00ff">document</span><span style="color:#ff0000">.</span><span style="color:#000000">getElementById</span><span style="color:#ff0000">(</span><span style="color:#4682b4">"</span><span style="color:#4682b4">mytable</span><span style="color:#4682b4">"</span><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><strong><span style="color:#0000ff">var</span></strong><span style="color:#000000"> </span><span style="color:#000000">oTd</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#008000">//</span><span style="color:#008000">动态添加delete链接</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><strong><span style="color:#0000ff">for</span></strong><span style="color:#ff0000">(</span><strong><span style="color:#0000ff">var</span></strong><span style="color:#000000"> </span><span style="color:#000000">i</span><span style="color:#ff0000">=</span><strong><span style="color:#008080">1</span></strong><span style="color:#ff0000">;</span><span style="color:#000000">i</span><span style="color:#ff0000">&lt;</span><span style="color:#000000">oTable</span><span style="color:#ff0000">.</span><span style="color:#000000">rows</span><span style="color:#ff0000">.</span><span style="color:#ff00ff">length</span><span style="color:#ff0000">;</span><span style="color:#000000">i</span><span style="color:#ff0000">+</span><span style="color:#ff0000">+</span><span style="color:#ff0000">)</span><span style="color:#000000">{</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#000000">oTd</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#000000">oTable</span><span style="color:#ff0000">.</span><span style="color:#000000">rows</span><span style="color:#ff0000">[</span><span style="color:#000000">i</span><span style="color:#ff0000">]</span><span style="color:#ff0000">.</span><span style="color:#000000">insertCell</span><span style="color:#ff0000">(</span><strong><span style="color:#008080">5</span></strong><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#000000">oTd</span><span style="color:#ff0000">.</span><span style="color:#ff00ff">innerHTML</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#4682b4">"</span><span style="color:#4682b4">&lt;a</span><span style="color:#4682b4"> </span><span style="color:#4682b4">href=&#39;#&#39;&gt;delete&lt;/a&gt;</span><span style="color:#4682b4">"</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#000000">oTd</span><span style="color:#ff0000">.</span><span style="color:#000000">firstChild</span><span style="color:#ff0000">.</span><span style="color:#ff00ff">onclick</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#000000">myDelete</span><span style="color:#ff0000">;</span><span style="color:#000000"> </span><span style="color:#008000">//</span><span style="color:#008000">添加删除事件</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">}</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">}</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">&lt;</span><span style="color:#ff0000">/</span><span style="color:#000000">script</span><span style="color:#ff0000">&gt;</span></td>
         </tr>
     </tbody>
</table>
</div>
<p style="line-height:20px">  </p>
<p> </p>
<p><font color="#0000ff" size="4">E  利用DOM 动态删除某一列</font></p>
<div style="border:1px solid #999999;padding-left:18px;font-size:12px;width:700px;line-height:14px;font-family:courier new;background-color:#eeeeee">
<table style="border:0px none;padding:0px;width:100%" cellspacing="0">
     <tbody>
         <tr>
             <td><span style="color:#ff0000">&lt;</span><span style="color:#000000">script</span><span style="color:#000000"> </span><span style="color:#ff00ff">language</span><span style="color:#ff0000">=</span><span style="color:#4682b4">"</span><span style="color:#4682b4">javascript</span><span style="color:#4682b4">"</span><span style="color:#ff0000">&gt;</span></td>
         </tr>
         <tr>
             <td><strong><span style="color:#0000ff">function</span></strong><span style="color:#000000"> </span><span style="color:#000000">deleteColumn</span><span style="color:#ff0000">(</span><span style="color:#000000">oTable</span><span style="color:#ff0000">,</span><span style="color:#000000">iNum</span><span style="color:#ff0000">)</span><span style="color:#000000">{</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#008000">//</span><span style="color:#008000">自定义删除列函数，即每行删除相应单元格</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><strong><span style="color:#0000ff">for</span></strong><span style="color:#ff0000">(</span><strong><span style="color:#0000ff">var</span></strong><span style="color:#000000"> </span><span style="color:#000000">i</span><span style="color:#ff0000">=</span><strong><span style="color:#008080">0</span></strong><span style="color:#ff0000">;</span><span style="color:#000000">i</span><span style="color:#ff0000">&lt;</span><span style="color:#000000">oTable</span><span style="color:#ff0000">.</span><span style="color:#000000">rows</span><span style="color:#ff0000">.</span><span style="color:#ff00ff">length</span><span style="color:#ff0000">;</span><span style="color:#000000">i</span><span style="color:#ff0000">+</span><span style="color:#ff0000">+</span><span style="color:#ff0000">)</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">    </span><span style="color:#000000">oTable</span><span style="color:#ff0000">.</span><span style="color:#000000">rows</span><span style="color:#ff0000">[</span><span style="color:#000000">i</span><span style="color:#ff0000">]</span><span style="color:#ff0000">.</span><span style="color:#000000">deleteCell</span><span style="color:#ff0000">(</span><span style="color:#000000">iNum</span><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">}</span></td>
         </tr>
         <tr>
             <td> </td>
         </tr>
         <tr>
             <td><span style="color:#ff00ff">window</span><span style="color:#ff0000">.</span><span style="color:#ff00ff">onload</span><span style="color:#ff0000">=</span><strong><span style="color:#0000ff">function</span></strong><span style="color:#ff0000">(</span><span style="color:#ff0000">)</span><span style="color:#000000">{</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><strong><span style="color:#0000ff">var</span></strong><span style="color:#000000"> </span><span style="color:#000000">oTable</span><span style="color:#000000"> </span><span style="color:#ff0000">=</span><span style="color:#000000"> </span><span style="color:#ff00ff">document</span><span style="color:#ff0000">.</span><span style="color:#000000">getElementById</span><span style="color:#ff0000">(</span><span style="color:#4682b4">"</span><span style="color:#4682b4">mytable</span><span style="color:#4682b4">"</span><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">    </span><span style="color:#000000">deleteColumn</span><span style="color:#ff0000">(</span><span style="color:#000000">oTable</span><span style="color:#ff0000">,</span><strong><span style="color:#008080">2</span></strong><span style="color:#ff0000">)</span><span style="color:#ff0000">;</span><span style="color:#000000"> </span><span style="color:#008000">//</span><span style="color:#008000">参数2:表示要删除的列号</span></td>
         </tr>
         <tr>
             <td><span style="color:#000000">}</span></td>
         </tr>
         <tr>
             <td><span style="color:#ff0000">&lt;</span><span style="color:#ff0000">/</span><span style="color:#000000">script</span><span style="color:#ff0000">&gt;</span></td>
         </tr>
     </tbody>
</table>
</div>
<p>完毕。</p>
<p style="line-height:2px">  </p>
<p>
</p><img src="http://www.cnblogs.com/xugang/aggbug/1797305.html?type=1" width="1" height="1" alt=""><p>评论: 17　<a href="http://www.cnblogs.com/xugang/archive/2010/08/11/1797305.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/xugang/archive/2010/08/11/1797305.html#commentform">发表评论</a></p><p><a href="http://job.cnblogs.com/">程序员找工作，就在博客园</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/70615/">俄将在2011年前开发基于Linux的操作系统</a><span style="color:gray">(2010-08-12 10:03)</span><br>· <a href="http://news.cnblogs.com/n/70614/">百度成立移动互联网事业部 岳国峰任总经理</a><span style="color:gray">(2010-08-12 09:59)</span><br>· <a href="http://news.cnblogs.com/n/70613/">大脑中藏“互联网” 或将绘出大脑神经网络图</a><span style="color:gray">(2010-08-12 09:55)</span><br>· <a href="http://news.cnblogs.com/n/70612/">土豆网再获5000万美元风投 最快明年海外上市</a><span style="color:gray">(2010-08-12 09:48)</span><br>· <a href="http://news.cnblogs.com/n/70610/">Windows Live Gallery 将不再托管 Writer 插件，面临关闭？</a><span style="color:gray">(2010-08-12 09:00)</span><br></p><p>编辑推荐：<a href="http://news.cnblogs.com/n/70605/">周鸿祎构建360客户端帝国：可进军搜索战百度</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">个人主页</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://space.cnblogs.com">社区</a>  <a href="http://kb.cnblogs.com">知识库</a></p>