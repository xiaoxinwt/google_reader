---
layout: post
title:  "慢慢琢磨JVM——恭喜JavaEye重新开张"
date:   2010-11-24 21:56:40
author: 
categories: program
---

## 慢慢琢磨JVM——恭喜JavaEye重新开张
### by 
### at 2010-11-24 21:56:40
### original <http://www.javaeye.com/topic/821872>

<p> </p>
<h2>
<span lang="EN-US">1 JVM</span><span>简介</span>
</h2>
<p><span lang="EN-US">JVM</span><span>是我们<span lang="EN-US">Javaer</span>的最基本功底了，刚开始学<span lang="EN-US">Java</span>的时候，一般都是从“<span lang="EN-US">Hello World</span>”开始的，然后会写个复杂点<span lang="EN-US">class</span>，然后再找一些开源框架，比如<span lang="EN-US">Spring</span>，<span lang="EN-US">Hibernate</span>等等，再然后就开发企业级的应用，比如网站、企业内部应用、实时交易系统等等，直到某一天突然发现做的系统咋就这么慢呢，而且时不时还来个内存溢出什么的，今天是交易系统报了<span lang="EN-US">StackOverflowError</span>，明天是网站系统报了个<span lang="EN-US">OutOfMemoryError</span>，这种错误又很难重现，只有分析<span lang="EN-US">Javacore</span>和<span lang="EN-US">dump</span>文件，运气好点还能分析出个结果，运行遭的点，就直接去庙里烧香吧！每天接客户的电话都是战战兢兢的，生怕再出什么幺蛾子了。我想<span lang="EN-US">Java</span>做的久一点的都有这样的经历，那这些问题的最终根结是在哪呢？——<span lang="EN-US"> JVM</span>。</span></p>
<p><span lang="EN-US">JVM</span><span>全称是<span lang="EN-US">Java Virtual Machine</span>，<span lang="EN-US">Java</span>虚拟机，也就是在计算机上再虚拟一个计算机，这和我们使用<span lang="EN-US"> VMWare</span>不一样，那个虚拟的东西你是可以看到的，这个<span lang="EN-US">JVM</span>你是看不到的，它存在内存中。我们知道计算机的基本构成是：运算器、控制器、存储器、输入和输出设备，那这个<span lang="EN-US">JVM</span>也是有这成套的元素，运算器是当然是交给硬件<span lang="EN-US">CPU</span>还处理了，只是为了适应“一次编译，随处运行”的情况，需要做一个翻译动作，于是就用了<span lang="EN-US">JVM</span>自己的命令集，这与汇编的命令集有点类似，每一种汇编命令集针对一个系列的<span lang="EN-US">CPU</span>，比如<span lang="EN-US">8086</span>系列的汇编也是可以用在<span lang="EN-US">8088</span>上的，但是就不能跑在<span lang="EN-US">8051</span>上，而<span lang="EN-US">JVM</span>的命令集则是可以到处运行的，因为<span lang="EN-US">JVM</span>做了翻译，根据不同的<span lang="EN-US">CPU</span>，翻译成不同的机器语言。</span></p>
<p><span lang="EN-US">JVM</span><span>中我们最需要深入理解的就是它的存储部分，存储？硬盘？<span lang="EN-US">NO</span>，<span lang="EN-US">NO</span>，<span lang="EN-US"> JVM</span>是一个内存中的虚拟机，那它的存储就是内存了，我们写的所有类、常量、变量、方法都在内存中，这决定着我们程序运行的是否健壮、是否高效，接下来的部分就是重点介绍之。</span></p>
<h2>
<span lang="EN-US">2 JVM</span><span>的组成部分</span>
</h2>
<p><span>我们先把<span lang="EN-US">JVM</span>这个虚拟机画出来，如下图所示：</span></p>
<p>
<br><img src="http://dl.javaeye.com/upload/attachment/353109/0c3fda2c-286b-3819-b47a-4daf15d3d08c.png" alt=""></p>
<p> </p>
<p> </p>
<p><span>从这个图中可以看到，<span lang="EN-US">JVM</span>是运行在操作系统之上的，它与硬件没有直接的交互。我们再来看下<span lang="EN-US">JVM</span>有哪些组成部分，如下图所示：</span></p>
<p><br><img src="http://dl.javaeye.com/upload/attachment/353113/a3a81279-4006-3fa6-9b7f-e1ac95228efd.png" alt=""><br> <span>该图参考了网上广为流传的<span lang="EN-US">JVM</span>构成图，大家看这个图，整个<span lang="EN-US">JVM</span>分为四部分：</span></p>
<p><span style="font-family:Wingdings" lang="EN-US"><span>q<span style="font:7.0pt"> 
</span></span></span><span lang="EN-US">Class Loader </span><span>类加载器</span></p>
<p><span>类加载器的作用是加载类文件到内存，比如编写一个<span lang="EN-US">HelloWord.java</span>程序，然后通过<span lang="EN-US">javac</span>编译成<span lang="EN-US">class</span>文件，那怎么才能加载到内存中被执行呢？<span lang="EN-US">Class Loader</span>承担的就是这个责任，那不可能随便建立一个<span lang="EN-US">.class</span>文件就能被加载的，<span lang="EN-US">Class Loader</span>加载的<span lang="EN-US">class</span>文件是有格式要求，在《<span lang="EN-US">JVM Specification</span>》中式这样定义<span lang="EN-US">Class</span>文件的结构：</span></p>
<div style="padding:4.0pt 4.0pt 4.0pt 4.0pt;margin-left:0cm;margin-right:28.65pt">
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span>ClassFile {</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span><span>  </span>u4
magic;</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span><span>  </span>u2
minor_version;</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span> </span><span>   </span><span>  </span>u2 major_version;</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span><span>  </span>u2
constant_pool_count;</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span><span>  </span>cp_info
constant_pool[constant_pool_count-1];</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span><span>  </span>u2
access_flags;</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span><span>  </span>u2
this_class;</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span><span>  </span>u2
super_class;</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span><span>  </span>u2
interfaces_count;</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span><span>  </span>u2
interfaces[interfaces_count];</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span><span>  </span>u2
fields_count;</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span><span>  </span>field_info
fields[fields_count];</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span><span>  </span>u2
methods_count;</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span><span>  </span>method_info
methods[methods_count];</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span><span>  </span>u2
attributes_count;</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span><span>  </span>attribute_info
attributes[attributes_count];</span></p>
<p style="margin-right:0cm;text-indent:17.5pt"><span style="font-size:10.0pt;color:windowtext" lang="EN-US"><span>    </span>}</span></p>
</div>
<p><span>需要详细了解的话，可以仔细阅读《<span lang="EN-US">JVM Specification</span>》的第四章“<span lang="EN-US">The class File Format</span>”，这里不再详细说明。</span></p>
<p><span>友情提示：<span lang="EN-US">Class
Loader</span>只管加载，只要符合文件结构就加载，至于说能不能运行，则不是它负责的，那是由<span lang="EN-US">Execution
Engine</span>负责的。</span></p>
<p><span style="font-family:Wingdings" lang="EN-US"><span>q<span style="font:7.0pt"> 
</span></span></span><span lang="EN-US">Execution Engine </span><span>执行引擎</span></p>
<p><span>执行引擎也叫做解释器<span lang="EN-US">(Interpreter)</span>，负责解释命令，提交操作系统执行。</span></p>
<p><span style="font-family:Wingdings" lang="EN-US"><span>q<span style="font:7.0pt"> 
</span></span></span><span lang="EN-US">Native Interface</span><span>本地接口</span></p>
<p><span>本地接口的作用是融合不同的编程语言为<span lang="EN-US">Java</span>所用，它的初衷是融合<span lang="EN-US">C/C++</span>程序，<span lang="EN-US">Java</span>诞生的时候是<span lang="EN-US">C/C++</span>横行的时候，要想立足，必须有一个聪明的、睿智的调用<span lang="EN-US">C/C++</span>程序，于是就在内存中专门开辟了一块区域处理标记为<span lang="EN-US">native</span>的代码，它的具体做法是<span lang="EN-US">Native Method Stack</span>中登记<span lang="EN-US">native</span>方法，在<span lang="EN-US">Execution Engine</span>执行时加载<span lang="EN-US">native libraies</span>。目前该方法使用的是越来越少了，除非是与硬件有关的应用，比如通过<span lang="EN-US">Java</span>程序驱动打印机，或者<span lang="EN-US">Java</span>系统管理生产设备，在企业级应用中已经比较少见，因为现在的异构领域间的通信很发达，比如可以使用<span lang="EN-US">Socket</span>通信，也可以使用<span lang="EN-US">Web Service</span>等等，不多做介绍。</span></p>
<p><span style="font-family:Wingdings" lang="EN-US"><span>q<span style="font:7.0pt"> 
</span></span></span><span lang="EN-US">Runtime data area</span><span>运行数据区</span></p>
<p><span>运行数据区是整个<span lang="EN-US">JVM</span>的重点。我们所有写的程序都被加载到这里，之后才开始运行，<span lang="EN-US">Java</span>生态系统如此的繁荣，得益于该区域的优良自治，下一章节详细介绍之。</span></p>
<p><span lang="EN-US"> </span></p>
<p><span>整个<span lang="EN-US">JVM</span>框架由加载器加载文件，然后执行器在内存中处理数据，需要与异构系统交互是可以通过本地接口进行，瞧，一个完整的系统诞生了！</span></p>
<h2>
<span lang="EN-US">2 JVM</span><span>的内存管理</span>
</h2>
<p><span>所有的数据和程序都是在运行数据区存放，它包括以下几部分：</span></p>
<p><span style="font-family:Wingdings" lang="EN-US"><span>q<span style="font:7.0pt"> 
</span></span></span><span lang="EN-US">Stack </span><span>栈</span></p>
<p><span>栈也叫栈内存，是<span lang="EN-US">Java</span>程序的运行区，是在线程创建时创建，它的生命期是跟随线程的生命期，线程结束栈内存也就释放，对于栈来说不存在垃圾回收问题，只要线程一结束，该栈就<span lang="EN-US">Over</span>。问题出来了：栈中存的是那些数据呢？又什么是格式呢？</span></p>
<p><span>栈中的数据都是以栈帧（</span><span lang="EN-US">Stack Frame</span><span>）的格式存在，栈帧是一个内存区块，是一个数据集，是一个有关方法</span><span lang="EN-US">(Method)</span><span>和运行期数据的数据集，当一个方法</span><span lang="EN-US">A</span><span>被调用时就产生了一个栈帧</span><span lang="EN-US">F1</span><span>，并被压入到栈中，</span><span lang="EN-US">A</span><span>方法又调用了</span><span lang="EN-US">B</span><span>方法，于是产生栈帧</span><span lang="EN-US">F2</span><span>也被压入栈，执行完毕后，先弹出</span><span lang="EN-US">F2</span><span>栈帧，再弹出</span><span lang="EN-US">F1</span><span>栈帧，遵循“先进后出”原则。</span></p>
<p><span>那栈帧中到底存在着什么数据呢？栈帧中主要保存<span lang="EN-US">3</span>类数据：本地变量（<span lang="EN-US">Local Variables</span>），包括输入参数和输出参数以及方法内的变量；栈操作（<span lang="EN-US">Operand Stack</span>），记录出栈、入栈的操作；栈帧数据（<span lang="EN-US">Frame Data</span>），包括类文件、方法等等。光说比较枯燥，我们画个图来理解一下<span lang="EN-US">Java</span>栈，如下图所示：</span></p>
<p><br><img src="http://dl.javaeye.com/upload/attachment/353115/183617ef-4489-3b4d-a3f0-ec628cd7241f.png" alt=""><br> <span>图示在一个栈中有两个栈帧，栈帧<span lang="EN-US">2</span>是最先被调用的方法，先入栈，然后方法<span lang="EN-US">2</span>又调用了方法<span lang="EN-US">1</span>，栈帧<span lang="EN-US">1</span>处于栈顶的位置，栈帧<span lang="EN-US">2</span>处于栈底，执行完毕后，依次弹出栈帧<span lang="EN-US">1</span>和栈帧<span lang="EN-US">2</span>，线程结束，栈释放。</span></p>
<p><span style="font-family:Wingdings" lang="EN-US"><span>q<span style="font:7.0pt"> 
</span></span></span><span lang="EN-US"><span> </span></span><span lang="EN-US">Heap </span><span>堆内存</span></p>
<p><span>一个<span lang="EN-US">JVM</span>实例只存在一个堆类存，堆内存的大小是可以调节的。类加载器读取了类文件后，需要把类、方法、常变量放到堆内存中，以方便执行器执行，堆内存分为三部分：</span></p>
<p style="text-indent:21.1pt"><strong><span lang="EN-US">Permanent Space </span></strong><strong><span>永久存储区</span></strong></p>
<p><span>永久存储区是一个常驻内存区域，用于存放<span lang="EN-US">JDK</span>自身所携带的<span lang="EN-US">Class,Interface</span>的元数据，也就是说它存储的是运行环境必须的类信息，被装载进此区域的数据是不会被垃圾回收器回收掉的，关闭<span lang="EN-US">JVM</span>才会释放此区域所占用的内存。</span></p>
<p style="text-indent:21.1pt"><strong><span lang="EN-US">Young Generation Space </span></strong><strong><span>新生区</span></strong></p>
<p><span>新生区是类的诞生、成长、消亡的区域，一个类在这里产生，应用，最后被垃圾回收器收集，结束生命。新生区又分为两部分：
伊甸区（<span lang="EN-US">Eden space</span>）和幸存者区（<span lang="EN-US">Survivor pace</span>），所有的类都是在伊甸区被<span lang="EN-US">new</span>出来的。幸存区有两个：<span lang="EN-US"> 0</span>区（<span lang="EN-US">Survivor
0 space</span>）和<span lang="EN-US">1</span>区（<span lang="EN-US">Survivor 1 space</span>）。当伊甸园的空间用完时，程序又需要创建对象，<span lang="EN-US">JVM</span>的垃圾回收器将对伊甸园区进行垃圾回收，将伊甸园区中的不再被其他对象所引用的对象进行销毁。然后将伊甸园中的剩余对象移动到幸存<span lang="EN-US">0</span>区。若幸存<span lang="EN-US">0</span>区也满了，再对该区进行垃圾回收，然后移动到<span lang="EN-US">1</span>区。那如果<span lang="EN-US">1</span>区也满了呢？再移动到养老区。</span></p>
<p style="text-indent:21.1pt"><strong><span lang="EN-US">Tenure generation space</span></strong><strong><span>养老区</span></strong></p>
<p><span>养老区用于保存从新生区筛选出来的<span lang="EN-US">JAVA</span>对象，一般池对象都在这个区域活跃。<span lang="EN-US"><span>   </span></span>三个区的示意图如下：</span></p>
<p><br><img src="http://dl.javaeye.com/upload/attachment/353117/e1476bc8-85c2-36ba-9e47-77d34361f097.jpg" alt=""><br> <span style="font-family:Wingdings" lang="EN-US"><span>q<span style="font:7.0pt"> 
</span></span></span><span lang="EN-US">Method Area </span><span>方法区</span></p>
<p><span>方法区是被所有线程共享，该区域保存所有字段和方法字节码，以及一些特殊方法如构造函数，接口代码也在此定义。</span></p>
<p><span style="font-family:Wingdings" lang="EN-US"><span>q<span style="font:7.0pt"> 
</span></span></span><span lang="EN-US">PC Register </span><span>程序计数器</span></p>
<p><span>每个线程都有一个程序计数器，就是一个指针，指向方法区中的方法字节码，由执行引擎读取下一条指令。</span></p>
<p><span style="font-family:Wingdings" lang="EN-US"><span>q<span style="font:7.0pt"> 
</span></span></span><span lang="EN-US">Native Method Stack </span><span>本地方法栈</span></p>
<p> </p>
<p> </p>
<p><span lang="EN-US"> </span></p>
<h2>
<span lang="EN-US">3 JVM</span><span>相关问题</span>
</h2>
<p style="text-indent:21.1pt"><strong><span>问：堆和栈有什么区别</span></strong></p>
<p><span>答：堆是存放对象的，但是对象内的临时变量是存在栈内存中，如例子中的</span><span lang="EN-US">methodVar</span><span>是在运行期存放到栈中的。</span></p>
<p><span>栈是跟随线程的，有线程就有栈，堆是跟随</span><span lang="EN-US">JVM</span><span>的，有</span><span lang="EN-US">JVM</span><span>就有堆内存。</span></p>
<p><span lang="EN-US"> </span></p>
<p style="text-indent:21.1pt"><strong><span>问：堆内存中到底存在着什么东西？</span></strong></p>
<p><span>答：对象，包括对象变量以及对象方法。</span></p>
<p><span lang="EN-US"> </span></p>
<p style="text-indent:21.1pt"><strong><span>问：类变量和实例变量有什么区别？</span></strong></p>
<p><span>答：静态变量是类变量，非静态变量是实例变量，直白的说，有</span><span lang="EN-US">static</span><span>修饰的变量是静态变量，没有</span><span lang="EN-US">static</span><span>修饰的变量是实例变量。静态变量存在方法区中，实例变量存在堆内存中。</span></p>
<p><span lang="EN-US"> </span></p>
<p style="text-indent:21.1pt"><strong><span>问：我听说类变量是在</span><span lang="EN-US">JVM</span></strong><strong><span>启动时就初始化好的，和你这说的不同呀！</span></strong></p>
<p><span>答：那你是道听途说，信我的，没错。</span></p>
<p><span lang="EN-US"> </span></p>
<p style="text-indent:21.1pt"><strong><span>问：</span><span lang="EN-US">Java</span></strong><strong><span>的方法（函数）到底是传值还是传址？</span></strong></p>
<p><span>答：都不是，是以传值的方式传递地址，具体的说原生数据类型传递的值，引用类型传递的地址。对于原始数据类型，</span><span lang="EN-US">JVM</span><span>的处理方法是从</span><span lang="EN-US">Method Area</span><span>或</span><span lang="EN-US">Heap</span><span>中拷贝到</span><span lang="EN-US">Stack</span><span>，然后运行</span><span lang="EN-US">frame</span><span>中的方法，运行完毕后再把变量指拷贝回去。</span></p>
<p><span lang="EN-US"> </span></p>
<p style="text-indent:21.1pt"><strong><span>问：为什么会产生</span><span lang="EN-US">OutOfMemory</span></strong><strong><span>产生？</span></strong></p>
<p><span>答：一句话：</span><span lang="EN-US">Heap</span><span>内存中没有足够的可用内存了。这句话要好好理解，不是说</span><span lang="EN-US">Heap</span><span>没有内存了，是说新申请内存的对象大于</span><span lang="EN-US">Heap</span><span>空闲内存，比如现在</span><span lang="EN-US">Heap</span><span>还空闲</span><span lang="EN-US">1M</span><span>，但是新申请的内存需要</span><span lang="EN-US">1.1M</span><span>，于是就会报</span><span lang="EN-US">OutOfMemory</span><span>了，可能以后的对象申请的内存都只要</span><span lang="EN-US">0.9M</span><span>，于是就只出现一次</span><span lang="EN-US">OutOfMemory</span><span>，</span><span lang="EN-US">GC</span><span>也正常了，看起来像偶发事件，就是这么回事。</span><span lang="EN-US"><span>       </span></span><span>但如果此时</span><span lang="EN-US">GC</span><span>没有回收就会产生挂起情况，系统不响应了。</span></p>
<p><span lang="EN-US"> </span></p>
<p style="text-indent:21.1pt"><strong><span>问：我产生的对象不多呀，为什么还会产生</span><span lang="EN-US">OutOfMemory</span></strong><strong><span>？</span></strong></p>
<p><span>答：你继承层次忒多了，</span><span lang="EN-US">Heap</span><span>中</span>
<span>产生的对象是先产生</span>
<span>父类，然后才产生子类，明白不？</span></p>
<p><span lang="EN-US"> </span></p>
<p style="text-indent:21.1pt"><strong><span>问：</span><span lang="EN-US">OutOfMemory</span></strong><strong><span>错误分几种？</span></strong></p>
<p><span>答：分两种，分别是“</span><span lang="EN-US">OutOfMemoryError:java heap size</span><span lang="EN-US">”</span><span>和<span lang="EN-US">”</span></span><span lang="EN-US">OutOfMemoryError: PermGen space</span><span lang="EN-US">”</span><span>，两种都是内存溢出，</span><span lang="EN-US">heap size</span><span>是说申请不到新的内存了，这个很常见，检查应用或调整堆内存大小。</span></p>
<p><span>“</span><span lang="EN-US">PermGen space</span><span lang="EN-US">”</span><span>是因为永久存储区满了，这个也很常见，一般在热发布的环境中出现，是因为每次发布应用系统都不重启，久而久之永久存储区中的死对象太多导致新对象无法申请内存，一般重新启动一下即可。</span></p>
<p><span lang="EN-US"> </span></p>
<p style="text-indent:21.1pt"><strong><span>问：为什么会产生</span><span lang="EN-US">StackOverflowError</span></strong><strong><span>？</span></strong></p>
<p><span>答：因为一个线程把</span><span lang="EN-US">Stack</span><span>内存全部耗尽了，一般是递归函数造成的。</span></p>
<p><span lang="EN-US"> </span></p>
<p style="text-indent:21.1pt"><strong><span>问：一个机器上可以看多个</span><span lang="EN-US">JVM</span></strong><strong><span>吗？</span><span lang="EN-US">JVM</span></strong><strong><span>之间可以互访吗？</span></strong></p>
<p><span>答：可以多个</span><span lang="EN-US">JVM</span><span>，只要机器承受得了。</span><span lang="EN-US">JVM</span><span>之间是不可以互访，你不能在</span><span lang="EN-US">A-JVM</span><span>中访问</span><span lang="EN-US">B-JVM</span><span>的</span><span lang="EN-US">Heap</span><span>内存，这是不可能的。在以前老版本的</span><span lang="EN-US">JVM</span><span>中，会出现</span><span lang="EN-US">A-JVM Crack</span><span>后影响到</span><span lang="EN-US">B-JVM</span><span>，现在版本非常少见。</span></p>
<p><span lang="EN-US"> </span></p>
<p style="text-indent:21.1pt"><strong><span>问：为什么</span><span lang="EN-US">Java</span></strong><strong><span>要采用垃圾回收机制，而不采用</span><span lang="EN-US">C/C++</span></strong><strong><span>的显式内存管理？</span></strong></p>
<p><span>答：为了简单，内存管理不是每个程序员都能折腾好的。</span></p>
<p><span lang="EN-US"> </span></p>
<p style="text-indent:21.1pt"><strong><span>问：为什么你没有详细介绍垃圾回收机制？</span></strong></p>
<p><span>答：垃圾回收机制每个</span><span lang="EN-US">JVM</span><span>都不同，</span><span lang="EN-US">JVM Specification</span><span>只是定义了要自动释放内存，也就是说它只定义了垃圾回收的抽象方法，具体怎么实现各个厂商都不同，算法各异，这东西实在没必要深入。</span></p>
<p><span lang="EN-US"> </span></p>
<p style="text-indent:21.1pt"><strong><span>问：</span><span lang="EN-US">JVM</span></strong><strong><span>中到底哪些区域是共享的？哪些是私有的？</span></strong></p>
<p><span>答：</span><span lang="EN-US">Heap</span><span>和</span><span lang="EN-US">Method Area</span><span>是共享的，其他都是私有的，</span></p>
<p><span lang="EN-US"> </span></p>
<p style="text-indent:21.1pt"><strong><span>问：什么是</span><span lang="EN-US">JIT</span></strong><strong><span>，你怎么没说？</span></strong></p>
<p><span>答：</span><span lang="EN-US">JIT</span><span>是指</span><span lang="EN-US">Just In Time</span><span>，有的文档把</span><span lang="EN-US">JIT</span><span>作为</span><span lang="EN-US">JVM</span><span>的一个部件来介绍，有的是作为执行引擎的一部分来介绍，这都能理解。</span><span lang="EN-US">Java</span><span>刚诞生的时候是一个解释性语言，别嘘，即使编译成了字节码（</span><span lang="EN-US">byte code</span><span>）也是针对</span><span lang="EN-US">JVM</span><span>的，它需要再次翻译成原生代码</span><span lang="EN-US">(native code)</span><span>才能被机器执行，于是效率的担忧就提出来了。</span><span lang="EN-US">Sun</span><span>为了解决该问题提出了一套新的机制，好，你想编译成原生代码，没问题，我在</span><span lang="EN-US">JVM</span><span>上提供一个工具，把字节码编译成原生码，下次你来访问的时候直接访问原生码就成了，于是</span><span lang="EN-US">JIT</span><span>就诞生了，就这么回事。</span></p>
<p><span lang="EN-US"> </span></p>
<p style="text-indent:21.1pt"><strong><span>问：</span><span lang="EN-US">JVM</span></strong><strong><span>还有哪些部分是你没有提到的？</span></strong></p>
<p><span>答：</span><span lang="EN-US">JVM</span><span>是一个异常复杂的东西，写一本砖头书都不为过，还有几个要说明的：</span></p>
<p><span>常量池（</span><span lang="EN-US">constant pool</span><span>）：按照顺序存放程序中的常量，并且进行索引编号的区域。比如</span><span lang="EN-US">int i
=100</span><span>，这个</span><span lang="EN-US">100</span><span>就放在常量池中。</span></p>
<p><span>安全管理器（</span><span lang="EN-US">Security Manager</span><span>）：提供</span><span lang="EN-US">Java</span><span>运行期的安全控制，防止恶意攻击，比如指定读取文件，写入文件权限，网络访问，创建进程等等，</span><span lang="EN-US">Class Loader</span><span>在</span><span lang="EN-US">Security Manager</span><span>认证通过后才能加载</span><span lang="EN-US">class</span><span>文件的。</span></p>
<p><span>方法索引表（</span><span lang="EN-US">Methods
table</span><span>），记录的是每个</span><span lang="EN-US">method</span><span>的地址信息，</span><span lang="EN-US">Stack</span><span>和</span><span lang="EN-US">Heap</span><span>中的地址指针其实是指向</span><span lang="EN-US">Methods table</span><span>地址。</span></p>
<p style="text-indent:0cm"><span lang="EN-US"><span>       </span></span></p>
<p style="text-indent:21.1pt"><strong><span>问：为什么不建议在程序中显式的生命</span><span lang="EN-US">System.gc()</span></strong><strong><span>？</span></strong></p>
<p><span>答：因为显式声明是做堆内存全扫描，也就是</span><span lang="EN-US">Full GC</span><span>，是需要停止所有的活动的（</span><span lang="EN-US">Stop <span> </span>The World Collection</span><span>），你的应用能承受这个吗？</span></p>
<p><span lang="EN-US"> </span></p>
<p style="text-indent:21.1pt"><strong><span>问：</span><span lang="EN-US">JVM</span></strong><strong><span>有哪些调整参数？</span></strong></p>
<p><span>答：非常多，自己去找，堆内存、栈内存的大小都可以定义，甚至是堆内存的三个部分、新生代的各个比例都能调整。</span></p>
<p> </p>
<p><span style="color:#ff0000"><strong><span style="font-size:x-large"><span style="font-size:x-large"><span style="color:#ff0000"><span>拷贝过来后格式都变了，就附上PDF文件。</span></span></span></span></strong></span></p>
<p><span style="font-size:x-large"><strong><span style="color:#ff0000"><span>欢迎重拍，别手下留情，我喜欢~~~~</span></span></strong></span></p>
          
  <br><br>
  <ul>
    本文附件下载:
    
      <li><a href="http://dl.javaeye.com/topics/download/d10b2741-0a46-3ab2-a6c7-fbfa7723310a">慢慢琢磨jvm.pdf</a> (246.3 KB)</li>
    
  </ul>

          <br><br>
          作者: <a href="http://strong-life-126-com.javaeye.com">倔强的小强</a> 
          <br>
          声明: 本文系JavaEye网站发布的原创文章，未经作者书面许可，严禁任何网站转载本文，否则必将追究法律责任！
          <br><br>
          <span style="color:red">
            <a href="http://www.javaeye.com/topic/821872" style="color:red">已有 <strong>27</strong> 人发表回复，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">JavaEye推荐</span>
<br>
<ul><li><a href="http://www.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>