title: "[藏]Class.getResource和ClassLoader.getResource不同点"
tags:
  - java
  - 学习
  - 工作
  - 收藏
id: 3371
categories:
  - 文章收藏
date: 2013-12-14 18:18:53
---

有一次遇到了，查了查。[原文地址](http://www.cnblogs.com/yejg1212/p/3270152.html)

Java中取资源时，经常用到Class.getResource和ClassLoader.getResource，这里来看看他们在取资源文件时候的路径问题。

## Class.getResource(String path)

<div>
<pre>path不以’/'开头时，默认是从此类所在的包下取资源；
path  以’/'开头时，则是从ClassPath根下获取；</pre>
</div>
什么意思呢？看下面这段代码的输出结果就明白了：
<div>
<pre>package testpackage;
public class TestMain {
    public static void main(String[] args) {
        System.out.println(TestMain.class.getResource(""));
        System.out.println(TestMain.class.getResource("/"));
    }
}</pre>
</div>
输出结果：
<div>
<pre>file:/E:/workspace/Test/bin/testpackage/
file:/E:/workspace/Test/bin/</pre>
</div>
上面说到的【path以’/'开头时，则是从ClassPath根下获取；】在这里就是相当于bin目录(Eclipse环境下)。

再来一个实例，假设有如下Project结构：

<<<<<<< HEAD
[![o_PackageStructure1]({{BASE_PATH}}/images/94eb328bce2cf9ff32430cc4075af46091202a22.png)](http://leaverimage.b0.upaiyun.com/2013/12/o_PackageStructure1.png)
=======
[![o_PackageStructure1](/images/94eb328bce2cf9ff32430cc4075af46091202a22.png)](http://leaverimage.b0.upaiyun.com/2013/12/o_PackageStructure1.png)
>>>>>>> 换电脑之后重新备份

如果我们想在TestMain.java中分别取到1~3.properties文件，该怎么写路径呢？代码如下：
<pre class="lang:default decode:true">package testpackage;

public class TestMain {

    public static void main(String[] args) {
        // 当前类(class)所在的包目录
        System.out.println(TestMain.class.getResource(""));
        // class path根目录
        System.out.println(TestMain.class.getResource("/"));

        // TestMain.class在&lt;bin&gt;/testpackage包中
        // 2.properties  在&lt;bin&gt;/testpackage包中
        System.out.println(TestMain.class.getResource("2.properties"));

        // TestMain.class在&lt;bin&gt;/testpackage包中
        // 3.properties  在&lt;bin&gt;/testpackage.subpackage包中
        System.out.println(TestMain.class.getResource("subpackage/3.properties"));

        // TestMain.class在&lt;bin&gt;/testpackage包中
        // 1.properties  在bin目录（class根目录）
        System.out.println(TestMain.class.getResource("/1.properties"));
    }
}</pre>
&nbsp;

※Class.getResource和Class.getResourceAsStream在使用时，路径选择上是一样的。

&nbsp;

&nbsp;

## Class.getClassLoader（）.getResource(String path)

<div>
<pre>path不能以’/'开头时；
path是从ClassPath根下获取；</pre>
</div>
还是先看一下下面这段代码的输出：
<div>
<pre>package testpackage;
public class TestMain {
    public static void main(String[] args) {
        TestMain t = new TestMain();
        System.out.println(t.getClass());
        System.out.println(t.getClass().getClassLoader());
        System.out.println(t.getClass().getClassLoader().getResource(""));
        System.out.println(t.getClass().getClassLoader().getResource("/"));//null
    }
}</pre>
</div>
输出结果：
<div>
<pre>class testpackage.TestMain
sun.misc.Launcher$AppClassLoader@1fb8ee3
file:/E:/workspace/Test/bin/
null</pre>
</div>
从结果来看【TestMain.class.getResource("/") == t.getClass().getClassLoader().getResource("")】

如果有同样的Project结构

<<<<<<< HEAD
[![o_PackageStructure]({{BASE_PATH}}/images/46e184c9601d96bbb9fbb19e475c7909c85fe52c.png)](http://leaverimage.b0.upaiyun.com/2013/12/o_PackageStructure.png)
=======
[![o_PackageStructure](/images/46e184c9601d96bbb9fbb19e475c7909c85fe52c.png)](http://leaverimage.b0.upaiyun.com/2013/12/o_PackageStructure.png)
>>>>>>> 换电脑之后重新备份

使用Class.getClassLoader（）.getResource(String path)可以这么写：
<div>
<pre>package testpackage;

public class TestMain {
    public static void main(String[] args) {
        TestMain t = new TestMain();
        System.out.println(t.getClass().getClassLoader().getResource(""));

        System.out.println(t.getClass().getClassLoader().getResource("1.properties"));
        System.out.println(t.getClass().getClassLoader().getResource("testpackage/2.properties"));
        System.out.println(t.getClass().getClassLoader().getResource("testpackage/subpackage/3.properties"));
    }
}</pre>
</div>
&nbsp;

※Class.getClassLoader（）.getResource和Class.getClassLoader（）.getResourceAsStream在使用时，路径选择上也是一样的。