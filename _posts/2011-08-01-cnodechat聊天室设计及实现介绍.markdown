---
layout: post
title:  "cnodechat聊天室设计及实现介绍"
date:   2011-08-01 23:08:02
author: yixuan
categories: program
---

## cnodechat聊天室设计及实现介绍
### by yixuan
### at 2011-08-01 23:08:02
### original <http://cnodejs.org/blog/?p=1074>

<p>我们cnodejs社区使用nodejs，结合http streaming服务器推送技术实现了下面这样一个小型的在线聊天室，我们取名为cnodechat。这里介绍一下我们聊天室的设计和实现，欢迎大家交流</p>
<p><a href="http://cnodejs.org/blog/wp-content/uploads/2011/08/%E6%95%88%E6%9E%9C%E5%9B%BE.jpg"><img src="http://cnodejs.org/blog/wp-content/uploads/2011/08/%E6%95%88%E6%9E%9C%E5%9B%BE.jpg" alt="" title="效果图" width="803" height="604"></a></p>
<p>如果你是直接看到这篇文章，而没有使用过cnodechat聊天室的话，我还是希望你先去玩玩我们的小产品，看看上面有些什么人，这个东西怎么聊，但你可别一聊就不回来了。我们的聊天室暂时不支持ie内核的，可以使用chrome浏览器。地址：http://cnodechat.cnodejs.net/ 聊天室的简单使用说明如下：<br>
1.输入框内直接输入信息，回车，是给所有人发送信息。<br>
2.输入框内＠某个人，回车，是给某个人发送信息，例如给alice发送信息，则为@alice something。不要忘记在名字和输入的内容之间加入空格。另外，直接点击右侧某个人的名字，输入框会直接填充＠信息。</p>
<p>用过之后觉得还可以吧，虽然多少存在一些问题，如果发现希望大家能够提出来。我就cnodechat聊天室做一个说明，偏重于设计，供大家学习，参考，交流。</p>
<p>Cnodechat是使用nodejs技术实现的基于web浏览器的聊天室，是一个为大家学习nodejs开发的小产品。<br>
聊天室实现中，服务器往客户端（web浏览器）传送数据的方式采用的是http streaming的服务器推方式。与long polling方式不同的是，http streaming保持数据通道的打开状态。而客户端传递消息给服务器则都是通过传递http 请求。</p>
<p>那客户端和服务器端的数据通道是如何打开的呢？<br>
服务器端是如何维护这个数据通道，并且是如何推送数据的呢？<br>
服务器端如何识别每个具体的用户的呢？<br>
………</p>
<p>让我们带着这些疑问来看看，cnodechat是如何使用NodeJS来完成这些工作的。</p>
<p>我们先说一下当启动聊天室server的时候，server会做些什么。<br>
1. server首先会读取所有需要加载的静态文件，包括聊天室的客户端html,css,js代码，除此之外还有诸如png图片等等。当服务器之后接收到http请求时将这些资源返回。<br>
2. 然后server会初始化一个pipeClient对象。有人会问，这个对象是做什么的？<br>
问得好！这个对象是用来管理所有与服务器连接的聊天成员的，并且提供了一系列管理和操作的函数，包括往具体用户推送数据，删除某个具体用户等等。<br>
当一个用户连接到聊天室后，这个pipeClient对象会保存这个用户的response对象，用于之后服务器端推送数据。<br>
3. 当然server在初始化时会加载各种需要的类库和定义获得http请求后的回调函数，这就不多说了。</p>
<p>我们先来看一下，当一个用户访问聊天室的时候会发生一些什么样的事情。</p>
<p><a href="http://cnodejs.org/blog/wp-content/uploads/2011/08/%E6%9C%AA%E5%91%BD%E5%90%8D1.jpg"><img src="http://cnodejs.org/blog/wp-content/uploads/2011/08/%E6%9C%AA%E5%91%BD%E5%90%8D1.jpg" alt="" title="nodechat1" width="481" height="624"></a></p>
<p>用户连入聊天室的大致过程就是上图这样。客户端会先请求聊天室代码，服务器端返回，客户端的js代码进行初始化设置并且发起join chat（加入聊天请求），服务器端设置新的聊天成员并将其信息保存在pipeclient中，然后将其他用户的信息返回给这个用户，over。</p>
<p>客户端会打开自己的数据通道，而服务器端则会保存这个用户的response对象用于服务器数据推。</p>
<p>需要注意的是：<br>
1. 除了最后推送其他聊天成员的信息时使用的是服务器推技术（因为已经建立好通道并且服务端获得了http写回对象），所有开始时的通信都采用http请求方式。<br>
2. 服务器端的return pages动作和set new visitor动作之间并非阻塞等待，return pages动作只是单纯的页面返回而已。<br>
3. 客户端 init objects, open streaming pipe和ask for joining动作都是服务器返回的js文件执行的，使用jquery包装的ajax方法。<br>
4. 至于服务器对于不同的http请求为何会采取不同的动作以及采取什么动作，会在后面说明，看到这不明白的先不要着急。</p>
<p>好了，看到这里你应该已经明白，用户是如何加入聊天室并且获得大家的信息，然后参与大家的聊天了，至于聊什么，我就管不着了。</p>
<p>然后问题又来了，当用户进行一些操作的时候，比如输入聊天信息，修改名字等等操作的时候是怎么和服务器来相互通信？别的用户又是如何得到实时的聊天信息？服务器推送技术在哪里用到了？不要急不要急，我们来举个例子看看就明白了，就拿发送某个消息来举例，例如换名字之类的过程就和这个差不多了，朋友们，请看下图！</p>
<p><a href="http://cnodejs.org/blog/wp-content/uploads/2011/08/%E6%9C%AA%E5%91%BD%E5%90%8D2.jpg"><img src="http://cnodejs.org/blog/wp-content/uploads/2011/08/%E6%9C%AA%E5%91%BD%E5%90%8D2.jpg" alt="" title="nodechat2" width="704" height="549"></a></p>
<p>上图描述的web browser1发送消息给web browser2的过程。<br>
1. browser1把消息和包括自己的信息组合成一个JSON对象串传送给server（这里请求的发送依旧使用的是http请求）。<br>
2. server获得之后解析JSON对象后会得到这个消息的标识符，上例中得到的是“发送消息”这个标识符（标识符也有可能是改名字，用户离线等）。<br>
3. 然后获得其中需要发送消息的对象id，把id和内容传送给pipeclient（如果你忘记pipeclient是什么了可以翻到上面去温故一下），pipeclient根据id从维护的用户队列中获得用户的信息（关键是什么？对了，response对象），然后使用response对象把数据通过管道推送给browser2，同样也推送回browser1（人家自己发的消息自己也总得得到吧）。</p>
<p>到了这里，我相信你应该知道我们的cnodechat是如何工作的了吧。感觉介绍到这里，总觉得不太爽，似乎说程序说半天不贴代码说不过去，感觉纸上谈兵一样。把所有代码贴上来一下几百上千的，看着累，我就挑重点的代码贴一点。主要是服务器端对请求的区分和处理，还有是客户端的一些重要代码。</p>
<p>下面这段代码是服务器端区分请求并处理的代码：</p>
<pre lang="javascript">var server = http.createServer(function (req, res){
	if(reqq.length &lt; 1){
		reqq.push(req);
	}else{
		res.writeHeader(200);
		res.end(&#39;too busy&#39;);
		return ;
	}
	var req = reqq[0];
	req.time = new Date().getTime();
	// main page
	if (req.url == &#39;/&#39;) {
		var p = page[&quot;static/chat.html&quot;];
		res.writeHeader(200,{&#39;Content-Type&#39;:p[&#39;type&#39;],
							&#39;Content-Length&#39;:p.length});
		res.end(p);
	} else if(req.url.match(/\.(css|js|png|html)$/)) {
		var p = page[&quot;static&quot; + req.url];
		res.writeHeader(200,{&#39;Content-Type&#39;:p[&#39;type&#39;],
							&#39;Content-Length&#39;:p.length});
		res.end(p);
	} else if(req.url.match(/^\/join\//)){
		join(req,res);
	} else if(req.url.match(/^\/rbjoin\//)){
		rbjoin(req,res);
	} else if(req.url.match(/^\/send\//)){
		send(req,res);
		writeBack(res);
	} else if(req.url.match(/^\/quit\//)){
		writeBack(res);
		var uid = req.url.substr(6);
		quit(uid);
	} else if(req.url.match(/^\/cname\//)){
		writeBack(res);
		var parsedUrl = req.url.split(&#39;/&#39;);
		changeName(parsedUrl[2],parsedUrl[3]);
	} else if(req.url.match(/^\/heartbeat\//)){
		writeBack(res);
		var uid = req.url.substr(11);
		heartbeat(uid);
	} else {
		res.writeHeader(404, {&quot;Content-Type&quot; : &quot;text/plain&quot;});
		res.end();
	}
	reqq.shift();
});</pre>
<p>这段代码就是根据http请求中带有的参数来判断用户的行为，比如是改名字或者发送信息，这些参数会在用户进行操作的时候自动加上，这由客户端的js代码控制，用户不用关心，这下明白server是怎么知道你想干嘛的了吧。</p>
<p>从上面可以看到，处理都是调用一个函数，这些函数封装了对请求的处理，具体的实现就不贴出来了，实质就是调用pipeclient中的方法来进行消息转发（消息包括用户发的消息，用户改名等系统信息等等），当然还会进行一些比如格式化数据等等的操作，这就不是介绍的重点了。</p>
<p>下面介绍一下客户端js关键的一些代码：</p>
<pre lang="javascript">$(document).ready(function(){
        /*Comet类封装了客户端（浏览器）与服务器通信的方式，
        包括客户端发送数据的操作和获得数据的操作，
        获得数据的操作是在实例化Comet对象时传参设置的*/
	var Comet = function(options) {
                //一些变量的设置
                .......
		var me = {
                //对外可调用函数的定义
                ......
		}
                /*这里是关键，生成一个ajax请求并持续等待服务器端的
                数据推送（readyState==3）*/
		me.conn = new XMLHttpRequest();
		me.conn.open("GET", me.joinUrl + (me.token ? me.token : ''), true);
                          ......
		me.conn.onreadystatechange = function(){
			if (this.readyState == 3 || this.readyState == 4) {
                           //这里定义收到服务器端推过来的数据后的操作
                           ......
			}
		}
		me.conn.send();
		return me;
	}

        /*ChatBox封装了js对页面元素的修改操作，
        例如修改页面上的在线人数，聊天的消息等*/
	var ChatBox = function(id, conf){
	   ......
	}

        /*chatStick将Comet和ChatBox粘合起来，
        定义Comet中收到数据后的具体动作，动作包括使用ChatBox来修改页面元素。
        除此之外还定义了客户端发送数据给服务端的动作。*/
	function chatStick(id, conf){
		var me = {
                   //具体对象的变量设置
                    ......
		};
                //定义Comet中收到数据后的具体动作
                ......
               /*初始化comet对象，并且将上述定义的具体动作作为参数传给comet对象，
               告诉客户端（浏览器）收到服务器推过来的数据后如何处理*/
		me.comet = new Comet({
			token : getCookie("cnode-chat-token"),
			cb : {
                        //针对服务器传输过来数据的各种处理
                        ......
			}
		});
		me.box.setCallback({
                       /*定义客户端发送给服务器的操作，
                       当用户执行操作例如修改名字的时候，会触发comet中的方法，
                       例如修改名字的方法。这些方法由这里传入。*/
			......
		});
	}

	window.setTimeout(function(){
		 chatStick('chat', {maxLog : 200});
	}, 0)

});</pre>
<p>根据上述代码中的注释，相信你也明白客户端在这个那个聊天系统中是如何工作的了吧。<br>
如果你真的很喜欢我们的cnodechat聊天室，想细探究竟，学习一下的话，不要着急，聊天室的源代码不久就会在github上发布，敬请期待啊。</p>
<p>什么？你有些问题吗？我试着自问自答一下，你看看是不是在其中呢？<br>
1. Q：服务器是如何识别每个具体的用户的呢？<br>
A：每个用户在加入聊天室的时候，服务器会随机产生一个token，这个token会贯穿用户和聊天室交互的所有过程中，以此来表明身份。</p>
<p>2. Q：我在聊天室上聊天，突然掉线了，重新链接后发现自己的名字还是之前设置的名字，这是怎么办到的呢？<br>
A：每一个用户服务器都会生成唯一的token，然后会发送给客户端，客户端（浏览器）会将这个token作为cookie保存。如果在一段时间内重新登录，服务器端读取cookie时发现用户信息在服务器端还未被删除，依然有效，则会继续使用之前保存在服务器端的信息，例如用户名字等。</p>
<p>3. Q：一个用户不小心断线，服务器如何知道这个用户掉线并通知其他聊天者呢？<br>
A：用户和服务器之间存在双向的心跳信息传输，一段时间后用户没有传送心跳信息给服务器后，服务器就认为这个用户已经离线，并将其token删除。并且通知其他用户。</p>
<p>4. Q：能够再详细介绍下服务器推技术吗？<br>
A：那我就给你个链接吧，这里有关于服务器推使用nodejs技术的代码，http://cnodejs.org/blog/?p=112。我们的cnodechat聊天室就是使用这样的方式来实现服务器推的。</p>
<p>5. Q：聊天室的代码开源吗？<br>
A：很抱歉告诉您，暂时还不开源，但是过段时间我们会把源代码上传至github上，届时我们会在社区上说明。只要你多关注我们cnodejs社区，你会得到越来越多关于nodejs方面的学习资料。</p>
<p>什么？我没有预计到你的问题？没关系，那就在下面留言吧，我们会尽力解答大家的困惑，和大家一起交流，互相学习。<br>
希望大家能支持cnodejs社区，我们愿意在更多方面支持大家，共同学习，共同成长。</p>