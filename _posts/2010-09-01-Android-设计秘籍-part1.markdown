---
layout: post
title:  "Android 设计秘籍 part1"
date:   2010-09-01 11:25:37
author: 
categories: program
---

## Android 设计秘籍 part1
### by 
### at 2010-09-01 11:25:37
### original <http://www.javaeye.com/topic/752506>

<p>1. 五不要和九要<br>
    1. 不要照搬其他平台上的UI设计。应该让用户感觉是在使用一个真正的 Android 软件，在你的品牌显示和平台整体观感之间做好平衡。<br>
    2. 不要过度使用模态进度条和确认对话框。<br>
    3. 不要使用固定的绝对定位布局。<br>
    4. 不要使用px单位，使用dp；如果是文本，使用sp。<br>
    5. 不要使用太小的字体。 <br><br>
    1. 所有资源都要针对高分辨率屏幕创建（缩小总比放大好）<br>
    2. 需要点击的元素要够大<br>
    3. 图标设计要遵循 Android 的准则<br>
    4. 要使用适当的间距（margins, padding）<br>
    5. 要支持D-pad和trackball导航<br>
    6. 要正确管理活动（activity）栈<br>
    7. 要正确处理屏幕方向变化<br>
    8. 要使用主题/样式、尺寸和颜色资源来减少界面冗余<br>
    9. 要和视觉与交互设计师合作!!!<br><br>
2. 设计理念和考虑因素<br>
    设计理念<br>
        1. 干净而不过于简单<br>
        2. 关注内容而非修饰<br>
        3. 一致，吸引人，少量简洁的变化<br>
        4. 使用云端服务（同时在PC桌面和手机上同步用户使用场景）来加强用户体验<br><br>
    优秀界面的设计准则<br>
        1. 关注用户<br>
            了解你的用户（年龄，技能，文化，是否有身体障碍，对应用的需求，使用的设备，何时何地如何使用设备）<br>
            “用户优先”的设计心态 （用户通常是任务导向的）<br>
            更早、更频繁的由真实用户来测试<br>
        2. 显示正确的内容<br>
            最常用的操作用户应该立即可以看到并且使用<br>
            次要功能可以放到菜单里面<br>
        3. 给予用户适当的回馈<br>
            交互式的UI元素最少需要反映出4种不同的状态 （default，disabled，focused，pressed）<br>
            保证操作的结果是清晰可见的<br>
            多给予用户进度提示，但是不要干扰他们当前的操作<br>
        4. 有章可循的行为模式<br>
            行为模式遵循用户的期望（正确的操作活动栈，显示用户期望看到的信息和动作）<br>
            使用合适的方式来加强功能可见性（可点击的元素就应该看起来是可以点击的）<br>
            如果用户完成一项任务需要复杂的操作，重新思考你的设计!!!<br>
        5. 容忍错误<br>
            只允许有意义的操作（适当禁用一些UI元素）<br>
            尽量减少不可回退的操作<br>
            允许回退（undo）比使用确定对话框更好（实际上，应该尽量少用确定对话框，它对用户是一种干扰<br><br>
设计上的考虑因素<br>
1.  屏幕的物理尺寸<br>
2.  屏幕密度<br>
3.  屏幕的方向（竖向和横向）<br>
4.  主要的UI交互方式（触屏还是使用D-pad/trackball）<br>
5.  软键盘还是物理键盘<br>
6.  了解不同设备之间的相异之处是非常重要的!<br>
7.  阅读CDD（兼容性定义文档），了解设备可能的差异<br>
8.  了解屏幕尺寸和密度分类（网络资料）</p>
<p> </p>
<p>移动开发，设计是关键。收藏一下，以备今后设计之用。</p>
<p> </p>
<p>转载于：<a href="http://mobile.csdn.net/n/20100901/278894.html">http://mobile.csdn.net/n/20100901/278894.html</a></p>
          
          <br><br>
          作者: <a href="http://yuanyao.javaeye.com">yuanyao</a> 
          <br>
          声明: 本文系JavaEye网站发布的原创文章，未经作者书面许可，严禁任何网站转载本文，否则必将追究法律责任！
          <br><br>
          <span style="color:red">
            <a href="http://www.javaeye.com/topic/752506" style="color:red">已有 <strong>0</strong> 人发表回复，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">JavaEye推荐</span>
<br>
<ul><li><a href="http://www.iteye.com/clicks/138"><span style="color:red;font-weight:bold">上海：高薪诚聘Python开发人员</span></a></li><li><a href="http://www.iteye.com/clicks/269"><span style="color:red;font-weight:bold">北京：手机之家网站诚聘PHP程序员</span></a></li></ul>
<br><br><br>