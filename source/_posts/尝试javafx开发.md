title: 尝试JavaFX开发
tags:
  - java
  - 上海
  - 学习
  - 工作
id: 3486
categories:
  - 学习笔记
date: 2014-02-11 20:40:12
---

曾经有报道说JavaFX将使java在桌面开发上大有作为，感觉好像是很高端的样子，今天尝试了一下，界面对于自用的工具来说，本来也不多重要，只是一个简单的尝试

简单说下大致的步骤和一些思路，可能有错误。

javafx需要sdk支持，java7之后的都有的。设计思路是数据和代码分离，界面通过xml或json数据来描述，这样就把业务逻辑代码和界面实现代码分开了

一个简单的开发过程应该是这样的

1.使用JavaFX Scene Builder来绘制界面，保存为xml/json格式

画的话没啥要说的，了解一下基本的整体概念就行。

2.在eclipse里新建工程，可以是普通工程，将1中的文件放到资源目录，在代码里加载，然后界面就加载成功了。
<pre class="lang:default decode:true">Node topNode = FXMLLoader.load(AFI.class.getResource("/afimain.fxml"));</pre>
逻辑代码，比如一个简单的按钮事件可以通过
<pre>Node node = topNode.lookup("#paneRightBottom");</pre>
来查找到id为paneRightBottom的元素，然后就可以通过对node添加事件监听器来完成一些功能了

_**主要想说的是：**_

JavaFX和WPF其实思路是一模一样的，恰好WPF我也用过，感觉两个都没搞起来，虽然界面炫，然后我去维基看了下：
> 该产品于2007年5月在JavaOne大会上首次对外公布。JavaFX技术主要应用于创建Rich Internet application（[RIAs](http://zh.wikipedia.org/wiki/RIA "RIA")）。JavaFX期望能够在桌面应用的开发领域与Adobe公司的AIR、[OpenLaszlo](http://zh.wikipedia.org/w/index.php?title=OpenLaszlo&amp;action=edit&amp;redlink=1 "OpenLaszlo（页面不存在）")以及[微软](http://zh.wikipedia.org/wiki/%E5%BE%AE%E8%BD%AF "微软")公司的[Silverlight](http://zh.wikipedia.org/wiki/Silverlight "Silverlight")相竞争
已经7年了，用户数应该是非常少的，**成熟的商业型产品也没几个**，在尝试的过程中，我在stackoverflow，以及一些很不错的java博客上，大量查找基本也没有太多的信息，都是一些很浅的应用，包括stackoverflow上的回答数，基本还是能反映出来的，主要应用于RIA，而当前RIA已经出html5的天下了。SL早都不更新了，这种坑爹的所谓新技术。

&nbsp;

Oracle还是好好把Swing搞好吧。。不建议尝试。

&nbsp;