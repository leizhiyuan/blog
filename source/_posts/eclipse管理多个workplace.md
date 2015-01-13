title: eclipse管理多个workplace
tags:
  - 上海
  - 工作
id: 3534
categories:
  - 学习笔记
date: 2014-03-08 16:34:34
---

由于eclipse用的比较多，管理多个workplace很麻烦，经常需要打开以后再切换，简单介绍个方法。

一

1.进入Eclipse的安装目录，鼠标点击eclipse.exe，右键菜单--&gt;发送到--&gt;桌面快捷方式

2.到桌面上找到“eclipse.exe - 快捷方式”，鼠标右键点击查看属性，弹出菜单中选择“快捷方式”标签，然后在“目标”中增加内容：-data e:\workspace，保存后即可

3.双击这个快捷方式，eclipse就会使用e:\workspace作为工作空间启动。

这样你多复制几个，就好了。

二

1.找个目录，新建一个文件夹，名称为workspace_aaa

2.然后在当前目录下新建一个txt文件

3.输入内容为：

<pre class="lang:default decode:true">start E:\eclipse\eclipse.exe -data workspace1</pre>

说明：前面是eclipse的路径，中间加上"-data” ，后面为工作空间的路径，start要有，不然打开eclipse之后，命令行窗口不会自动消失的，很是碍眼。

4.将这个txt保存为workspace1.bat

5.双击这个workspace1.bat，eclipse就会使用workspace1.bat 作为工作空间启动。

&nbsp;

对launchy党来说，新建个目录，然后创建好多个bat，以后直接快速启动真是太方便了