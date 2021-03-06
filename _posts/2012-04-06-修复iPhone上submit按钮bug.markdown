---
layout: post
title:  "修复iPhone上submit按钮bug"
date:   2012-04-06 20:21:35
author: Airen
categories: program
---

## 修复iPhone上submit按钮bug
### by Airen
### at 2012-04-06 20:21:35
### original <http://www.w3cplus.com/css3/style-submit-buttons-for-iphone-safari>

<div><div><div><p>自从完成上次iPhone的几个页面效果后，一直在没有制作iPhone的页面效果了，今天在公司写了一个登录页面效果，让我碰到一个怪异的问题——“<strong style="color:red">表单中的input[type="submit"]和input[type="reset"]按钮在iPhone的safari浏览器下圆角有一个bug</strong>”。下面我来简单的描述一下这个bug的样子：</p>
<p>初载入页面后，表单中的input[type="submit"]和input[type="reset"]按钮渲染成下图的样子：</p>
<p><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/mobile-safari-submit-button-1.jpg"></p>
<p>奇怪的是你点击以后就会正常：</p>
<p><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/mobile-safari-submit-button-2.jpg"></p>
<p>对比一下，你也会觉得怪，怪都算了，还不知道如何下手:</p>
<p><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/mobile-safari-submit-button-3.jpg"></p>
<p>或许很多同学会认为我的样式代码没写好，那么想让大家知道是怎么一回事，先来看看我写的代码：</p>
<p><strong>input[type="submit"]和input[type="reset"]样式代码：</strong></p>
<pre>		.form-actions input{
			width: 30%;
			cursor: pointer;	
			background: rgb(61, 157, 179);
			padding: 8px 5px;
			font-family: 'BebasNeueRegular','Arial Narrow',Arial,sans-serif;
			color: #fff;
			font-size: 24px;	
			margin: 5px;
			border: 1px solid rgb(28, 108, 122);	
			margin-bottom: 10px;	
			text-shadow: 0 1px 1px rgba(0, 0, 0, 0.5);
			border-radius: 3px;	
			box-shadow:0px 1px 6px 4px rgba(0, 0, 0, 0.07) inset,
	        0px 0px 0px 3px rgb(254, 254, 254),
	        0px 5px 3px 3px rgb(210, 210, 210);
		 -webkit-transition: all 0.2s linear;
	        transition: all 0.2s linear;
		}
		.form-actions input:hover{
			background: rgb(74, 179, 198);
		}
		.form-actions input:active,
		.form-actions input:focus{
			background: rgb(40, 137, 154);
			position: relative;
			top: 1px;
			border: 1px solid rgb(12, 76, 87);	
		  box-shadow: 0px 1px 6px 4px rgba(0, 0, 0, 0.2) inset;
		}
	</pre><p>这样的代码在浏览器中浏览是完全没有问题的：</p>
<p><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/mobile-safari-submit-button-4.jpg"></p>
<p><strong style="color:red">注：请使用safari测试上面代码</strong>。</p>
<p>可是上面的代码就在iPhone的Safari浏览器下出开头所陈述的问题。一下真不好如何动手解决，因为从来没有接触过，所以就一直没有碰到过。但问题出了，就要想办法解决，于是在GG上搜索“input submit for iPhone”，还真找到了问题所在。<a href="http://thinkvitamin.com/author/keir-whitaker/" rel="author" title="Posts by Keir Whitaker">Keir Whitaker</a>在<a href="http://thinkvitamin.com/design/styling-submit-buttons-for-mobile-safari/">Styling Submit Buttons for Mobile Safari</a>中介绍的内容和我碰到的问题可真是一模一样，按其方法在样式中加入：</p>
<pre>	.form-actions input{
		...	
	  <strong style="color:red"> -webkit-appearance: none;</strong>     
	}
	</pre><p>更新到iPhone一看，真爽，问题解决了。</p>
<p><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/mobile-safari-submit-button-5.jpg"></p>
<p>原来问题出在这里，iPhone上的safari解析input[type="submit"]和input[type="reset"]按钮会以苹果浏览器的默认UI渲染，这样就出现我刚才那种现像，我们在样式中明确的设置了button的圆角值，但到iPhone的safari上就不生效了。要想让他生效，就需要在样式中明确的指名：</p>
<pre>	.form-actions input{
		...	
	  <strong style="color:red"> -webkit-appearance: none;</strong>     
	}
	</pre><p>告诉浏览器我们不希望按钮按苹果的默认UI来渲染。</p>
<p>那么"-webkit-appearance"对button还有什么影响呢？大家可以参考下面的截图：</p>
<p><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/mobile-safari-submit-button-6.jpg"></p>
<p><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/mobile-safari-submit-button-7.jpg"></p>
<p><img alt="" src="http://www.w3cplus.com/sites/default/files/201202/mobile-safari-submit-button-8.jpg"></p>
<p>上图所显示的效果，都将button设置了：</p>
<pre>		.button {
			border-radius: 0;
		}
	</pre><p>效果图明显的告诉我们，在不同的“-webkit-appearance”选值情况下，button所渲染的效果是不一样的，详细的测试代码大家可使用safari浏览器点击<a href="http://www.deleri.com/test.html">这里</a>。有关于“-webkit-appearance”的详细介绍，大家还可以参阅：</p>
<ol><li><a href="http://css-infos.net/property/-webkit-appearance">-webkit-appearance properties</a></li>
<li><a href="http://trentwalton.com/2010/07/14/css-webkit-appearance/">Trent Walton on -webkit-appearance</a></li>
<li><a href="http://developer.apple.com/safari/library/documentation/AppleApplications/Reference/SafariCSSRef/Articles/StandardCSSProperties.html#//apple_ref/doc/uid/TP30001266-_webkit_appearance">Safari CSS Reference</a></li>
</ol><p>这回算是知道了，最后我建议大家，我们可以直接在“reset.css”样式文件中加处这么一句：</p>
<pre>		input[type="submit"],
		input[type="reset"],
		input[type="button"],
		button {
			-webkit-appearance: none;
		}
	</pre><p>这样一来就不会为这样的问题头痛了。</p>
<p>如果你还没有碰到，或者你也在开发移动端web，都希望你记住这个小技巧，因为当你在制作中碰到这样的问题时，不会为此抓破头皮，能解决你问题。最后希望大家喜欢这篇文章，如果你觉得对你有所帮助，可以推荐给你的朋友，或者有更好的分享可以在下面的评论中直接给我们留言。</p>
<p>如需转载烦请注明出处：<strong><a href="http://www.w3cplus.com">W3CPLUS</a></strong></p>
</div></div></div><div><ul><li><a href="http://www.w3cplus.com/blogclassify/11.html">css3</a></li></ul></div><div><div><div><div>
      <div>0</div>
                  <a href="http://www.w3cplus.com/vote/node/271/1/vote/alternate/8LwZzeBL0RKo-k2Vp9aTkSO1GcxQsEDDeQGlMjyb22M/nojs" rel="nofollow">
                <div title="Vote up!"></div>
          <div>Vote up!</div>
              </a>
                </div>
</div></div></div><img src="http://www1.feedsky.com/t1/654894287/W3CPlus/feedsky/s.gif?r=http://www.w3cplus.com/css3/style-submit-buttons-for-iphone-safari" border="0" height="0" width="0">