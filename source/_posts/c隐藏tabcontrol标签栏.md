title: "C#隐藏TabControl标签栏"
tags:
  - C＃
  - windows
  - 大学
  - 软件
id: 2570
categories:
  - 学习笔记
date: 2013-01-11 21:31:52
---

今天考过了微软的那个70-562和70-536的考试。然后下午把软件体系结构的作业做了。然后看了一下栈溢出，我博客首页右侧的那个就是我的栈溢出id了。。

然后就看到了这个问题。这个问题。我曾经遇到过。貌似大家知道比较多的是两种。第一种就是设置大小。
<pre class="lang:default decode:true">tabControl1.SizeMode = TabSizeMode.Fixed;
tabControl1.ItemSize = new Size(0, 1);</pre>
[![]({{BASE_PATH}}/images/3070c473ca2d01dd23741af3e13d2f4328b9d89d.jpg)](http://leaverimage.b0.upaiyun.com/31378_o.jpg)

但是这样你注意看的话，左上角有个小的瑕疵。这个没办法的。。还有一种比较低级但还算有效的方法就是在设计的时候将TabControl向上移动。运行以后就会遮住了。

我当时不过取巧了。好像就用的第二种。。今天看到这个题目的时候，就做了下标记。刚才去看。大牛已经给出答案了。就是自己继承一个TabControl控件。重写
<pre>void WndProc(ref Message m)</pre>
方法，在方法里拦截系统消息。
<pre class="lang:default decode:true">using System;
using System.Windows.Forms;

class TablessControl : TabControl {
  protected override void WndProc(ref Message m) {
    // Hide tabs by trapping the TCM_ADJUSTRECT message
    if (m.Msg == 0x1328 &amp;&amp; !DesignMode) m.Result = (IntPtr)1;
    else base.WndProc(ref m);
  }
}</pre>
具体用法。就是在你的项目里新建一个类文件。然后把上面的代码拷进去。然后编译一下。就会在工具箱里多出一个TablessControl控件。拖进来即可使用。当然你也可以自定义一个用户控件。都不是事。这个控件设计时标签页可见。运行时由于拦截了信息消息。标签栏就不可见了。堪称完美。。