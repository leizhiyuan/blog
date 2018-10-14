---
title: "《Effective C#》Item 9：区别和认识四个判等函数"
tags:
  - C＃
  - 开发
  - 收藏
  - 设计
id: 1139
categories:
  - 文章收藏
date: 2012-07-05 22:56:00
---

　　.Net有四个判等函数？不少人看到这个标题，会对此感到怀疑。事实上确是如此，.Net提供了ReferenceEquals、静态Equals，具体类型的Equals以及==操作符这四个判等函数。但是这四个函数之间有细微的关系，改变其中一个函数的实现会影响到其他函数的操作结果。

　　首先要说的是Object.ReferenceEquals和Object.Equals这两个静态函数，对于它们俩来说，是不需要进行重写的，因为它们已经完成它们所要得做的操作。

对于Object.ReferenceEquals这个静态函数，函数形式如下：
<pre class="lang:c# decode:true">public static bool ReferenceEquals( object left, object right );</pre>
　　这个函数就是判断两个引用类型对象是否指向同一个地址。有此说明后，就确定了它的使用范围，即只能对于引用类型操作。那么对于任何值类型数据操作，即使是与自身的判别，都会返回false。这主要因为在调用此函数的时候，值类型数据要进行装箱操作，也就是对于如下的形式来说。
<pre class="lang:c# decode:true">int n = 10;
Object.ReferenceEquals( n, n );</pre>
这是因为对于n这个数据装箱两次，而每次装箱后的地址有不同，而造成Object.ReferenceEquals( n, n )的结果永远为false。

对于第一个判等函数来说，没有什么好扩展的，因为本身已经很好地完成了它所要做的。

对于第二个Object.Equals这个静态函数，其形式如下：
<pre class="lang:c# decode:true">public static bool Equals( object left, object right );</pre>
　　按照书中对它的分析，其大致函数代码如下：
<pre class="lang:c# decode:true">public static void Equals( object left, object right )
{
  // Check object identity
  if( left == right )
     return true;

// both null references handled above
if( ( left == null ) || ( right == null ) )
   return false;
  return left.Equals( right );
}</pre>
　可以说，Object.Equals这个函数完成判等操作，需要经过三个步骤，

第一步是需要根据对象所属类型的==操作符的执行结果；

第二步是判别是否为null，也是和第一步一样，需要根据类型的==操作符的执行结果；

最后一步要使用到类型的Equals函数的执行结果。

也就是说这个静态函数的返回结果，要取决于后面要提到的两个判等函数。类型是否提供相应的判等函数，成为这个函数返回结果的重要因素。

　　那么对于Object.Equals这个静态方法来说，虽说接受参数的类型也属于引用类型，但是不同于Object.ReferenceEquals函数，对于如下的代码，能得出正确的结果。
<pre class="lang:c# decode:true">int n = 10;
Debug.WriteLine( string.Format( "{0}", Object.Equals( n, n ) ) );
Debug.WriteLine( string.Format( "{0}", Object.Equals( n, 10 ) ) );</pre>
&nbsp;

这是因为在此函数中要用到具体类型的两个判等函数，不过就函数本身而言，该做的判断都做了，因此不需要去重载添加复杂的操作。

为了更好的述说剩下两个函数，先解释一下等价的意义。对于等价的意义，就是自反、对称以及传递。

1.  所谓自反，即a == a；
2.  而对称，是a == b，则b == a；
3.  传递是 a == b，b == c，则 a == c；
理解等价的意义后，那么在实现类型的判等函数也要满足这个等价规则。

对于可以重载的两个判等函数，首先来介绍的是类型的Equals函数，其大致形式如下：
<pre class="lang:c# decode:true">public override bool Equals( object right );

那么对于一个类型的Equals要做些什么操作呢，一般来说大致如下：
public class KeyData
{
private int nData;
public int Data
{
   get{ return nData;}
   set{ nData = value; }
}

public override bool Equals( object right )
{
//Check null
if( right == null )
   return false;

//check reference equality
if( object.ReferenceEquals( this, right ) )
   return true;

//check type
if( this.GetType() != right.GetType() )
   return false;

//convert to current type
  KeyData rightASKeyData = right as KeyData;

//check members value
  return this.Data == rightASKeyData.Data;
 }
}</pre>
&nbsp;

如上增加了一个类型检查，即
<pre class="lang:c# decode:true">if( this.GetType() != right.GetType() )</pre>
　　这部分，这是由于子类对象可以通过as转化成基类对象，从而造成不同类型对象可以进行判等操作，违反了等价关系。

　　除此外对于类型的Equals函数来，其实并没有限制类型非要属于引用类型，对于值类型也是可以重载此函数，但是我并不推荐，主要是Equals函数的参数类型是不可变的，也就是说通过此方法，值类型要经过装箱操作，而这是比较影响效率的。

　　而对于值类型来说，我推荐使用最后一种判等函数，即重载运算符==函数，其大致形式如下：
<pre class="lang:c# decode:true">public static bool operator == ( KeyData left, KeyData right );</pre>
&nbsp;

对于一个值类型而言，其的大致形式应该如下：
<pre class="lang:c# decode:true">public struct KeyData
{
  private int nData;

public int Data
{
  get{ return nData;}
  set{ nData = value; }
}

public static bool operator == ( KeyData left, KeyData right )
{
   return left.Data == right.Data;
}

public static bool operator != ( KeyData left, KeyData right )
{
   return left.Data != right.Data;
}
}</pre>
&nbsp;

　　由于==操作与!=操作要同步定义，所以在定义==重载函数的时候，也要定义!=重载函数。这也是.Net在判等操作保持一致性。那么对于最后一个判等函数，这种重载运算符的方法并不适合引用类型。这就是.Net经常现象，去判断两个引用类型，不要用==，而要用某个对象的Equals函数。所以在编写自己类型的时候，要保留这种风格。

那么对于以上介绍的四种判等函数，会产生如下类似的对比表格。
<table border="1" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td valign="top" width="151"><span style="font-family: 'Times New Roman'; font-size: medium;"> </span></td>
<td width="156">

**_<span style="font-size: medium;">操作结果取决于</span>_**

</td>
<td width="84">

**_<span style="font-size: medium;">适用范围</span>_**

</td>
<td width="177">

**_<span style="font-size: medium;">建议</span>_**

</td>
</tr>
<tr>
<td width="151">

**<span style="font-size: medium;"><span style="font-family: 'Times New Roman';">Object.ReferenceEquals</span></span>**

</td>
<td valign="top" width="156">_<span style="font-size: medium;">两个参数对象是否属于同一个引用</span>_</td>
<td width="84">

**<span style="font-size: medium;">引用类型</span>**

</td>
<td valign="top" width="177">**_<span style="font-size: medium;">不要用它来判断值类型数据</span>_**</td>
</tr>
<tr>
<td width="151">

**<span style="font-size: medium;"><span style="font-family: 'Times New Roman';">Object.Equals</span></span>**

</td>
<td valign="top" width="156">_<span style="font-size: medium;">参数类型自身的判等函数</span>_</td>
<td width="84">

**<span style="font-size: medium;">无限制</span>**

</td>
<td valign="top" width="177">**_<span style="font-size: medium;">考虑装箱操作对值类型数据产生的影响</span>_**</td>
</tr>
<tr>
<td width="151">

**<span style="font-size: medium;">类型的<span style="font-family: 'Times New Roman';">Equals</span></span>**

</td>
<td valign="top" width="156">_<span style="font-size: medium;">类型重载函数</span>_</td>
<td width="84">

**<span style="font-size: medium;">无限制</span>**

</td>
<td valign="top" width="177">**_<span style="font-size: medium;">考虑装箱操作对值类型数据产生的影响</span>_**</td>
</tr>
<tr>
<td width="151">

<span style="font-size: medium;">**类型的<span style="font-family: 'Times New Roman';">==</span>****重载**</span>

</td>
<td valign="top" width="156">_<span style="font-size: medium;">类型重载函数</span>_</td>
<td width="84">

**<span style="font-size: medium;">无限制</span>**

</td>
<td valign="top" width="177">**_<span style="font-size: medium;">不要在引用类型中重载此运算符</span>_**</td>
</tr>
</tbody>
</table>
那么在编写类型判等函数的时候，要注意些什么呢，给出如下几点建议。
首先，要判断当前定义的类型是否具有判等的意义；
其次，定义类型的判等函数要满足等价规则；
最后一点，值类型最好不要重载定义Equals函数，而引用类型最好不要重载定义==操作符。

来源：[http://blog.csdn.net/knight94/article/details/1050901](http://blog.csdn.net/knight94/article/details/1050901)