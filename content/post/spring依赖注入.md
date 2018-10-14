---
title: Spring依赖注入
tags:
  - java
  - spring
  - 笔记
  - 翻译
id: 3284
categories:
  - 我的翻译
date: 2013-09-20 19:06:00
---

在Spring框架中，依赖注入设计模式主要用来定义对象之间的依赖，存在两种主要类型

1)setter注入(设置器)

2)constructor注入(构造器)

## 1.Setter注入

是最流行最简单的DI方法，通过一个setter方法来完成依赖。

例子：

一个有一个setter方法的Helper类
<pre class="lang:default decode:true">package com.mkyong.output;

import com.mkyong.output.IOutputGenerator;

public class OutputHelper
{
	IOutputGenerator outputGenerator;

	public void setOutputGenerator(IOutputGenerator outputGenerator){
		this.outputGenerator = outputGenerator;
	}

}</pre>
再写一个bean配合iwenjianshengming这些bean，并且通过property(属性)标签来设置依赖
<pre class="lang:default decode:true">&lt;beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-2.5.xsd"&gt;

	&lt;bean id="OutputHelper" class="com.mkyong.output.OutputHelper"&gt;
		&lt;property name="outputGenerator"&gt;
			&lt;ref bean="CsvOutputGenerator" /&gt;
		&lt;/property&gt;
	&lt;/bean&gt;

&lt;bean id="CsvOutputGenerator" class="com.mkyong.output.impl.CsvOutputGenerator" /&gt;
&lt;bean id="JsonOutputGenerator" class="com.mkyong.output.impl.JsonOutputGenerator" /&gt;

&lt;/beans&gt;</pre>
看到了把。我们只需要一个setter方法把CsvOutputGenerator注入进去就行了

## 2.Constructor注入

这种方式是通过一个构造函数来完成依赖设置的

例子：

一个有着一个构造函数的Helper类
<pre class="lang:default decode:true">package com.mkyong.output;

import com.mkyong.output.IOutputGenerator;

public class OutputHelper
{
	IOutputGenerator outputGenerator;

        OutputHelper(IOutputGenerator outputGenerator){
		this.outputGenerator = outputGenerator;
	}
}</pre>
然后当然是一个bean配置文件了。通过constructor-arg标签来写依赖
<pre class="lang:default decode:true">&lt;beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-2.5.xsd"&gt;

	&lt;bean id="OutputHelper" class="com.mkyong.output.OutputHelper"&gt;
		&lt;constructor-arg&gt;
                     &lt;ref bean="JsonOutputGenerator" /&gt;
                 &lt;/constructor-arg&gt; 
       &lt;/bean&gt; 
       &lt;bean id="CsvOutputGenerator" class="com.mkyong.output.impl.CsvOutputGenerator" /&gt; 
       &lt;bean id="JsonOutputGenerator" class="com.mkyong.output.impl.JsonOutputGenerator" /&gt; 
&lt;/beans&gt;</pre>

## 该选哪个？

没有硬性规定，哪个合适就用那个，由于setter的简单性，一般还是setter用得多。