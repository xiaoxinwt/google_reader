---
layout: post
title:  "KickJS, Stage3D vs WebGL Performance, Large Commercial Node App"
date:   2011-10-28 07:00:00
author: 
categories: program
---

## KickJS, Stage3D vs WebGL Performance, Large Commercial Node App
### by 
### at 2011-10-28 07:00:00
### original <http://feedproxy.google.com/~r/dailyjs/~3/nbTJ05RAaaU/stage3d-kickjs-eat-out>

<h3>KickJS</h3>
<p><a href="http://www.kickjs.org/">KickJS</a> (GitHub: <a href="https://github.com/mortennobel/KickJS">mortennobel / KickJS</a>, License: <em>New <span>BSD</span></em>) by Morten Nobel-Jørgensen is a new WebGL game engine.  It looks like the author has only been working on it for about a month, but there’s already a <a href="https://github.com/mortennobel/KickJS/tree/master/example/snake">snake game example</a> and an interesting <a href="http://www.kickjs.org/example/shader_editor/shader_editor.html">shader editor example</a>.</p>
<p>The <a href="http://www.kickjs.org/api/">KickJS <span>API</span> documentation</a> seems fairly complete, and provides an overview of the main functionality.  It looks like games are constructed based on <code>KICK.scene.Component</code> objects, and events based on these components.  There’s also WebGL abstraction which reminds me a little bit of <a href="https://github.com/mrdoob/three.js/">three.js</a> — there’s a camera class, and classes for lots of other things like textures, materials, lighting, and meshes.</p>
<h3>Stage3D vs WebGL Performance</h3>
<p><img src="http://dailyjs.com/images/posts/stage3d_Webgl1.png" alt=""></p>
<p>In <a href="http://www.airtightinteractive.com/2011/10/stage3d-vs-webgl-performance/">Stage3D vs WebGL Performance</a>, Felix Turner compares Flash 11’s Stage3D <span>API</span> with WebGL.  Two demos of equivalent code are compared, and both can run so you can see how they perform on your own hardware.  It’s interesting to see how the code compares, because Felix has used three.js and the <span>API</span> doesn’t seem too different to Stage3D.</p>
<p>The post has a good discussion as well, with contributions from three.js’s author.</p>
<h3>Large Commercial Node App</h3>
<p>Sam Fresco who works at an agency called <a href="http://www.clock.co.uk">Clock</a> sent me some interesting PR about a project built with Node called Eat Out that his company has developed for The Times and Sunday Times.  I searched high and low to actually try this project, but when I found Sam’s blog post I realised it’s only accessible to Times subscribers until 2012, when it should be released to a wider audience.  More details are available in his blog post: <a href="http://www.clock.co.uk/blog/entry/view.php?Id=36">There’s Never Been A Better Time To Eat Out</a>.</p>
<p>Clock’s technical blog also has posts on working with Node, including one on <a href="http://blog.clock.co.uk/2011/04/11/deploying-node-js-apps/">deploying Node apps</a>.</p>
<p>Why do I bring this up?  Well, it’s always interesting to hear about large commercial projects that use Node, and also because it helps answer the dreaded boss/client question: “what popular sites use Node, anyway?”</p><img src="http://feeds.feedburner.com/~r/dailyjs/~4/nbTJ05RAaaU" height="1" width="1">