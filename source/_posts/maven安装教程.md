title: Maven安装教程
tags:
  - java
  - maven
  - 学习
  - 翻译
id: 3251
categories:
  - 我的翻译
date: 2013-09-19 21:15:09
---

Maven不需要作为服务组件安装到windows上，仅仅需要下载，解压，然后配置一下环境变量就行了

## 1.JDK和JAVA_HOME

确保JDK已经安装，同时JAVA_HOME变量已经添加到了windows环境变量里，指向jdk目录

[![maven-java-home]({{BASE_PATH}}/images/3161766919fb6fd118699cc45753f0740be3a6b4.png)](http://leaverimage.b0.upaiyun.com/2013/09/maven-java-home.png)

&nbsp;

## 2.下载Maven

去[Maven主页](http://maven.apache.org/download.html),选个版本，点击下载

## 3.解压

解压下载的zip文件，重命名，比如我放到D盘的Maven目录

## 4.添加MAVEN_HOME环境变量

添加一个新的环境变量MAVEN_HOME到环境变量，指向Maven目录

[![maven-maven-home]({{BASE_PATH}}/images/5a2878c27f389f27afa72040040a0cc54312b388.png)](http://leaverimage.b0.upaiyun.com/2013/09/maven-maven-home.png)

&nbsp;

## 5.添加path变量

更新Path变量，把Maven的bin目录添加进去，这样就可以在任何地方执行mvn命令了

[![Maven-Path]({{BASE_PATH}}/images/ec2bd24fcc118b64cb10405acb807c59490c10d6.png)](http://leaverimage.b0.upaiyun.com/2013/09/Maven-Path.png)

&nbsp;

## 6.验证

打开命令行，输入
<pre class="lang:default decode:true">mvn -version</pre>
如果看到类似下面的
<pre class="lang:default decode:true">C:\Documents and Settings\mkyong&gt;mvn -version
Apache Maven 2.2.1 (r801777; 2009-08-07 03:16:01+0800)
Java version: 1.6.0_13
Java home: C:\Program Files\Java\jdk1.6.0_13\jre
Default locale: en_US, platform encoding: Cp1252
OS name: "windows xp" version: "5.1" arch: "x86" Family: "windows"</pre>
Maven已经成功的安装配置了。

老外写的太详细了。。。

&nbsp;