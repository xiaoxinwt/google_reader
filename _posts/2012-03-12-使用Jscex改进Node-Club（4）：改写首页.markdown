---
layout: post
title:  "使用Jscex改进Node Club（4）：改写首页"
date:   2012-03-12 10:00:53
author: jeffz@live.com (老赵)
categories: program
---

## 使用Jscex改进Node Club（4）：改写首页
### by jeffz@live.com (老赵)
### at 2012-03-12 10:00:53
### original <http://blog.zhaojie.me/2012/03/jscexify-nodeclub-4-jscexify-home-page.html>

<p>上次我们分析了Node Club的首页实现，了解了它的功能以及目前的实现方式。不过在我看来，如今使用EventProxy来辅助页面开发并没有解决部分异步编程中的主要问题。甚至可以说，就目前的EventProxy的使用方式而言，即便不借助任何类库，单纯基于JavaScript也可以得到有过之而无不及的编程体验。这次我们便来使用尝试使用<a href="https://github.com/JeffreyZhao/jscex">Jscex</a>来改进首页的逻辑。</p>

<h1>最后准备</h1>

<p>还记得首页的逻辑吗？其中可是用到了许多异步方法：</p>

<pre>tag_ctrl.get_all_tags(<span style="color:blue">function </span>(err, tags) { ... });
topic_ctrl.get_topics_by_query(..., <span style="color:blue">function </span>(err, topics) { ... });
topic_ctrl.get_topics_by_query(..., <span style="color:blue">function </span>(err, hot_topics) { ... });
user_ctrl.get_users_by_query(..., <span style="color:blue">function </span>(err, users) { ... });
user_ctrl.get_users_by_query(..., <span style="color:blue">function </span>(err, tops) { ... });
topic_ctrl.get_topics_by_query(..., <span style="color:blue">function </span>(err, no_reply_topics) { ... });
topic_ctrl.get_count_by_query(..., <span style="color:blue">function </span>(err, all_topics_count) { ... });</pre>

<p>如果要用Jscex来实现首页，这些方法都必须变成Jscex异步方法。之前有朋友说，在一个现成的项目中使用Jscex代价太高，因为每个函数都必须Jscex化。但事实上，我们完全可以在一个现有的项目逐步地引入Jscex，因为我们轻易地将已有的异步操作“封装”为Jscex异步方法，例如<a href="https://github.com/JeffreyZhao/nodeclub/commit/cb7082873452261a175c282c8c5ee6f2257a8f46">在controllers/topic.js文件中</a>：</p>

<pre><span style="color:#006400">/********** Jscex ************/
</span><span style="color:blue">var </span>Jscex = require(<span style="color:maroon">&quot;../libs/jscex&quot;</span>).Jscex;
<span style="color:blue">var </span>Jscexify = Jscex.Async.Jscexify;

exports.get_topics_by_query_async = Jscexify.fromStandard(get_topics_by_query);
exports.get_count_by_query_async = Jscexify.fromStandard(get_count_by_query);</pre>

<p><a href="https://github.com/JeffreyZhao/nodeclub/commit/a1b7635ea846828045b4e539887b20b0c6a472ff">其他两个方法的封装</a>就不列举出来了。可以发现，我们完全不需要一次性把所有的依赖都“重新实现”，可以用到哪儿再封装哪儿，稍后再进行真正的Jscex化——有时候甚至完全无需基于Jscex再写一遍。Jscex适合编写异步操作之间相对复杂的交互，但对于原本就十分简单的异步操作来说，Jscex也并不会带来明显的附加优势。此时我们完全可以保留最普通的异步回调写法，这从任何角度来说都造成问题。</p>

<h1>实现首页</h1>

<p>还记得之前提出的首页逻辑吗？</p>

<pre><span style="color:blue">function </span>(request, response) {
    <span style="color:blue">var </span>tags = tag_ctrl.get_all_tags(); <span style="color:#006400">// 标签
    </span><span style="color:blue">var </span>topics = topic_ctrl.get_topics_by_query(...); <span style="color:#006400">// 最新话题
    </span><span style="color:blue">var </span>hot_topics = topic_ctrl.get_topics_by_query(...); <span style="color:#006400">// 热门话题
    </span><span style="color:blue">var </span>stars = user_ctrl.get_topics_by_query(...); <span style="color:#006400">// 明星用户
    </span><span style="color:blue">var </span>tops = user_ctrl.get_users_by_query(...); <span style="color:#006400">// 得分最高用户
    </span><span style="color:blue">var </span>no_reply_topics = topic_ctrl.get_topics_by_query(...); <span style="color:#006400">// 无回复话题
    </span><span style="color:blue">var </span>topic_count = topic_ctrl.get_count_by_query(...); <span style="color:#006400">// 话题总数

    </span>response.render(<span style="color:maroon">&quot;index&quot;</span>, { ... }); <span style="color:#006400">// 输出HTML
</span>}</pre>

<p><a href="https://github.com/JeffreyZhao/nodeclub/commit/5959221771f3bfac27abc466de019cd394f80654#diff-0">使用Jscex来实现这个逻辑</a>的话，与上述“伪代码”可谓完全一致，唯一的区别只是多了些前后附加的计算逻辑：</p>

<pre><span style="color:blue">var </span>indexAsync = eval(Jscex.compile(<span style="color:maroon">&quot;async&quot;</span>, <span style="color:blue">function </span>(req, res) {
    <span style="color:blue">var </span>page = Number(req.query.page) || 1;
    <span style="color:blue">var </span>limit = config.list_topic_count;

    <span style="color:blue">var </span>data = {
        current_page: page,
        list_topic_count: limit
    };

    data.topics = $await(topic_ctrl.get_topics_by_query_async(...));

    data.hot_topics = $await(topic_ctrl.get_topics_by_query_async(...));

    data.stars = $await(user_ctrl.get_users_by_query_async(...));

    data.tops = $await(user_ctrl.get_users_by_query_async(...));

    data.no_reply_topics = $await(topic_ctrl.get_topics_by_query_async(...));

    <span style="color:blue">var </span>all_topics_count = $await(topic_ctrl.get_count_by_query_async(...));
    data.pages = Math.ceil(all_topics_count / limit);

    data.tags = $await(tag_ctrl.get_all_tags_async());

    <span style="color:#006400">// 计算最热标签
    </span>data.hot_tags = _.chain(data.tags)
        .sortBy(<span style="color:blue">function </span>(t) { <span style="color:blue">return </span>-t.topic_count; })
        .first(5);

    <span style="color:#006400">// 计算最新标签
    </span>data.recent_tags = _.chain(data.tags)
        .sortBy(<span style="color:blue">function </span>(t) { <span style="color:blue">return </span>-t.create_at.valueOf() })
        .first(5);

    res.render(<span style="color:maroon">'index'</span>, data);
}));

exports.index = Unjscexify.toRequestHandler(indexAsync);</pre>

<p>最后一段代码，是使用一个<a href="https://github.com/JeffreyZhao/nodeclub/commit/5959221771f3bfac27abc466de019cd394f80654#diff-1">辅助方法</a>，将一个Jscex异步方法转化为一个普通的HTTP Request Handler：</p>

<pre>Jscex.Unjscexify = {
    toRequestHandler: <span style="color:blue">function </span>(fn) {
        <span style="color:blue">return function </span>(req, res, next) {
            fn(req, res).addEventListener(<span style="color:maroon">&quot;failure&quot;</span>, <span style="color:blue">function </span>() {
                next(<span style="color:blue">this</span>.error);
            }).start();
        }
    }
}</pre>

<p>这里我们将发生的任何错误都通过next向外传递，这是标准的处理方式，当然您也可以使用您自己的错误处理策略。您会发现，我们无需复杂的错误处理方式，在编写Jscex方法时，错误会像普通异常一样向外抛出，直到被统一捕获。</p>

<p>我这里还想提一下Node Club代码里的一些问题。Node Club在我看来是一个比较粗糙的项目，代码质量不太好，一方面是代码风格有些乱（例如使用Tab，符号周围空格等等），还有便是把一些简单的代码写得复杂，例如上面“计算最热标签”这样的逻辑，目前的实现是：</p>

<pre>tags.sort(<span style="color:blue">function </span>(tag_a, tag_b) {
    <span style="color:blue">if </span>(tag_a.topic_count == tag_b.topic_count) <span style="color:blue">return </span>0;
    <span style="color:blue">if </span>(tag_a.topic_count &gt; tag_b.topic_count) <span style="color:blue">return </span>-1;
    <span style="color:blue">if </span>(tag_a.topic_count &lt; tag_b.topic_count) <span style="color:blue">return </span>1;
});</pre>

<p>其实这里完全可以一句话实现：</p>

<pre>tags.sort(<span style="color:blue">function </span>(tag_a, tag_b) {
    <span style="color:blue">return </span>tag_b.topic_count - tag_a.topic_count;
});</pre>

<p>但我更喜欢使用<a href="http://documentcloud.github.com/underscore/">Underscore</a>提供的函数式编程方式，很重要的一点是它保持了输入数据的不变性。数组的sort方法是对本身进行排序，因此Node Club不得不创建一个数组拷贝。使用Underscore可以简化许多代码逻辑，就好比在实现相同功能时，C#代码比Java语言要简单许多。</p>

<h1>并发</h1>

<p>并发是好事，不过在上面的实现中，所有的操作是串行的。有人会说这么做起不到并发的效果，但其实我并不在意，因为我们目前的应用是一个服务器端程序，本身就是并发地承受客户端的请求。如果把处理一个请求看作一个事务的话，我们认为单个事务时串行的，但是已经有大量并发的事务。即便事务里的每个操作都并发起来，但单个事务还是要等到所有操作结束后才能完成（即用户看到页面）。由于系统的负载并没有降低，单个事务处理的总时长并没有减少。</p>

<p>您可以做个实验，硬盘上有10个1G大小的文件，您使用顺序的方式复制所有文件，和同时复制这10个文件，所花时间分别是多少？做过服务器压力测试的同学一定知道，我们加大并发量时，处理相同数量请求所需的总时长并不会减少，甚至随着并发量增加，单位时间内的请求处理能力会明显降低。因此，现代的服务器一般都会在并发量增大的情况下采取保护措施，例如对请求排队，或是返回Service Unavailable错误等等。</p>

<p>并发还有个问题，就是当一个操作失败时，很难“取消”其他操作，这会造成无谓的资源浪费。因此，如果我们盲目地将可以并发的操作都并发起来，对服务的健康并没有什么好处，除非可以确定这个并发操作的确可以同时进行（例如网络访问和磁盘读取），否则我并不倾向于（在一个服务器应用程序里）并发访问。不过作为一个演示，我便<a href="https://github.com/JeffreyZhao/nodeclub/commit/39f75d74cce1bcd8fc9921aaf969c334e37336c2">将所有操作都并发起来</a>吧：</p>

<pre><span style="color:blue">var </span>indexAsync = eval(Jscex.compile(<span style="color:maroon">&quot;async&quot;</span>, <span style="color:blue">function </span>(req, res) {
    <span style="color:blue">var </span>page = Number(req.query.page) || 1;
    <span style="color:blue">var </span>limit = config.list_topic_count;

    <span style="color:blue">var </span>data = $await(<span style="color:red">Task.whenAll</span>({
        topics: topic_ctrl.get_topics_by_query_async({}, {
            skip: (page - 1) * limit,
            limit: limit,
            sort: [[<span style="color:maroon">'last_reply_at'</span>, <span style="color:maroon">'desc'</span>]]
        }),
        hot_topics: topic_ctrl.get_topics_by_query_async({}, {
            limit: 5,
            sort: [[<span style="color:maroon">'visit_count'</span>, <span style="color:maroon">'desc'</span>]]
        }),
        stars: user_ctrl.get_users_by_query_async(
            { is_star: <span style="color:blue">true </span>},
            { limit: 5 }
        ),
        tops: user_ctrl.get_users_by_query_async({}, {
            limit: 10,
            sort: [[<span style="color:maroon">'score'</span>, <span style="color:maroon">'desc'</span>]]
        }),
        no_reply_topics: topic_ctrl.get_topics_by_query_async(
            { reply_count: 0 },
            { limit: 5, sort: [[<span style="color:maroon">'create_at'</span>, <span style="color:maroon">'desc'</span>]] }
        ),
        tags: tag_ctrl.get_all_tags_async(),
        all_topics_count: topic_ctrl.get_count_by_query_async({})
    }));

    data.current_page = page;
    data.list_topic_count = limit;
    data.pages = Math.ceil(data.all_topics_count / limit);

    <span style="color:#006400">// 计算最热标签
    </span>data.hot_tags = _.chain(data.tags)
        .sortBy(<span style="color:blue">function </span>(t) { <span style="color:blue">return </span>-t.topic_count; })
        .first(5);

    <span style="color:#006400">// 计算最新标签
    </span>data.recent_tags = _.chain(data.tags)
        .sortBy(<span style="color:blue">function </span>(t) { <span style="color:blue">return </span>-t.create_at.valueOf() })
        .first(5);

    res.render(<span style="color:maroon">'index'</span>, data);
}))</pre>

<p>Jscex类库“不提倡”盲目并发，它的并发是“可选”的。如果您想要并发哪些操作，将其放在Task.whenAll即可。Task.whenAll是一个辅助方法，您可以输入一个保存Task的对象或是数组（甚至是对象和数组的嵌套），whenAll返回的Task对象会同时发起那些操作，并等待它们全部返回。返回的结果对象，其结构会和输入时完全一致，或是对象，或是数组（甚至对象和数组的嵌套）。在Jscex中，哪些操作串行，哪些操作并发都是由开发人员决定的，完全可以将其轻松地混合起来。例如，我们串行地执行一些前期处理，例如用户认证，然后再将后续的数个操作并发起来。</p>

<p>在Node Club中有个问题就是“盲目并发”，它是用EventProxy将所有的操作并发起来，而并非有选择的处理。EventProxy适合“盲目并发”的场景，但是对“有选择的并发”支持很差，我们很难选择部分操作串行执行。例如我之前的一个示例“<a href="https://github.com/JeffreyZhao/jscex/blob/master/doc/async/samples/copy-dir-cn.md">复制完整目录</a>”，<a href="https://github.com/JeffreyZhao/talks/blob/master/nodeparty-20120108/copy-dir/copy-dir.js">使用Jscex只要如普通编程那样直接实现逻辑</a>即可，而无需<a href="https://github.com/JeffreyZhao/talks/blob/master/nodeparty-20120108/copy-dir/copy-dir-raw.js">如传统编程那样使用各种回调</a>，但如果非要“事件驱动”，非要生搬硬套EventProxy，事件和回调交织在一起，<a href="https://github.com/JeffreyZhao/talks/blob/master/nodeparty-20120108/copy-dir/copy-dir-eventproxy.js">实现便会变得非常复杂</a>。</p>

<p>作为一个面向开发人员的工具，Jscex除了隐藏必要的复杂度之外，还要让目标程序“可控”，无论是串行、并发还是逻辑表达——Jscex使用JavaScript语法，保证了程序逻辑的灵活与可控，尽可能地避免出现<a href="http://en.wikipedia.org/wiki/Leaky_abstraction">Leaky Abstraction</a>。EventProxy的确提供了一种“完全并发”的抽象，但是对于需要“可控并发”，或是“串行执行”的逻辑和场景便显得无能为力了。</p>

<h1>相关文章</h1>

<ul>
  <li><a href="http://blog.zhaojie.me/2012/02/jscexify-nodeclub-1-prepare-nodeclub-website.html">使用Jscex改进Node Club（1）：运行Node Club网站</a></li>

  <li><a href="http://blog.zhaojie.me/2012/02/jscexify-nodeclub-2-import-jscex.html">使用Jscex改进Node Club（2）：引入Jscex类库</a></li>

  <li><a href="http://blog.zhaojie.me/2012/02/jscexify-nodeclub-3-home-page-implementation.html">使用Jscex改进Node Club（3）：分析首页实现</a></li>

  <li>使用Jscex改进Node Club（4）：改写首页</li>
</ul>