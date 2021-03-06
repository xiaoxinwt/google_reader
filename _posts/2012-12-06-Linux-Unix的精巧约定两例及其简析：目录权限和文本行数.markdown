---
layout: post
title:  "Linux/Unix的精巧约定两例及其简析：目录权限和文本行数"
date:   2012-12-06 18:46:19
author: 李 鼎
categories: program
---

## Linux/Unix的精巧约定两例及其简析：目录权限和文本行数
### by 李 鼎
### at 2012-12-06 18:46:19
### original <http://rdc.taobao.com/team/jm/archives/2569>

<p>学玩*nux时候，碰到的一些问题，弄明白了后也就过去了。今天看到旁边的同学对目录权限有些模糊，给解释了一下。想想不如把这些问题都记下来。</p>
<p>设计其实包含的是一套约定。能运行、解决问题的约定都是可用的约定。但解决的多种约定方式或说是设计中，作一些比较可以感觉到哪个会更统一更简单。下文提到的两例Linux/Unix约定说明后，我也简单分析一下约定，找出看似复杂或是不直觉约定中内部包含的简洁统一。</p>
<p>清楚的同学，就当是个活动脑子的问题，过一遍小乐一下 <img src="http://rdc.taobao.com/team/jm/wp-includes/images/smilies/icon_smile.gif" alt=":)">  如有谬误敬请指正。</p>
<h1>目录权限的约定</h1>
<h2>目录的执行权限</h2>
<p>普通文件（非目录）有执行权限、执行操作，很容易理解，目录执行权限是个啥？</p>
<p>我期望以尽量简明统一方式来描述：</p>
<blockquote><p>目录的执行权限 是 下面操作的前提：</p>
<ol>
<li>该目录下的文件（包含子目录）的读、写、执行操作（即目录下文件的全部操作）</li>
<li>该目录本身的写操作</li>
</ol>
</blockquote>
<p>注： 要对目录进行写操作，要同时有<strong>该目录的写权限</strong> 和 <strong>该目录的执行权限</strong>。</p>
<p>详见《<a title="Unix环境高级编程" href="http://book.douban.com/subject/1788421/">Unix环境高级编程</a>》第四章第5节“文件访问权限”；中文版第二版在第76页。</p>
<p>多谢<strong><em>My Friend and Mentor</em></strong> <a title="kiminotes" href="http://weibo.com/kimig">kiminotes</a>同学半夜还和我一起验证和确认这个问题，并给我找出了上面资料。</p>
<p>关于上面提到的<em>目录的读、写操作</em>的说明在下一节给出。</p>
<h3>示例说明和分析</h3>
<p>上面条款1实际上包含有<strong>递归说明</strong>：</p>
<ol>
<li>目录的执行权限前提要有父目录的执行权限；</li>
<li>父目录的执行权限的前提是要有父目录的父目录的执行权限；</li>
<li>……</li>
</ol>
<p>举个例子来讲解一下，比如要读文件<code>/home/foo/readme.txt</code>：</p>
<ol>
<li>要读<code>readme.txt</code>，前提要可以执行父目录<code>foo</code></li>
<li>要执行<code>foo</code>目录，前提要可以执行父目录<code>home</code></li>
<li>要执行<code>home</code>目录，前提要可以执行父目录<code>/</code>（即root目录）root目录没有父目录了（或者说，root目录的父目录是自己），递归终止。</li>
</ol>
<p>上面分析的结果是，要读文件<code>/home/foo/readme.txt</code>需要：</p>
<ol>
<li><code>readme.txt</code>的读权限</li>
<li><code>foo</code>目录的执行权限</li>
<li><code>home</code>目录的执行权限</li>
<li><code>/</code>目录的执行权限</li>
</ol>
<p>通过上面分析可以看出，目录的执行权限是对其下子文件进行操作的前提，所以只要对自己的Home目录的权限改成700，就可以拒绝他人查看自己Home下的文件的内容、在目录下新建或是删除文件。</p>
<p># 如果只给他Home目录的读权限，还是可以list出Home下有哪些文件，见下一节的说明</p>
<p><code>$ chmod 700 .<br>
$ ls -ld .<br>
drwx----- 9 foo foo 4.0K 2012-11-20 00:11 ./<br>
</code></p>
<p>注： <code>ls</code>的<code>-d</code>表示list文件是目录时，不去list目录下文件，而是list目录本身。</p>
<h2>目录读、写操作</h2>
<p>普通文件（非目录）的读、写操作很容易理解。目录的读、写操作说明如下：</p>
<blockquote>
<ul>
<li>目录读操作 = 可以list出目录下有哪些文件</li>
<li>写操作 = 可以在该目录下<strong>新建</strong>和<strong>删除</strong>这个目录下的文件（包含新建和删除子目录）</li>
</ul>
</blockquote>
<p>注：要对目录进行写操作，前提要同时有<strong>该目录的写权限</strong> 和 <strong>该目录的执行权限</strong>。</p>
<p>所以，为了删除一个现有的文件，必须对包含该文件的目录具有<strong>写权限</strong>和<strong>执行权限</strong>；对<em>该文件本身</em>不需要有读、写权限。</p>
<h2>讨论</h2>
<p>目录这些权限的约定，让整个目录（包含下面所有的子文件）的控制变得简单，简单调整这个目录上的执行权限就可以了；信息集中，方便维护。</p>
<p>想想Windows的约定吧，是不是想到那个“应用到所有子目录和文件”的选项，要把所有的子目录都设置一遍。</p>
<p>最早开始期望一句话来描述“目录的执行权限”，即只有条款1，结果发现还有条款2的内容，是“该目录本身的写操作”的前提。</p>
<p>注： 关于文件权限详细说明，对应系统调用，可以参见《<a title="Unix环境高级编程" href="http://book.douban.com/subject/1788421/">Unix环境高级编程</a>》第四章第5节“文件访问权限”。</p>
<p>这样不统一的描述，让我感觉到条款2可能是Unix设计为了解决一些实际问题而补入的条款。我的YY的场景是：</p>
<p>限制对HOME目录的操作（增删子文件，包含的递归子文件），如果没有条款2，关掉别人对<em>HOME目录执行权限</em>还不够，还要通过关掉别人对<em>HOME目录写权限</em>来限制。这样的常见需求两步操作就繁琐了！</p>
<h1>文本行数的约定</h1>
<blockquote><p>文本的行数 = 文本中换行符的个数</p></blockquote>
<p>可以使用下面的命令验证：</p>
<p><code>$ echo -n blablabla | wc<br>
0 1 9<br>
</code></p>
<p>看到统计行数的结果是<strong><code>0</code></strong>行。</p>
<p>注： <code>echo</code>的<code>-n</code>选项表示不输出换行。</p>
<h2>为什么这么约定</h2>
<h3>方便统计文本行数</h3>
<p>“文本的行数 = 文本中换行符的个数”的约定，只要找到换行就好。</p>
<p>不需要特殊处理最后一行可能没有换行。</p>
<p># Windows下不做这个约定，就要有这样的特殊处理。</p>
<h3>方便程序输出文本</h3>
<p>想想写个程序按行输出结果。</p>
<p>如果输出最后一个结果后不要换行：</p>
<p><code>int len = 10;<br>
int[] result = ...;<br>
for(int i = 0; i &lt; len; ++i) {<br>
if(i != 0) { // 如果不是第一个<br>
printf("\n");<br>
}<br>
printf("%d", i);<br>
}<br>
</code></p>
<p>如果每输出一个结果带一个换行：</p>
<p><code>int len = 10;<br>
int[] result = ...;<br>
for(int i = 0; i &lt; len; ++i) {<br>
printf("%d\n", i);<br>
}<br>
</code></p>
<p>这两种写法，第二种没有<code>if</code>判断，更整齐、更简单。</p>
<h2>讨论</h2>
<p>从上面2种情况，一种是统计输入文本行数，一种输出多行文本，这个约定都简化实现和说明。简单是美。</p>
<p>你可能发现，这个约定简单是对于程序，对于人来做就麻烦了，要保证最后一行后面有换行符！</p>
<p>实际上Linux/Unix下的文本编辑器会遵循这个约定，编辑时会自动给最后一行没有换行的文本加上一个换行。</p>
<p>这样这个约定就不会给人带来麻烦了，让编辑器来维护这个约定。</p>
<p>可以这样验证：</p>
<p>生成不带结尾换行的文件。</p>
<p><code>$ echo -n blablabla &gt; test.txt<br>
$ wc test.txt<br>
0 1 9 test.txt<br>
</code></p>
<p>用vi打开编辑，第一行行头加一个字符后保存退出，wc统计一下，已经有一行文本了。</p>
<p><code>$ wc test.txt<br>
1 1 11 test.txt<br>
</code></p>
<p>注： Eclipse里有时会提醒你一个Warning说文件不是以换行符结束（No newline at end of file），现在知道为什么了吧 <img src="http://rdc.taobao.com/team/jm/wp-includes/images/smilies/icon_smile.gif" alt=":)"> </p>
<h1>后记</h1>
<p>*nix的设计和约定博大精深，以后发现些新有意思的条目会加进来。</p>