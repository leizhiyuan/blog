---
title: Maven仓库详解
tags:
  - java
  - maven
  - 翻译
id: 3257
categories:
  - 我的翻译
date: 2013-09-19 22:05:10
---

本文由我翻译合并了多篇文章，整理成一篇。

# 一.本地仓库(Local Repository)

本地仓库就是一个本机的目录，这个目录被用来存储我们项目的所有依赖（插件的jar包还有一些其他的文件），简单的说，当你build一个Maven项目的时候，所有的依赖文件都会放在本地仓库里，仓库供所有项目都可以使用

默认情况下，本地仓库在.m2目录，windows下的话就是你的用户名目录下的.m2目录

## 1.更新本地仓库目录

找到你的MAVEN_HOME目录下的conf/setting.xml文件，更新localRepository节点
<pre class="lang:default decode:true" title="maven_home/conf/setting.xml">&lt;settings&gt;
  &lt;!-- localRepository
   | The path to the local repository maven will use to store artifacts.
   |
   | Default: ~/.m2/repository
  &lt;localRepository&gt;/path/to/local/repo&lt;/localRepository&gt;
  --&gt;

&lt;localRepository&gt;D:/maven/repo&lt;/localRepository&gt;</pre>

##  2.保存一下

完成了。新的本地仓库被放在了D:/maven/repo

看一下这个目录

[![maven-local-repo](/images/4bc19a12bd7d869d840c872a9c3bdbfbd55d339c.png)](http://leaverimage.b0.upaiyun.com/2013/09/maven-local-repo.png)

#  二.中央仓库(central repository)

当我们build一个Maven项目的时候，Maven会检查我们的pom.xml文件，来定义项目的依赖，然后Maven会在本地仓库里查找，如果没有找到，就去maven的中央库去下载，地址是

[http://search.maven.org/#browse](http://search.maven.org/#browse)

看起来是这样的

[![maven-center-repository-search](/images/1b90bc7c3b841d566a18777d661d40b159a29b16.png)](http://leaverimage.b0.upaiyun.com/2013/09/maven-center-repository-search.png)

&nbsp;

注意啊，虽然这个是新的中央仓库，但有时候还是会从_“**http://repo1.maven.org/maven/**_这个旧仓库下载东西，不过不要紧，理解就行了

# 三.远程仓库(Remote Respository)

在Maven中，当你在pom.xml中生命的依赖既不在本地库，也不在中央库的时候，就会报错。

## 1.例子

org.jvnet.localizer这个包仅在[java.net的仓库里](https://maven.java.net/content/repositories/public/)有(以前是，现在中央仓库也有了。但理解就行)
<pre class="lang:default decode:true" title="pom.xml">   &lt;dependency&gt;
        &lt;groupId&gt;org.jvnet.localizer&lt;/groupId&gt;
        &lt;artifactId&gt;localizer&lt;/artifactId&gt;
        &lt;version&gt;1.8&lt;/version&gt;
    &lt;/dependency&gt;</pre>
当我们build的时候，会失败，并输出未找到错误信息

## 2.声明java.net仓库

为了告诉Maven从远程仓库里获取依赖，我们需要声明一个远程仓库，在pom.xml里这样写
<pre class="lang:default decode:true">  &lt;repositories&gt;
	&lt;repository&gt;
	    &lt;id&gt;java.net&lt;/id&gt;
	    &lt;url&gt;https://maven.java.net/content/repositories/public/&lt;/url&gt;
	&lt;/repository&gt;
    &lt;/repositories&gt;</pre>
这样，Maven搜索依赖的顺序就是：

1）搜索本地仓库，没有找到，就去第2步，否则退出

2）搜索中央仓库，没有找到，就去第3步，否则退出

3）去java.net远程仓库获取，没有找到，就报错，否则退出

补充：JBoss也有个远程仓库，可以如下配置：
<pre class="lang:default decode:true">&lt;project ...&gt;
    &lt;repositories&gt;
      &lt;repository&gt;
	&lt;id&gt;JBoss repository&lt;/id&gt;
	&lt;url&gt;http://repository.jboss.org/nexus/content/groups/public/&lt;/url&gt;
      &lt;/repository&gt;
    &lt;/repositories&gt;
&lt;/project&gt;</pre>