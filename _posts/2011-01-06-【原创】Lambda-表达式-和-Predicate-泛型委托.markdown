---
layout: post
title:  "【原创】Lambda 表达式 和 Predicate 泛型委托"
date:   2011-01-06 14:36:00
author: 田志良
categories: program
---

## 【原创】Lambda 表达式 和 Predicate 泛型委托
### by 田志良
### at 2011-01-06 14:36:00
### original <http://www.cnblogs.com/tianzhiliang/archive/2011/01/06/1927691.html>

<p><div><strong>1. Lambda表达式</strong></div>
<div>　　“Lambda” 表达式”是一个匿名函数，用于创建委托或表达式树类型。</div>
<div>　　“=&gt;”为Lambda 运算符，读为“goes to”。Lambda 运算符左边是“输入参数”，右边是“表达式或语句块”，如下所示：</div>
<div>
<div>
<pre>delegate int del(int i);
static void Main(string[] args)
{
    del myDelegate = x =&gt; x * x;
    int j = myDelegate(5); //j = 25
}
</pre>
</div>
</div>
<div>
<div>　　使用“Lambda 表达式”请注意一下几点：</div>
<div>　　1）=&gt; 运算符具有与赋值运算符 (=) 相同的优先级，并且是右结合运算符。</div>
<div>　　2）在 is 或 as 运算符的左侧不允许使用 Lambda。</div>
<div>　　3）适用于匿名方法的所有限制也适用于 Lambda 表达式，如：Lambda 语句无法用于创建表达式树。</div>
<div>　　4）只有在 Lambda 有一个输入参数时，括号才是可选的，否则括号是必需的，两个或更多输入参数由括在括号中的逗号分隔。</div>
<div>　　5）有时，编译器难于或无法推断输入类型，如果出现这种情况，显式指定类型：</div>
</div>
<div>
<div>
<pre>(int x, string s) =&gt; s.Length &gt; x
</pre>
</div>
</div>
<div>
6）使用空括号指定零个输入参数：</div>
<div>
<div>
<pre>() =&gt; SomeMethod()
</pre>
</div>
</div>
<div>
7）Lambda 语句的主体可以包含任意数量的语句，但在实际运用中，不会多于两个或三个语句。</div>
<div>
<div>
<pre>delegate void TestDelegate(string s);
…
TestDelegate myDel = n =&gt; { string s = n + &quot; &quot; + &quot;World&quot;; Console.WriteLine(s); };
myDel(&quot;Hello&quot;);</pre>
</div>
</div>
<div>
8）在编写 Lambda 时，通常不必为输入参数指定类型，因为编译器可以根据 Lambda 主体、基础委托类型以及 C# 语言规范中描述的其他因素推断类型。Lambda 表达式本身没有类型，但是，有时会不正式地论及 Lambda 表达式的“类型”，在这些情况下，类型是指委托类型或 Lambda 表达式所转换为的 Expression 类型。Lambda 的一般规则如下：</div>
<div>
<ul>
<li>Lambda 包含的参数数量必须与委托类型包含的参数数量相同。</li>
<li>Lambda 中的每个输入参数必须都能够隐式转换为其对应的委托参数。</li>
<li>Lambda 的返回值（如果有）必须能够隐式转换为委托的返回类型。</li>
</ul>
<div>　　9）Lambda 可以引用“外部变量”，这些变量位于在其中定义 Lambda 的封闭方法或类型的范围内。 将会存储通过这种方法捕获的变量以供在 Lambda 表达式中使用，即使变量将以其他方式超出范围或被作为垃圾回收。 必须明确地分配外部变量，然后才能在 Lambda 表达式中使用该变量。如下例所示：</div>
</div>
<div>
<div>
<pre>using System;

delegate bool D();
delegate bool D2(int i);

class Test
{
    D del;
    D2 del2;
    public void TestMethod(int input)
    {
        int j = 0;

        // del在方法体内被调用
        del = () =&gt; { j = 10; return j &gt; input; };

        // del2在外部调用
        del2 = (x) =&gt; { return x == j; };

        // 输出: j = 0 
        Console.WriteLine(&quot;j = {0}&quot;, j);

        // 调用委托
        bool boolResult = del();

        // 输出: j = 10 b = True
        Console.WriteLine(&quot;j = {0}.b = {1}&quot;, j, boolResult);
    }

    static void Main()
    {
        Test test = new Test();
        test.TestMethod(5);

        // del2 仍然保存TestMethod方法的局部变量j
        bool result = test.del2(10);

        // 输出: True
        Console.WriteLine(result);

        Console.ReadKey();
    }
}
</pre>
</div>
</div>
<div>
10）下列规则适用于 Lambda 表达式中的变量范围：</div>
<div>
<ul>
<li>捕获的变量将不会被作为垃圾回收，直至引用变量的委托超出范围为止。</li>
<li>在外部方法中看不到 Lambda 表达式内引入的变量。</li>
<li>Lambda 表达式无法从封闭方法中直接捕获 ref 或 out 参数。</li>
<li>Lambda 表达式中的返回语句不会导致封闭方法返回。</li>
<li>Lambda 表达式不能包含其目标位于所包含匿名函数主体外部或内部的 goto 语句、break 语句或 continue 语句。</li>
</ul>
<div><strong>2. Predicate 泛型委托</strong></div>
</div>
<div>　　“Predicate 泛型委托”表示定义一组条件并确定指定对象是否符合这些条件的方法，其本质为一委托，在该委托中定义一组条件，用以判断传入对象是否符合这些条件，如果符合，返回True，如果不符合，返回false。</div>
<div>　　语法：</div>
<div>
<div>
<pre>public delegate bool Predicate&lt;T&gt; (
	T obj
)</pre>
</div>
</div>
<div>
类型参数说明：</div>
<div>
<ul>
<li>T：要比较的对象的类型。</li>
<li>obj：要按照由此委托表示的方法中定义的条件进行比较的对象。</li>
<li>返回值：如果 obj 符合由此委托表示的方法中定义的条件，则为 true；否则为 false。</li>
</ul>
<div>　　此委托由 Array 和 List 类的几种方法使用，用于在集合中搜索元素：</div>
</div>
<div>　　Array ： public T[] FindAll&lt;T&gt;(T[] array, Predicate&lt;T&gt; match)；</div>
<div>　　List：public List&lt;T&gt; FindAll(Predicate&lt;T&gt; match)；</div>
<div>　　下面给出两个例子，第一个例子演示“Predicate 泛型委托”在Array、List集合中的应用，第二个例子演示“Predicate 泛型委托”的拓展应用。</div>
<div>　　例子一：</div>
<div>
<div>
<pre>    public class GenericDelegateDemo
    {
        List&lt;String&gt; listString = new List&lt;String&gt;()
        {
            &quot;One&quot;,&quot;Two&quot;,&quot;Three&quot;,&quot;Four&quot;,&quot;Fice&quot;,&quot;Six&quot;,&quot;Seven&quot;,&quot;Eight&quot;,&quot;Nine&quot;,&quot;Ten&quot;
        };

        String[] arrayString = new String[] 
        {
             &quot;One&quot;,&quot;Two&quot;,&quot;Three&quot;,&quot;Four&quot;,&quot;Fice&quot;,&quot;Six&quot;,&quot;Seven&quot;,&quot;Eight&quot;,&quot;Nine&quot;,&quot;Ten&quot;
        };

        public String[] GetFirstStringFromArray()
        {
            return Array.FindAll(arrayString, (c) =&gt; { return c.Length &lt;= 3; });
        }

        public List&lt;String&gt; GetFirstStringFromList()
        {
            return listString.FindAll((c) =&gt; { return c.Length &lt;= 3; });
        }

        public String[] GetFirstStringFromArray_1()
        {
            return Array.FindAll(arrayString, GetString);
        }

        public List&lt;String&gt; GetFirstStringFromList_1()
        {
            return listString.FindAll(GetString);
        }

        private bool GetString(String str)
        {
            if (str.Length &lt;= 3)
                return true;
            else
                return false;
        }
    }
</pre>
</div>
</div>
<div>
　　例子二：</div>
<div>
<div>
<pre>    public class GenericDelegateDemo
    {
        List&lt;String&gt; listString = new List&lt;String&gt;() { &quot;One&quot;, &quot;Two&quot;, &quot;Three&quot;, &quot;Four&quot;, &quot;Fice&quot;, &quot;Six&quot;, &quot;Seven&quot;, &quot;Eight&quot;, &quot;Nine&quot;, &quot;Ten&quot; };
        
        public String GetStringList(Predicate&lt;String&gt; p)
        {
            foreach (string item in listString)
            {
                if (p(item))
                    return item;
            }
            return null;
        }

        public bool ExistString()
        {
            string str = GetStringList(a =&gt; { return a.Length &lt;= 3 &amp;&amp; a.Contains(&#39;S&#39;); });
            if (str == null)
                return false;
            else
                return true;
        }
    }
</pre>
</div>
</div><img src="http://www.cnblogs.com/tianzhiliang/aggbug/1927691.html?type=1" width="1" height="1" alt=""><p>作者: <a href="http://www.cnblogs.com/tianzhiliang/">田志良</a> 发表于 2011-01-06 14:36 <a href="http://www.cnblogs.com/tianzhiliang/archive/2011/01/06/1927691.html">原文链接</a></p><p>评论: 6　<a href="http://www.cnblogs.com/tianzhiliang/archive/2011/01/06/1927691.html#pagedcomment">查看评论</a>　<a href="http://www.cnblogs.com/tianzhiliang/archive/2011/01/06/1927691.html#commentform">发表评论</a></p><hr><p>最新新闻：<br>· <a href="http://news.cnblogs.com/n/87481/">微软三星联合展示新一代Surface触摸式计算机</a><span style="color:gray">(2011-01-06 23:03)</span><br>· <a href="http://news.cnblogs.com/n/87483/">2011年Google、Apple十大交锋预测</a><span style="color:gray">(2011-01-06 23:01)</span><br>· <a href="http://news.cnblogs.com/n/87480/">分析：Flipboard 价值何在？</a><span style="color:gray">(2011-01-06 22:48)</span><br>· <a href="http://news.cnblogs.com/n/87479/">Skype联合创始人称下一个谷歌或诞生在中国</a><span style="color:gray">(2011-01-06 22:37)</span><br>· <a href="http://news.cnblogs.com/n/87478/">摩托罗拉推出3G版Xoom平板电脑 随后将推4G版</a><span style="color:gray">(2011-01-06 22:36)</span><br></p><p>编辑推荐：<a href="http://www.cnblogs.com/cmt/archive/2011/01/06/1927547.html">博客园电子期刊2010年12月刊发布</a><br></p><p>网站导航：<a href="http://www.cnblogs.com">博客园首页</a>  <a href="http://home.cnblogs.com/">我的园子</a>  <a href="http://news.cnblogs.com">新闻</a>  <a href="http://home.cnblogs.com/ing/">闪存</a>  <a href="http://home.cnblogs.com/group/">小组</a>  <a href="http://space.cnblogs.com/q/">博问</a>  <a href="http://kb.cnblogs.com">知识库</a></p></p>