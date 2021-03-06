---
layout: post
title:  "与君共品代码: Spelling Corrector"
date:   2011-04-30 15:36:49
author: 
categories: program
---

## 与君共品代码: Spelling Corrector
### by 
### at 2011-04-30 15:36:49
### original <http://www.iteye.com/topic/1025490>

<p>这是我在ITEYE发的第一个帖子：</p>
<p><br>1. 一段代码(很短，才21行)<br>2. 代码赏析(泡杯咖啡，慢慢品)<br><br><strong>1. 一段代码</strong><br><br>如果有人面试问我写过最棒的一段代码是什么，我可能真说不上什么。<br>但要是有人问我见过最棒的一段代码是什么？那非<a href="http://norvig.com">Peter Norvig</a>的<a href="http://norvig.com/spell-correct.html">Spelling Corrector</a>莫属了。<br><br>好，大餐上来啦(python2.5, 21行)：</p>
<p> </p>
<pre name="code">import re, collections

def words(text): return re.findall(&#39;[a-z]+&#39;, text.lower()) 

def train(features):
    model = collections.defaultdict(lambda: 1)
    for f in features:
        model[f] += 1
    return model

NWORDS = train(words(file(&#39;big.txt&#39;).read()))

alphabet = &#39;abcdefghijklmnopqrstuvwxyz&#39;

def edits1(word):
   splits     = [(word[:i], word[i:]) for i in range(len(word) + 1)]
   deletes    = [a + b[1:] for a, b in splits if b]
   transposes = [a + b[1] + b[0] + b[2:] for a, b in splits if len(b)&gt;1]
   replaces   = [a + c + b[1:] for a, b in splits for c in alphabet if b]
   inserts    = [a + c + b     for a, b in splits for c in alphabet]
   return set(deletes + transposes + replaces + inserts)

def known_edits2(word):
    return set(e2 for e1 in edits1(word) for e2 in edits1(e1) if e2 in NWORDS)

def known(words): return set(w for w in words if w in NWORDS)

def correct(word):
    candidates = known([word]) or known(edits1(word)) or known_edits2(word) or [word]
    return max(candidates, key=NWORDS.get)</pre>
<p><br><br><strong>2. 代码赏析</strong><br><br>这里只是简单介绍一下，<strong>强烈建议看<a href="http://norvig.com/spell-correct.html">原文</a>，英文不好的可以看徐大哥的<a href="http://blog.youxu.info/spell-correct.html">翻译</a></strong>(需fq)<br><br>大家一定都曾经在Google里输入错误的单词，比方说输入javaeya，Google会提示:<br><em>Showing results for javaeye. Search instead for javaeya</em><br>(其实我想举iteye为例子的，不幸的是Google还没把这词给当回事...)<br><br>是的，Peter的这段代码就是干这事的，输入为一个单词，输出为单词的纠正词：<br><br><em>&gt;&gt;&gt; correct(&#39;speling&#39;)<br>'spelling'<br>&gt;&gt;&gt; correct(&#39;korrecter&#39;)<br>'corrector'</em><span style="text-decoration:underline"><br></span><br>好吧，简单分析一下：<br><br>第一个方法：</p>
<pre name="code">def words(text): return re.findall('[a-z]+', text.lower())</pre>
<p> </p>
<p>返回文本里所有单词，并使其字母变为小写(Google里搜索也是不分大小写的，因为如果分大小写的话太复杂了)<br><br>第二个方法：</p>
<pre name="code">def train(features):
    model = collections.defaultdict(lambda: 1)
    for f in features:
        model[f] += 1
    return model</pre>
<p> </p>
<p>返回一个'key=word', value='word出现个数'的字典(相当于java里的Hashtable)<br>用lambda:1(即没有出现的单词的个数记为1)的原因是，概率里用小概率而不是用0来替代不可能事件<br><br>于是:</p>
<pre name="code">NWORDS = train(words(file('big.txt').read()))</pre>
<p> </p>
<p>即把所有在big.txt里的单词按照出现的个数做了统计<br>(Peter在灰机上写的这段代码，big.txt是他自己电脑的一些文档)<br><br>再下来，Peter对输入错误的单词的情况做了几种分类：</p>
<pre name="code">def edits1(word):
    splits     = [(word[:i], word[i:]) for i in range(len(word) + 1)] #@IndentOk
    deletes    = [a + b[1:] for a, b in splits if b]
    transposes = [a + b[1] + b[0] + b[2:] for a, b in splits if len(b)&gt;1]
    replaces   = [a + c + b[1:] for a, b in splits for c in alphabet if b]
    inserts    = [a + c + b     for a, b in splits for c in alphabet]
    return set(deletes + transposes + replaces + inserts)</pre>
<p> </p>
<p>即几下四种(这里以iteye做为例)：<br>1. 漏输字母：iteye -&gt; itye<br>2. 字母错位： iteye -&gt; ityee<br>3. 输错字母： iteye -&gt; iteya<br>4. 多输字母： iteye -&gt; iteyee<br>可以看出edits1(word)这个方法即把所有可能字母出错个数为1的单词作了枚举<br>(注意这里字母出错个数为1的意思是不包括输漏，错，多超过两个字母的情况，比如iteyeee是不被包含的)<br><br>(从这个角度来看，iteye比javaeye这个域名好多了，我自己试了下javaeye输错的概率会比iteye大很多)<br><br>好，接着下一个方法：</p>
<pre name="code">def known_edits2(word):
    return set(e2 for e1 in edits1(word) for e2 in edits1(e1) if e2 in NWORDS)</pre>
<p> </p>
<p>这段代码返回的是出错字母个数为2且出现在培训样本里的单词，之所以这样做是因为：<br>假如不加判断if e2 in NWORDS的话，"something"这样一个单词的两个字母错误情况居然达到了 114,324 个. <br>Peter在做了些验证后决定只把那些正确的词作为候选词<br><br>好的，可以进行纠错了</p>
<pre name="code">def correct(word):
    candidates = known([word]) or known(edits1(word)) or known_edits2(word) or [word]
    return max(candidates, key=NWORDS.get)</pre>
<p> </p>
<p>在这里，Peter做了个直接了断的假设，即纠正词的选取优先级依次为 ：库里的词，出错一个词，出错两个词<br><br><br>现在从三个方面来欣赏这段代码：<br><br><strong>2.1. 数学之美</strong><br><br>贝叶斯定理是这段代码的核心，做为人工智能领域的大牛，Peter对这种问题的数学建模能力确实非凡<br><br>设若单词c(correction)是输入单词w(word)的正确原型，那么P(c\w)即为给定的单词w, 单词c是w的正确原型的概率<br><br>通过贝叶斯做定理的变型，可得:<br><strong>P(c|w)=P(c*w)/P(w)=(P(w|c)*P(c))/P(w) </strong><br>因为w是用户输入给定的，故p(w)为1，得：<strong>P(c|w)=P(w|c)*P(c)</strong></p>
<p><br>为什么要把p(c\w)转化为p(w\c)呢？Peter给出的解释是：</p>
<div> 写道</div>
<div>The answer is that P(c|w) is already conflating two factors, and it is easier to separate the two out and deal with them explicitly.<br>Consider the misspelled word w="thew" and the two candidate corrections c="the" and c="thaw". Which has a higher P(c|w)? Well,<br>"thaw" seems good because the only change is "a" to "e", which is a small change. On the other hand,<br>"the" seems good because "the" is a very common word, and perhaps the typist's finger slipped off the "e" onto the "w".<br>The point is that to estimate P(c|w) we have to consider both the probability of c and the probability of the change from c to w anyway,<br>so it is cleaner to formally separate the two factors.</div>
<p> </p>
<p>即要比较不同c的p(c|w)是件很难的事，因为我们是无法知道单词是如何被写错的<br>但把p(c|w)转化为P(w|c)*P(c)时，事情就变得简单了，因此我们的问题是找出max(p(w|c)*p(c))的c，<br>而如何找到最大的p(w|c)*p(c)呢？恩，在Peter的假设(纠正词的选取优先级依次为 ：库里的词，出错一个词，出错两个词)下就是这行代码了：<br>return max(candidates, key=NWORDS.get). 按照优先级获得p(c)最大的c, Cool!<br><br>推荐大家看原文接下来的一些延伸。<br>刘未鹏在数学之美番外篇：《<a href="http://mindhacks.cn/2008/09/21/the-magical-bayesian-method/">平凡而又神奇的贝叶斯方法</a>》里也提到了这段程序，推荐大家读读。<br>恩，为什么有人说互联网公司拼的是数据？为什么当初李开复能把语音识别准确率提升了一个台阶？为什么贝叶斯定理在Google里有这么重要的地位？(Just Google it)<br><br><strong>2.2. Python之美</strong><br><br>老实说这段代码是我最初学Python的动力<br><br>看看这简单之美：</p>
<pre name="code">    splits     = [(word[:i], word[i:]) for i in range(len(word) + 1)]
    deletes    = [a + b[1:] for a, b in splits if b]
    transposes = [a + b[1] + b[0] + b[2:] for a, b in splits if len(b)&gt;1]
    replaces   = [a + c + b[1:] for a, b in splits for c in alphabet if b]
    inserts    = [a + c + b     for a, b in splits for c in alphabet]</pre>
 
<p><br><strong>2.3. 程序之美</strong><br><br>你一定看过乔丹跳投的视频吧？很美吧？持球，下蹲，发力，起跳，在最高点时移开左手，右手轻轻一拨，3 point，perfect.<br>Peter的&#39;跳投&#39;也很美，read,  train, edit, known, 最后: &gt;&gt;&gt; correct(&#39;speling&#39;) &#39;spelling&#39;，pefect!</p>
<p> </p>
<p>前段时间到上海博物馆里，看到那些巧夺天工的玉器，惊叹之余我竟然第一个想到的，是那些代码。<br>是的，几百年前Bayes提出了贝叶斯定理，几十年前Guido van Rossum整出了python，几年前Peter Norvig写出了这段代码。<br>若把spelling correct这个需求比作玉的话，Peter便是拿着这块玉，用Guido van Rossum作的工具，一行一行地在Bayes的思想下做出了这艺术品。<br>把编程喻为匠艺，挺确切，但不同的是，这份艺术不仅是大脑来雕砌更不止于欣赏，它不在博客馆里陈列着，而在计算机上运行着，它带来的是真实的价值。<br>倘若Google的一次corrector能为用户省下哪怕是1秒钟的时间，那以亿计的用户使用所省下的时间所创造的财富，足以给予Google和Googler们最丰厚的回报和自豪感。</p>
<p><br><strong>3. 一个小活动</strong></p>
<p> </p>
<p>其实很早时我就想把酷代码印在T-shirt上了，夏天终于来啦，我决定把这段代码印在我的T-shirt上，Peter也同意啦： <br> <br><img src="http://dl.iteye.com/upload/attachment/473512/e0906393-a910-3362-8c16-ab1a4c2ec9a1.jpg" alt=""><br> <br><img src="http://dl.iteye.com/upload/attachment/473514/f69129c9-ecff-329b-adb6-d36e0087ba9e.jpg" alt=""><br> <br><br> (这里面有个让我很囧的大笑话，希望你能发现)<br><br>我想一定有人会说，这样走在街上是不是太招摇了？是的，哥就是出来装AC的。</p>
<p><br>当有人拿很好的MPx，里面塞满一堆垃圾音乐，却在你面前装AC时。<br>当有人拿着xphone，里面塞着一堆美女的号码却不是朋友和亲人，却在你面前装AC时。<br>我希望你能和我一样，转过身去，让他看看这便宜的T-shirt背面印着的代码，当你转回身子看着他一脸茫然时，微笑着告诉他："Well, what monkey can't buy!".</p>
<p>MPx, xphone, money, 美女这些东西终要腐烂的，唯有艺术与爱不朽于世。</p>
<p><br>"Smart is the new sexy"，哥不穿黑丝，还不能性感了？<br><br>是的，一个夏天一件T-shirt是不够的，那就快点把你最喜欢的代码分享出来吧！如果它也很美，我一定会把它印在T-shirt上的。</p>
<p> </p>
<p>想要印这段代码的同学也可以找我要印的图，哈哈，和大家一起分享，Habe a good summer, and happy programming！</p>
          
          <br><br>
          作者: <a href="http://codeincoffee.iteye.com">codeincoffee</a> 
          <br>
          声明: 本文系ITeye网站发布的原创文章，未经作者书面许可，严禁任何网站转载本文，否则必将追究法律责任！
          <br><br>
          <span style="color:red">
            <a href="http://www.iteye.com/topic/1025490" style="color:red">已有 <strong>22</strong> 人发表回复，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">ITeye推荐</span>
<br>
<ul><li><a href="http://www.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>