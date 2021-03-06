---
layout: post
title:  "Pure CSS3 Ribbons"
date:   2012-04-03 18:40:24
author: Airen
categories: program
---

## Pure CSS3 Ribbons
### by Airen
### at 2012-04-03 18:40:24
### original <http://www.w3cplus.com/css3/pure-css3-ribbons>

<div><div><div><p>前面在<a href="http://www.w3cplus.com/css3/css3-ribbons">CSS3 Ribbons</a>一文中介绍了一个纯CSS3制作的“Ribbons”效果，今天收集了更多的“Ribbons”效果，以供大家参考与学习，希望大家能喜欢。</p>
<p>在看具体每个demo之前，我们一起来看下面一个截图：</p>
<p><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons.png"></p>
<p>上图是一个典型的“Ribbons”各部位的示意图，但每一个“Ribbons”并不会都使用上图示意的各个部分，在下面的实例中大家可以明显的看到或者说感受到。别的我就不多说了，我想上图能示意出“Ribbons”各个部位。那么每个部位我们可以使用一个“html”标签，当然有了CSS3的伪元素“::before”和“::after”后，我们可以省去一些标签。</p>
<h4>效果一：</h4>
<p><strong>HTML Markup</strong></p>
<pre>	&lt;div class=&quot;ribbons&quot; id=&quot;ribbon-1&quot;&gt;Ribbon-1&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		#ribbon-1 {
				font: bold 16px/48px Cambria,Georgia,Times,serif;
				color: #000;
				text-shadow: 1px 0 0 hsla(20,50%,30%,0.6);
				width: 300px;
				padding: 0 0 0 30px;
				background: hsla(200, 20%, 80%, 0.8);
				position: relative;
			}
			#ribbon-1::after {
				content: "";
				position: absolute;
				top: 0;
				left: 100%;
				width: 0;
				height: 0;
				border: 24px solid hsla(200, 20%, 80%, 0.8);
				border-right-color: transparent;
			}
	</pre><p><strong>DEMO效果</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/index.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-1.jpg"></a></p>
<h4>效果二</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div class=&quot;ribbons&quot; id=&quot;ribbon-2&quot;&gt;Ribbon-2&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		#ribbon-2 {
			font: bold 16px/48px Cambria,Georgia,Times,sans-serif;
			color:hsla(25,50%,80%,1);
			text-shadow: 1px 0 0 hsla(20,50%,30%,0.6);
			width: 300px;
			position: relative;
			background: hsla(255,45%,50%,0.8);
		}
		#ribbon-2::before {
			content:"";
			position: absolute;
			top:0;
			right: 100%;
			width: 0;
			height: 0;
			border: 24px solid hsla(255,45%,50%,0.8);
			border-left-color:transparent;
		}
		#ribbon-2::after {
			content:"";
			position:absolute;
			top:0px;
			left: 100%;
			width:0;
			height:0;
			border:24px solid transparent;
			border-left-color: hsla(255,45%,50%,0.8);
		}
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo2.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-2.jpg"></a></p>
<h4>效果三</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div class=&quot;ribbons&quot; id=&quot;ribbon-3&quot;&gt;Ribbon-3&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		#ribbon-3 {
			font: bold 16px/48px Cambria,Georgia,Times,serif;
			color: hsla(100,80%,20%,0.6);
			text-shadow: 1px 0 0 hsla(20,50%,30%,0.6);
			width: 300px;
			padding: 0 0 0 30px;
			position: relative;
			background: hsla(200,80%,50%,0.8);
		}
		#ribbon-3::before {
			content:"";
			position: absolute;
			top:100%;
			left:0px;
			width:0;
			height: 0;
			border: 5px solid transparent;
			border-color: hsla(200,70%,42%,0.8) hsla(200,70%,42%,0.8) transparent transparent;
		}
		#ribbon-3::after {
			content:"";
			position: absolute;
			top:0;
			left: 100%;
			width:0;
			height: 0;
			border: 24px solid hsla(200,80%,50%,0.8);
			border-right-color: transparent;	
		}
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo3.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-3.jpg"></a></p>
<h4>效果四</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div class=&quot;ribbons&quot; id=&quot;ribbon-4&quot;&gt;Ribbon-4&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		#ribbon-4 {
			font: bold 16px Cambria,Georgia,Times,serif;
			line-height: 48px;
			width: 315px;
			padding: 0 30px;
			position: relative;
			text-align: center;
			color: hsla(180,60%,80%,0.8);
			text-shadow: 1px 0 0 hsla(20,50%,30%,0.6);
			background: hsla(20,30%,30%,0.8);
		}
		#ribbon-4::before {
			content:"";
			position: absolute;
			top: 100%;
			left:0;
			width: 0;
			height: 0;
			border: 5px solid transparent;
			border-color: hsla(20,25%,25%,0.85) hsla(20,25%,25%,0.85) transparent transparent;
		}
		#ribbon-4::after {
			content:"";
			position: absolute;
			top: 100%;
			right: 0;
			width: 0;
			height: 0;
			border: 5px solid transparent;
			border-color: hsla(20,25%,25%,0.85) transparent transparent hsla(20,25%,25%,0.85);
		}
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo4.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-4.jpg"></a></p>
<h4>效果五</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div id=&quot;ribbon-5-wrap&quot;&gt;
			&lt;div class=&quot;ribbons&quot; id=&quot;ribbon-5&quot;&gt;
				&lt;div&gt;Ribbon-5&lt;/div&gt;
			&lt;/div&gt;
		&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		#ribbon-5-wrap {
			position: relative;
			z-index: 1;
		}
		#ribbon-5 {
			font: bold 16px Cambria,Georgia,Times,serif;
			line-height: 48px;
			width: 300px;
			padding: 0 30px;
			position: relative;
			color: hsla(350,80%,30%,0.8);
			text-shadow: 1px 0 0 hsla(20,50%,30%,0.6);
			background: hsla(315,40%,60%,1);
			text-align: center;
		}
		#ribbon-5::before {
			content:&quot;&quot;;
			position:absolute;
			top:100%;
			left:0;
			width:0;
			height:0;
			border: 5px solid transparent;
			border-color: hsla(315,28%,50%,0.95) hsla(315,28%,50%,0.95) transparent transparent;
		}
		#ribbon-5::after {
			content:&quot;&quot;;
			position:absolute;
			top:100%;
			right:0;
			width:0;
			height:0;
			border: 5px solid transparent;
			border-color: hsla(315,28%,50%,0.95) transparent transparent hsla(315,28%,50%,0.95);
		}
		#ribbon-5 &gt; div {
			width: 100%;
		}
		#ribbon-5 &gt; div::before,
		#ribbon-5 &gt; div::after {
			content:&quot;&quot;;
			position: absolute;
			width: 0;
			height:0;
			border: 24px solid hsla(315,23%,40%,0.95);
			top:10px;
			z-index: -1;
		}
		#ribbon-5 &gt; div::before {
			border-left-color: transparent;
			right: 100%;
			margin-right: -10px;
		}
		#ribbon-5 &gt; div::after {
			border-right-color: transparent;
			left: 100%;
			margin-left: -10px;
		}
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo5.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-5.jpg"></a></p>
<h4>效果六</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div class=&quot;ribbons&quot; id=&quot;ribbon-6&quot;&gt;Ribbon-6&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		#ribbon-6 {
			font: bold 12px Cambria,Georgia,Times,sans-serif;
			color:hsla(315,28%,50%,0.95);
			text-align: center;
			width: 90px;
			height: 50px;
			line-height: 50px;
			padding:15px 0;
			position: relative;
			background: hsla(5,58%,20%,0.95);
			margin-bottom: 120px;
		}
		#ribbon-6::after{
			content:"";
			position: absolute;
			top: 100%;
			width: 0;
			height: 0;
			border: 45px solid  hsla(5,58%,20%,0.95);
			left: 0;
			border-bottom-color: transparent;
		}
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo6.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-6.jpg"></a></p>
<h4>效果七</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div class=&quot;ribbons&quot; id=&quot;ribbon-7&quot;&gt;Ribbon-7&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		#ribbon-7 {
			font:bold 12px Cambria,Georgia,Times,Serif;
			color:#fff;
			width:100px;
			text-align:center;
			padding:15px 0px 15px;
			height:100px;
			line-height: 40px;
			background:#3B5998;
			position:relative;
			margin:20px auto 100px;
		}
		#ribbon-7:before, 
		#ribbon-7:after {
			content:"";
			position:absolute;
			bottom:-20px;
			left:-10px;
			width:0px;
			height:20px;
			border-width:20px 60px;
			border-style:solid;
			border-color:transparent #3B5998;
		}
		#ribbon-7:after {
			bottom:10px;
		}
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo7.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-7.jpg"></a></p>
<h4>效果八</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div id=&quot;ribbon-8-wrap&quot;&gt;
			&lt;div class=&quot;ribbons&quot; id=&quot;ribbon-8&quot;&gt;
				&lt;div&gt;
					&lt;div&gt;
						&lt;div&gt;Ribbon-8&lt;/div&gt;
					&lt;/div&gt;
				&lt;/div&gt;
			&lt;/div&gt;
		&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		#ribbon-8-wrap {
			position: relative;
			z-index: 1;
		}
		#ribbon-8 {
			font:bold 16px Cambria,Georgia,Times,Serif;
			color:#fff;
			width:50%;
			height:48px;
			text-align:center;
			padding:0px 30px;
			background:#AF3605;
			position:relative;
			line-height:48px;
			margin:50px auto;
		}
		#ribbon-8:before {
			content:"";
			position:absolute;
			top:100%;
			left:0px;
			width:0px;
			height:0px;
			border-width:10px;
			border-style:solid;
			border-color:#76290A #76290A transparent transparent;
		}
		#ribbon-8:after {
			content:"";
			position:absolute;
			top:100%;
			right:0px;
			width:0px;
			height:0px;
			border-width:10px;
			border-style:solid;
			border-color:#76290A transparent transparent #76290A;
		}
		#ribbon-8 div {
			width:100%;
			height:100%;
		}
		#ribbon-8 div:before, 
		#ribbon-8 div:after {
			content:"";
			position:absolute;
			width:40px;
			height:100%;
			background:#983912;
			top:20px;
			z-index:-1;
		}
		#ribbon-8 div:before {
			border-left-color:transparent;
			right:100%;
			margin-right:-20px;
		}
		#ribbon-8 div:after {
			border-right-color:transparent;
			left:100%;
			margin-left:-20px;
		}
		#ribbon-8 div div:before, 
		#ribbon-8 div div:after {
			content:"";
			position:absolute;
			width:20px;
			height:0px;
			background:transparent;
			border:25px solid #AF3605;
			top:10px;
			z-index:3;
		}
		#ribbon-8 div div:before {
			border-left-color:transparent;
			margin-right:10px;
		}
		#ribbon-8 div div:after {
			border-right-color:transparent;
			margin-left:10px;
		}
		#ribbon-8 div div div:before, 
		#ribbon-8 div div div:after {
			content:"";
			position:absolute;
			width:0px;
			height:0px;
			background:transparent;
			border:5px solid transparent;
			top:100%;
			margin-top:10px;
			z-index:1;
		}
		#ribbon-8 div div div:before {
			border-top-color:#76290A;
			border-left-color:#76290A;
			margin-left:20px;
		}
		#ribbon-8 div div div:after {
			border-top-color:#76290A;
			border-right-color:#76290A;
			margin-right:20px;
		}
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo8.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-8.jpg"></a></p>
<h4>效果九</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div class=&quot;ribbons left ribbon-holder&quot; id=&quot;ribbon-9&quot;&gt;
			&lt;a href=&quot;#&quot; class=&quot;ribbon red&quot;&gt;&lt;span class=&quot;text&quot;&gt;Forke me on GitHub&lt;/span&gt;&lt;/a&gt;
		&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		.ribbon-holder {
			position: relative;
		}
		.ribbon-holder {
			position: absolute;
			top: 0;
			overflow: hidden;
			height: 10em;
		}
		.left.ribbon-holder {
			left: 0;
		}
		.ribbon-holder  .ribbon,
		.ribbon-holder .ribbon:hover {
			text-decoration: none;
		}
		.ribbon-holder  .ribbon{
			font-family: Collegiate, sans-serif;
			letter-spacing: -.1px;
			opacity: 0.95;
			padding: 0.25em 0;
			position: relative;
			top: 3.2em;
			box-shadow: 0 0 13px #888;
			color: #FFF;
			display: block;
			line-height: 1.35em;
		}
		.ribbon-holder .ribbon .text {
			padding: 0.1em 3em;
		}
		.left .ribbon {
			-moz-transform: rotate(-45deg);
			-webkit-transform: rotate(-45deg);
			-ms-transform: rotate(-45deg);
			-o-transform: rotate(-45deg);
			transform: rotate(-45deg);
			left: -2.82em;
		}
		.red.ribbon {
			background-color: #9a0000;
			background-image: linear-gradient(bottom, #9a0000, #a90000);
			background-image: -o-linear-gradient(bottom, #9a0000, #a90000);
			background-image: -moz-linear-gradient(bottom,  #9a0000, #a90000);
			background-image: -webkit-linear-gradient(bottom,  #9a0000, #a90000);
			background-image: -ms-linear-gradient(bottom,  #9a0000, #a90000);
			background-image: -webkit-gradient(
				linear,
				left bottom,
				left top,
				color-stop(#9a0000),
				color-stop(#a90000)
			);
		}
		.red.ribbon .text {
			border: 1px solid #bf6060;
		}
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo9.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-9.jpg"></a></p>
<h4>效果十</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div class=&quot;ribbons right ribbon-holder&quot; id=&quot;ribbon-10&quot;&gt;
			&lt;a href=&quot;#&quot; class=&quot;ribbon green&quot;&gt;&lt;span class=&quot;text&quot;&gt;Forke me on GitHub&lt;/span&gt;&lt;/a&gt;
		&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		.ribbon-holder {
			position: relative;
		}
		.ribbon-holder {
			position: absolute;
			top: 0;
			overflow: hidden;
			height: 10em;
		}
		.right.ribbon-holder {
			right: 0;
		}
		.ribbon-holder  .ribbon,
		.ribbon-holder .ribbon:hover {
			text-decoration: none;
		}
		.ribbon-holder  .ribbon{
			font-family: Collegiate, sans-serif;
			letter-spacing: -.1px;
			opacity: 0.95;
			padding: 0.25em 0;
			position: relative;
			top: 3.2em;
			box-shadow: 0 0 13px #888;
			color: #FFF;
			display: block;
			line-height: 1.35em;
		}
		.ribbon-holder .ribbon .text {
			padding: 0.1em 3em;
		}
		.right .ribbon {
			-moz-transform: rotate(45deg);
			-webkit-transform: rotate(45deg);
			-o-transform: rotate(45deg);
			-ms-transform: rotate(45deg);
			transform: rotate(45deg);
			right: -2.82em;
		}
		.green.ribbon {
			background-color: #006e00;
			background-image: linear-gradient(bottom, #006e00, #007200);
			background-image: -o-linear-gradient(bottom, #006e00, #007200);
			background-image: -moz-linear-gradient(bottom,  #006e00, #007200);
			background-image: -webkit-linear-gradient(bottom,  #006e00, #007200);
			background-image: -ms-linear-gradient(bottom,  #006e00, #007200);
			background-image: -webkit-gradient(
				linear,
				left bottom,
				left top,
				color-stop(#006e00),
				color-stop(#007200)
			);
		}
		.green.ribbon .text {
			border: 1px solid #6bac6b;
		}
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo10.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-10.jpg"></a></p>
<h4>效果十一</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div class=&quot;ribbons&quot; id=&quot;ribbon-11&quot;&gt;Ribbon-11&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		#ribbon-11 {
			padding: 0 25px;
			height: 80px;
			color: #301607;
			background-color: #c94700;
			background-image: -webkit-gradient(linear, left top, left bottom, from(#c94700), to(#b84100)); 
			background-image: -webkit-linear-gradient(top, #c94700, #b84100); 
			background-image:    -moz-linear-gradient(top, #c94700, #b84100); 
			background-image:     -ms-linear-gradient(top, #c94700, #b84100); 
			background-image:      -o-linear-gradient(top, #c94700, #b84100); 
			background-image:         linear-gradient(top, #c94700, #b84100);
			border-bottom: 1px solid rgba(255, 255, 255, 0.3);
			position: relative;
			float: left;
			clear: left;
			font-family: 'Montez', cursive;
			font-size: 32px;			
			line-height: 80px;
			text-shadow: 1px 1px 0 rgba(255, 255, 255, 0.3);
			box-shadow: -7px 7px 0px rgba(0, 0, 0, 0.3);
			border-radius: 0 0 20px 0 / 0 0 5px 0;
			margin-top: 60px;
		}			
		#ribbon-11::after {
			content: "";	
			display: block;
			width: 40px;
			height: 0px;
			position: absolute;
			right: 0;
			bottom: 4px;
			z-index: 20;
			border-bottom: 80px solid #de6625;
			border-right: 80px solid transparent;
			-webkit-transform: rotate(90deg);
			-webkit-transform-origin: right bottom;
			-moz-transform: rotate(90deg);
			-moz-transform-origin: right bottom;
			-o-transform: rotate(90deg);
			-o-transform-origin: right bottom;
			-ms-transform: rotate(90deg);
			-ms-transform-origin: right bottom;
			transform: rotate(90deg);
			transform-origin: right bottom;
		}				
		#ribbon-11::before {
			content: "";	
			display: block;
			width: 20px;
			height: 0px;
			position: absolute;
			right: 0;
			bottom: 4px;
			z-index: 10;
			border-bottom: 80px solid rgba(0, 0, 0, 0.3);
			border-right: 80px solid transparent;
			-webkit-transform: rotate(80deg);
			-webkit-transform-origin: right bottom;
			-moz-transform: rotate(80deg);
			-moz-transform-origin: right bottom;
			-o-transform: rotate(80deg);
			-o-transform-origin: right bottom;
			-ms-transform: rotate(80deg);
			-ms-transform-origin: right bottom;
			transform: rotate(80deg);
			transform-origin: right bottom;
		}
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo11.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-11.jpg"></a></p>
<h4>效果十二</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div id=&quot;ribbon-12&quot; class=&quot;ribbons&quot;&gt;Ribbon-12&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		#ribbon-12  {
			background-color: #999999;
			box-shadow: 0 2px 4px #888888;
			color: #555555;
			font-size: 1.5em;
			font-weight: bold;
			padding: 6px 20px 6px 71px;
			position: relative;
			text-shadow: 0 1px 2px #BBBBBB;
			width: 50%;
			clear: both;
		}
		#ribbon-12::before, 
		#ribbon-12::after {
			content: " ";
			height: 0;
			position: absolute;
			width: 0;
		}
		#ribbon-12::before {
			border-color: #999999 #999999 #999999 transparent;
			border-style: solid;
			border-width: 20px 10px;
			left: -30px;
			top: 12px;
			width: 30px;
		}	
		#ribbon-12::after {
			border-color: #666666 #666666 transparent transparent;
			border-style: solid;
			border-width: 5px 10px;
			left: 0;
			top: 100%;
		}
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo12.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-12.jpg"></a></p>
<h4>效果十三</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div class=&quot;ribbons&quot; id=&quot;ribbon-13&quot;&gt;
			&lt;h1 class=&quot;ribbon-content&quot;&gt;&lt;a&gt;A Pure CSS Ribbon&lt;/a&gt;&lt;/h1&gt;
		&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		#ribbon-13 {
		 width: 300px;
		 position: relative;
		 text-align: center;
		 font-size: 20px!important;
		 background: #d64b4b;
		 background: -webkit-gradient(linear, left top, left bottom, from(#d64b4b), to(#ab2c2c));
		 background: -webkit-linear-gradient(top, #d64b4b, #ab2c2c);
		 background: -moz-linear-gradient(top, #d64b4b, #ab2c2c);
		 background: -ms-linear-gradient(top, #d64b4b, #ab2c2c);
		 background: -o-linear-gradient(top, #d64b4b, #ab2c2c);
		 background-image: -ms-linear-gradient(top, #d64b4b 0%, #ab2c2c 100%);
		 -webkit-box-shadow: rgba(000,000,000,0.3) 0 1px 1px;
		 -moz-box-shadow: rgba(000,000,000,0.3) 0 1px 1px;
		 box-shadow: rgba(000,000,000,0.3) 0 1px 1px;
		 font-family: 'Helvetica Neue',Helvetica, sans-serif;
		 margin: 50px auto;
		 }
	#ribbon-13 h1 {
		 color: #801111;
		 text-shadow: #d65c5c 0 1px 0;
		 margin:0px;
		 padding: 15px 10px;
		 font-size: 1em;
		 }
	#ribbon-13::before, 
	#ribbon-13::after {
		 content: '';
		 position: absolute;
		 display: block;
		 bottom: -1em;
		 border: 1.5em solid #c23a3a;
		 z-index: -1;
		 }
	#ribbon-13::before {
		 left: -2em;
		 border-right-width: 1.5em;
		 border-left-color: transparent;
		 -webkit-box-shadow: rgba(000,000,000,0.4) 1px 1px 1px;
		 -moz-box-shadow: rgba(000,000,000,0.4) 1px 1px 1px;
		 box-shadow: rgba(000,000,000,0.4) 1px 1px 1px;
		 }
	#ribbon-13::after {
		 right: -2em;
		 border-left-width: 1.5em;
		 border-right-color: transparent;
		 -webkit-box-shadow: rgba(000,000,000,0.4) -1px 1px 1px;
		 -moz-box-shadow: rgba(000,000,000,0.4) -1px 1px 1px;
		 box-shadow: rgba(000,000,000,0.4) -1px 1px 1px;
		 }
	#ribbon-13 .ribbon-content:before, 
	#ribbon-13 .ribbon-content:after {
		 border-color: #871616 transparent transparent transparent;
		 position: absolute;
		 display: block;
		 border-style: solid;
		 bottom: -1em;
		 content: '';
		 }
	#ribbon-13 .ribbon-content:before {
		 left: 0;
		 border-width: 1em 0 0 1em;
		 }
	#ribbon-13 .ribbon-content:after {
		 right: 0;
		 border-width: 1em 1em 0 0;
		 }
	#ribbon-13 a::before{
		content:"";
		display:block;
		margin-top:2px;
		border-top: 1px dashed rgba(0, 0, 0, 0.2);
		box-shadow: 0px 0px 2px rgba(255, 255, 255, 0.5);
		position: absolute;
		top: 0;
		left: 0;
		width: 100%;
	}
	#ribbon-13 a::after{
		 margin-bottom:2px;
		 border-top: 1px dashed rgba(0, 0, 0, 0.2);
		 box-shadow: 0px 0px 2px rgba(255, 255, 255, 0.3);
		 content: "";
		 display:block;
		 width: 100%;
		 bottom: 0;
		 left: 0;
		 position: absolute;
	 }
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo13.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-13.jpg"></a></p>
<h4>效果十四</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div class=&quot;ribbons&quot; id=&quot;ribbon-14&quot;&gt;
			&lt;div class=&quot;ribbon-wrapper&quot;&gt;
				&lt;div class=&quot;ribbon&quot;&gt;NEWS&lt;/div&gt;
			&lt;/div&gt;
		&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		#ribbon-14 {
			margin: 50px auto;
			width: 280px;
			height: 100px;
			background: white;
			border-radius: 10px;
			box-shadow:         0px 0px 8px rgba(0,0,0,0.3);
			position: relative;
			z-index: 90;
		}
		#ribbon-14 .ribbon-wrapper{
			width: 85px;
			height: 88px;
			overflow: hidden;
			position: absolute;
			top: -3px;
			right: -3px;
		}
		#ribbon-14 .ribbon{
			font: bold 15px Sans-Serif;
			color: #333;
			text-align: center;
			text-shadow: rgba(255,255,255,0.5) 0px 1px 0px;
			-webkit-transform: rotate(45deg);
			-moz-transform:    rotate(45deg);
			-ms-transform:     rotate(45deg);
			-o-transform:      rotate(45deg);
			position: relative;
			padding: 7px 0;
			left: -5px;
			top: 15px;
			width: 120px;
			background-color: #BFDC7A;
			background-image: -webkit-gradient(linear, left top, left bottom, from(#BFDC7A), to(#8EBF45));
			background-image: -webkit-linear-gradient(top, #BFDC7A, #8EBF45);
			background-image:    -moz-linear-gradient(top, #BFDC7A, #8EBF45);
			background-image:     -ms-linear-gradient(top, #BFDC7A, #8EBF45);
			background-image:      -o-linear-gradient(top, #BFDC7A, #8EBF45);
			color: #6a6340;
			box-shadow:         0px 0px 3px rgba(0,0,0,0.3);
		}
		#ribbon-14 .ribbon::before, 
		#ribbon-14 .ribbon::after {
			content: "";
			border-top:   3px solid #6e8900;   
			border-left:  3px solid transparent;
			border-right: 3px solid transparent;
			position:absolute;
			bottom: -3px;
		}
		#ribbon-14 .ribbon::before {
			left: 0;
		}
		#ribbon-14 .ribbon::after {
			right: 0;
		}
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo14.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-14.jpg"></a></p>
<h4>效果十五</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div class=&quot;ribbons&quot; id=&quot;ribbon-15&quot;&gt;
			&lt;div class=&quot;ribbon-wrap&quot;&gt;			
				&lt;div class=&quot;content&quot;&gt;
					&lt;div class=&quot;ribbon&quot;&gt;&lt;span&gt;NEWS&lt;/span&gt;&lt;/div&gt;
					&lt;div class=&quot;box&quot;&gt;box&lt;/div&gt;
				&lt;/div&gt;
			&lt;/div&gt;
		&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		#ribbon-15 {
			margin: 50px aut0;
			position: relative;
			width: 400px;
		} 
		#ribbon-15 .ribbon-wrap {
			position: absolute;
			tip: 0;
			left: 0;
			width: 100%;
			padding: 5px;
			overflow: hidden;
		}
		#ribbon-15 .content {
			box-shadow: 0 0 1px rgba(0, 0, 0, 0.15);
			color: #444444;
			font: 12px/14px Arial,Helvetica,Sans-serif;
			margin: 0 auto 30px;
			position: relative;
			border: 1px solid #ccc;
			border-radius: 5px;
			background: #fff;
			min-height: 100px;
		}
		#ribbon-15 .ribbon {
				display: inline;
		}
		#ribbon-15 .ribbon span {
				-moz-transform: rotate(-45deg);
				-webkit-transform: rotate(-45deg);
				-o-transform: rotate(-45deg);
				-ms-transform: rotate(-45deg);
				transform: rotate(-45deg);
				background: none repeat scroll 0 0 #D93131;
				box-shadow: 0 0 10px rgba(0, 0, 0, 0.2), 0 5px 30px rgba(255, 255, 255, 0.2) inset;
				color: #FFFFFF;
				display: inline-block;
				font-weight: 900;
				padding: 3px 10px;
				position: absolute;
				left: -29px;
				text-align: center;
				text-transform: uppercase;
				top: 16px;
				width: 90px;
		}
		#ribbon-15 .ribbon::before {
				border-color: transparent transparent #662121;
				border-style: solid;
				border-width: 17px;
				content: "";
				height: 0;
				position: absolute;
				left: 54px;
				top: -23px;
				width: 0;
				z-index: -1;
		}
		#ribbon-15 .ribbon::after {
				border-color: #662121 transparent transparent;
				border-style: solid;
				border-width: 17px;
				content: "";
				height: 0;
				position: absolute;
				left: -10px;
				top: 67px;
				width: 0;
				z-index: -1;
		}
		#ribbon-15 .box {
			margin-left: 50px;
			padding: 20px;
		}
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo15.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-15.jpg"></a></p>
<h4>效果十六</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div class=&quot;ribbons&quot; id=&quot;ribbon-16&quot;&gt;&lt;a href=&quot;&quot;&gt;&lt;span&gt;Ribbon-16&lt;/span&gt;&lt;/a&gt;&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		#ribbon-16::after, 
		#ribbon-16::before {
			margin-top:0.5em;
			content: "";
			float:left;
			border:1.5em solid hsla(20,50%,23%,0.3);
		}
		#ribbon-16::after {
			border-right-color:transparent;
		}
		#ribbon-16::before {
			border-left-color:transparent;
		}
		#ribbon-16 a:link, 
		#ribbon-16 a:visited { 
				color:#000;
				text-decoration:none;
				float:left;
				height:3.45em;
				overflow:hidden;
		}
		#ribbon-16 span {
				background:hsla(20,50%,23%,0.3);
				display:inline-block;
				line-height:2.9em;
				padding:0 1em;
				margin-top:0.5em;
				position:relative;
				-webkit-transition: background-color 0.2s, margin-top 0.2s;  /* Saf3.2+, Chrome */
				-moz-transition: background-color 0.2s, margin-top 0.2s;  /* FF4+ */
				-ms-transition: background-color 0.2s, margin-top 0.2s;  /* IE10 */
				-o-transition: background-color 0.2s, margin-top 0.2s;  /* Opera 10.5+ */
				transition: background-color 0.2s, margin-top 0.2s;
		}
		#ribbon-16 a:hover span {
				background:#FFD204;
				margin-top:0;
		}
		#ribbon-16 span::before {
				content: "";
				position:absolute;
				top:3em;
				left:0;
				border-right:0.5em solid #9B8651;
				border-bottom:0.5em solid hsla(20,50%,23%,0.3);
		}
		#ribbon-16 span::after {
				content: "";
				position:absolute;
				top:3em;
				right:0;
				border-left:0.5em solid #9B8651;
				border-bottom:0.5em solid hsla(20,50%,23%,0.3);
		}
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo16.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-16.jpg"></a></p>
<h4>效果十七</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div class=&quot;ribbons&quot; id=&quot;ribbon-17&quot;&gt;
			&lt;h1&gt;Ribbon-17&lt;/h1&gt;
		&lt;/div&gt;
	</pre><p><strong>CSS Code</strong></p>
<pre>		#ribbon-17 {
			background-color:#999;
			background-image:-webkit-linear-gradient(top, rgba(255,255,255,0.1), rgba(0,0,0,.1));
			background-image:-moz-linear-gradient(top, rgba(255,255,255,0.1), rgba(0,0,0,.1));
			background-image:linear-gradient(top, rgba(255,255,255,0.1), rgba(0,0,0,.1));
			height:40px;
			clear:both;
			color:#fff;
			font:bold 12px/40px sans-serif;
			text-align:center;
			text-shadow:0 1px #666;
			position:relative;
			box-shadow:	0 1px 3px rgba(0,0,0,.3),0 0 0 1px #777,0 1px 0 #bbb inset;
		}
		#ribbon-17::before, 
		#ribbon-17::after {
			content:'';
			border:6px solid transparent;
			position:absolute;
			display:block;
			width:0;
			height:0;
			top:100%;
		}
		#ribbon-17::before{
			left:0;
			border-top-color:#666;
			border-left-width:7px;
			border-right-width:0;
		}
		#ribbon-17::after {
			right:0;
			border-top-color:#666;
			border-right-width:7px;
			border-left-width:0;
		}
		#ribbon-17 h1::after,
		#ribbon-17 h1::before{
			content:"";
			border:19px solid #999;
			position:absolute;
			display:block;
			width:0; height:0;
			top:7px;
			z-index:-1;
		}
		#ribbon-17 h1::after{
			box-shadow:
					0 1px 0 #777,
					0 -1px 0 #bbb,
					0 -2px 0 #777,
					1px 0 0 #777;
			border-left-color:transparent;
			left:-32px;
		}
		#ribbon-17 h1::before {
			box-shadow:
					0 1px 0 #777,
					0 -1px 0 #bbb,
					0 -2px 0 #777,
					-1px 0 0 #777;
			border-right-color:transparent;
			right:-32px;
		}
	</pre><p><strong>DEMO效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo17.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-17.jpg"></a></p>
<h4>效果十八</h4>
<p><strong>HTML Markup</strong></p>
<pre>		&lt;div id=&quot;ribbon-18&quot; class=&quot;ribbons&quot;&gt;	
			&lt;div class=&quot;inset&quot;&gt;&lt;/div&gt;		
			&lt;div class=&quot;container&quot;&gt;
				&lt;div class=&quot;base&quot;&gt;&lt;/div&gt;
				&lt;div class=&quot;left_corner&quot;&gt;&lt;/div&gt;
				&lt;div class=&quot;right_corner&quot;&gt;&lt;/div&gt;
			&lt;/div&gt;
		&lt;/div&gt;
	</pre><p><strong>CSS Demo</strong></p>
<pre>		#ribbon-18 {
			width: 180px;
			height: 280px;
			margin: 50px auto 0;
			position: relative;
			overflow: hidden;
		}
		#ribbon-18 .inset {
			width: 200px;
			height: 55px;
			position: absolute;
			top: -50px;
			left: -10px;
			z-index: 5;
			border-radius: 50%;
			background: rgba(0,0,0,0.3);
			box-shadow: 0px 5px 10px 0px rgba(0,0,0,0.3);
		}
		#ribbon-18 .container {
			position: relative;
			width: 100px;
			height: 250px;
			overflow: hidden;
			margin: 0 auto;
			border-left: 1px solid #631a15;
			border-right: 1px solid #631a15;
		}
		#ribbon-18 .base {
			height: 200px;
			width: 100px;			
			background: rgb(199,59,60);
			background: -moz-linear-gradient(top,  rgba(199,59,60,1) 0%, rgba(184,32,31,1) 100%);
			background: -webkit-gradient(linear, left top, left bottom, color-stop(0%,rgba(199,59,60,1)), color-stop(100%,rgba(184,32,31,1)));
			background: -webkit-linear-gradient(top,  rgba(199,59,60,1) 0%,rgba(184,32,31,1) 100%);
			background: -o-linear-gradient(top,  rgba(199,59,60,1) 0%,rgba(184,32,31,1) 100%);
			background: -ms-linear-gradient(top,  rgba(199,59,60,1) 0%,rgba(184,32,31,1) 100%);
			background: linear-gradient(top,  rgba(199,59,60,1) 0%,rgba(184,32,31,1) 100%);
			filter: progid:DXImageTransform.Microsoft.gradient( startColorstr='#c73b3c', endColorstr='#b8201f',GradientType=0 );
			position: relative;
			z-index: 2;
		}
		#ribbon-18 .base:after {
			content: '';
			position: absolute;
			top: 0;
			width: 86px;
			left: 6px;
			height: 242px;
			border-left: 1px dashed #631a15;
			border-right: 1px dashed #631a15;
		}
		#ribbon-18 .base:before {
			content: '';
			position: absolute;
			top: 0;
			width: 86px;
			left: 7px;
			height: 242px;
			border-left: 1px dashed #da5050;
			border-right: 1px dashed #da5050;
		}
		#ribbon-18 .left_corner {
			width: 100px;
			height: 100px;
			background: #b8201f;
			position: absolute;
			bottom: 20px;
			left: -50px;
			z-index: 1;			
			-webkit-transform: rotate(45deg);
			-moz-transform: rotate(45deg);
			-ms-transform: rotate(45deg);
			-o-transform: rotate(45deg);
			transform: rotate(45deg);
		}
		#ribbon-18 .right_corner {
			width: 100px;
			height: 100px;
			background: #b8201f;
			position: absolute;
			bottom: 20px;
			right: -50px;
			z-index: 1;			
			-webkit-transform: rotate(45deg);
			-moz-transform: rotate(45deg);
			-ms-transform: rotate(45deg);
			-o-transform: rotate(45deg);
			transform: rotate(45deg);
		}
	</pre><p><strong>效果：</strong></p>
<p><a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons/demo18.html"><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/css3-ribbons-18.jpg"></a></p>
<p>上面总共收集了十八种CSS3制作的Ribbons，我想这些效果较为齐全了，希望对大家日后的工作有所帮助，或者对您的学习有所帮助，希望大家喜欢。如果您想了解更多，可以点击<a href="http://www.w3cplus.com/w3cplusDemo/demos/CSS3Ribbons.html">这里</a>查看所有DEMO效果，也可以<a href="http://www.w3cplus.com/w3cplusDemo/demos/demoZip/CSS3Ribbons.zip">下载</a>源码到本地慢慢学习。如果您有更好的实例或者想法可以在下面的评论中给我留言，与我们一起分享。</p>
<p>如需转载烦请注明出处：<strong><a href="http://www.w3cplus.com">W3CPLUS</a></strong></p>
</div></div></div><div><ul><li><a href="http://www.w3cplus.com/blogclassify/11.html">css3</a></li></ul></div><div><div><div><div>
      <div>0</div>
                  <a href="http://www.w3cplus.com/vote/node/268/1/vote/alternate/wlvGVlS747fEE0N008_xTEiL_TASzMaAGx-1rxteesU/nojs" rel="nofollow">
                <div title="Vote up!"></div>
          <div>Vote up!</div>
              </a>
                </div>
</div></div></div><img src="http://www1.feedsky.com/t1/654894290/W3CPlus/feedsky/s.gif?r=http://www.w3cplus.com/css3/pure-css3-ribbons" border="0" height="0" width="0">