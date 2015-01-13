title: "实现IEnumerable接口&理解yield关键字"
tags:
  - C＃
  - 大学
  - 学习
  - 开发
  - 翻译
id: 2116
categories:
  - 我的翻译
date: 2012-10-19 07:33:12
---

本文讨论题目的内容。然后讨论IEnumerable接口如何使得foreach语句可以使用。之后会展示如果实现自定义的集合类，该集合类实现了IEnumerable接口。Yield关键字和遍历集合后面也讨论。

**背景**

一使用集合。就发现遍历集合就跟着来了。遍历集合最好的方式是实现迭代器模式-[Understanding and Implementing the Iterator Pattern in C# and C++](http://www.codeproject.com/Articles/362986/Understanding-and-Implementing-the-Iterator-Patter)(这篇文章我过几天翻译一下) ，C#提供foreach来以一种优雅的方式遍历

只要集合实现了IEnumerable 接口就可以用foreach来遍历。

**使用代码**

首先先看一下内置的集合类如何使用foreach来遍历的。ArrayList实现了IEnumerable 接口。我们看一下
<pre class="lang:default decode:true">// 看一下实现了IEnumerable 接口的集合如何遍历
ArrayList list = new ArrayList();

list.Add("1");
list.Add(2);
list.Add("3");
list.Add('4');

foreach (object s in list)
{
    Console.WriteLine(s);
}</pre>
&nbsp;

**遍历泛型集合类**

Arraylist 是一个通用集合类，遍历泛型集合类也可以。因为这些泛型集合类实现了IEnumerable&lt;T&gt;接口，看一下吧。
<pre class="lang:default decode:true">// 遍历实现了IEnumerable&lt;T&gt;接口的泛型类
List&lt;string&gt; listOfStrings = new List&lt;string&gt;();

listOfStrings.Add("one");
listOfStrings.Add("two");
listOfStrings.Add("three");
listOfStrings.Add("four");

foreach (string s in listOfStrings)
{
    Console.WriteLine(s);
}</pre>
&nbsp;

发现了吧。我们自定义的集合类或是泛型集合类应该实现IEnumerable和IEnumerable&lt;T&gt;接口。这样就可以遍历了。

&nbsp;

**理解yield关键字**

在写个实现接口的例子之前，先理解一下yield关键字，yield会记录集合位置。当从一个函数返回一个值的时候，yield可以用。

如下的普通的方法。不论调用多少次，都只会返回一个return
<pre class="lang:default decode:true">static int SimpleReturn()
{
    return 1;
    return 2;
    return 3;
}

static void Main(string[] args)
{
    // 看看
    Console.WriteLine(SimpleReturn());
    Console.WriteLine(SimpleReturn());
    Console.WriteLine(SimpleReturn());
    Console.WriteLine(SimpleReturn());
}</pre>
&nbsp;

原因就是普通的return语句不保留函数的返回状态。每一次都是新的调用。然后返回第一个值。

但是使用下面的语句替换后就不一样。当函数第二次调用的时候。会从上次返回的地方继续调用
<pre class="lang:default decode:true">static IEnumerable&lt;int&gt; YieldReturn()
{
    yield return 1;
    yield return 2;
    yield return 3;
}
static void Main(string[] args)

{
    // 看看yield return的效果
    foreach (int i in YieldReturn())
    {
        Console.WriteLine(i);
    }
}</pre>
显然返回1，2，3，唯一要注意的就是函数需要返回IEnumerable。，然后通过foreach调用。

**在自定义的集合类里实现Ienumerable接口**

现在如果我们在我们的自定义集合里定义一个方法。来迭代所有元素。然后通过使用yield返回。我们就可以成功了。

好。我们定义MyArrayList 类，实现IEnumerable 接口，该接口就会强制我们实现GetEnumerator 函数。这里我们就要使用yield了。
<pre class="lang:default decode:true">class MyArrayList : IEnumerable
{
    object[] m_Items = null;
    int freeIndex = 0;

    public MyArrayList()
    {
        // 对了方便我直接用数组了，其实应该用链表
       m_Items = new object[100];
    }

    public void Add(object item)
    {
        // 考虑添加元素的时候
        m_Items[freeIndex] = item;
        freeIndex++;
    }

    // IEnumerable 函数
    public IEnumerator GetEnumerator()
   {
       foreach (object o in m_Items)
        {
           // 检查是否到了末尾。数组的话。。。没写好
            if(o == null)
            {
                break;
            }

            // 返回当前元素。然后前进一步
            yield return o;
        }
    }
}</pre>
&nbsp;

&nbsp;

之后你就可以用foreach遍历了。
<pre class="lang:default decode:true">static void Main(string[] args)
{
    //看看调用部分
    MyArrayList myList = new MyArrayList();

    myList.Add("1");
    myList.Add(2);
    myList.Add("3");
    myList.Add('4');

    foreach (object s in myList)
    {
        Console.WriteLine(s);
    }
}</pre>
&nbsp;

这个类啊。没写好。也不完整。只要是让你理解。。模拟一下而已。

自定义泛型类里实现Ienumerable&lt;T&gt;接口
<pre class="lang:default decode:true">class MyList&lt;T&gt; : IEnumerable&lt;T&gt;
{
    T[] m_Items = null;
    int freeIndex = 0;

    public MyList()
    {
        // 为了方便。使用数组
        m_Items = new T[100];
    }

    public void Add(T item)
    {
        //添加元素
        m_Items[freeIndex] = item;
        freeIndex++;
    }

    #region IEnumerable&lt;T&gt; Members 

    public IEnumerator&lt;T&gt; GetEnumerator()
    {
        foreach (T t in m_Items)
        {
            //检查是否到了末尾。数组的话。。。没写好
            if (t == null) // 如果T不是一个可空类型。就中断
            {
                break;
            }

            // 返回当前元素，然后前进一步
            yield return t;
        }
    }

    #endregion

     #region IEnumerable Members
    System.Collections.IEnumerator System.Collections.IEnumerable.GetEnumerator()
    {
        // 此处调用泛型版本
        return this.GetEnumerator();
    }

    #endregion
}</pre>
&nbsp;

之后就可以使用foreach了。
<pre class="lang:default decode:true">static void Main(string[] args)
{
    // 使用示例
    MyList&lt;string&gt; myListOfStrings = new MyList&lt;string&gt;();

    myListOfStrings.Add("one");
    myListOfStrings.Add("two");
    myListOfStrings.Add("three");
    myListOfStrings.Add("four");

    foreach (string s in myListOfStrings)
    {
        Console.WriteLine(s);
    }
}</pre>
** 源代码下载**

[downloadicon href=http://pan.baidu.com/share/link?shareid=86013&amp;uk=1493685990]EnumerableDemo.7z[/downloadicon]

原文地址： [A-Beginners-Tutorial-on-Implementing-IEnumerable-I](http://www.codeproject.com/Articles/474678/A-Beginners-Tutorial-on-Implementing-IEnumerable-I)

著作权声明：本文由[http://leaver.me](http://leaver.me/) 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！