---
layout: post
title:  "Redis 源码分析：dict.c 和 dict.h"
date:   2012-03-17 19:45:36
author: nosqlfan
categories: program
---

## Redis 源码分析：dict.c 和 dict.h
### by nosqlfan
### at 2012-03-17 19:45:36
### original <http://item.feedsky.com/~feedsky/nosqlfan/~8149226/623558057/6253001/1/item.html>

<p>本文作者为@<a href="http://twitter.com/huangz1990">huangz</a>，也就是<span><a href="http://blog.nosqlfan.com/tags/redis" title="查看 Redis 的全部文章">Redis</a></span>文档中文翻译版的创建和维护者，也是<a href="http://github.com/huangz1990/ooredis">OOREDIS</a>的作者。文章对Redis的核心数据结构，<span><a href="http://blog.nosqlfan.com/tags/dict" title="查看 dict 的全部文章">dict</a></span>结构的内部实现进行了分析。是介绍Redis内部结构的好文章。</p>
<p>原文链接：<a href="http://huangz.iteye.com/blog/1455808">http://huangz.iteye.com/blog/1455808</a></p>
<h3>简介</h3>
<p>哈希表是 redis 的核心结构之一，在 redis 的<span><a href="http://blog.nosqlfan.com/tags/%e6%ba%90%e7%a0%81" title="查看 源码 的全部文章">源码</a></span>中， dict.c 和 dict.h 就定义了 redis 所使用的哈希结构，在这篇文章中，我们将对 dict.c 和 dict.h 进行注解和分析，籍此加深对 redis 的理解。</p>
<h3>数据结构概览</h3>
<p>dict.h 中定义了被 dict.c 的程序所使用的几个数据结构，如 dict 、dictht 和 dictEntry 等，它们之间的关系可以用下图来描述(点击放大）：</p>
<p><a href="http://pic.yupoo.com/iammutex/BP1UB3tY/vhzdt.png"><img title="redis dict" src="http://pic.yupoo.com/iammutex/BP1UB3tY/vhzdt.png" alt="" width="610"></a></p>
<h3>数据结构实现细节</h3>
<p>上一节的大图给出了数据结构之间相互关系，现在，让我们将注意力集中到 dict 、 dictht 和 dictEntry 这三个核心数据结构上面。</p>
<p>dict 结构的定义如下：</p>
<pre>/* 字典结构 */
typedef struct dict {
    dictType *type;     // 为哈希表中不同类型的值所使用的一族函数
    void *privdata;
    dictht ht[2];       // 每个字典使用两个哈希表
    int rehashidx;      // 指示 rehash 是否正在进行，如果不是则为 -1
    int iterators;      // 当前正在使用的 iterator 的数量
} dict;</pre>
<p>代码的注释基本都说明相关属性的作用了，需要补充的一些是：</p>
<p>每个字典使用两个哈希表，是因为要实现渐增式 <span><a href="http://blog.nosqlfan.com/tags/rehash" title="查看 rehash 的全部文章">rehash</a></span> ，redis 会逐个逐个地将 0 号哈希表的元素移动到 1 号哈希表，直到 0 号哈希表被清空为止，文章的后面会给出相关细节，不要心急！</p>
<p>另外， rehashidx 记录的实际上是 rehash 进行到的索引，比如如果 rehash 进行到第 10 个元素，那么 rehashidx 的值就为 9，以此类推，如果没有在进行 rehash ，rehashidx 的值就为 -1 。</p>
<p>接着来看看哈希表结构 —— dictht 结构，这个哈希表是一个 seprate chaining hash table 实现，它通过将哈希值相同的元素放到一个链表中来解决冲突问题：</p>
<pre>typedef struct dictht {
    dictEntry **table;      // 节点指针数组
    unsigned long size;     // 桶的数量
    unsigned long sizemask; // mask 码，用于地址索引计算
    unsigned long used;     // 已有节点数量
} dictht;</pre>
<p>table 属性组成了一个数组，数组里带有节点指针，用作链表。</p>
<p>size 、 sizemask 和 used 这三个属性初看上去让人有点头晕，实际上，它们分别代表的是：</p>
<p>size ：桶的数量，也即是， table 数组的大小。</p>
<p>sizemask ：这个值通过 size – 1 计算出来，给定 key 的哈希值计算出来之后，就会和这个数值进行 &amp; 操作，决定元素被放到 table 数组的那一个位置上。</p>
<p>used ：这个值代表目前哈希表中元素的数量，也即是哈希表总共保存了多少 dictEntry 结构。</p>
<p>好的，最后，就是链表节点结构 —— dictEntry 了：</p>
<pre>typedef struct dictEntry {
    void *key;              // 键
    union {
        void *val;
        uint64_t u64;
        int64_t s64;
    } v;                    // 值(可以有几种不同类型)
    struct dictEntry *next; // 指向下一个哈希节点(形成链表)
} dictEntry;</pre>
<p>这个结构。。。阿，好吧，这个结构没啥要说的，赶紧进入下一部分吧。</p>
<h3>字典创建流程</h3>
<p>在初步解了几个核心数据结构之后，是时候可以来看看相关的函数怎么来使用这些数据结构了，让我们从最开始的创建字典开始，一步步研究字典（以及哈希表）的运作流程。</p>
<p>因为调用流程可以给我们一个高层次的观点来了解数据结构的运作流程，而不必陷入到代码细节中，因此，文章这里只给出程序调用流程的部分核心代码，如果你对代码的其他细节有兴趣，可以到<a href="https://github.com/huangz1990/reading_redis_source">我的 github 上去找注释版的代码</a>，上面有完整的代码，而且我给大部分函数都加上了注释。</p>
<p>OK，说回来字典这边，创建新字典执行的调用链是： dictCreate -&gt; _dictInit -&gt; _dictReset</p>
<p>其中 dictCreate 函数为 dict 结构分配了空间，然后将新的 dict 传给 _dictInit 函数，让它初始化 dict 结构的相关属性，而 _dictInit 又调用 _dictReset ，对字典的 ht 属性（也即是两个哈希表）进行常量属性的设置。</p>
<p>注意， _dictReset 只是为字典所属的两个哈希表进行常量属性的设置(size、 sizemask 和 used），但并不为哈希表的链表数组分配内存：</p>
<pre>static void _dictReset(dictht *ht)
{
    ht-&gt;table = NULL;
    ht-&gt;size = 0;
    ht-&gt;sizemask = 0;
    ht-&gt;used = 0;
}</pre>
<h3>0 号哈希表的创建流程</h3>
<p>我们知道，一个 dict 结构使用两个哈希表，也即是 d-&gt;ht[0] 和 d-&gt;ht[1] ，为了称呼方便，我们将他们分别叫做 0 号和 1 号哈希表。</p>
<p>从上一节可以知道， dictCreate 并不为哈希表的链表数组分配内存( d-&gt;ht[0]-&gt;table 和 d-&gt;ht[1]-&gt;table 都被设为 NULL），那么，什么时候哈希表的链表数组会被初始化呢？</p>
<p>答案是，当首次通过 dictAdd 向字典添加元素的时候， 0 号哈希表的链表数组会被初始化。</p>
<p>首次向字典增加元素将执行以下的调用序列： dictAdd -&gt; dictAddRaw -&gt; _dictKeyIndex -&gt; dictExpandIfNeeded -&gt; dictExpand</p>
<p>其中 dictAdd 是 dictAddRaw 的调用者， dictAddRaw 是添加元素这一工作的底层实现，而 dictAddRaw 为了计算新元素的 key 的地址索引，会调用 _dictKeyIndex ：</p>
<pre>dictEntry *dictAddRaw(dict *d, void *key)
{
    // 被省略的代码...   

    // 计算 key 的 index 值
    // 如果 key 已经存在，_dictKeyIndex 返回 -1
    if ((index = _dictKeyIndex(d, key)) == -1)
        return NULL;  

    // 被省略的代码...
}</pre>
<p>然后 _dictKeyIndex 会在计算 地址索引前，会先调用 _dictExpandIfNeeded 检查两个哈希表是否有空间容纳新元素：</p>
<pre>static int _dictKeyIndex(dict *d, const void *key)
{
    // 被省略的代码...  

    /* Expand the hashtable if needed */
    if (_dictExpandIfNeeded(d) == DICT_ERR)
        return -1;  

    // 被省略的代码...
}</pre>
<p>到 _dictExpandIfNeeded 这步，一些有趣的事情就开始发生了， _dictExpandIfNeeded 会检测到 0 号哈希表还没有分配任何空间，于是它调用 dictExpand ，传入 DICT_HT_INITIAL_SIZE 常量作为 0 号哈希表的初始大小（目前的版本 DICT_HT_INITIAL_SIZE = 4 )，为 0 号哈希表分配空间：</p>
<pre>static int _dictExpandIfNeeded(dict *d)
{
    // 被忽略的代码...  

    /* If the hash table is empty expand it to the intial size. */
    if (d-&gt;ht[0].size == 0) return dictExpand(d, DICT_HT_INITIAL_SIZE);  

    // 被忽略的代码...
}</pre>
<p>dictExpand 会创建一个分配了链表数组的新哈希表，然后进行判断，决定是该将新哈希表赋值给 0 号哈希表还是 1 号哈希表。</p>
<p>这里因为我们的 0 号哈希表的 size 还是 0 ，因此，这里会执行 if 语句的第一个 case ，将新哈希表赋值给 0 号哈希表：</p>
<pre>int dictExpand(dict *d, unsigned long size)
{
    // 被省略的代码...  

    // 计算哈希表的(真正)大小
    unsigned long realsize = _dictNextPower(size);  

    // 创建新哈希表
    dictht n;
    n.size = realsize;
    n.sizemask = realsize-1;
    n.table = zcalloc(realsize*sizeof(dictEntry*));  // 分配链表数组
    n.used = 0;  

    // 字典的 0 号哈希表是否已经初始化？
    // 如果没有的话，我们将新建哈希表作为字典的 0 号哈希表
    if (d-&gt;ht[0].table == NULL) {
        d-&gt;ht[0] = n;
    } else {
    // 否则，将新建哈希表作为字典的 1 号哈希表，并将它用于 rehash
        d-&gt;ht[1] = n;
        d-&gt;rehashidx = 0;
    }  

    // 被省略的代码...
}</pre>
<h3>字典的扩展和 1 号哈希表的创建</h3>
<p>在 0 号哈希表创建之后，我们就有了一个可以执各式各样操作（添加、删除、查找，诸如此类）的字典实例了。</p>
<p>但是这里还有一个问题： 这个最初创建的 0 号哈希表非常小（当前版本的 DICT_HT_INITIAL_SIZE = 4)，它很快就会被元素填满，这时候， rehash 操作就会被激活。</p>
<p>（字典的）哈希表的完整 rehash 操作分为两步：</p>
<p>1） 首先就是创建一个比现有哈希表更大的新哈希表（expand）</p>
<p>2） 然后将旧哈希表的所有元素都迁移到新哈希表去（rehash）</p>
<p>当使用 dictAdd 对字典添加元素的时候， _dictExpandIfNeeded 会一直对 0 号哈希表的使用情况进行检查（还记得 dictAdd 的调用链吗？忘了的话翻到上一节去看看），当 rehash 条件被满足的时候，它就会调用 dictExpand 函数，对字典进行扩展：</p>
<pre>static int _dictExpandIfNeeded(dict *d)
{
    // 被省略的代码...  

    // 当 0 号哈希表的已用节点数大于等于它的桶数量，
    // 且以下两个条件的其中之一被满足时，执行 expand 操作：
    // 1) dict_can_resize 变量为真，正常 expand
    // 2) 已用节点数除以桶数量的比率超过变量 dict_force_resize_ratio ，强制 expand
    // (目前版本中 dict_force_resize_ratio = 5)
    if (d-&gt;ht[0].used &gt;= d-&gt;ht[0].size &amp;&amp;
        (dict_can_resize ||
         d-&gt;ht[0].used/d-&gt;ht[0].size &gt; dict_force_resize_ratio))
    {
        return dictExpand(d, ((d-&gt;ht[0].size &gt; d-&gt;ht[0].used) ?
                                    d-&gt;ht[0].size : d-&gt;ht[0].used)*2);
    }  

    // 被省略的代码...
}</pre>
<p>可以看到，当代码注释中所说的两种情况的其中一种被满足的时候， dictExpand 函数就会被调用， 0 号哈希表的桶数量和节点数量两个数值之间的较大者乘以 2 ，就会被作为第二个参数传入 dictExpand 函数。</p>
<p>这次调用 dictExpand 函数执行的是和之前创建 0 号哈希表时不同的路径 —— 这一次，程序执行的是 else case，它将新哈希表赋值给 1 号哈希表，并将字典的 rehashidx 属性从 -1 改为 0：</p>
<pre>int dictExpand(dict *d, unsigned long size)
{
    // 被省略的代码...  

    // 计算哈希表的(真正)大小
    unsigned long realsize = _dictNextPower(size);  

    // 创建新哈希表
    dictht n;
    n.size = realsize;
    n.sizemask = realsize-1;
    n.table = zcalloc(realsize*sizeof(dictEntry*));
    n.used = 0;  

    // 字典的 0 号哈希表是否已经初始化？
    // 如果没有的话，我们将新建哈希表作为字典的 0 号哈希表
    if (d-&gt;ht[0].table == NULL) {
        d-&gt;ht[0] = n;
    } else {
    // 否则，将新建哈希表作为字典的 1 号哈希表，并将它用于 rehash
        d-&gt;ht[1] = n;
        d-&gt;rehashidx = 0;
    }  

    // 被省略的代码...
}</pre>
<h3>渐增式 rehash 和平摊操作</h3>
<p>在上一节的最后，我们看到，当 expand 执行完毕之后，字典同时使用两个哈希表，并且字典的 rehashidx 属性从 -1 被改为 0 ，这是一个重要的改动，它标志着 rehash 可以进行了。</p>
<p>但是 rehash 操作在那里？我们什么时候调用它？嗯。。。源码里的确有一个 dictRehash 函数，它可以将指定数量的元素从 0 号哈希表 rehash 到 1 号哈希表，但是，redis 并不是使用它一下子将 0 号哈希表的所有元素全都 rehash 到 1 号哈希表，因为这样集中式的 rehash 会引起大量的计算工作（想一想，假如现在需要 rehash 的不是 4 个元素，而是 4 十万个，4 千万个，4 亿呢？），进而影响整个系统的性能。</p>
<p>为了避免上面所说的问题， redis 采取了一种更平滑的 rehash 策略，redis 文档里称之为渐增式 rehash (incremental rehashing)，它将 rehash 操作平摊到 dictAddRaw 、dictGetRandomKey 、dictFind 、dictGenericDelete 这些函数里面，每当上面这些函数被执行的时候（或者其他人调用它们时）, _dictRehashStep 函数就会执行，将 1 个元素从 0 号哈希表 rehash 到 1 号哈希表，这样就避免了集中式的 rehash 。</p>
<p>以下是 dictFind 函数，它是其中一个平摊 rehash 操作的函数：</p>
<pre>dictEntry *dictFind(dict *d, const void *key)
{
    // 被忽略的代码...  

    // 检查字典(的哈希表)能否执行 rehash 操作
    // 如果可以的话，执行平摊 rehash 操作
    if (dictIsRehashing(d)) _dictRehashStep(d);  

    // 被忽略的代码...
}</pre>
<p>其中 dictIsRehashing 就是检查字典的 rehashidx 属性是否不为 -1 ：</p>
<pre>#define dictIsRehashing(ht) ((ht)-&gt;rehashidx != -1)</pre>
<p>如果条件成立成立的话， _dictRehashStep 就会被执行，将一个元素从 0 号哈希表转移到 1 号哈希表：</p>
<pre>static void _dictRehashStep(dict *d) {
    if (d-&gt;iterators == 0) dictRehash(d,1);
}</pre>
<p>（代码中的 iterators == 0 表示在 rehash 时不能有迭代器，因为迭代器可能会修改元素，所以不能在有迭代器的情况下进行 rehash 。）</p>
<p>就这样，如同愚公移山一般， 0 号哈希表的元素被逐个逐个地，从 0 号 rehash 到 1 号，最终整个 0 号哈希表被清空，这时 _dictRehashStep 再调用 dictRehash ，被清空的 0 号哈希表就会被删除，然后原来的  1 号哈希表成为新的 0 号哈希表（当有需要再次进行 rehash 的时候，这个循环就会再次开始）：</p>
<pre>int dictRehash(dict *d, int n) {
    // 被忽略的代码...  

    while(n--) {
        dictEntry *de, *nextde;  

        // 0 号哈希表的所有元素 rehash 完毕？
        if (d-&gt;ht[0].used == 0) {
            zfree(d-&gt;ht[0].table);  // 清空 0 号
            d-&gt;ht[0] = d-&gt;ht[1];   // 用原来的 1 号代替 0 号  

            _dictReset(&amp;d-&gt;ht[1]);  // 重置 1 号哈希表
            d-&gt;rehashidx = -1;      // 重置字典的 rehash flag  

            return 0;
        }
    // 被忽略的代码...
    }  

    // 被忽略的代码...
}</pre>
<p>最后，还有之前一直都没有提到的一个确保 rehash 得以最终完成的重要条件是：当 rehashidx 不等于 -1 ，也即是 dictIsRehashing 为真时，所有新添加的元素都会直接被加到 1 号数据库，这样 0 号哈希表的大小就会只减不增，最终 rehash 总会有完成的一刻（假如新加入的元素还继续被放进 0 号哈希表，那么尽管平摊 rehash 一直在努力地进行，但说不定 rehash 还是永远也完成不了）：</p>
<pre>dictEntry *dictAddRaw(dict *d, void *key)
{
    // 被省略的代码...  

    // 如果字典正在进行 rehash ，那么将新元素添加到 1 号哈希表，
    // 否则，使用 0 号哈希表
    ht = dictIsRehashing(d) ? &amp;d-&gt;ht[1] : &amp;d-&gt;ht[0];  

    // 被省略的代码...
}</pre>
<h3>最后：哈希表的大小</h3>
<p>我们知道哈希表最初的大小是由 DICT_HT_INITIAL_SIZE 决定的，而当 rehash 开始之后，根据给定的条件，哈希表的大小就会发生变动：</p>
<pre>static int _dictExpandIfNeeded(dict *d)
{
    // 被省略的代码...  

    if (d-&gt;ht[0].used &gt;= d-&gt;ht[0].size &amp;&amp;
        (dict_can_resize ||
         d-&gt;ht[0].used/d-&gt;ht[0].size &gt; dict_force_resize_ratio))
    {
        return dictExpand(d, ((d-&gt;ht[0].size &gt; d-&gt;ht[0].used) ?
                                    d-&gt;ht[0].size : d-&gt;ht[0].used)*2);
    }  

    // 被省略的代码...
}</pre>
<p>可以看到， d-&gt;ht[0].size 和 d-&gt;ht[0].used 两个数之间的较大者乘以 2 ，会作为 size 参数被传入 dictExpand 函数，但是，尽管如此，这个数值仍然还不是哈希表的最终大小，因为在 dictExpand 里面，_dictNextPower 函数会根据传入的 size 参数计算出真正的表大小：</p>
<pre>int dictExpand(dict *d, unsigned long size)
{
    // 被省略的代码...  

    // 计算哈希表的(真正)大小
    unsigned long realsize = _dictNextPower(size);  

    // 创建新哈希表
    dictht n;
    n.size = realsize;
    n.sizemask = realsize-1;
    n.table = zcalloc(realsize*sizeof(dictEntry*));
    n.used = 0;  

    // 被省略的代码...
}</pre>
<p>至于 _dictNextPower 函数，它不断计算 2 的乘幂，直到遇到比 size 参数大的幂，就返回这个乘幂作为哈希表的大小：</p>
<pre>static unsigned long _dictNextPower(unsigned long size)
{
    unsigned long i = DICT_HT_INITIAL_SIZE;  

    if (size &gt;= LONG_MAX) return LONG_MAX;
    while(1) {
        if (i &gt;= size)
            return i;
        i *= 2;
    }
}</pre>
<p>虽然桶的元素个数 d-&gt;ht[0].size 刚开始是固定的(DICT_HT_INITIAL_SIZE)，但是，因为我们没有办法预知 d-&gt;ht[0].used 的值，所以我们没有办法预知哈希表的大小，不过，我们可以确定以下两个哈希表大小的性质：</p>
<p>1） 哈希表的大小总是 2 的乘幂（也即是 2^N，此处 N 未知）</p>
<p>2）1 号哈希表的大小总比 0 号哈希表大</p>
<h3>就这样了！</h3>
<p>对 redis 的 dict.c 和 dict.h 的源码分析就到这了，本人一直认为，好的源码分析应该能在不贴太多代码的情况下把原理和流程讲明白，在这篇文章我已经尽力地将实践这个原则，不过因为是第一次写源码分析类的文章，而且才刚开始读 redis 的源码，难免有不如人意之处，希望朋友们不吝指出。</p>
<p>dict 的结构本身还是很简单的，唯一的遗憾是，dictht 作为字典的内部实现，本该被隐藏起来的，现在却完全暴露了出来（什么， _dictReset(&amp;d-&gt;ht[1])，你是说真的？），如果能在模块化方面多做一点努力的话，代码会更容易看一些，当然这里也有历史遗留的因素在里面。</p>
<p>最后， 我为 redis 的源码分析项目专门建立了一个 github project ，上面有完整的源码文件，大部分加上了注释（目前只有 dict.c 和 dict.h），如果对代码的完整细节有兴趣，可以到上面去取：  <a href="https://github.com/huangz1990/reading_redis_source">https://github.com/huangz1990/reading_redis_source</a></p>
<p style="margin:0;padding:0;height:1px;overflow:hidden">
    <a href="http://www.wumii.com/widget/relatedItems.htm" style="border:0"><img src="http://static.wumii.com/images/pixel.png" alt="无觅相关文章插件，快速提升流量" style="border:0;padding:0;margin:0"></a></p>
<table cellspacing="0" cellpadding="2" border="0" width="100%" style="clear:both">
    
    <tr>
        <td><b><font size="-1" style="display:block!important;padding:20px 0 5px!important">相关文章：</font></b></td>
    </tr>
    
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.com/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F2949.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3784.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">Redis源码分析系列文章</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.com/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F1413.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3784.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">深入Redis内部-Redis 源码讲解</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.com/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F1655.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3784.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">redis源码分析-如何rehash</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.com/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3525.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3784.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">Redis深入浅出</font>
                    </a>
                </td>
            </tr>
            <tr>
                <td style="margin:0!important;padding:0!important;line-height:20px!important">
                    <img border="0" src="http://static.wumii.com/images/widget/widget_solidPoint.gif">
                    <a style="text-decoration:none!important" href="http://app.wumii.com/ext/redirect?url=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3705.html&amp;from=http%3A%2F%2Fblog.nosqlfan.com%2Fhtml%2F3784.html">
                        <font size="-1" color="#333333" style="line-height:1.65em;font-size:14px!important">使用Redis的五个注意事项</font>
                    </a>
                </td>
            </tr>
    
    <tr>
        <td align="right">
            <a style="text-decoration:none!important" href="http://www.wumii.com/widget/relatedItems" title="无觅相关文章插件">
                <font size="-1" color="#bbbbbb" style="display:block!important;font-family:arial!important;padding:5px 0!important;font-size:12px!important;color:#bbb!important">无觅</font>
            </a>
        </td>
    </tr>
</table><img src="http://www1.feedsky.com/t1/623558057/nosqlfan/feedsky/s.gif?r=http://item.feedsky.com/~feedsky/nosqlfan/~8149226/623558057/6253001/1/item.html" border="0" height="0" width="0">