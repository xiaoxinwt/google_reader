---
layout: post
title:  "CSS Bug Table"
date:   2011-06-16 13:58:44
author: jefferkim
categories: program
---

## CSS Bug Table
### by jefferkim
### at 2011-06-16 13:58:44
### original <http://yoho88.cn/html/css-bug-table.html>

<p>作为一名前端，我们通常要做的就是让页面在各系统A-Grade浏览器，甚至网站浏览份额0.1%以上的浏览器上良好显示。当然，还有性能问题。不过，今天要说的是样式的兼容问题。在IE/Mozilla/Webkit/Opera四分天下的今天，IE6-9/Mozilla（Gecko）系列/Chrome/Safari/Opera etc. 这些浏览器的兼容，无不让前端们头痛。而在这之中，最让人头痛的当数IE，特别是IE6。搞定了IE6，基本也就能称霸半个江山了。搞定了IE，也相当于占领了7、80%的领地。你想做一个统治页面兼容的主么？反正我是想的。</p>
<p></p>
<table width="520">
<thead>
<tr>
<th width="30"></th>
<th width="120">问题</th>
<th width="60">浏览器</th>
<th width="90">DEMO</th>
<th width="120">解决方法</th>
</tr>
</thead>
<tbody>
<tr>
<td>1</td>
<td>input[button | submit] 不能用 margin:0 auto; 居中</td>
<td>IE8</td>
<td><a href="http://haslayout.net/demos/No-Auto-Margin-Center-on-Buttons-Inconsistency-Demo.html">bug</a> | <a href="http://haslayout.net/demos/No-Auto-Margin-Center-on-Buttons-Inconsistency-Fixed-Demo-CS.html">fixed</a></td>
<td>为input添加width</td>
</tr>
<tr>
<td>2</td>
<td>body{overflow:hidden;}没有去掉滚动条</td>
<td>IE6/7</td>
<td><a href="http://haslayout.net/demos/Document-Scrollbars-Overflow-Inconsistency-Demo.html">bug</a> | <a href="http://haslayout.net/demos/Document-Scrollbars-Overflow-Inconsistency-Fixed-Demo.html">fixed</a></td>
<td>设置html{overflow:hidden;}</td>
</tr>
<tr>
<td>3</td>
<td>hasLayout的标签拥有高度</td>
<td>IE6/7</td>
<td><a href="http://haslayout.net/demos/Empty-Element-Height-Bug-Demo.html">bug</a> | <a href="http://haslayout.net/demos/Empty-Element-Height-Bug-Fixed-Demo.html">fixed</a></td>
<td>*height:0;_overflow:hidden;</td>
</tr>
<tr>
<td>4</td>
<td>form&gt;[hasLayout]元素有margin-left时，子元素中的[input | textarea] 出现2×margin-left</td>
<td>IE6/7</td>
<td><a href="http://haslayout.net/demos/Form-Control-Double-Margin-Bug-Demo.html">bug</a> | <a href="http://haslayout.net/demos/Form-Control-Double-Margin-Bug-Fixed-Demo.html">fixed</a></td>
<td>form &gt; [hasLayout 元素]{margin-left:宽度;}form div{*margin-left:宽度÷2;}</td>
</tr>
<tr>
<td>5</td>
<td>当border-width有1条&lt;边3条时被设置成dotted时，1px的边dotted显示成dashed</td>
<td>IE7</td>
<td><a href="http://haslayout.net/demos/IE7-1px-Dotted-Border-Appears-As-Dashed-Bug-Demo.html">bug</a> | <a href="http://haslayout.net/demos/IE7-1px-Dotted-Border-Appears-As-Dashed-Bug-Fixed-Demo.html">fixed</a></td>
<td>不在同一个元素上使用不同宽度的 dotted</td>
</tr>
<tr>
<td>6</td>
<td>当子元素有position:relative的时候，父元素设置overflow:[hidden|auto]相当于给子元素设置了position:visible;</td>
<td>IE6/7</td>
<td><a href="http://haslayout.net/demos/relative-overflow-failure-bug-demo.html">bug</a> | <a href="http://haslayout.net/demos/relative-overflow-failure-bug-demo-fixed.html">fixed</a></td>
<td>给父元素设置position:relative;</td>
</tr>
<tr>
<td>7</td>
<td>:hover伪类不能改变有position:absolute的子级元素的left/top值</td>
<td>IE7</td>
<td><a href="http://haslayout.net/demos/ie7-broken-hover-absolute-bug-demo.html">bug</a> | <a href="http://haslayout.net/demos/ie7-broken-hover-absolute-bug-demo-fixed.html">fixed</a></td>
<td>把top/left的值设置成除0%外的所有百分值；或添加一个margin-[所有方向]除0外的所有值，包括0%</td>
</tr>
<tr>
<td>8</td>
<td>:focus + selector {} 选择器失效</td>
<td>IE8</td>
<td><a href="http://haslayout.net/demos/ignored-focus-bug-demo.html">bug</a> | <a href="http://haslayout.net/demos/ignored-focus-bug-demo-fixed.html">fixed</a></td>
<td>在失效选择器后面添加一个空选择器, :focus{}</td>
</tr>
<tr>
<td>9</td>
<td>列表中混乱的浮动：在list中浮动图片时，图片出现溢出正常位置；或没有list-style</td>
<td>IE8</td>
<td><a href="http://haslayout.net/demos/image-float-bullet-chaos-demo.html">bug</a> | <a href="http://haslayout.net/demos/image-float-bullet-chaos-demo-fixed.html">fixed</a></td>
<td>用背景图片替换list-style</td>
</tr>
<tr>
<td>10</td>
<td>th 不会自动继承上级元素的 text-align</td>
<td>IE8</td>
<td><a href="http://haslayout.net/demos/non-inherited-th-text-align-bug-demo.html">bug</a> | <a href="http://haslayout.net/demos/non-inherited-th-text-align-bug-demo-fixed.html">fixed</a></td>
<td>给th添加text-align:inherit;</td>
</tr>
<tr>
<td>11</td>
<td>样式(包括link/style/@import(link)) 最多允许个为是：32</td>
<td>IE6-8</td>
<td>─ 常识</td>
<td>99.99%的情况下，不会遇到</td>
</tr>
<tr>
<td>12</td>
<td>:hover 时若background-color为#fff, 失效</td>
<td>IE7</td>
<td><a href="http://haslayout.net/demos/hover-white-background-ignore-bug.html">bug</a> | <a href="http://haslayout.net/demos/hover-white-background-ignore-bug-fixed-demo.html">fixed</a></td>
<td>把background-color改成background。或者，非#fff || #ffffff</td>
</tr>
<tr>
<td>13</td>
<td>忽略’&gt;’后有注释的选择器：selector&gt; /**/ selector{}</td>
<td>IE7</td>
<td><a href="http://haslayout.net/demos/ie7-child-selector-comment-bug-demo.html">bug</a> | <a href="http://haslayout.net/demos/ie7-child-selector-comment-bug-demo.html">fixed</a></td>
<td>官方DEMO有误</td>
</tr>
<tr>
<td>14</td>
<td>* html</td>
<td>IE6</td>
<td>─ HACK</td>
<td>只对IE6有效</td>
</tr>
<tr>
<td>15</td>
<td>PNG图片中的颜色和背景颜色的值相同，但显示不同</td>
<td>IE6-7</td>
<td><a href="http://haslayout.net/demos/png-color-mismatch-demo.html">bug</a> | <a href="http://haslayout.net/demos/png-color-mismatch-demo-fixed.html">fixed</a></td>
<td>利用 <a href="http://pmt.sourceforge.net/pngcrush/">pngcrush</a> 去除图片中的 Gamma profiles</td>
</tr>
<tr>
<td>16</td>
<td>margin:0 auto; 不能让block元素水平居中</td>
<td>IE6-8</td>
<td><a href="http://haslayout.net/demos/no-auto-margin-center-pseudo-bug-demo.html">bug</a> | <a href="http://haslayout.net/demos/no-auto-margin-center-pseudo-bug-demo-fixed.html">fixed</a></td>
<td>给block元素添加一个width</td>
</tr>
<tr>
<td>17</td>
<td>使用伪类 :first-line | :first-letter, 属性的值中出现!important 会使属性失效</td>
<td>IE8</td>
<td><a href="http://haslayout.net/demos/ie8-first-line-important-bug.html">bug</a> | <a href="http://haslayout.net/demos/ie8-first-line-important-bug-fixed.html">fixed</a></td>
<td>!important is evil, don’t use it anymore</td>
</tr>
<tr>
<td>18</td>
<td>:first-letter 失效</td>
<td>IE6</td>
<td><a href="http://haslayout.net/demos/ie6-first-letter-ignore-bug.html">bug</a> | <a href="http://haslayout.net/demos/ie6-first-letter-ignore-bug-fixed.html">fixed</a></td>
<td>把 :first-letter 移到离{}最近的地方，如 h1, p:first-letter{}，而非 p:first-letter h1{}</td>
</tr>
<tr>
<td>19</td>
<td>Position:absolute元素中，a display:block, 在非:hover时只有文本可点击</td>
<td>IE6/7</td>
<td><a href="http://haslayout.net/demos/partial-click-v2-demo.html">bug</a> | <a href="http://haslayout.net/demos/partial-click-v2-demo-fixed.html">fixed</a></td>
<td>给a添加background, 如果背景透明，使用background:url(‘任何页面中已经缓存的文件链接’)，不推荐background：url(#)[<a href="http://haslayout.net/css/Partial-Click-Bug-v2">官方的解决方法</a>]，因为会增加一下HTTP请求</td>
</tr>
<tr>
<td>20</td>
<td>float列表元素不水平对齐：li不设置float，a设置display:block;float:[方向]，li不水平对齐</td>
<td>IE6/7</td>
<td><a href="http://haslayout.net/demos/staircase-bug-demo.html">bug</a> | <a href="http://haslayout.net/demos/staircase-bug-demo-fixed.html">fixed</a></td>
<td>给li设置display:inline 或 float:[方向]</td>
</tr>
<tr>
<td>21</td>
<td>dt, dd, li 背景失效</td>
<td>IE6</td>
<td><a href="http://haslayout.net/demos/disappearing-list-background-bug-demo.html">bug</a> | <a href="http://haslayout.net/demos/disappearing-list-background-bug-demo-fixed.html">fixed</a></td>
<td>dt, dd, li{position:relative;}</td>
</tr>
<tr>
<td>22</td>
<td>&lt;noscript /&gt;元素的样式在启用javascript的情况下显示了样式</td>
<td>IE6-8</td>
<td><a href="http://haslayout.net/demos/noscript-ghost-bug-demo.html">bug</a> | <a href="http://haslayout.net/demos/noscript-ghost-bug-demo-fixed.html">fixed</a></td>
<td>利用js给&lt;noscript /&gt;添加display:none;</td>
</tr>
<tr>
<td>23</td>
<td>使用filter处理的透明背景图片的透明部分不可点</td>
<td>IE6-8</td>
<td><a href="http://haslayout.net/demos/no-transparency-click-bug-demo.html">bug</a> | <a href="http://haslayout.net/demos/no-transparency-click-bug-demo-fixed.html">fixed</a></td>
<td>把background:none变成background:url(‘链接’)，链接到本身和图片之外的任何文件</td>
</tr>
<tr>
<td>24</td>
<td>li内元素偏离 baseline 向下拉</td>
<td>IE8</td>
<td><a href="http://haslayout.net/demos/list-drop-shift-bug-demo.html">bug</a> | <a href="http://haslayout.net/demos/list-drop-shift-bug-demo-fixed.html">fixed</a></td>
<td>给li设置display:inline 或 float:[方向]</td>
</tr>
<tr>
<td>25</td>
<td>列表中li的list-style不显示</td>
<td>IE6/7</td>
<td><a href="http://haslayout.net/demos/no_li_ol_bullets_demo.html">bug</a> | <a href="http://haslayout.net/demos/no_li_ol_bullets_demo_fixed.html">fixed</a></td>
<td>给li添加margin-left，留空间来显示（不要加在ul上）</td>
</tr>
<tr>
<td>26</td>
<td>图片不能垂直居中</td>
<td>IE6/7</td>
<td><a href="http://haslayout.net/css/No-line-height-Vertical-Center-on-Images-Bug">bug/fixed</a></td>
<td>添加一个空标签，并赋给”Layout”, 比如display:inline-block;</td>
</tr>
<tr>
<td>27</td>
<td>不能自定义指针样式</td>
<td>IE6-8</td>
<td><a href="http://haslayout.net/demos/cursor/a/demo.html">bug</a> | <a href="http://haslayout.net/demos/cursor/a/demo_fixed.html">fixed</a></td>
<td>给指针文件设置绝对路径</td>
</tr>
<tr>
<td>28</td>
<td>背景溢出，拖动滚动条后显示正常</td>
<td>IE6</td>
<td><a href="http://haslayout.net/demos/backgroundleak_demo.html">bug</a> | <a href="http://haslayout.net/demos/backgroundleak_demo_ls_fixed.html">fixed</a></td>
<td>给父元素添加overflow:hidden防止溢出，并赋予hasLayout,如果添加_zoom:1;</td>
</tr>
<tr>
<td>29</td>
<td>高度超过height定义的高</td>
<td>IE6</td>
<td><a href="http://haslayout.net/css/Expanding-Height-Bug">bug/fixed</a></td>
<td>添加_overflow:hidden;(推荐)或者_font-size:0;</td>
</tr>
<tr>
<td>30</td>
<td>宽度超过width定义的宽</td>
<td>IE6</td>
<td><a href="http://haslayout.net/css/Expanding-Width-Bug">bug/fixed</a></td>
<td>添加_overflow:hidden;</td>
</tr>
<tr>
<td>31</td>
<td>双倍边距</td>
<td>IE6</td>
<td>─ 常识</td>
<td>添加display:inline到float元素中</td>
</tr>
<tr>
<td>32</td>
<td>margin负值隐藏：hasLayout的父元素内的非hasLayout元素，使用负边距时，超出父元素部分不可见</td>
<td>IE6/7</td>
<td><a href="http://haslayout.net/css/Negative-Margin-Bug">bug/fixed</a></td>
<td>去掉父元素的hasLayout；或者赋hasLayout给子元素,并添加position:relative;</td>
</tr>
<tr>
<td>33</td>
<td>给两个浮动元素的某中一个的文字设定为斜体，另一个元素下拉在有斜体文字元素的下面</td>
<td>IE6</td>
<td><a href="http://haslayout.net/css/Italics-Float-Bug">bug/fixed</a></td>
<td>给有斜体文字的元素添加overflow:hidden;</td>
</tr>
<tr>
<td>35</td>
<td>3px 间隔：在float元素后的元素，会有3px间隔</td>
<td>IE6</td>
<td><a href="http://haslayout.net/css/3px-Gap-Bug-aka-Text-Jog-Bug">bug/fixed</a></td>
<td>因为是确切的3px，所以，用“暴力破解”吧，比如_margin-left:-3px;</td>
</tr>
<tr>
<td>35</td>
<td>text-align 影响块级元素</td>
<td>IE6/7</td>
<td><a href="http://haslayout.net/css/Text-Align-Bug">bug/fixed</a></td>
<td>整理你的float；或者分开设置text-align</td>
</tr>
</tbody>
</table>