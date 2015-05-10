title: 理解并实现原型模式-实现ICloneable接口.理解深浅拷贝
tags:
  - C＃
  - 大学
  - 开发
  - 武汉
  - 设计
id: 2126
categories:
  - 我的翻译
date: 2012-10-19 09:27:21
---

本文用C#实现原型模式,也会讨论深浅拷贝,已经如何在.net中高效实现ICloneable 接口
**介绍**
有时候我们需要从上下文得到一个对象的拷贝，然后通过一些独立的操作来处理他。原型模式在这种情况下很适用

GoF 定义原型模式为用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象。
Specify the kind of objects to create using a prototypical instance, and create new objects by copying this prototype."

看一下类图

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/e7e0d35a2192ac4360d169e4e1cb8fdb6a1113da.jpg "1")](http://leaverimage.b0.upaiyun.com/28239_o.jpg)
=======
[![](/images/e7e0d35a2192ac4360d169e4e1cb8fdb6a1113da.jpg "1")](http://leaverimage.b0.upaiyun.com/28239_o.jpg)
>>>>>>> 换电脑之后重新备份

主要的参与者有
• Prototype: 抽象类或接口，定义了方法来拷贝自己
• ConcretePrototype: 克隆的具体类.
• Client: 需要执行拷贝对象的软件对象
然后实现吧

使用代码

为了简化。我以一个著名的偷车游戏作为例子
我们说游戏里有一个注脚。这个主要有着一些定义游戏数据的统计量。保存游戏的时候我们就需要拷贝这个对象，然后序列化到文件中。（仅仅是举个例子，真实的游戏里很少这样做）

下面这个类抽象类就是概念中的Prototype
<pre class="lang:default decode:true">public abstract class AProtagonist
{
    int m_health;
    int m_felony;
    double m_money;

    public int Health
    {
        get { return m_health; }
        set { m_health = value; }
    }

    public int Felony
    {
        get { return m_felony; }
        set { m_felony = value; }
    }

    public double Money
    {
        get { return m_money; }
        set { m_money = value; }
    }

    public abstract AProtagonist Clone();
}</pre>
&nbsp;

接口定义了玩家重要的信息，然后定义了一个Clone方法。然后我们定义一个具体的玩家类CJ。这样我们可以克隆当前对象，然后异步的进行序列化
<pre class="lang:default decode:true">class CJ : AProtagonist
{
    public override AProtagonist Clone()
    {
        return this.MemberwiseClone() as AProtagonist;
    }
}</pre>
这个类就是概念中的ConcretePrototype 。这里为了简化也没有其他一些方法了。

现在看看客户端软件的写法
<pre class="lang:default decode:true">static void Main(string[] args)
{
    // 演示原型模式
    CJ player = new CJ();
    player.Health = 1;
    player.Felony = 10;
    player.Money = 2.0;

    Console.WriteLine("Original Player stats:");
    Console.WriteLine("Health: {0}, Felony: {1}, Money: {2}", 
        player.Health.ToString(), 
        player.Felony.ToString(), 
        player.Money.ToString());

    // 这里是拷贝部分.
    CJ playerToSave = player.Clone() as CJ;            

    Console.WriteLine("\nCopy of player to save on disk:");
    Console.WriteLine("Health: {0}, Felony: {1}, Money: {2}", 
        playerToSave.Health.ToString(), 
        playerToSave.Felony.ToString(), 
        playerToSave.Money.ToString());
}</pre>
<<<<<<< HEAD
[![]({{BASE_PATH}}/images/ab28ca0c722912c55072bc471d50cb94d9ee8bb8.jpg "2")](http://leaverimage.b0.upaiyun.com/28245_o.jpg)
=======
[![](/images/ab28ca0c722912c55072bc471d50cb94d9ee8bb8.jpg "2")](http://leaverimage.b0.upaiyun.com/28245_o.jpg)
>>>>>>> 换电脑之后重新备份

**理解深浅拷贝**

上面的代码就模拟了原型模式，有一个问题就是我们使用了MemberwiseCopy 方法。而这个方法是创建浅拷贝的。如果对象里包含引用类型，引用类型的地址就会被从前者拷贝到后者。这样。两个就指向了同样的对象。

为了说明情况。我们让主角类包含更多的，叫做AdditionalDetails
<pre class="lang:default decode:true">public class AdditionalDetails
{
    int m_charisma;
    int m_fitness;

    public int Charisma
    {
        get { return m_charisma; }
        set { m_charisma = value; }
    }

    public int Fitness
    {
        get { return m_fitness; }
        set { m_fitness = value; }
    }
}</pre>
&nbsp;

然后抽象类将会一个AdditionalDetails 对象
<pre class="lang:default decode:true">public abstract class AProtagonistEx
{
    int m_health;
    int m_felony;
    double m_money;
    // 引用类型
    AdditionalDetails m_details = new AdditionalDetails();

    public int Health
    {
        get { return m_health; }
        set { m_health = value; }
    }

    public int Felony
    {
        get { return m_felony; }
        set { m_felony = value; }
    }

    public double Money
    {
        get { return m_money; }
        set { m_money = value; }
    }

    public AdditionalDetails Details
    {
        get { return m_details; }
        set { m_details = value; }
    }

    public abstract AProtagonistEx Clone();
}</pre>
&nbsp;

具体类既然使用MemberwiseCopy实现拷贝
<pre class="lang:default decode:true">class CJEx : AProtagonistEx
{
    public override AProtagonistEx Clone()
    {
        return this.MemberwiseClone() as AProtagonistEx;
    }
}</pre>
&nbsp;

问题来了。两份拷贝会指向同一个AdditionalDetials 对象
<pre class="lang:default decode:true">static void Main(string[] args)
{
    // 演示浅拷贝
    CJEx playerEx = new CJEx();
    playerEx.Health = 1;
    playerEx.Felony = 10;
    playerEx.Money = 2.0;
    playerEx.Details.Fitness = 5;
    playerEx.Details.Charisma = 5;

    // 克隆对象然后改变引用类型的值
    CJEx shallowClonedPlayer = playerEx.Clone() as CJEx;
    shallowClonedPlayer.Details.Charisma = 10;
    shallowClonedPlayer.Details.Fitness = 10;

    //看看发生了什么
    Console.WriteLine("\nOriginal Object:");
    Console.WriteLine("Charisma: {0}, Fitness: {1}",
        playerEx.Details.Charisma.ToString(),
        playerEx.Details.Fitness.ToString());
    Console.WriteLine("\nShallow Cloned Object:");
    Console.WriteLine("Charisma: {0}, Fitness: {1}",
        shallowClonedPlayer.Details.Charisma.ToString(),
        shallowClonedPlayer.Details.Fitness.ToString());
}</pre>
<<<<<<< HEAD
[![]({{BASE_PATH}}/images/863b924dcc5e05338a089ed0832503ba3ace600f.jpeg "3")](http://leaverimage.b0.upaiyun.com/28244_o.jpeg)

画个图。看得清楚些

[![]({{BASE_PATH}}/images/21512f67bcb7bcfcc7c220a3732d98feb4d4ed89.jpg "2")](http://leaverimage.b0.upaiyun.com/28240_o.jpg)
=======
[![](/images/863b924dcc5e05338a089ed0832503ba3ace600f.jpeg "3")](http://leaverimage.b0.upaiyun.com/28244_o.jpeg)

画个图。看得清楚些

[![](/images/21512f67bcb7bcfcc7c220a3732d98feb4d4ed89.jpg "2")](http://leaverimage.b0.upaiyun.com/28240_o.jpg)
>>>>>>> 换电脑之后重新备份
为了避免这种期刊。我们需要在堆上创建引用类型的拷贝，然后对应赋值
<pre class="lang:default decode:true">class CJEx : AProtagonistEx
{
    public override AProtagonistEx Clone()
    {
        CJEx cloned = this.MemberwiseClone() as CJEx;
        cloned.Details = new AdditionalDetails();
        cloned.Details.Charisma = this.Details.Charisma;
        cloned.Details.Fitness = this.Details.Fitness;

        return cloned as AProtagonistEx;
    }
}</pre>
&nbsp;

这样就可以了

<<<<<<< HEAD
[![]({{BASE_PATH}}/images/5a1c4910991230f8d746cc39e1f427c29e247164.jpg "5")](http://leaverimage.b0.upaiyun.com/28242_o.jpg)
=======
[![](/images/5a1c4910991230f8d746cc39e1f427c29e247164.jpg "5")](http://leaverimage.b0.upaiyun.com/28242_o.jpg)
>>>>>>> 换电脑之后重新备份

注意。当执行深拷贝的时候，可能引用类型来包括引用类型，因此好的做法是用反射。并且递归的拷贝。直到没有引用类型，具体可参考另一文

实现ICloneable接口
ICloneable接口提供了定义拷贝方法的接口。我们可以使用ICloneable 作为概念中的Prototype，我们看一下如果实现ConcretePrototype
<pre class="lang:default decode:true">public class CJFinal : ICloneable
{
    int m_health;
    int m_felony;
    double m_money;
    AdditionalDetails m_details = new AdditionalDetails();

    public int Health
    {
        get { return m_health; }
        set { m_health = value; }
    }

    public int Felony
    {
        get { return m_felony; }
        set { m_felony = value; }
    }

    public double Money
    {
        get { return m_money; }
        set { m_money = value; }
    }

    public AdditionalDetails Details
    {
        get { return m_details; }
        set { m_details = value; }
    }

    private object ShallowCopy()
    {
        return this.MemberwiseClone();
    }

    private object DeepCopy()
    {
        CJFinal cloned = this.MemberwiseClone() as CJFinal;
        cloned.Details = new AdditionalDetails();
        cloned.Details.Charisma = this.Details.Charisma;
        cloned.Details.Fitness = this.Details.Fitness;

        return cloned;
    }

    #region ICloneable Members

    public object Clone()
    {
        return DeepCopy();
    }

    #endregion
}</pre>
&nbsp;

客户端代码如下
<pre class="lang:default decode:true">private static void ICloneableVersionCopy()
{
    // 演示深拷贝
    CJFinal player = new CJFinal();
    player.Health = 1;
    player.Felony = 10;
    player.Money = 2.0;
    player.Details.Fitness = 5;
    player.Details.Charisma = 5;

    // 演示深拷贝
    CJFinal clonedPlayer = player.Clone() as CJFinal;
    clonedPlayer.Details.Charisma = 10;
    clonedPlayer.Details.Fitness = 10;

    // 看看效果
    Console.WriteLine("\nOriginal Object:");
    Console.WriteLine("Charisma: {0}, Fitness: {1}",
        player.Details.Charisma.ToString(),
        player.Details.Fitness.ToString());
    Console.WriteLine("\nICloneable Deep Cloned Object:");
    Console.WriteLine("Charisma: {0}, Fitness: {1}",
        clonedPlayer.Details.Charisma.ToString(),
        clonedPlayer.Details.Fitness.ToString());
}</pre>
<<<<<<< HEAD
[![]({{BASE_PATH}}/images/be282d2800c83c0aa1fa411f51dc3ac55e442f35.jpg "6")](http://leaverimage.b0.upaiyun.com/28243_o.jpg)
=======
[![](/images/be282d2800c83c0aa1fa411f51dc3ac55e442f35.jpg "6")](http://leaverimage.b0.upaiyun.com/28243_o.jpg)
>>>>>>> 换电脑之后重新备份

**Demo下载**
[downloadicon href=http://pan.baidu.com/share/link?shareid=86332&amp;uk=1493685990]PrototypeDemo.zip[/downloadicon]

原文地址：[Understanding-and-Implementing-Prototype-Pattern-i](http://www.codeproject.com/Articles/476807/Understanding-and-Implementing-Prototype-Pattern-i)

著作权声明：本文由[http://leaver.me](http://leaver.me) 翻译，欢迎转载分享。请尊重作者劳动，转载时保留该声明和作者博客链接，谢谢！