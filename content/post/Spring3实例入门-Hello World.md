---
title: Spring3实例入门-Hello World
tags:
  - java
  - maven
  - spring
  - 翻译
  - 软件
id: 3278
categories:
  - 我的翻译
date: 2013-09-20 13:14:39
---

每次看到hello world,都有一种说不出的赶脚，想起了一个程序员，退休后写毛笔字，取笔研磨铺纸，大笔一挥，写下了“hello world”。

## 1.使用Maven生成项目结构

<pre class="lang:default decode:true ">mvn archetype:generate -DgroupId=com.mkyong.core -DartifactId=Spring3Example 
	-DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false</pre>

##  2.转换成Eclipse项目

<pre class="lang:default decode:true ">mvn eclipse:eclipse</pre>

##  3.添加Spring3.0 依赖

在pom.xml文件里添加Spring 3.0 依赖，然后依赖会从Maven中央仓库自动下载
<pre class="lang:default decode:true" title="pom.xml">&lt;project xmlns="http://maven.apache.org/POM/4.0.0" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
	http://maven.apache.org/maven-v4_0_0.xsd"&gt;
	&lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
	&lt;groupId&gt;com.mkyong.core&lt;/groupId&gt;
	&lt;artifactId&gt;Spring3Example&lt;/artifactId&gt;
	&lt;packaging&gt;jar&lt;/packaging&gt;
	&lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;
	&lt;name&gt;Spring3Example&lt;/name&gt;
	&lt;url&gt;http://maven.apache.org&lt;/url&gt;

	&lt;properties&gt;
		&lt;spring.version&gt;3.0.5.RELEASE&lt;/spring.version&gt;
	&lt;/properties&gt;

	&lt;dependencies&gt;

		&lt;!-- Spring 3 dependencies --&gt;
		&lt;dependency&gt;
			&lt;groupId&gt;org.springframework&lt;/groupId&gt;
			&lt;artifactId&gt;spring-core&lt;/artifactId&gt;
			&lt;version&gt;${spring.version}&lt;/version&gt;
		&lt;/dependency&gt;

		&lt;dependency&gt;
			&lt;groupId&gt;org.springframework&lt;/groupId&gt;
			&lt;artifactId&gt;spring-context&lt;/artifactId&gt;
			&lt;version&gt;${spring.version}&lt;/version&gt;
		&lt;/dependency&gt;

	&lt;/dependencies&gt;
&lt;/project&gt;</pre>

##  4.Spring bean

写个简单的bean
<pre class="lang:default decode:true" title="HelloWorld">package com.mkyong.core;

/**
 * Spring bean
 * 
 */
public class HelloWorld {
	private String name;

	public void setName(String name) {
		this.name = name;
	}

	public void printHello() {
		System.out.println("Spring 3 : Hello ! " + name);
	}
}</pre>

##  5.Spring bean配置文件

创建一个配置文件，在里面声明所有可用的Spring beans
<pre class="lang:default decode:true" title="SpringBeans.xml">&lt;beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans
	http://www.springframework.org/schema/beans/spring-beans-3.0.xsd"&gt;

	&lt;bean id="helloBean" class="com.mkyong.core.HelloWorld"&gt;
		&lt;property name="name" value="Mkyong" /&gt;
	&lt;/bean&gt;

&lt;/beans&gt;</pre>

##  6.最终的目录结构

[![Spring3-hello-world-example](/images/bbe4d8f74c9337766eb4a368bb71c6c0d09a248b.png)](http://leaverimage.b0.upaiyun.com/2013/09/Spring3-hello-world-example.png)

&nbsp;

## 7.运行

<pre class="lang:default decode:true" title="App.java">package com.mkyong.core;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {
	public static void main(String[] args) {
		ApplicationContext context = new ClassPathXmlApplicationContext(
				"SpringBeans.xml");

		HelloWorld obj = (HelloWorld) context.getBean("helloBean");
		obj.printHello();
	}
}</pre>

##  8.输出

<pre class="lang:default decode:true">Spring 3 : Hello ! Mkyong</pre>

##  9.Demo下载

[Spring3-hello-world-example.zip ](http://pan.baidu.com/share/link?shareid=2464458395&amp;uk=1493685990)