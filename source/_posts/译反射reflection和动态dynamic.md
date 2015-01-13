title: "[译]反射(Reflection)和动态(dynamic)"
tags:
  - C＃
  - 大学
  - 学习
  - 翻译
  - 软件
id: 2995
categories:
  - 我的翻译
date: 2013-05-27 08:47:34
---

# 反射

&nbsp;
<div>当我们需要检查，调用一个程序集的内容的时候，用反射，比如，当VS给智能提示的时候，就应用了反射。</div>
<div></div>
<div></div>

## 简单用法实例：

&nbsp;
<div>
<pre class="lang:default decode:true">var myAssembly = Assembly.LoadFile(@"C:\ClassLibrary1.dll");
var myType = myAssembly.GetType("ClassLibrary1.Class1");
dynamic objMyClass = Activator.CreateInstance(myType);
// 获取类的类型信息
Type parameterType = objMyClass.GetType();

// 浏览方法
foreach (MemberInfo objMemberInfo in parameterType.GetMembers())
{Console.WriteLine(objMemberInfo.Name);}

// 浏览属性.
foreach (PropertyInfo objPropertyInfo in parameterType.GetProperties())
{Console.WriteLine(objPropertyInfo.Name);}

//开始调用
parameterType.InvokeMember("Display",BindingFlags.Public | 
BindingFlags.NonPublic | BindingFlags.InvokeMethod | 
BindingFlags.Instance,null, objMyClass, null);</pre>
</div>
<div></div>
<div></div>

## 实际一点的用处呢：

&nbsp;
<div>1.当你也要开发一个类似VS的编辑器的时候，要提供智能提示就需要反射</div>
<div>2.当创建单元测试框架的时候，为了测试需要动态调用方法和属性的时候</div>
<div>3.有时候我们想把类型的属性，方法等全部导出的时候</div>
<div></div>
<div></div>

# 动态dynamic

&nbsp;
<div>编程语言分为强/弱类型，dynamic是弱类型，此关键字会让编译器不做编译时的类型检查，只做运行时的检查。</div>
<div></div>
<div></div>

## 简单用法示例：

&nbsp;
<div>
<pre lang="cs">dynamic x = "c#";
x++;</pre>
</div>
<div>所以上面这行代码可以编译通过，但会产生运行时一场。</div>
<div></div>
<div></div>

## 实际用处：

&nbsp;
<div>最多的就是通过互操作来操作Office组件的时候了</div>
<div>没有dynamic的时候</div>
<div>
<pre lang="cs">/ Before the introduction of dynamic.
Application excelApplication = new  Application();
((Excel.Range)excelApp.Cells[1, 1]).Value2 = "Name";
Excel.Range range2008 = (Excel.Range)excelApp.Cells[1, 1];</pre>
</div>
<div>有了dynamic之后世界就不一样了</div>
<div>
<pre lang="cs">dynamic excelApp = new Application();
excelApp.Cells[1, 1].Value = "Name";
Excel.Range range2010 = excelApp.Cells[1, 1];</pre>
</div>
<div></div>

# 两者的区别和联系呢

<div></div>
<div></div>
<div>1.当我们想要在运行时操作一个对象的时候，就会用到这两个</div>
<div>2.反射可以用来检测对象的元数据，也能在运行时调用对象的方法和属性</div>
<div>3.dynamic是.net 4.0新出的关键字，知道方法被调用的时候，编译器才会晓得这个方法到底有还是没有。</div>
<div>4.dynamic内部是使用反射来实现的，它缓存了方法调用来提高性能</div>
<div>5.反射可以调用公有和私有成员，而dynamic智能调用用公有成员</div>
<div>6.dynamic是实例相关的，无法访问静态成员，这种情况下使用反射吧。</div>
<div></div>
<div></div>
<div>
<table>
<tbody>
<tr>
<td width="163"></td>
<td width="68">**Reflection**</td>
<td>**Dynamic**</td>
</tr>
<tr>
<td width="163">**Inspect (meta-data) **</td>
<td width="68">Yes</td>
<td>No</td>
</tr>
<tr>
<td width="163">**Invoke public members**</td>
<td width="68">Yes</td>
<td>Yes</td>
</tr>
<tr>
<td width="163">**Invoke private members**</td>
<td width="68">Yes</td>
<td>No</td>
</tr>
<tr>
<td width="163">**Caching**</td>
<td width="68">No</td>
<td>Yes</td>
</tr>
<tr>
<td width="163">**Static class **</td>
<td width="68">Yes</td>
<td>No</td>
</tr>
</tbody>
</table>
再来一张图...
[![]({{BASE_PATH}}/images/d0e1ec824f1199a847d9a16237673a4a299e0dce.jpg)](http://leaverimage.b0.upaiyun.com/36512_o.jpg)

</div>
<div></div>
<div>译自：[http://www.codeproject.com/Articles/593881/What-is-the-difference-between?utm_source=feedly](http://www.codeproject.com/Articles/593881/What-is-the-difference-between?utm_source=feedly)</div>
<div></div>