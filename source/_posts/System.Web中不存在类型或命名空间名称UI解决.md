title: System.Web中不存在类型或命名空间名称UI解决
date: 2012-04-13 16:52:29
id: 177
categories:
  - 学习笔记
tags:
  - 开发
  - 编程
---

今天打算用C#写个模拟登录的程序。从类库里找到一个HTMLHelper的类。用来处理html的请求接收等处理。为了让类能够先运行起来，先把该类拖入解决方案，引用就不用添加了，因为该类没写命名空间，可以直接用，我测试其中一个最简单的函数
<pre lang="C#"> private void btnGet_Click(object sender, EventArgs e)
        {
            txtContent.Text=HTMLHelper.Get_Http("http://leaver.me");
        }</pre>
获取我网站的首页源代码。到TextBox控件
编译，运行。出现如题错误。

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/54e8c1e1dd772294c6666aa9a404276089188b20.png)
=======
[![](/images/54e8c1e1dd772294c6666aa9a404276089188b20.png)
>>>>>>> 换电脑之后重新备份
](http://leaverimage.b0.upaiyun.com/20479_o.png)
命名空间“System.Web”中不存在类型或命名空间名称“UI”。是否缺少程序集引用? E:\project\C#\GTest\GTest\HTMLHelper.cs
直接对着错误点右键，复制。然后删掉后面的路径。使关键字包括前面几个就行了，到Google中搜索。大部分人给的说明都是
原因是缺少System.web的引用，只要右键单击项目，添加引用就行了！
那咱就添加呗 对着工程点击引用-&gt;添加引用

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/a0a47e17fbab5a4472ba94d77e51f8d372c64121.png)](http://leaverimage.b0.upaiyun.com/20480_o.png)
悲剧发生了。。
[![]({{BASE_PATH}}/images/64261ab1e4ed043cf5aa5c973c324795405e4201.png)](http://leaverimage.b0.upaiyun.com/20481_o.png)

根本就没有该dll文件。。泪奔了。。于是Google之。。发现没有一个讲清楚的。都是夸夸其谈。。没有任何实质性方案。于是换英语搜。
关键字：Cannot add System.Web.dll reference
[![]({{BASE_PATH}}/images/d2f3cc95979d5c583471580599b36d269791eae4.png)
=======
[![](/images/a0a47e17fbab5a4472ba94d77e51f8d372c64121.png)](http://leaverimage.b0.upaiyun.com/20480_o.png)
悲剧发生了。。
[![](/images/64261ab1e4ed043cf5aa5c973c324795405e4201.png)](http://leaverimage.b0.upaiyun.com/20481_o.png)

根本就没有该dll文件。。泪奔了。。于是Google之。。发现没有一个讲清楚的。都是夸夸其谈。。没有任何实质性方案。于是换英语搜。
关键字：Cannot add System.Web.dll reference
[![](/images/d2f3cc95979d5c583471580599b36d269791eae4.png)
>>>>>>> 换电脑之后重新备份
](http://leaverimage.b0.upaiyun.com/20482_o.png)
第一个是stackoverflow，大名鼎鼎的栈溢出啊。。进去看看。
> For people that cant find "System.Web" at .Net References, this might be the answer:
> 
> "you need to right-click the project -&gt; properties -&gt; then change the "Target framework" which will probably be ".NET Framework 4 Client Profile" to just ".NET Framework 4".
> 
> The answer was given by Tinister (thanks) as a reply to a previuos post. But I thought it was a little bit hidden at his original post. So i put it here to make it easer to be seen. Hope it helps.
大牛的我就不翻译了，很简单。项目-&gt;项目属性-&gt;目标框架 改成.net framework 4
<<<<<<< HEAD
[![]({{BASE_PATH}}/images/457f4431088a7cb6b474f3d36cbc9ec738499ab1.png)](http://leaverimage.b0.upaiyun.com/20483_o.png)

[![]({{BASE_PATH}}/images/a7ba54a6e73b0b536554c04415d6a72d4cd89491.png)](http://leaverimage.b0.upaiyun.com/20484_o.png)

这个步骤完成后，重复前面的添加引用的步骤就会发现已经有了System.Web引用了
[![]({{BASE_PATH}}/images/9379ef1ef5dba7bad0d039ce91ea9174d1610f65.png)](http://leaverimage.b0.upaiyun.com/20487_o.png)
编译运行。一切ok
[![]({{BASE_PATH}}/images/8e5ae5c835f250b1bceca5e8d3a8bda384df2bab.png)](http://leaverimage.b0.upaiyun.com/20485_o.png)
=======
[![](/images/457f4431088a7cb6b474f3d36cbc9ec738499ab1.png)](http://leaverimage.b0.upaiyun.com/20483_o.png)

[![](/images/a7ba54a6e73b0b536554c04415d6a72d4cd89491.png)](http://leaverimage.b0.upaiyun.com/20484_o.png)

这个步骤完成后，重复前面的添加引用的步骤就会发现已经有了System.Web引用了
[![](/images/9379ef1ef5dba7bad0d039ce91ea9174d1610f65.png)](http://leaverimage.b0.upaiyun.com/20487_o.png)
编译运行。一切ok
[![](/images/8e5ae5c835f250b1bceca5e8d3a8bda384df2bab.png)](http://leaverimage.b0.upaiyun.com/20485_o.png)
>>>>>>> 换电脑之后重新备份

如果你遇到了这样的问题也可以试试吧。

转自请注明：[http://leaver.me/archives/177.html](http://leaver.me/archives/177.html)