---
layout: post
title:  "tolua的tolua_toxxx系列API设计"
date:   2012-05-10 15:38:00
author: Kevin Lynx
categories: program
---

## tolua的tolua_toxxx系列API设计
### by Kevin Lynx
### at 2012-05-10 15:38:00
### original <http://www.udpwork.com/item/7266.html>

<p>原文链接：<a href="http://codemacro.com/2012/05/10/tolua-api/">http://codemacro.com/2012/05/10/tolua-api/</a></p>
<p>我们使用tolua++手工绑定c/c++接口到lua中，在绑定的接口实现里，就需要取出传入的参数。tolua++中提供了一系列tolua_toxxx函数，例如：</p>
<div><pre>lua_Number tolua_tonumber(lua_State *L, int narg, lua_Number def)
const char *tolua_tostring(lua_State *L, int narg, const char *def)
</pre></div>
<p>这些函数都有一个def参数。乍一看，这些函数使用起来很简单。传入lua_State，传入参数在栈中的位置，然后再传一个<strong>失败</strong>
后返回的默认值。</p>
<p>我重点要说的是这里这个<strong>失败</strong>
，按正常程序员的理解，针对lua而言，什么情况下算失败呢？lua语言里函数参数支持不传，此时实参为nil，将nil转换为一个c类型必然失败；参数类型不正确算不算失败？你传一个user data，c里按数字来取，这也算失败。</p>
<p>这么简单的API还需要多纠结什么呢？然后我们浩浩荡荡地写了上百个接口，什么tolua_tostring/tolua_tonumber的使用少说也有500了吧？</p>
<p>然后有一天，服务器宕机了，空指针:</p>
<div><pre>/* 失败返回&quot;&quot;，还能省空指针的判断 */
const char *name = tolua_tostring(L, 1, &quot;&quot;);
if (name[0] == &#39;\0&#39;) { /* 空串总得判断吧 */
 ...
}
</pre></div>
<p>跟踪后发现，脚本里传入的是nil，这里的name取出来是NULL，而不是”“（的地址）。然后吐槽了一下这个API，辛苦地修改了所有类似代码，增加对空指针的判断。我没有多想。</p>
<p>故事继续，有一天服务器虽然没宕机，但功能不正常了:</p>
<div><pre>float angle = (float) tolua_tonumber(L, 1, 2 * PI);
...
</pre></div>
<p>这个意思是，这个函数的参数1默认是2*PI，什么是默认？lua里某函数参数不传，或传nil就是使用默认。因为不传的话，这个实参本身就是nil。但，tolua_tonumber的行为不是这样的，它的实现真是偷懒:</p>
<div><pre>TOLUA_API lua_Number tolua_tonumber (lua_State* L, int narg, lua_Number def)
{
 return lua_gettop(L)&lt;abs(narg) ? def : lua_tonumber(L,narg);
}
TOLUA_API const char* tolua_tostring (lua_State* L, int narg, const char* def)
{
 return lua_gettop(L)&lt;abs(narg) ? def : lua_tostring(L,narg);
}
</pre></div>
<p>意思是，只有当你不传的时候，它才返回默认值，否则就交给lua的API来管，而lua这些API是不支持应用层的默认参数的，对于lua_tonumber错误时就返回0，lua_tostring错误时就返回NULL。</p>
<p>这种其行为和其带来的common sense不一致的API设计，实在让人蛋疼。什么是common sense呢？就像一个UI库里的按钮，我们都知道有click事件，hover事件，UI库的文档甚至都不需要解释什么是click什么是hover，因为大家看到这个东西，就有了共识，无需废话，这就是common sense。就像tolua的这些API，非常普通，大家一看都期待在意外情况下你能返回def值。但它竟然不是。实在不行，你可以模仿lua的check系列函数的实现嘛:</p>
<div><pre>LUALIB_API lua_Number luaL_checknumber (lua_State *L, int narg) {
 lua_Number d = lua_tonumber(L, narg);
 if (d == 0 &amp;&amp; !lua_isnumber(L, narg)) /* avoid extra test when d is not 0 */
 tag_error(L, narg, LUA_TNUMBER);
 return d;
}
</pre></div>
<p>即，根本不用去检查栈问题，直接在lua_tonumber之后再做包装检查。何况，lua需要你去检查栈吗？当你访问了栈外的元素时，lua会自动返回一个全局常量luaO_nilobject:</p>
<div><pre>static TValue *index2adr(lua_State *L, int idx) {
 ...
 if (o &gt;= L-&gt;top) return cast(TValue*, luaO_nilobject);
}
</pre></div>
<p>另，程序悲剧也来源于臆想。</p>
<img src="http://www.cppblog.com/kevinlynx/aggbug/174460.html">
<br>

<br>
<div><a href="http://www.cppblog.com/kevinlynx/">Kevin Lynx</a>2012-05-10 15:38<a href="http://www.cppblog.com/kevinlynx/archive/2012/05/10/174460.html#Feedback">发表评论</a></div>

			<div style="margin-top:8px;padding:6px 0;border-top:1px solid #3cf">
				<div style="text-align:center;margin:16px 0;padding:6px;border:0px dashed #999;font-family:arial;font-size:26px;font-weight:bold">
	<a href="http://www.udpwork.com/item/7266.html#review_form" title="不喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_down24.gif" alt="">
		<span style="color:#f33">0</span>
	</a>
	   
	<a href="http://www.udpwork.com/item/7266.html#review_form" title="喜欢" style="text-decoration:none">
		<img src="http://www.udpwork.com//images/thumb_up24.gif" alt="">
		<span style="color:#3c3">0</span>
	</a>
</div>				<p>
					由 <a href="http://www.udpwork.com/">IT 牛人博客聚合网站(udpwork.com)</a> 聚合
					|
					<a href="http://www.udpwork.com/item/7266.html#reviews">评论: 0</a>
					|
					<a href="http://book.benegg.com/tag/%E7%BC%96%E7%A8%8B?from=udpwork-feed">10000+ 本编程/Linux PDF/CHM 电子书下载</a>
				</p>
			</div>