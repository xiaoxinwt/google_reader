---
layout: post
title:  "Typed Shell：给Unix Shell加上类型系统（2）"
date:   2013-05-26 15:00:00
author: 
categories: program
---

## Typed Shell：给Unix Shell加上类型系统（2）
### by 
### at 2013-05-26 15:00:00
### original <http://www.soimort.org/posts/159>

<p>（上篇：<a href="http://www.soimort.org/posts/158">Typed Shell：给Unix Shell加上类型系统（1）</a>）</p>

<blockquote>
<blockquote>
<blockquote>
<p><em>To go wrong in one&#39;s own way is better than to go right in someone else&#39;s.</em><br>
<em>- Dostoyevsky</em></p>
</blockquote>
</blockquote>
</blockquote>

<hr>

<h2>系统环境</h2>

<ul>
<li>GNU/Linux</li>
<li>GCC</li>
<li>GHC 7.6.2</li>
<li>Idris 0.9.7。可从Hackage或GitHub安装：

<ul>
<li><code>$ cabal install idris</code></li>
<li><a href="https://github.com/edwinb/Idris-dev">https://github.com/edwinb/Idris-dev</a></li>
</ul></li>
</ul>

<h2>C FFI</h2>

<p>因为Idris的标准库中没有提供POSIX相关系统API的支持，所以第一步工作是为Unix系统调用创建相应的Idris wrapper。我们把头文件和辅助函数的定义放在一个C文件<code>shell.h</code>里。</p>

<p>C代码：（<code>shell.h</code>）</p>
<div><pre><code><span>#include &lt;stdio.h&gt;</span>
<span>#include &lt;stdlib.h&gt;</span>
<span>#include &lt;errno.h&gt;</span>
<span>#include &lt;string.h&gt;</span>
<span>#include &lt;sys/types.h&gt;</span>
<span>#include &lt;sys/stat.h&gt;</span>
<span>#include &lt;dirent.h&gt;</span>
<span>#include &lt;unistd.h&gt;</span>
</code></pre></div>
<p>在Idris中使用<code>%include</code>指定包含C的头文件。为系统调用<code>opendir()</code>创建一个foreign function call：</p>

<p>Idris代码：（<code>Shell.idr</code>）</p>
<div><pre><code><span>module</span> <span>Shell</span>

<span>%</span><span>include</span> <span>&quot;shell.h&quot;</span>

<span>opendir</span> <span>:</span> <span>String</span> <span>-&gt;</span> <span>IO</span> <span>Ptr</span>
<span>opendir</span> <span>args</span> <span>=</span> <span>mkForeign</span> <span>(</span><span>FFun</span> <span>&quot;opendir&quot;</span> <span>[</span><span>FString</span><span>]</span> <span>FPtr</span><span>)</span> <span>args</span>
</code></pre></div>
<p><strong><em>TIPS：</em></strong><em>注意到Idris和Haskell语法上的一些重要差别：</em></p>

<ul>
<li><em><code>module</code>后不需要跟<code>where</code>从句。</em></li>
<li><em>因为依赖类型的本质使得类型推断（type inference）变得更困难，在Idris中无法省略函数签名。</em></li>
<li><em>类型签名使用<code>:</code>而不是<code>::</code>，强调类型的重要性。（相应地，cons运算采用了和ML/Scala相同的<code>::</code>而不是Haskell中的<code>:</code>）</em></li>
</ul>

<p>创建用于读取下一个文件entry的C函数<code>readdir_next</code>，自动忽略<code>&quot;.&quot;</code>和<code>&quot;..&quot;</code>；若失败则指针返回<code>NULL</code>：</p>

<p>C：</p>
<div><pre><code><span>struct</span> <span>dirent</span> <span>*</span><span>readdir_next</span><span>(</span><span>DIR</span> <span>*</span><span>dir</span><span>)</span>
<span>{</span>
    <span>struct</span> <span>dirent</span> <span>entry</span><span>;</span>
    <span>struct</span> <span>dirent</span> <span>*</span><span>entryPtr</span> <span>=</span> <span>NULL</span><span>;</span>

    <span>readdir_r</span><span>(</span><span>dir</span><span>,</span> <span>&amp;</span><span>entry</span><span>,</span> <span>&amp;</span><span>entryPtr</span><span>);</span>
    <span>while</span> <span>(</span><span>entryPtr</span> <span>!=</span> <span>NULL</span> <span>&amp;&amp;</span> 
        <span>(</span><span>strncmp</span><span>(</span><span>entry</span><span>.</span><span>d_name</span><span>,</span> <span>&quot;.&quot;</span><span>,</span> <span>PATH_MAX</span><span>)</span> <span>==</span> <span>0</span> <span>||</span>
        <span>strncmp</span><span>(</span><span>entry</span><span>.</span><span>d_name</span><span>,</span> <span>&quot;..&quot;</span><span>,</span> <span>PATH_MAX</span><span>)</span> <span>==</span> <span>0</span><span>))</span>
        <span>readdir_r</span><span>(</span><span>dir</span><span>,</span> <span>&amp;</span><span>entry</span><span>,</span> <span>&amp;</span><span>entryPtr</span><span>);</span>

    <span>return</span> <span>entryPtr</span><span>;</span>
<span>}</span>
</code></pre></div>
<p>Idris：</p>
<div><pre><code><span>readdir_next</span> <span>:</span> <span>Ptr</span> <span>-&gt;</span> <span>IO</span> <span>Ptr</span>
<span>readdir_next</span> <span>args</span> <span>=</span> <span>mkForeign</span> <span>(</span><span>FFun</span> <span>&quot;readdir_next&quot;</span> <span>[</span><span>FPtr</span><span>]</span> <span>FPtr</span><span>)</span> <span>args</span>
</code></pre></div>
<p><code>read_d_name</code>函数用于读取<code>struct dirent</code>结构中的<code>d_name</code>域：</p>

<p>C：</p>
<div><pre><code><span>char</span> <span>*</span><span>read_d_name</span><span>(</span><span>struct</span> <span>dirent</span> <span>*</span><span>entry</span><span>)</span>
<span>{</span>
    <span>return</span> <span>entry</span><span>-&gt;</span><span>d_name</span><span>;</span>
<span>}</span>
</code></pre></div>
<p>Idris：</p>
<div><pre><code><span>read_d_name</span> <span>:</span> <span>Ptr</span> <span>-&gt;</span> <span>IO</span> <span>String</span>
<span>read_d_name</span> <span>args</span> <span>=</span> <span>mkForeign</span> <span>(</span><span>FFun</span> <span>&quot;read_d_name&quot;</span> <span>[</span><span>FPtr</span><span>]</span> <span>FString</span><span>)</span> <span>args</span>
</code></pre></div>
<p><code>is_st_mode_dir</code>函数根据<code>struct stat</code>的<code>st_mode</code>域判断该entry是否为一个文件夹。因为C89没有bool类型，故这里返回一个非<code>0</code>的<code>int</code>值表示是文件夹，否则返回<code>0</code>。</p>

<p>C：</p>
<div><pre><code><span>unsigned</span> <span>int</span> <span>is_st_mode_dir</span><span>(</span><span>char</span> <span>*</span><span>path</span><span>)</span>
<span>{</span>
    <span>struct</span> <span>stat</span> <span>entryInfo</span><span>;</span>

    <span>if</span> <span>(</span><span>lstat</span><span>(</span><span>path</span><span>,</span> <span>&amp;</span><span>entryInfo</span><span>)</span> <span>==</span> <span>0</span><span>)</span>
        <span>return</span> <span>S_ISDIR</span><span>(</span><span>entryInfo</span><span>.</span><span>st_mode</span><span>);</span>
    <span>else</span>
        <span>return</span> <span>0</span><span>;</span>
<span>}</span>
</code></pre></div>
<p>在Idris中，我们可以根据判断C foreign function的返回值是否为0把它转成<code>IO Bool</code>类型。</p>

<p>Idris：</p>
<div><pre><code><span>is_st_mode_dir</span> <span>:</span> <span>String</span> <span>-&gt;</span> <span>IO</span> <span>Bool</span>
<span>is_st_mode_dir</span> <span>args</span> <span>=</span>
  <span>mkForeign</span> <span>(</span><span>FFun</span> <span>&quot;is_st_mode_dir&quot;</span> <span>[</span><span>FString</span><span>]</span> <span>FInt</span><span>)</span> <span>args</span> <span>&gt;&gt;=</span>
    <span>return</span> <span>.</span> <span>(</span><span>/=</span> <span>0</span><span>)</span>
</code></pre></div>
<p><code>read_st_size</code>函数读取<code>struct stat</code>的<code>st_size</code>域，返回文件entry的大小：</p>

<p>C：</p>
<div><pre><code><span>off_t</span> <span>read_st_size</span><span>(</span><span>char</span> <span>*</span><span>path</span><span>)</span>
<span>{</span>
    <span>struct</span> <span>stat</span> <span>entryInfo</span><span>;</span>

    <span>if</span> <span>(</span><span>lstat</span><span>(</span><span>path</span><span>,</span> <span>&amp;</span><span>entryInfo</span><span>)</span> <span>==</span> <span>0</span><span>)</span>
        <span>return</span> <span>entryInfo</span><span>.</span><span>st_size</span><span>;</span>
    <span>else</span>
        <span>return</span> <span>0</span><span>;</span>
<span>}</span>
</code></pre></div>
<p>Idris：</p>
<div><pre><code><span>read_st_size</span> <span>:</span> <span>String</span> <span>-&gt;</span> <span>IO</span> <span>Int</span>
<span>read_st_size</span> <span>args</span> <span>=</span> <span>mkForeign</span> <span>(</span><span>FFun</span> <span>&quot;read_st_size&quot;</span> <span>[</span><span>FString</span><span>]</span> <span>FInt</span><span>)</span> <span>args</span>
</code></pre></div>
<p>剩下来的工作，只需要直接在Idris中创建 <code>closedir()</code>和<code>remove()</code>这两个系统调用即可：</p>
<div><pre><code><span>closedir</span> <span>:</span> <span>Ptr</span> <span>-&gt;</span> <span>IO</span> <span>()</span>
<span>closedir</span> <span>args</span> <span>=</span> <span>mkForeign</span> <span>(</span><span>FFun</span> <span>&quot;closedir&quot;</span> <span>[</span><span>FPtr</span><span>]</span> <span>FUnit</span><span>)</span> <span>args</span>
</code></pre></div><div><pre><code><span>remove</span> <span>:</span> <span>String</span> <span>-&gt;</span> <span>IO</span> <span>Int</span>
<span>remove</span> <span>args</span> <span>=</span> <span>mkForeign</span> <span>(</span><span>FFun</span> <span>&quot;remove&quot;</span> <span>[</span><span>FString</span><span>]</span> <span>FInt</span><span>)</span> <span>args</span>
</code></pre></div>
<h2>命令选项<code>Option</code></h2>

<p>我们约定，所有系统函数的第一个参数为我们自定义的<code>List Option</code>类型，表示需要传递的附加命令选项；第二个参数表示真正需要处理的值。</p>
<div><pre><code><span>data</span> <span>Option</span> <span>=</span> <span>All</span> <span>|</span> <span>NoDir</span> <span>|</span> <span>Rec</span> <span>|</span> <span>OnePerLine</span>
</code></pre></div>
<p>是否忽略以<code>&#39;.&#39;</code>打头的文件名。</p>
<div><pre><code><span>-- Do not ignore entries starting with .</span>
<span>is_All</span> <span>:</span> <span>Option</span> <span>-&gt;</span> <span>Bool</span>
<span>is_All</span> <span>All</span> <span>=</span> <span>True</span>
<span>is_All</span> <span>_</span> <span>=</span> <span>False</span>
</code></pre></div>
<p>是否处理文件夹。</p>
<div><pre><code><span>-- Do not list directory entries</span>
<span>is_NoDir</span> <span>:</span> <span>Option</span> <span>-&gt;</span> <span>Bool</span>
<span>is_NoDir</span> <span>NoDir</span> <span>=</span> <span>True</span>
<span>is_NoDir</span> <span>_</span> <span>=</span> <span>False</span>
</code></pre></div>
<p>是否递归地处理子文件夹。</p>
<div><pre><code><span>-- List subdirectories recursively</span>
<span>is_Rec</span> <span>:</span> <span>Option</span> <span>-&gt;</span> <span>Bool</span>
<span>is_Rec</span> <span>Rec</span> <span>=</span> <span>True</span>
<span>is_Rec</span> <span>_</span> <span>=</span> <span>False</span>
</code></pre></div>
<p>是否每行仅列出一项。（用于标准输出）</p>
<div><pre><code><span>-- List one entry per line</span>
<span>is_OnePerLine</span> <span>:</span> <span>Option</span> <span>-&gt;</span> <span>Bool</span>
<span>is_OnePerLine</span> <span>OnePerLine</span> <span>=</span> <span>True</span>
<span>is_OnePerLine</span> <span>_</span> <span>=</span> <span>False</span>
</code></pre></div>
<h2><code>ls</code>的实现</h2>
<div><pre><code><span>-- List directory contents</span>
<span>ls</span> <span>:</span> <span>List</span> <span>Option</span> <span>-&gt;</span> <span>List</span> <span>String</span> <span>-&gt;</span> <span>List</span> <span>String</span>
<span>ls</span> <span>options</span> <span>targets</span> <span>=</span> <span>ls&#39;</span> <span>targets</span>
  <span>where</span>
    <span>-- Do not ignore entries starting with .</span>
    <span>has_All</span> <span>:</span> <span>Bool</span>
    <span>has_All</span> <span>=</span> <span>any</span> <span>is_All</span> <span>options</span>

    <span>-- Do not list directory entries</span>
    <span>has_NoDir</span> <span>:</span> <span>Bool</span>
    <span>has_NoDir</span> <span>=</span> <span>any</span> <span>is_NoDir</span> <span>options</span>

    <span>-- List subdirectories recursively</span>
    <span>has_Rec</span> <span>:</span> <span>Bool</span>
    <span>has_Rec</span> <span>=</span> <span>any</span> <span>is_Rec</span> <span>options</span>
</code></pre></div>
<p>到这里，我们定义了三个函数<code>has_All</code>、<code>has_NoDir</code>、<code>has_Rec</code>，它们用来检查<code>options</code>中是否存在相应的选项。</p>

<p>该辅助函数用来检查文件名是否以<code>&#39;.&#39;</code>打头（隐藏）：</p>
<div><pre><code>    <span>-- Check if a name starts with .</span>
    <span>isEntryHidden</span> <span>:</span> <span>String</span> <span>-&gt;</span> <span>Bool</span>
    <span>isEntryHidden</span> <span>name</span> <span>=</span> <span>case</span> <span>unpack</span> <span>name</span> <span>of</span>
      <span>&#39;.&#39;</span> <span>::</span> <span>_</span> <span>=&gt;</span> <span>True</span>
      <span>_</span> <span>=&gt;</span> <span>False</span>
</code></pre></div>
<p>主要的实现部分：</p>
<div><pre><code>    <span>lsPath</span> <span>:</span> <span>String</span> <span>-&gt;</span> <span>List</span> <span>String</span>
    <span>lsPath</span> <span>path</span> <span>=</span> <span>unsafePerformIO</span> <span>$</span>
      <span>do</span>
        <span>dir</span> <span>&lt;-</span> <span>opendir</span> <span>path</span>
        <span>result</span> <span>&lt;-</span> <span>lsDir</span> <span>dir</span>
        <span>closedir</span> <span>dir</span>
        <span>return</span> <span>result</span>
      <span>where</span>
        <span>lsDir</span> <span>:</span> <span>Ptr</span> <span>-&gt;</span> <span>IO</span> <span>(</span><span>List</span> <span>String</span><span>)</span>
        <span>lsDir</span> <span>dir</span> <span>=</span> <span>do</span>
          <span>entry</span> <span>&lt;-</span> <span>readdir_next</span> <span>dir</span>
          <span>isNull</span> <span>&lt;-</span> <span>nullPtr</span> <span>entry</span>
          <span>if</span> <span>not</span> <span>isNull</span>
            <span>then</span> <span>do</span>
              <span>d_name</span> <span>&lt;-</span> <span>read_d_name</span> <span>entry</span>
              <span>let</span> <span>myPath</span> <span>=</span> <span>path</span> <span>++</span> <span>&quot;/&quot;</span> <span>++</span> <span>d_name</span>

              <span>xs</span> <span>&lt;-</span> <span>lsDir</span> <span>dir</span>

              <span>let</span> <span>isHidden</span> <span>=</span> <span>isEntryHidden</span> <span>d_name</span>
              <span>if</span> <span>not</span> <span>has_All</span> <span>&amp;&amp;</span> <span>isHidden</span>
                <span>then</span> <span>-- Ignore hidden entry when required</span>
                  <span>return</span> <span>xs</span>
                <span>else</span> <span>do</span>
                  <span>isDir</span> <span>&lt;-</span> <span>is_st_mode_dir</span> <span>myPath</span>
                  <span>if</span> <span>not</span> <span>isDir</span>
                    <span>then</span> <span>-- Normal file entry</span>
                      <span>return</span> <span>(</span><span>myPath</span> <span>::</span> <span>xs</span><span>)</span>
                    <span>else</span> <span>do</span> <span>-- Process directory...</span>
                      <span>let</span> <span>recs</span> <span>=</span> <span>lazy</span> <span>lsPath</span> <span>myPath</span>

                      <span>-- Recurse into subdirectory when required</span>
                      <span>return</span> <span>(</span><span>has_Rec</span> <span>?</span> <span>recs</span> <span>++</span> <span>xs</span> <span>:</span> <span>xs</span><span>)</span> <span>&gt;&gt;=</span>
                        <span>has_NoDir</span> <span>?</span> <span>-- Ignore directory entry when required</span>
                          <span>return</span>
                        <span>:</span> <span>-- Normal directory entry</span>
                          <span>return</span> <span>.</span> <span>(</span><span>myPath</span> <span>::</span><span>)</span>
            <span>else</span>
              <span>return</span> <span>[]</span>

    <span>ls&#39;</span> <span>:</span> <span>List</span> <span>String</span> <span>-&gt;</span> <span>List</span> <span>String</span>
    <span>ls&#39;</span> <span>[]</span> <span>=</span> <span>[]</span>
    <span>ls&#39;</span> <span>(</span><span>x</span> <span>::</span> <span>xs</span><span>)</span> <span>=</span> <span>lsPath</span> <span>x</span> <span>++</span> <span>ls&#39;</span> <span>xs</span>
</code></pre></div>
<p>在熟悉Haskell的前提下，基本可以毫无障碍地读懂用Idris写成的代码。但这里有几点仍然需要特别说明一下：</p>

<ul>
<li><code>lsPath</code>函数是实现的主要部分，它根据一个路径名返回所有文件名称的String List。

<ul>
<li>因为访问了文件系统，而返回值的类型又必须是一个List，所以我们在这里直接使用了<code>unsafePerformIO</code>来处理IO monad。</li>
<li>你可以认为“读文件系统”这个操作没有产生任何副作用，但显然这么做是有问题的。具体的分析放在后面。</li>
</ul></li>
<li>在决定是否递归访问文件夹结构之前，<code>lazy</code>可以避免不必要的求值。这个问题在Haskell中不存在，但Idris是一种严格求值的语言。

<ul>
<li>当然，还可以有别的解决方式。这也将在后面提到。</li>
</ul></li>
</ul>

<h2>Composition over Coupling</h2>

<p>在面向对象的设计中，“<a href="http://en.wikipedia.org/wiki/Composition_over_inheritance">Composition over inheritance</a>”是一条重要的原则。</p>

<p>简单地讲就是：如果说法拉利Enzo需要一个V12引擎，LaFerrari需要一个V12引擎，那么最好的设计方式是把“引擎”这件东西抽象出来作为一个单独的类，而不是笼统地说：LaFerrari继承了Enzo，包括继承了它的V12引擎。把完成某特定子功能的、可以被重用的部分抽象出来作为一个独立的组件开发，这是较为合理的工程学方法。</p>

<p>在函数式编程中亦是如此。如果软件的一项功能是求得一个数据类型的值然后将其输出到文件；另一项功能是求得另一个同数据类型的值将其输出到文件。那么为这两项功能仅仅设计两个函数是不合理的；更好的选择是，考虑到这两种同类型的数据可以采取同一种方式输出，将其打印到文件的函数完全可以放到一个type class的instance中去定义。这样完成我们想要功能的部分就从一个单一的函数实现，变成了一个求值的纯函数与处理I/O的非纯函数的组合。<strong>Composition over coupling</strong>——Coupling意味着耦合度，意即功能上的相关性；而Composition可以更为直观地理解为<a href="http://en.wikipedia.org/wiki/Function_composition_(computer_science)">函数的组合（function composition）</a>，越多的Composition象征着越高的聚合度。</p>

<p>这就是为什么我们应该让<code>ls</code>只返回一个求值结果的List，而不是同时做“求值”和“标准输出”这两件事情的原因。</p>

<p>更进一步，你会注意到我们前面定义的<code>ls</code>的目标参数是一个List。这不是必需的，因为你知道，如果<code>ls</code>能处理一个String，那么理所当然也能用它来处理一个List String——只要通过适当的函数组合就能达到我们的目的。如果需要有一个能够处理List参数的函数（假定它叫<code>lss</code>），那么至少我们可以把“处理单个String参数”的<code>ls</code>函数抽出来，然后把<code>lss</code>定义为<code>ls</code>和<code>map</code>的组合。问题解决。</p>

<p>从现在起，我们定义的函数均为处理单个参数，不再处理更多的List。</p>

<h2><code>fileSize</code>的实现</h2>

<p>这个函数用来返回一个文件的大小（字节）。它的定义非常简单：</p>
<div><pre><code><span>fileSize</span> <span>:</span> <span>List</span> <span>Option</span> <span>-&gt;</span> <span>String</span> <span>-&gt;</span> <span>Int</span>
<span>fileSize</span> <span>options</span> <span>file</span> <span>=</span> <span>unsafePerformIO</span> <span>$</span> <span>read_st_size</span> <span>file</span>
</code></pre></div>
<h2><code>rm</code>的实现</h2>

<p>显而易见，<code>rm</code>必须放在IO Monad里去实现。它的返回值遵循POSIX惯例，为<code>0</code>则表示执行成功。</p>
<div><pre><code><span>rm</span> <span>:</span> <span>List</span> <span>Option</span> <span>-&gt;</span> <span>String</span> <span>-&gt;</span> <span>IO</span> <span>Int</span>
<span>rm</span> <span>options</span> <span>=</span> <span>remove</span>
</code></pre></div>
<p>如果要想显式地输出这个返回的Int值，可以为<code>Show</code>的type class创建一个instance：</p>
<div><pre><code><span>instance</span> <span>Show</span> <span>a</span> <span>=&gt;</span> <span>Show</span> <span>(</span><span>IO</span> <span>a</span><span>)</span> <span>where</span>
  <span>show</span> <span>x</span> <span>=</span> <span>show</span> <span>$</span> <span>unsafePerformIO</span> <span>x</span>
</code></pre></div>
<h2>System Call is Never Pure</h2>

<p>在前面，我们已经完成了一些系统函数在Idris中的类型定义。在继续下去以前，我想问一个问题：它们是“纯”的吗？</p>

<p><code>rm</code>操作修改了文件系统的状态，很显然它不是；问题在于我们用来“读”文件系统的这个<code>ls</code>函数。</p>

<p>记住这一点：系统调用永远是有副作用的，哪怕是只读不写的操作，也不例外；原因就在于<strong>人无法两次踏进同一条河流</strong>。操作系统与它的环境无法被抽象成某个可预测的数学模型，它的状态是可以随时改变的；上一个时间点对<code>ls</code>求值的结果很可能与下一个时间点求值的结果不同，类似种种的系统底层API，也就不符合函数式语言中对“纯函数”的定义——数学函数的定义是一个已知的从定义域到值域的映射，从函数的输入可以精确地计算出唯一的输出。而系统调用的行为并不符合这个定义。这也就是为什么Haskell和Idris这样的纯函数式语言要把所有调用C的FFI都默认放在IO Monad中去实现一样，因为确实除了少数做纯数学计算的函数（如<code>math.h</code>中的<code>sin()</code>、<code>cos()</code>），其他大多数C函数多半是不符合“纯函数”这个定义的。</p>

<p>结论是，<code>ls</code>求值的结果，虽然看来是一个List且无副作用，但是从本质上讲它并不是一个函数式的List；我们应该把它塞到一个IO List里去，这从纯函数和线程安全的角度考虑似乎更正确。</p>

<h2><code>echo</code>和标准输出符</h2>

<p>到目前为止，我们所定义的函数仍然没有任何执行标准输出的能力。现在，可以实现这样一个把任意List输出到终端的函数：</p>
<div><pre><code><span>echo</span> <span>:</span> <span>Show</span> <span>a</span> <span>=&gt;</span> <span>List</span> <span>Option</span> <span>-&gt;</span> <span>List</span> <span>a</span> <span>-&gt;</span> <span>IO</span> <span>()</span>
<span>echo</span> <span>options</span> <span>=</span> <span>echo&#39;</span>
  <span>where</span>
    <span>-- List one entry per line</span>
    <span>has_OnePerLine</span> <span>:</span> <span>Bool</span>
    <span>has_OnePerLine</span> <span>=</span> <span>any</span> <span>is_OnePerLine</span> <span>options</span>

    <span>echo&#39;</span> <span>:</span> <span>Show</span> <span>a</span> <span>=&gt;</span> <span>List</span> <span>a</span> <span>-&gt;</span> <span>IO</span> <span>()</span>
    <span>echo&#39;</span> <span>=</span> <span>has_OnePerLine</span> <span>?</span> <span>sequence_</span> <span>.</span> <span>map</span> <span>print</span> <span>:</span> <span>print</span>
</code></pre></div>
<p>考虑到在未来的脚本程序中，标准输出会是非常常见的操作，我们定义这样一个前缀运算符：</p>
<div><pre><code><span>prefix</span> <span>0</span> <span>#</span>
<span>(</span><span>#</span><span>)</span> <span>:</span> <span>Show</span> <span>a</span> <span>=&gt;</span> <span>a</span> <span>-&gt;</span> <span>IO</span> <span>()</span>
<span>(</span><span>#</span><span>)</span> <span>=</span> <span>print</span>
</code></pre></div>
<p>它会把后面表达式求值的结果直接输出到终端。</p>

<h2>文件重定向符</h2>

<p>在Unix shell中，用<code>&gt;</code>符号把标准输出重定向到文件的用法十分便利。在我们这个Shell的namespace中，同样可以定义这样的中缀运算符，让它覆盖Prelude中的原定义：</p>
<div><pre><code><span>(</span><span>&gt;</span><span>)</span> <span>:</span> <span>Show</span> <span>a</span> <span>=&gt;</span> <span>|</span><span>(</span><span>contents</span> <span>:</span> <span>a</span><span>)</span> <span>-&gt;</span> <span>String</span> <span>-&gt;</span> <span>IO</span> <span>Int</span>
<span>contents</span> <span>&gt;</span> <span>fileName</span> <span>=</span> <span>do</span>
  <span>output</span> <span>&lt;-</span> <span>openFile</span> <span>fileName</span> <span>Write</span>
  <span>outputError</span> <span>&lt;-</span> <span>ferror</span> <span>output</span>
  <span>outputFileValid</span> <span>&lt;-</span> <span>validFile</span> <span>output</span>
  <span>status</span> <span>&lt;-</span>
    <span>if</span> <span>not</span> <span>outputError</span> <span>&amp;&amp;</span> <span>outputFileValid</span>
      <span>then</span> <span>do</span>
        <span>fwrite</span> <span>output</span> <span>$</span> <span>show</span> <span>contents</span>
        <span>return</span> <span>0</span>
      <span>else</span>
        <span>return</span> <span>(</span><span>-</span><span>1</span><span>)</span>
  <span>closeFile</span> <span>output</span>
  <span>return</span> <span>status</span>
</code></pre></div>
<p>这个也可以有：</p>
<div><pre><code><span>(</span><span>&gt;&gt;</span><span>)</span> <span>:</span> <span>Show</span> <span>a</span> <span>=&gt;</span> <span>|</span><span>(</span><span>contents</span> <span>:</span> <span>a</span><span>)</span> <span>-&gt;</span> <span>String</span> <span>-&gt;</span> <span>IO</span> <span>Int</span>
<span>contents</span> <span>&gt;&gt;</span> <span>fileName</span> <span>=</span> <span>do</span>
  <span>output</span> <span>&lt;-</span> <span>fopen</span> <span>fileName</span> <span>&quot;a&quot;</span>
  <span>outputError</span> <span>&lt;-</span> <span>ferror</span> <span>output</span>
  <span>outputFileValid</span> <span>&lt;-</span> <span>validFile</span> <span>output</span>
  <span>status</span> <span>&lt;-</span>
    <span>if</span> <span>not</span> <span>outputError</span> <span>&amp;&amp;</span> <span>outputFileValid</span>
      <span>then</span> <span>do</span>
        <span>fwrite</span> <span>output</span> <span>$</span> <span>show</span> <span>contents</span>
        <span>return</span> <span>0</span>
      <span>else</span>
        <span>return</span> <span>(</span><span>-</span><span>1</span><span>)</span>
  <span>closeFile</span> <span>output</span>
  <span>return</span> <span>status</span>
</code></pre></div>
<p>注意<code>contents</code>参数采取了惰性求值方式，这样可以避免在文件写操作失败情况下不必要的求值。</p>

<h2>管道机制</h2>

<p>前面早就提到过，Unix的管道机制本身就可以和函数式编程中的概念加以类比。</p>

<p>现在，在我们的这个新Shell里面，将要定义4种管道运算符，它们有着不同的类型签名（实质上它们代表了函数式语言中的四个不同概念）。</p>

<h3>1. Run-through <code>(|$)</code></h3>

<p>第一个管道符叫“Run-through”。</p>
<div><pre><code><span>infixl</span> <span>2</span> <span>|$</span>
<span>(</span><span>|$</span><span>)</span> <span>:</span> <span>List</span> <span>a</span> <span>-&gt;</span> <span>(</span><span>List</span> <span>a</span> <span>-&gt;</span> <span>List</span> <span>b</span><span>)</span> <span>-&gt;</span> <span>List</span> <span>b</span>
<span>(</span><span>|$</span><span>)</span> <span>=</span> <span>flip</span> <span>(</span><span>$</span><span>)</span>
</code></pre></div>
<p>在Idris的标准库（<code>Builtins.idr</code>）中，<code>flip</code>和<code>($)</code>分别是这么定义的：</p>
<div><pre><code><span>flip</span> <span>:</span> <span>(</span><span>a</span> <span>-&gt;</span> <span>b</span> <span>-&gt;</span> <span>c</span><span>)</span> <span>-&gt;</span> <span>b</span> <span>-&gt;</span> <span>a</span> <span>-&gt;</span> <span>c</span>
<span>flip</span> <span>f</span> <span>x</span> <span>y</span> <span>=</span> <span>f</span> <span>y</span> <span>x</span>
</code></pre></div><div><pre><code><span>(</span><span>$</span><span>)</span> <span>:</span> <span>(</span><span>a</span> <span>-&gt;</span> <span>b</span><span>)</span> <span>-&gt;</span> <span>a</span> <span>-&gt;</span> <span>b</span>
<span>f</span> <span>$</span> <span>a</span> <span>=</span> <span>f</span> <span>a</span>
</code></pre></div>
<p>所以，我们刚才定义的东西事实上就等价于：</p>
<div><pre><code><span>(</span><span>|$</span><span>)</span> <span>:</span> <span>List</span> <span>a</span> <span>-&gt;</span> <span>(</span><span>List</span> <span>a</span> <span>-&gt;</span> <span>List</span> <span>b</span><span>)</span> <span>-&gt;</span> <span>List</span> <span>b</span>
<span>x</span> <span>|$</span> <span>f</span> <span>=</span> <span>f</span> <span>x</span>
</code></pre></div>
<p>你会说，这种东西定义出来有啥用？后面将展示它的实际用途。</p>

<h3>2. Map <code>(|.)</code></h3>

<p>第二个管道相当于一个左右参数倒置的<code>`map`</code>。</p>
<div><pre><code><span>infixl</span> <span>2</span> <span>|.</span>
<span>(</span><span>|.</span><span>)</span> <span>:</span> <span>List</span> <span>a</span> <span>-&gt;</span> <span>(</span><span>a</span> <span>-&gt;</span> <span>b</span><span>)</span> <span>-&gt;</span> <span>List</span> <span>b</span>
<span>(</span><span>|.</span><span>)</span> <span>=</span> <span>flip</span> <span>map</span>
</code></pre></div>
<h3>3. Filter <code>(|&amp;)</code></h3>

<p>第三个管道相当于一个左右参数倒置的<code>`filter`</code>。</p>
<div><pre><code><span>infixl</span> <span>2</span> <span>|&amp;</span>
<span>(</span><span>|&amp;</span><span>)</span> <span>:</span> <span>List</span> <span>a</span> <span>-&gt;</span> <span>(</span><span>a</span> <span>-&gt;</span> <span>Bool</span><span>)</span> <span>-&gt;</span> <span>List</span> <span>a</span>
<span>(</span><span>|&amp;</span><span>)</span> <span>=</span> <span>flip</span> <span>filter</span>
</code></pre></div>
<h3>4. Foldl <code>(|+)</code></h3>

<p>对的，你一定已经猜到了，剩下最后一个要定义的，自然就是实现fold操作的管道！</p>
<div><pre><code><span>infixl</span> <span>2</span> <span>|+</span>
<span>(</span><span>|+</span><span>)</span> <span>:</span> <span>List</span> <span>a</span> <span>-&gt;</span> <span>(</span><span>c</span> <span>-&gt;</span> <span>a</span> <span>-&gt;</span> <span>c</span><span>)</span> <span>-&gt;</span> <span>c</span> <span>-&gt;</span> <span>c</span>
<span>(</span><span>|+</span><span>)</span> <span>xs</span> <span>f</span> <span>init</span> <span>=</span> <span>foldl</span> <span>f</span> <span>init</span> <span>xs</span>
</code></pre></div>
<h2>示例</h2>

<p>现在，我们可以用前面的函数定义来做一些简单的系统任务了，</p>
<div><pre><code><span>module</span> <span>Main</span>
<span>import</span> <span>Shell</span>

<span>main</span> <span>:</span> <span>IO</span> <span>()</span>
<span>main</span> <span>=</span> <span>do</span>
  <span>echo</span> <span>[]</span> <span>$</span> <span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;db&quot;</span><span>]</span>
</code></pre></div>
<p>它的作用是对<code>ls [All, Rec, NoDir] [&quot;db&quot;]</code>进行求值（递归地访问<code>db/</code>目录取得全部文件名的列表），并输出到终端。</p>

<p>加上<code>OnePerLine</code>选项让它每次输出一行：</p>
<div><pre><code>  <span>echo</span> <span>[</span><span>OnePerLine</span><span>]</span> <span>$</span> <span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;db&quot;</span><span>]</span>
</code></pre></div>
<p>回想起前面定义过的标准输出符，我们有更简短的写法：</p>
<div><pre><code>  <span>#</span> <span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;db&quot;</span><span>]</span>
</code></pre></div>
<p>在REPL里当然可以直接使用：</p>
<div><pre><code><span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;db&quot;</span><span>]</span>
</code></pre></div>
<p>来查看结果，不过这里是作为一个脚本程序运行，因此必须指定输出到终端。</p>

<p>输出文件的大小到终端：</p>
<div><pre><code>  <span>#</span> <span>fileSize</span> <span>[]</span> <span>&quot;db/Movies.db&quot;</span>
</code></pre></div>
<p>以<code>(文件名, 文件大小)</code>的格式输出<code>db/</code>目录下的全部文件信息，借助Idris的List comprehension：</p>
<div><pre><code>  <span>#</span> <span>[</span> <span>(</span><span>f</span><span>,</span> <span>fileSize</span> <span>[]</span> <span>f</span><span>)</span> <span>|</span> <span>f</span> <span>&lt;-</span> <span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;db&quot;</span><span>]</span> <span>]</span>
</code></pre></div>
<p>利用Idris标准库函数中的<code>sort</code>，对文件名进行按ASCII顺序排序后输出：</p>
<div><pre><code>  <span>#</span> <span>sort</span> <span>(</span><span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;db&quot;</span><span>])</span>
</code></pre></div>
<p>回想起我们前面定义的“Run-through”管道符，上面的写法其实可以等效改写为：</p>
<div><pre><code>  <span>#</span> <span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;db&quot;</span><span>]</span> <span>|$</span> <span>sort</span>
</code></pre></div>
<p>这就是一个与Unix shell的管道非常相似的形式了。</p>

<p>我们也可以用Idris的<code>map</code>函数来对文件名的List执行操作，返回一个相应的文件大小列表：</p>
<div><pre><code>  <span>#</span> <span>map</span> <span>(</span><span>fileSize</span> <span>[]</span><span>)</span> <span>(</span><span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;db&quot;</span><span>])</span>
</code></pre></div>
<p>不过，既然有了现成的map管道符，以上的写法就可以等效成：</p>
<div><pre><code>  <span>#</span> <span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;db&quot;</span><span>]</span> <span>|.</span> <span>fileSize</span> <span>[]</span>
</code></pre></div>
<p>同样，若要用<code>filter</code>过滤出所有文件中大小不为0的并输出：</p>
<div><pre><code>  <span>#</span> <span>filter</span> <span>((</span><span>&gt;</span> <span>0</span><span>)</span> <span>.</span> <span>fileSize</span> <span>[]</span><span>)</span> <span>(</span><span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;db&quot;</span><span>])</span>
</code></pre></div>
<p>它的仿Shell写法就是：</p>
<div><pre><code>  <span>#</span> <span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;db&quot;</span><span>]</span> <span>|&amp;</span> <span>(</span><span>&gt;</span> <span>0</span><span>)</span> <span>.</span> <span>fileSize</span> <span>[]</span>
</code></pre></div>
<p>对所有文件的大小进行求和：</p>
<div><pre><code>  <span>#</span> <span>foldl</span> <span>(</span><span>flip</span> <span>$</span> <span>(</span><span>+</span><span>)</span> <span>.</span> <span>fileSize</span> <span>[]</span><span>)</span> <span>0</span> <span>(</span><span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;db&quot;</span><span>])</span>
</code></pre></div>
<p>等价于：</p>
<div><pre><code>  <span>#</span> <span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;db&quot;</span><span>]</span> <span>|+</span> <span>(</span><span>flip</span> <span>$</span> <span>(</span><span>+</span><span>)</span> <span>.</span> <span>fileSize</span> <span>[]</span><span>)</span> <span>$</span> <span>0</span>
</code></pre></div>
<p>管道可以按照从左到右的顺序依次结合，这相当有用。如，定义获取文件扩展名的函数为：</p>
<div><pre><code><span>extension</span> <span>:</span> <span>String</span> <span>-&gt;</span> <span>String</span>
<span>extension</span> <span>s</span> <span>=</span>
  <span>let</span>
    <span>l</span> <span>:</span> <span>List</span> <span>String</span> <span>=</span> <span>reverse</span> <span>$</span> <span>split</span> <span>(</span><span>==</span> <span>&#39;.&#39;</span><span>)</span> <span>s</span>
  <span>in</span>
    <span>case</span> <span>l</span> <span>of</span>
      <span>x</span> <span>::</span> <span>(</span><span>y</span> <span>::</span> <span>_</span><span>)</span> <span>=&gt;</span> <span>x</span>
      <span>_</span> <span>=&gt;</span> <span>&quot;&quot;</span>
</code></pre></div>
<p>利用filter管道，找出<code>db/</code>下所有扩展名为<code>.db</code>的文件：</p>
<div><pre><code>  <span>#</span> <span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;db&quot;</span><span>]</span> <span>|&amp;</span> <span>(</span><span>==</span> <span>&quot;db&quot;</span><span>)</span> <span>.</span> <span>extension</span>
</code></pre></div>
<p>利用filter管道和map管道的结合，即可删除<code>db/</code>下所有扩展名为<code>.db</code>的文件：</p>
<div><pre><code>  <span>#</span> <span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;db&quot;</span><span>]</span> <span>|&amp;</span> <span>(</span><span>==</span> <span>&quot;db&quot;</span><span>)</span> <span>.</span> <span>extension</span> <span>|.</span> <span>rm</span> <span>[]</span>
</code></pre></div>
<p>直接重定向输出到文件，这也十分容易做到：</p>
<div><pre><code>  <span>&quot;hello, world&quot;</span> <span>&gt;&gt;</span> <span>&quot;output.txt&quot;</span>

  <span>fileSize</span> <span>[]</span> <span>&quot;db/Movies.db&quot;</span> <span>&gt;</span> <span>&quot;output.txt&quot;</span>
</code></pre></div>
<h2>Syntax is Important</h2>

<p>上面给出了这么多例子，包括对Idris的一般写法和运用仿Shell管道符的写法进行了对比，其实只是为了说明一个道理：<strong>Syntax is important</strong>。</p>

<p>LISPer们喜欢声称语法（syntax）不重要，语法糖（syntactic sugar）是无益的；语义（semantics）才是程序语言的精髓。然而他们忘记了最重要的一点：Syntax是一种程序语言的User Interface。如果一个用户界面不去讨好用户反而去讨好编译器；如果一个用户界面反而不能让用户轻松地表达自己的思想，那么谈再多的semantics也毫无意义。</p>

<p>从我们的例子来看，首先，作为一个交互式shell，你设计的这种语言不能有过多的符号匹配；适当的空格和运算符都OK，但你不应该让用户敲命令的时候需要在脑子里面先建立一个栈，左括号进右括号出，这种反人类的表达方式注定要被自然选择所淘汰；其次，要有适当的中缀形式，比如，Unix shell的管道机制比起现有函数式语言中的<code>map</code>、<code>filter</code>，确实更容易被非程序员用户所理解，所以在新shell的设计中借鉴它们是理所当然的。</p>

<p>我们设计的shell其实做得还不够多。例如，为了吸收Zsh中的便利设计，应当支持直接使用目录名进入该目录的方式（在交互式shell中）；还应当支持在不引起歧义的情况下省略引号。但这在Idris的语言环境中我们做不到。</p>

<p>所以，未来的工作或许可以考虑以下两个方面，</p>

<ol>
<li><p>一个完整的Shell interpreter或类似SML/NJ那样的interactive compiler。</p></li>
<li><p>一个REPL。显然，Idris自己的REPL非常不适合做Shell的交互式界面。</p></li>
</ol>

<h2>与Unix Shell的对比</h2>

<p>最后简要地分析一下这个新的Typed shell与传统Unix shell之间的差异，当然，还有它自身存在的缺陷。</p>

<p>在Unix shell里：</p>
<div><pre><code>find . -type f -name &quot;*.db&quot; -print0 | xargs -0 rm
</code></pre></div>
<p>这里管道的工作方式是：<code>find</code>和<code>xargs</code>命令同时被fork出独立的进程；<code>find</code>在执行过程中访问文件系统查找符合要求的文件，即时生成作为结果的字符stream，<code>xargs</code>负责即时接受这个stream，通过以<code>NUL</code>作为分隔符的方式解析出每个参数，然后将其传递给<code>rm</code>进程。Unix pipeline的缓冲机制可以确保这个stream的传输不受<code>find</code>和<code>xargs</code>处理效率不同步的影响。</p>

<p><img src="http://i.imgur.com/koJAXay.png" width="60%"></p>

<p>而在这个新的Typed shell里，等效的写法为：</p>
<div><pre><code><span>ls</span> <span>[</span><span>All</span><span>,</span> <span>Rec</span><span>,</span> <span>NoDir</span><span>]</span> <span>[</span><span>&quot;.&quot;</span><span>]</span> <span>|&amp;</span> <span>(</span><span>==</span> <span>&quot;db&quot;</span><span>)</span> <span>.</span> <span>extension</span> <span>|.</span> <span>rm</span> <span>[]</span>
</code></pre></div>
<p>这段代码的执行方式是：对系统调用<code>ls [All, Rec, NoDir] [&quot;.&quot;]</code>进行立即求值，求值的结果和lambda（<code>(== &quot;db&quot;) . extension</code>）一同传递给filter函数（中缀形式的<code>|&amp;</code>管道符），再次立即求值，求值的结果和lambda（<code>rm []</code>）一同传递给map函数（中缀形式的<code>|.</code>管道符），进行立即求值。</p>

<p>这比起传统的Unix shell来有哪些优/缺点呢？</p>

<p>优势很明显，</p>

<ol>
<li><p>它基于一个full-fledged的程序语言（纯函数式的Idris，拥有可扩展的标准库，高度DSL表达能力），从语言本身的设计上大大超越了严重受限的Shell。</p></li>
<li><p>得益于类型系统，可以通过类型检查提供系统脚本本身的类型安全性，避开了无类型的Unix文本流导致的问题。</p></li>
</ol>

<p>它最主要的缺陷，目前存在于两处：</p>

<ol>
<li><p>系统调用不应被视作无副作用的纯函数，它应该一律放到IO Monad中去实现。这一点在前面已经提到过了。</p></li>
<li><p>我们采用Idris中的List取代了Unix用管道为进程间传递数据的方式，保证了类型的安全。但<strong>Unix管道仍然有一个重要的好处：它从本质上说是基于stream的。</strong></p></li>
</ol>

<p>很明显，<a href="http://en.wikipedia.org/wiki/Stream_(type_theory)">stream</a>的优势是按需求值，而不是一次性把所有值全部求出来之后才执行下一步操作；这对于处理较大的数据集，或者我们的程序干脆需要这种stream的工作方式时尤其有用。</p>

<p>Haskell对于解决这件事情具有先天的优势，它可以通过惰性求值的方式来处理它的List。</p>

<p>而在许多严格求值的语言中，亦可通过创建thunk的方式来实现这种按需求值的“List”，这种数据类型在<a href="http://caml.inria.fr/pub/docs/manual-ocaml/libref/Stream.html">ML</a>和<a href="http://www.scala-lang.org/api/current/index.html#scala.collection.immutable.Stream">Scala</a>中被称作Stream。它和Unix管道中所说的stream从理念上是非常接近的。</p>

<p><strong><em>TIPS：</em></strong> <em>在所谓的“<a href="http://en.wikipedia.org/wiki/Total_functional_programming">强函数式语言</a>”中，所有的函数必须都是<a href="http://en.wikipedia.org/wiki/Partial_function#Total_function">完整的（total）</a>，意即：函数对于其参数类型域中的每一个取值都必须有定义，这是一个程序总是能停机（即<a href="http://zh.wikipedia.org/wiki/%E5%88%A4%E5%AE%9A%E5%99%A8">判定器</a>）的必要非充分条件之一；在强函数式语言中，Church–Rosser定理完全成立。这使得在强函数式语言中，急性求值和惰性求值的结果将完全相同。</em></p>

<p>Idris是一个强函数式语言（而Haskell对惰性求值的某些处理方式决定了它不是强函数式的，这点可参见Idris FAQ：<a href="https://github.com/edwinb/Idris-dev/wiki/Unofficial-FAQ">Why isn&#39;t Idris lazy?</a>）。在强函数式语言中，为了实现类似于“惰性求值的List”（或Stream）的数据结构，需借助于<a href="http://en.wikipedia.org/wiki/Coinduction">codata</a>类型：</p>
<div><pre><code><span>codata</span> <span>Stream</span> <span>a</span> <span>=</span> <span>Nil</span> <span>|</span> <span>(</span><span>::</span><span>)</span> <span>a</span> <span>(</span><span>Stream</span> <span>a</span><span>)</span> 

<span>countFrom</span> <span>:</span> <span>Int</span> <span>-&gt;</span> <span>Stream</span> <span>Int</span> 
<span>countFrom</span> <span>x</span> <span>=</span> <span>x</span> <span>::</span> <span>countFrom</span> <span>(</span><span>x</span> <span>+</span> <span>1</span><span>)</span> 

<span>take</span> <span>:</span> <span>Int</span> <span>-&gt;</span> <span>Stream</span> <span>a</span> <span>-&gt;</span> <span>List</span> <span>a</span> 
<span>take</span> <span>0</span> <span>_</span> <span>=</span> <span>[]</span> 
<span>take</span> <span>n</span> <span>(</span><span>x</span> <span>::</span> <span>xs</span><span>)</span> <span>=</span> <span>x</span> <span>::</span> <span>take</span> <span>(</span><span>n</span> <span>-</span> <span>1</span><span>)</span> <span>xs</span> 
<span>take</span> <span>n</span> <span>[]</span> <span>=</span> <span>[]</span> 

<span>main</span> <span>:</span> <span>IO</span> <span>()</span> 
<span>main</span> <span>=</span> <span>do</span> <span>print</span> <span>(</span><span>take</span> <span>10</span> <span>(</span><span>main</span><span>.</span><span>countFrom</span> <span>10</span><span>))</span> 
</code></pre></div>
<p>结合以上两点，可以考虑采用Stream对<code>ls</code>函数进行重新定义：</p>
<div><pre><code><span>ls</span> <span>:</span> <span>List</span> <span>Option</span> <span>-&gt;</span> <span>String</span> <span>-&gt;</span> <span>IO</span> <span>Stream</span>
</code></pre></div>
<hr>

<p>（<em>To be continued…</em>）</p>