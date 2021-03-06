---
layout: post
title:  "Linux内核的Oops"
date:   2010-11-14 23:13:00
author: wwang
categories: program
---

## Linux内核的Oops
### by wwang
### at 2010-11-14 23:13:00
### original <http://www.cnblogs.com/wwang/archive/2010/11/14/1876735.html>

<p><p>什么是Oops？从语言学的角度说，Oops应该是一个拟声词。当出了点小事故，或者做了比较尴尬的事之后，你可以说&quot;Oops&quot;，翻译成中国话就叫做“哎呦”。“哎呦，对不起，对不起，我真不是故意打碎您的杯子的”。看，Oops就是这个意思。</p>
<p>在Linux内核开发中的Oops是什么呢？其实，它和上面的解释也没什么本质的差别，只不过说话的主角变成了Linux。当某些比较致命的问题出现时，我们的Linux内核也会抱歉的对我们说：“哎呦（Oops），对不起，我把事情搞砸了”。Linux内核在发生kernel panic时会打印出Oops信息，把目前的寄存器状态、堆栈内容、以及完整的Call trace都show给我们看，这样就可以帮助我们定位错误。</p>
<p>下面，我们来看一个实例。为了突出本文的主角--Oops，这个例子唯一的作用就是造一个空指针引用错误。</p>
<div>
<pre>#include &lt;linux/kernel.h&gt;
#include &lt;linux/module.h&gt;

static int __init hello_init(void)
{
	int *p = 0;
	
	*p = 1;	
	return 0;
}

static void __exit hello_exit(void)
{
	return;
}

module_init(hello_init);
module_exit(hello_exit);

MODULE_LICENSE(&quot;GPL&quot;);
</pre>
</div>
<p>很明显，错误的地方就是第8行。</p>
<p>接下来，我们把这个模块编译出来，再用insmod来插入到内核空间，正如我们预期的那样，Oops出现了。</p>
<blockquote>
<p>[  100.243737] BUG: unable to handle kernel NULL pointer dereference at (null)</p>
<p>[  100.244985] IP: [&lt;f82d2005&gt;] hello_init+0x5/0x11 [hello]</p>
<p>[  100.262266] *pde = 00000000 </p>
<p>[  100.288395] Oops: 0002 [#1] SMP </p>
<p>[  100.305468] last sysfs file: /sys/devices/virtual/sound/timer/uevent</p>
<p>[  100.325955] Modules linked in: hello(+) vmblock vsock vmmemctl vmhgfs acpiphp snd_ens1371 gameport snd_ac97_codec ac97_bus snd_pcm_oss snd_mixer_oss snd_pcm snd_seq_dummy snd_seq_oss snd_seq_midi snd_rawmidi snd_seq_midi_event snd_seq snd_timer snd_seq_device ppdev psmouse serio_raw fbcon tileblit font bitblit softcursor snd parport_pc soundcore snd_page_alloc vmci i2c_piix4 vga16fb vgastate intel_agp agpgart shpchp lp parport floppy pcnet32 mii mptspi mptscsih mptbase scsi_transport_spi vmxnet</p>
<p>[  100.472178] [  100.494931] Pid: 1586, comm: insmod Not tainted (2.6.32-21-generic #32-Ubuntu) VMware Virtual Platform</p>
<p>[  100.540018] EIP: 0060:[&lt;f82d2005&gt;] EFLAGS: 00010246 CPU: 0</p>
<p>[  100.562844] EIP is at hello_init+0x5/0x11 [hello]</p>
<p>[  100.584351] EAX: 00000000 EBX: fffffffc ECX: f82cf040 EDX: 00000001</p>
<p>[  100.609358] ESI: f82cf040 EDI: 00000000 EBP: f1b9ff5c ESP: f1b9ff5c</p>
<p>[  100.631467]  DS: 007b ES: 007b FS: 00d8 GS: 00e0 SS: 0068</p>
<p>[  100.657664] Process insmod (pid: 1586, ti=f1b9e000 task=f137b340 task.ti=f1b9e000)</p>
<p>[  100.706083] Stack:</p>
<p>[  100.731783]  f1b9ff88 c0101131 f82cf040 c076d240 fffffffc f82cf040 0072cff4 f82d2000</p>
<p>[  100.759324] &lt;0&gt; fffffffc f82cf040 0072cff4 f1b9ffac c0182340 f19638f8 f137b340 f19638c0</p>
<p>[  100.811396] &lt;0&gt; 00000004 09cc9018 09cc9018 00020000 f1b9e000 c01033ec 09cc9018 00015324</p>
<p>[  100.891922] Call Trace:</p>
<p>[  100.916257]  [&lt;c0101131&gt;] ? do_one_initcall+0x31/0x190</p>
<p>[  100.943670]  [&lt;f82d2000&gt;] ? hello_init+0x0/0x11 [hello]</p>
<p>[  100.970905]  [&lt;c0182340&gt;] ? sys_init_module+0xb0/0x210</p>
<p>[  100.995542]  [&lt;c01033ec&gt;] ? syscall_call+0x7/0xb</p>
<p>[  101.024087] Code: &lt;c7&gt; 05 00 00 00 00 01 00 00 00 5d c3 00 00 00 00 00 00 00 00 00 00 </p>
<p>[  101.079592] EIP: [&lt;f82d2005&gt;] hello_init+0x5/0x11 [hello] SS:ESP 0068:f1b9ff5c</p>
<p>[  101.134682] CR2: 0000000000000000</p>
<p>[  101.158929] ---[ end trace e294b69a66d752cb ]---</p>
</blockquote>
<p>Oops首先描述了这是一个什么样的bug，然后指出了发生bug的位置，即“IP: [&lt;f82d2005&gt;] hello_init+0x5/0x11 [hello]”。</p>
<p>在这里，我们需要用到一个辅助工具objdump来帮助分析问题。objdump可以用来反汇编，命令格式如下：</p>
<blockquote>
<p>objdump -S  hello.o</p>
</blockquote>
<p>下面是hello.o反汇编的结果，而且是和C代码混排的，非常的直观。</p>
<div>
<pre>hello.o:     file format elf32-i386


Disassembly of section .init.text:

00000000 &lt;init_module&gt;:
#include &lt;linux/kernel.h&gt;
#include &lt;linux/module.h&gt;

static int __init hello_init(void)
{
   0:	55                   	push   %ebp
	int *p = 0;
	
	*p = 1;
	
	return 0;
}
   1:	31 c0                	xor    %eax,%eax
#include &lt;linux/kernel.h&gt;
#include &lt;linux/module.h&gt;

static int __init hello_init(void)
{
   3:	89 e5                	mov    %esp,%ebp
	int *p = 0;
	
	*p = 1;
   5:	c7 05 00 00 00 00 01 	movl   $0x1,0x0
   c:	00 00 00 
	
	return 0;
}
   f:	5d                   	pop    %ebp
  10:	c3                   	ret    

Disassembly of section .exit.text:

00000000 &lt;cleanup_module&gt;:

static void __exit hello_exit(void)
{
   0:	55                   	push   %ebp
   1:	89 e5                	mov    %esp,%ebp
   3:	e8 fc ff ff ff       	call   4 &lt;cleanup_module+0x4&gt;
	return;
}
   8:	5d                   	pop    %ebp
   9:	c3                   	ret    
</pre>
</div>
<p>对照Oops的提示，我们可以很清楚的看到，出错的位置hello_init+0x5的汇编代码是：</p>
<div>
<pre>5:c7 05 00 00 00 00 01 movl   $0x1,0x0</pre>
</div>
<p>这句代码的作用是把数值1存入0这个地址，这个操作当然是非法的。</p>
<p>我们还能看到它对应的c代码是：</p>
<div>
<pre>*p = 1;</pre>
</div>
<p>Bingo！在Oops的帮助下我们很快就解决了问题。</p>
<p> </p>
<p>我们再回过头来检查一下上面的Oops，看看Linux内核还有没有给我们留下其他的有用信息。</p>
<blockquote>
<p>Oops: 0002 [#1]</p>
</blockquote>
<p>这里面，0002表示Oops的错误代码（写错误，发生在内核空间），#1表示这个错误发生一次。</p>
<p>Oops的错误代码是按照下面这样定义的：</p>
<blockquote>
<p> * error_code:<br> *      bit 0 == 0 means no page found, 1 means protection fault<br> *      bit 1 == 0 means read, 1 means write<br> *      bit 2 == 0 means kernel, 1 means user-mode</p>
</blockquote>
<p>有时候，Oops还会打印出Tainted信息。这个信息用来指出内核是因何种原因被tainted（直译为“玷污”）。具体的定义如下：</p>
<blockquote>
<p>  1: &#39;G&#39; if all modules loaded have a GPL or compatible license, &#39;P&#39; if any proprietary module has been loaded.  Modules without a MODULE_LICENSE or with a MODULE_LICENSE that is not recognised by insmod as GPL compatible are assumed to be proprietary.<br>  2: &#39;F&#39; if any module was force loaded by &quot;insmod -f&quot;, &#39; &#39; if all modules were loaded normally.<br>  3: &#39;S&#39; if the oops occurred on an SMP kernel running on hardware that hasn&#39;t been certified as safe to run multiprocessor. Currently this occurs only on various Athlons that are not SMP capable.<br>  4: &#39;R&#39; if a module was force unloaded by &quot;rmmod -f&quot;, &#39; &#39; if all modules were unloaded normally.<br>  5: &#39;M&#39; if any processor has reported a Machine Check Exception, &#39; &#39; if no Machine Check Exceptions have occurred.<br>  6: &#39;B&#39; if a page-release function has found a bad page reference or some unexpected page flags.<br>  7: &#39;U&#39; if a user or user application specifically requested that the Tainted flag be set, &#39; &#39; otherwise.<br>  8: &#39;D&#39; if the kernel has died recently, i.e. there was an OOPS or BUG.<br>  9: &#39;A&#39; if the ACPI table has been overridden.<br> 10: &#39;W&#39; if a warning has previously been issued by the kernel. (Though some warnings may set more specific taint flags.)<br> 11: &#39;C&#39; if a staging driver has been loaded.<br> 12: &#39;I&#39; if the kernel is working around a severe bug in the platform firmware (BIOS or similar).</p>
</blockquote>
<p>基本上，这个Tainted信息是留给内核开发者看的。用户在使用Linux的过程中如果遇到Oops，可以把Oops的内容发送给内核开发者去debug，内核开发者根据这个Tainted信息大概可以判断出kernel panic时内核运行的环境。如果我们只是debug自己的驱动，这个信息就没什么意义了。</p>
<p> </p>
<p>本文的这个例子非常简单，Oops发生以后没有造成宕机，这样我们就可以从dmesg中查看到完整的信息。但更多的情况是Oops发生的同时系统也会宕机，此时这些出错信息是来不及存入文件中的，关掉电源后就无法再看到了。我们只能通过其他的方式来记录：手抄或者拍照。</p>
<p>还有更坏的情况，如果Oops信息过多的话，一页屏幕显示不全，我们怎么来查看完整的内容呢？第一种方法，在grub里用vga参数指定更高的分辨率以使屏幕可以显示更多的内容。很明显，这个方法其实解决不了太多的问题；第二种方法，使用两台机器，把调试机的Oops信息通过串口打印到宿主机的屏幕上。但现在大部分的笔记本电脑是没有串口的，这个解决方法也有很大的局限性；第三种方法，使用内核转储工具<a href="http://lse.sourceforge.net/kdump/">kdump</a>把发生Oops时的内存和CPU寄存器的内容dump到一个文件里，之后我们再用gdb来分析问题。</p>
<p> </p>
<p>开发内核驱动的过程中可能遇到的问题是千奇百怪的，调试的方法也是多种多样，Oops是Linux内核给我们的提示，我们要用好它。</p><img src="http://www.cnblogs.com/wwang/aggbug/1876735.html?type=1" width="1" height="1" alt=""><p>作者: <a href="http://www.cnblogs.com/wwang/">wwang</a> 发表于 2010-11-14 23:13 <a href="http://www.cnblogs.com/wwang/archive/2010/11/14/1876735.html">原文链接</a></p><p>评论: 2　<a href="http://www.cnblogs.com/wwang/archive/2010/11/14/1876735.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/wwang/archive/2010/11/14/1876735.html#commentform">发表评论</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/81087/">科技日报：细胞间也有“木马”</a><span style="color:gray">(2010-11-15 14:00)</span><br>· <a href="http://news.cnblogs.com/n/81086/">Android，WP7，iPhone4 大 PK……看谁更耐烤</a><span style="color:gray">(2010-11-15 13:47)</span><br>· <a href="http://news.cnblogs.com/n/81085/">玩转促销：价格优惠+社交网络+团购网站+GPS+？</a><span style="color:gray">(2010-11-15 13:43)</span><br>· <a href="http://news.cnblogs.com/n/81084/">浏览器大洗牌：用户是如何喜新厌旧的</a><span style="color:gray">(2010-11-15 13:30)</span><br>· <a href="http://news.cnblogs.com/n/81083/">Gmail 雷声大雨点小 不敌Hotmail/雅虎Mail</a><span style="color:gray">(2010-11-15 13:24)</span><br></p><p>编辑推荐：<a href="http://news.cnblogs.com/n/80931/">3Q大战精彩分析：马化腾应该感谢周鸿祎</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">我的园子</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://kb.cnblogs.com">知识库</a></p></p>