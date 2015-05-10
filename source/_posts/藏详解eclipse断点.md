title: "[藏]图文并茂详解Eclipse断点"
tags:
  - java
  - 开发
  - 总结
  - 收藏
  - 软件
id: 3328
categories:
  - 文章收藏
date: 2013-11-16 14:21:11
---

本文转自：[http://my.oschina.net/colorleaf/blog/176569](http://my.oschina.net/colorleaf/blog/176569)

这个算说的比较清楚的了，虽然简单但是很有用。收藏一下。

**详解Eclipse断点**

大家肯定都用过Eclipse的调试的功能，在调试的过程中自然也无法避免要使用断点(breakpoint)，但不知是否对Eclipse中各类断点都有所了解。本篇图文并茂地介绍了Eclipse中全部类型的断点，及其设置，希望对大家有所帮助。(2011.11.20)

**1\. 示例程序**
BreakpointDemo是一个臆造的应用程序，只是为了便于讲解Eclipse中各类断点的使用罢了。其代码如下图所示，
<<<<<<< HEAD
[![15153245_BTUJ]({{BASE_PATH}}/images/abd1d53612eeef8d1feccdf17a894d3e71d41941.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153245_BTUJ.png)
=======
[![15153245_BTUJ](/images/abd1d53612eeef8d1feccdf17a894d3e71d41941.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153245_BTUJ.png)
>>>>>>> 换电脑之后重新备份
BreakpointDemo主要包含两个方法：
[1]setValue，该方法根据指定的次数(count)，对成员变量value进行赋值，值的范围为0-9的随机整数。
[2]printValue，该方法会调用setValue()对value进行赋值，并打印出value的值；但，如果value能被3整除，那么就会抛出IllegalArgumentException异常。

**2\. Line Breakpoint**
Line Breakpoin是最简单的Eclipse断点，只要双击某行代码对应的左侧栏，就对该行设置上断点。此处，对第20行代码设置上Line Breakpoint，如下图所示，
<<<<<<< HEAD
[![15153245_zkOp]({{BASE_PATH}}/images/ca7da4e9038334e0603072298cbaf834326da937.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153245_zkOp.png)
可以为Line Breakpoint设置一个条件，那么当程序运行到该断点时，只有满足设定的条件，才会被中断。右键点击第20行的断点，选择"Breakpoint Properties..."
[![15153245_I2Pe]({{BASE_PATH}}/images/ff95dcb0130a35d3e293a52487e4109316cba1f8.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153245_I2Pe.png)
在弹出的属性对话框中，勾选上"Conditional"，然后在文本框中输入"count % 2 == 0"。
[![15153246_rJtm]({{BASE_PATH}}/images/4f5d2d716473a55f1e3b96a59ac07f700657452a.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153246_rJtm.png)
该条件表示，当程序运行到第20行时，只有当count为偶数时，程序才会被中断。细心地话，你会发现该断点的图标发生了改变，多了一个问号。
[![15153246_5APe]({{BASE_PATH}}/images/e3d69e5d5b31edbf729ce4522f9b21e2f5024f17.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153246_5APe.png)

**3\. Watchpoint**
Line Breakpoint关注于程序运行的"过程"，大家也常把使用这种断点的调试称为单步调试。但有时候，我们对程序的运行过程不太了解，可能也不太关心，不能确定在什么地方设置断点比较合适，而可能比较关注某个关键变量的变化或使用。此时，就可以为该变量设置一种特殊的断点--Watchpoint。在此示例，我们最关心的就是成员变量value的值，那么就可以为它设置一个Watchpoint，双击第9行代码对应的左侧栏就可以了。
[![15153246_zzEM]({{BASE_PATH}}/images/082e1647ca341b0f3549dcfc136ce278d73f4acb.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153246_zzEM.png)
使用在2中所提及的方法，查看该断点的属性，
[![15153246_e1gH]({{BASE_PATH}}/images/f268f9ed80a672eeb51d164658a971d6777989b9.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153246_e1gH.png)
默认地，当该变量被访问或它的值被修改时，程序都会被中断。但在本示例中，只希望当对value的值进行修改时程序才需要被中断，所以取消对"Access"的勾选。
[![15153246_evtw]({{BASE_PATH}}/images/1deb043f0baa72b61a76e794f7acf9d25762112c.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153246_evtw.png)
这时，我们会发现原来的Watchpoin图标也有变化了。
[![15153246_Sw8K]({{BASE_PATH}}/images/d2dff57a03768ac00b84dd9bb651e99031d94cdc.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153246_Sw8K.png)

**4\. Method Breakpoint**
与关注对某个变量的访问与修改一样，我们也可以关注程序对某个方法的调用情况，即，可以设置Method Breakpoint。在此处，设置针对方法setValue的Method Breakpoint。同理，双击第11行代码对应的左侧栏即可。
[![15153247_xdH4]({{BASE_PATH}}/images/bd49da0a82d0dc044a917e5b53107b05b36f6378.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153247_xdH4.png)
仍然要查看该断点的属性。默认地，只勾选了"Entry"，而没有勾选"Exit"。
[![15153247_W1oy]({{BASE_PATH}}/images/7294186aaa782865383feb3a4e375a5a3b628c27.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153247_W1oy.png)
这表示，当刚进入该方法(调用开始)时，程序会被中断；而，离开该方法(调用结束)时，程序并不会被中断。在本示例中，需要同时勾选上"Exit"。
[![15153247_QVwz]({{BASE_PATH}}/images/39f23217e1d0ead7061f1d636b97f52b6ee886fd.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153247_QVwz.png)
点击OK之后，可以看到该断点的图标也有所改变。
[![15153247_YRMh]({{BASE_PATH}}/images/bdeda47c131aefb575366c4d23b16b3e0ee59321.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153247_YRMh.png)
=======
[![15153245_zkOp](/images/ca7da4e9038334e0603072298cbaf834326da937.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153245_zkOp.png)
可以为Line Breakpoint设置一个条件，那么当程序运行到该断点时，只有满足设定的条件，才会被中断。右键点击第20行的断点，选择"Breakpoint Properties..."
[![15153245_I2Pe](/images/ff95dcb0130a35d3e293a52487e4109316cba1f8.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153245_I2Pe.png)
在弹出的属性对话框中，勾选上"Conditional"，然后在文本框中输入"count % 2 == 0"。
[![15153246_rJtm](/images/4f5d2d716473a55f1e3b96a59ac07f700657452a.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153246_rJtm.png)
该条件表示，当程序运行到第20行时，只有当count为偶数时，程序才会被中断。细心地话，你会发现该断点的图标发生了改变，多了一个问号。
[![15153246_5APe](/images/e3d69e5d5b31edbf729ce4522f9b21e2f5024f17.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153246_5APe.png)

**3\. Watchpoint**
Line Breakpoint关注于程序运行的"过程"，大家也常把使用这种断点的调试称为单步调试。但有时候，我们对程序的运行过程不太了解，可能也不太关心，不能确定在什么地方设置断点比较合适，而可能比较关注某个关键变量的变化或使用。此时，就可以为该变量设置一种特殊的断点--Watchpoint。在此示例，我们最关心的就是成员变量value的值，那么就可以为它设置一个Watchpoint，双击第9行代码对应的左侧栏就可以了。
[![15153246_zzEM](/images/082e1647ca341b0f3549dcfc136ce278d73f4acb.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153246_zzEM.png)
使用在2中所提及的方法，查看该断点的属性，
[![15153246_e1gH](/images/f268f9ed80a672eeb51d164658a971d6777989b9.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153246_e1gH.png)
默认地，当该变量被访问或它的值被修改时，程序都会被中断。但在本示例中，只希望当对value的值进行修改时程序才需要被中断，所以取消对"Access"的勾选。
[![15153246_evtw](/images/1deb043f0baa72b61a76e794f7acf9d25762112c.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153246_evtw.png)
这时，我们会发现原来的Watchpoin图标也有变化了。
[![15153246_Sw8K](/images/d2dff57a03768ac00b84dd9bb651e99031d94cdc.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153246_Sw8K.png)

**4\. Method Breakpoint**
与关注对某个变量的访问与修改一样，我们也可以关注程序对某个方法的调用情况，即，可以设置Method Breakpoint。在此处，设置针对方法setValue的Method Breakpoint。同理，双击第11行代码对应的左侧栏即可。
[![15153247_xdH4](/images/bd49da0a82d0dc044a917e5b53107b05b36f6378.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153247_xdH4.png)
仍然要查看该断点的属性。默认地，只勾选了"Entry"，而没有勾选"Exit"。
[![15153247_W1oy](/images/7294186aaa782865383feb3a4e375a5a3b628c27.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153247_W1oy.png)
这表示，当刚进入该方法(调用开始)时，程序会被中断；而，离开该方法(调用结束)时，程序并不会被中断。在本示例中，需要同时勾选上"Exit"。
[![15153247_QVwz](/images/39f23217e1d0ead7061f1d636b97f52b6ee886fd.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153247_QVwz.png)
点击OK之后，可以看到该断点的图标也有所改变。
[![15153247_YRMh](/images/bdeda47c131aefb575366c4d23b16b3e0ee59321.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153247_YRMh.png)
>>>>>>> 换电脑之后重新备份
根据这里的设置，当程序运行到第20行后会在第12行被中断，尽管这里没有显式的断点，但这就是setValue()方法的入口(Entry)。必须注意地是，程序在运行到第16行时不会被中断，尽管它看起来像是setValue()方法的出口(Exit)。实际上，程序会在第17行被中断，这里才是setValue()调用结束的地方。

**5\. Exception Breakpoint**
如果，我们期望某个特定异常发生时程序能够被中断，以方便查看当时程序所处的状态。通过设置Exception Breakpoint就能达到这一目标。本示例故意在第23行抛出了IllegalArgumentException异常，我们期望程序运行到此处时会被中断。但我们不直接为此行代码设置Line Breakpoint，而是为IllegalArgumentException设置Exception Breakpoint。设置Exception Breakpoint的方法与其它类型断点都不同，它不能通过双击左侧栏的方式在代码编辑器上直接进行设置。点击Breakpoints视图右上角形如Ji的图标，
<<<<<<< HEAD
[![15153247_Wm2U]({{BASE_PATH}}/images/d4f298befa3d1c0100aef36797c56bbc1d2a423c.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153247_Wm2U.png)
会弹出如下所示的对话框，
[![15153248_WgLd]({{BASE_PATH}}/images/04e45e47d1e4248fd2ec8db8b13252e1c196e525.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153248_WgLd.png)
在其中选中IllegalArgumentException，并点击OK，这时一个Exception Breakpoint就设置好了。
[![15153248_kE2O]({{BASE_PATH}}/images/fd555622458bbb34ad01929c6a7483768f6b37a8.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153248_kE2O.png)
<div>当value为3的倍数时，程序会在第23行被中断，这时我们就能使用调试器来看看value具体是等于0，3或6。</div>
**6\. Class Load Breakpoint**
还有一种大家平时可能不太用的断点--Class Load Breakpoint，即当某个类被加载时，通过该断点可以中断程序。
[![15153248_2UA6]({{BASE_PATH}}/images/8158c897f4e784042063a484c7256901902b6c20.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153248_2UA6.png)
=======
[![15153247_Wm2U](/images/d4f298befa3d1c0100aef36797c56bbc1d2a423c.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153247_Wm2U.png)
会弹出如下所示的对话框，
[![15153248_WgLd](/images/04e45e47d1e4248fd2ec8db8b13252e1c196e525.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153248_WgLd.png)
在其中选中IllegalArgumentException，并点击OK，这时一个Exception Breakpoint就设置好了。
[![15153248_kE2O](/images/fd555622458bbb34ad01929c6a7483768f6b37a8.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153248_kE2O.png)
<div>当value为3的倍数时，程序会在第23行被中断，这时我们就能使用调试器来看看value具体是等于0，3或6。</div>
**6\. Class Load Breakpoint**
还有一种大家平时可能不太用的断点--Class Load Breakpoint，即当某个类被加载时，通过该断点可以中断程序。
[![15153248_2UA6](/images/8158c897f4e784042063a484c7256901902b6c20.png)](http://leaverimage.b0.upaiyun.com/2013/11/15153248_2UA6.png)
>>>>>>> 换电脑之后重新备份

**小结**
上述的Eclipse断点，我们在现实工作中肯定都有意或无意地使用过其中的几种，只是不一定十分了解内情罢了。使用好Eclipse的各种断点，可以把很好地帮助我们分析程序，定位问题。