---
title: "C#反射实现简单的插件系统"
tags:
  - C＃
  - 原创
  - 编程
  - 设计
  - 软件
id: 2711
categories:
  - 学习笔记
date: 2013-02-21 08:28:27
---

如果用过一些优秀的开源或是非开源的应用软件，就会发现这些软件的一个很大的优势就是其开放性，任何有能力的人都可以为其开发不同的插件来增强其的功能。比如著名的foobar2000，Vim和TotalCommander等等。

C#的反射可以用来实现一个简单的插件系统。思路很简单，我们创建一个解决方案，包含三个工程，一个为我们的软件MyApplication，一个为插件接口IPlugin，一个为具体的插件MyPlugin。插件系统的基本思路是我们用一个接口类库,来定义我们软件可以使用的插件必须实现的方法签名。然后我们的软件MyApplication通过引用该IPlugin dll来动态调用，而具体的实现插件MyPlugin则引用该接口来实现具体的方法。这样我们的应用程序就能在不知道具体插件的情况下调用插件了。。

结构图如下：

[![Myapplication](/images/bf202a9b50d5ce497f88718743c344d8579fadf1.jpg)](http://leaverimage.b0.upaiyun.com/32896_o.jpg)

关键代码也就是通过对程序集载入。搜索到对应接口的实现类。然后调用即可。
<pre class="lang:default decode:true">Assembly a = Assembly.LoadFrom(AssemblyName);
                foreach (Type t in a.GetTypes())
                {
                    if (t.GetInterface("IMyFunction") != null)
                    {
                        try
                        {
                            IMyFunction pluginclass = Activator.CreateInstance(t) as IMyFunction;
                            pluginclass.doSomething();
                        }
                        catch (Exception ex)
                        {
                            MessageBox.Show(ex.ToString());
                        }
                    }
                }</pre>
运行结果：

[![QQ截图20130220200408](/images/6c33ac761faef8c7d4beae4a478b4240cdeb7ee3.jpg)](http://leaverimage.b0.upaiyun.com/32897_o.jpg)

源码下载：[PluginSystem.zip](http://pan.baidu.com/share/link?shareid=302323&amp;uk=1493685990)