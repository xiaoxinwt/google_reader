---
layout: post
title:  "InvokeUI让人耳目一新的桌面程序开发框架，原来Java也可以这样不走寻常路"
date:   2011-07-06 22:25:58
author: 
categories: program
---

## InvokeUI让人耳目一新的桌面程序开发框架，原来Java也可以这样不走寻常路
### by 
### at 2011-07-06 22:25:58
### original <http://www.iteye.com/topic/1111880>

<p> </p>
<p><strong>InvokeUI是一个Java桌面程序开发框架。</strong></p>
<p> </p>
<p> 它将Flex界面API进行Java本地封装，以实现用Java快速构建</p>
<p> 漂亮桌面程序的目的。InvokeUI框架构建于SmartInvoke之上，整个</p>
<p> 系统结构如下：</p>
<p> </p>
<p><br><img src="http://dl.iteye.com/upload/attachment/511026/e18a9079-32a3-3b69-a318-5a6048883e5c.png" alt=""></p>
<p><strong>SmartInvoke:</strong></p>
<p> </p>
<p>  提供Java与Flash互调的功能，它是InvokeUI功能得以实现的核心。</p>
<p>  项目地址：http://smartrcp.org</p>
<p> </p>
<p><strong>Swt :</strong></p>
<p> </p>
<p>SmartInvoke与InvokeUI都是构建在Swt之上的，它是基础。</p>
<p><strong><br></strong></p>
<p><strong>InvokeUI的目标：</strong></p>
<p> </p>
<p> </p>
<ol>
<li>使Java程序员在完全不懂Flex的情况下也可以快速写出漂亮的客户端程序。</li>
<li>支持第三方Flex 库的动态加载，以增强标准flex库的功能。</li>
<li>支持将第三方Flex API自动转换为对应jar库文件，方便java调用。</li>
<li>完全针对swf进行动态调用，避开Flex SDK编译花费的时间，使程序开发更敏捷更愉快</li>
<li>支持类似mxml的界面定义语言，快速构建程序界面</li>
</ol>
<p> </p>
<div><br></div>
<div><strong>演示程序截图：</strong></div>
<div>      </div>
<div>     听起来有点儿玄乎上图上源代码让大家信服<img src="http://www.iteye.com/images/smiles/icon_biggrin.gif" alt="">示例程序运行效果如下图：</div>
<div>     </div>
<p><br><img src="http://dl.iteye.com/upload/attachment/511034/b10d7274-dc3b-36ea-8600-b67e8dcebdbc.png" alt=""></p>
<p> </p>
<p>嘿嘿，看起来有点儿流口水吧，此示例程序的源代码为一个eclipse java项目的压缩包，已上传到附件中。下面来讲解下本示例</p>
<p>程序的结构及代码。</p>
<p>   示例程序的项目名称为DemoIUI，test.IUIDemoTester为项目的入口类，执行他的main方法就可以运行示例程序了。</p>
<p>   swfs目录下为InvokeUI运行时需要载入的swf文件及图片。</p>
<p>   libs目录下为本项目引用的外部库</p>
<p>           flex.jar为flex的Java封装库。</p>
<p>           invokeUI.jar为 InvokeUI框架的核心库，cn.smartinvoke.core.jar为smartinvoke通信库，其他为swt/jface库。</p>
<p>    从这些库中你可以发现InvokeUI是构建在swt与smartinvoke之上的。</p>
<p> </p>
<p> </p>
<p>test.MainShell类为示例程序的主窗口，他继承自IUIShell,当swf载入完毕后，flex就准备好接受Java的调用了，但是现在窗口</p>
<p>还是白白的什么都没有，所以我们在loadComplete方法中添加界面的创建代码，添加一些控件到窗口之上。</p>
<p>代码如下：</p>
<p>   <span style="white-space:pre">	protected void loadComplete() {</span></p>
<pre name="code">		 //设置Application布局
		 app.setStyle("verticalAlign", "middle");
		 /**
		  * 通过调用wm变量的create方法，调用flex创建一个ToggleButton按钮，
		  * wm在这里为WidgetManager类型对象，java是通过他实现对flex的调用
		  * 的
		  */
		 ToggleButton toggleButton=wm.create(ToggleButton.class);
		 toggleButton.setLabel("点击改变程序外观");
		 toggleButton.setWidth(200);
		 toggleButton.setHeight(50);
		 /**
		  * app为mx.core.Application类型对象，熟悉Flex的朋友知道他是一个
		  * 顶层对象，是整个窗口显示控件树的根
		  */
		 app.addChild(toggleButton);
		 
		 //添加click监听器
		 toggleButton.addClickListener(new MouseEventListenerAdapter(){
			 //这里的皮肤swf由flash builder导出
			String path=Environment.getLocation()+"/styles/spark_cobalt.swf";
			public void click(MouseEvent e) {
				 ToggleButton target=(ToggleButton)e.getCurrentTarget();
				 System.out.println(target);
			}
			
		 });
		 
	}</pre>
 
<p>      怎么样，是不是感觉跟swing比较像呢？</p>
<p> </p>
<p>     创建表格：</p>
<p> </p>
<p>               <span style="white-space:pre">//创建表格并设置样式</span></p>
<pre name="code">			final DataGrid dataGrid=wm.create(DataGrid.class);
			dataGrid.setPercentHeight(100);
			dataGrid.setPercentWidth(100);
			dataGrid.setRowHeight(30);
			//设置itemRenderer的meta信息
			List&lt;Object&gt; data=new ArrayList&lt;Object&gt;();
			FlexObject metaObj=new FlexObject();
			metaObj.put(&quot;c2&quot;, DataGridRendererComponent.class.getName());
			//构造数据
			for(int i=0;i&lt;800;i++){
			    Map&lt;String,Object&gt; map=new HashMap&lt;String, Object&gt;();
			    map.put(&quot;c1&quot;, &quot;&quot;+i);
			    map.put(&quot;c2&quot;, &quot;第二列_&quot;+i);
			    map.put(&quot;c3&quot;, &quot;第三列_&quot;+i);
			    map.put(IUIConstants.META_TYPE_PROPERTY_NAME, metaObj);
				data.add(map);
			}
			dataGrid.setDataProvider(data);
			//设置第一列
			DataGridColumn c1=wm.create(DataGridColumn.class);
			c1.setHeaderText(&quot;c1Text&quot;);
			c1.setDataField(&quot;c1&quot;);c1.setWidth(200);
			//设置列的格式化函数
			c1.setLabelFunction(new FlFunction(){
				@Override
				public Object execute(Object[] params) {
					//Object[] pars=(Object[])params[0];
					FlexObject obj=(FlexObject)params[0];
					String idVal=obj.get(&quot;c1&quot;)+&quot;&quot;;
					return &quot;编号:&quot;+idVal;
				}
			});
			//设置第二列
			DataGridColumn c2=wm.create(DataGridColumn.class);
			c2.setHeaderText(&quot;c2Text&quot;);
			c2.setDataField(&quot;c2&quot;);
			//设置表格列的自定义渲染类
			c2.setItemRenderer(new RendererType(DataGridRendererComponent.class));
			//设置第三列
			DataGridColumn c3=wm.create(DataGridColumn.class);
			c3.setHeaderText(&quot;c3Text&quot;);
			c3.setDataField(&quot;c3&quot;);
			dataGrid.setColumns(new Object[]{c1,c2,c3});
			//添加事件
			dataGrid.addCreationCompleteListener(new FlexEventListenerAdapter(){
				@Override
				public void creationComplete(FlexEvent e) {
					   DataGrid grid=(DataGrid)e.getCurrentTarget();
					   System.out.println(grid+&quot;创建完毕...&quot;);
				}
			});
			//添加表格列选择事件
			dataGrid.addChangeListener(new ListEventListenerAdapter(){
				public void change(ListEvent e) {
					 System.out.println(e);
				}
			});</pre>
<p> </p>
<p> </p>
<p>  加载外部API:</p>
<p> </p>
<p>        <span style="white-space:pre">//首先获得样式管理器</span></p>
<pre name="code">		SuperWidget  styleManager=app.getStyleManager();
		//获得外部API的样式文件，这里的样式文件已经编译为swf了，在flex开发中他们是css文件
		final String path=Environment.getLocation()+"/map/styles.swf";
		//调用样式管理器对象的loadStyleDeclarations2方法加载样式，并返回evtDispatch加载事件对象
		SuperWidget  evtDispatch=(SuperWidget)wm.
		callFunction(styleManager.id, "loadStyleDeclarations2", new Object[]{path,true,ApplicationDomain.getCurrentDomain(wm)});
		//样式文件加载过程中的事件监听器对象
		StyleEventListenerAdapter listener=new StyleEventListenerAdapter(){
			public void error(StyleEvent e) {
				System.out.println("样式文件"+path+" 加载出错");
			}
			public void complete(StyleEvent e) {
				System.out.println("样式文件"+path+" 加载完毕");
				//加载外部API中所有类型定义所在的swf，在java中这里就像是加载某个jar库文件
				String url=Environment.getLocation()+"/map/ESRI_API.swf";
			  	SuperWidget moduleInfo=(SuperWidget)ModuleManager.getModule(wm, url);
			  	moduleInfo.addListener(ModuleEvent.READY, new ModuleEventListenerAdapter(){
			    	 @Override
			    	public void ready(ModuleEvent e) {
			    		   //API加载完毕后调用对应的API显示地图
			    		   SuperWidget map=wm.create("com.esri.ags.Map");//创建map地图对象
			    		   SuperWidget layer=wm.create("com.esri.ags.layers.ArcGISTiledMapServiceLayer");//创建一个地图图层
			    		   wm.setProp(layer.id,"url","http://server.arcgisonline.com/ArcGIS/rest/services/World_Street_Map/MapServer" );
			    		   wm.callFunction(map.id, "addLayer",new Object[]{layer});//添加图层
			    		   //将map地图控件添加到app中显示出来
			    		   wm.addChild(app.id, map.id);
			    	}
			    	public void error(ModuleEvent e) {
			    		System.out.println("模块加载错误");
			    	}
			    });
			   //加载模块
			   wm.callFunction(moduleInfo.id, "load", 
					   new Object[]{ApplicationDomain.getCurrentDomain(wm)});
			   
			}
		};
		//添加加载完毕监听器
		evtDispatch.addListener(StyleEvent.COMPLETE, listener );
		//添加加载错误监听器
		evtDispatch.addListener(StyleEvent.ERROR, listener);</pre>
<p> </p>
<p> </p>
<p>    其实InvokeUI的实现原理与swt非常相似，要想知道完整代码请查看附件。</p>
<p> </p>
<p><span style="color:#ff6600">     ps:InvokeUI的下一步开发计划包括：</span></p>
<p><span style="color:#ff6600">               1.继续稳定与完善InvokeUI核心库</span></p>
<p><span style="color:#ff6600">               2.实现用xml构建界面与IDE界面设计器</span></p>
<p><span style="color:#ff6600"><br></span></p>
<p><span style="color:#ff6600">     InvokeUI承诺完全免费使用，目前正处在功能完善阶段，欢迎大家多提宝贵意见</span></p>
<p> </p>
<p>===================网友疑问</p>
<p> </p>
<p>    1.  InvokeUI跨平台吗？</p>
<p> </p>
<p>          InvokeUI肯定是可以跨平台的只要有Java与Flash的地方就可以有他。</p>
<p> </p>
<p>     2.  InvokeUI需要哪些运行环境呢？</p>
<p> </p>
<p>          java运行环境与Flash Player</p>
<p> </p>
<p>     3.  InvokeUI的程序怎样打包成exe呢？</p>
<p>          InvokeUI说到底就是一个Java程序，是以与Java程序的打包发行方式一样。</p>
<p> </p>
<p> </p>
<p><span style="font-size:small">附上QQ讨论群：105221751</span></p>
          
  <br><br>
  <ul>
    本文附件下载:
    
      <li><a href="http://dl.iteye.com/topics/download/2047a1e4-1098-3569-8918-472e85d1d86c">DemoIUI.rar</a> (8.8 MB)</li>
    
      <li><a href="http://dl.iteye.com/topics/download/b640ec43-aee9-364b-a49d-7da3cf2aa457">DemoIUI可运行程序.rar</a> (6.4 MB)</li>
    
  </ul>

          <br><br>
          作者: <a href="http://smartinvoke.iteye.com">smartinvoke</a> 
          <br>
          声明: 本文系ITeye网站发布的原创文章，未经作者书面许可，严禁任何网站转载本文，否则必将追究法律责任！
          <br><br>
          <span style="color:red">
            <a href="http://www.iteye.com/topic/1111880" style="color:red">已有 <strong>52</strong> 人发表回复，猛击-&gt;&gt;<strong>这里</strong>&lt;&lt;-参与讨论</a>
          </span>
          <br><br><br>
<span style="color:#e28822">ITeye推荐</span>
<br>
<ul><li><a href="http://www.iteye.com/clicks/433"><span style="color:red;font-weight:bold">—软件人才免语言低担保 赴美带薪读研！— </span></a></li></ul>
<br><br><br>