title: Spring松耦合示例
tags:
  - java
  - spring
  - 开发
  - 翻译
id: 3281
categories:
  - 我的翻译
date: 2013-09-20 15:50:46
---

面向对象设计的理念是把整个系统分成一组可重用的组件，然而，当系统变得越大的时候，尤其是在java中，这最大的对象依赖将会紧紧耦合，以至于非常难以管理和修改，而现在，你可以使用Spring框架扮演一个中间模块的角色，方便高效地管理其他组件依赖

## 输出生成的例子

看个例子，假设你的项目有一个方法可以输出内容到csv或者json格式，你可能写出这样的代码
<pre class="lang:default decode:true" title="IOutputGenerator.java 输出生成器接口">package com.mkyong.output;

public interface IOutputGenerator
{
	public void generateOutput();
}</pre>
，然后是实现接口的类
<pre class="lang:default decode:true" title="CsvOutputGenerator.java csv生成类">package com.mkyong.output.impl;

import com.mkyong.output.IOutputGenerator;

public class CsvOutputGenerator implements IOutputGenerator
{
	public void generateOutput(){
		System.out.println("Csv Output Generator");
	}
}</pre>
再写个Json生成的类
<pre class="lang:default decode:true" title="JsonOutputGenerator.java json生成类">package com.mkyong.output.impl;

import com.mkyong.output.IOutputGenerator;

public class JsonOutputGenerator implements IOutputGenerator
{
	public void generateOutput(){
		System.out.println("Json Output Generator");
	}
}</pre>
有好几种方法来调用IOutputGenerator接口，以及我们如何使用Spring来避免对象的过度耦合。

## 方法1-直接调用

<pre class="lang:default decode:true">package com.mkyong.common;

import com.mkyong.output.IOutputGenerator;
import com.mkyong.output.impl.CsvOutputGenerator;

public class App 
{
    public static void main( String[] args )
    {
    	IOutputGenerator output = new CsvOutputGenerator();
    	output.generateOutput();
    }
}</pre>
问题：

这种方法，output这个对象和CsvOutputGenerator耦合在了一起，每次要改变输出格式的话都要修改代码，如果这类代码遍布项目，那么改起来就跪了

## 方法2-通过帮助类调用

也许你会想创建一个Helper类吧所有的output实现都移进去
<pre class="lang:default decode:true">package com.mkyong.output;

import com.mkyong.output.IOutputGenerator;
import com.mkyong.output.impl.CsvOutputGenerator;

public class OutputHelper
{
	IOutputGenerator outputGenerator;

	public OutputHelper(){
		outputGenerator = new CsvOutputGenerator();
	}

	public void generateOutput(){
		outputGenerator.generateOutput();
	}

}</pre>
然后可以这样调用
<pre class="lang:default decode:true">package com.mkyong.common;

import com.mkyong.output.OutputHelper;

public class App 
{
    public static void main( String[] args )
    {
    	OutputHelper output = new OutputHelper();
    	output.generateOutput(); 
    }
}</pre>
问题：

看起来似乎更加优雅了，你仅仅需要管理这个Helper类就可以实现不同格式的输出需求改变了，然而，Helper还是和CvsOutputGenerator耦合，每一次要改变输出格式的时候，都要对Helper类做一下微调。

## 方法3-Spring

Spring依赖注入很合适，可以使不同的格式生成类分离开来

首先对OutputHelper做一点微调，添加了一个参数
<pre class="lang:default decode:true">package com.mkyong.output;

import com.mkyong.output.IOutputGenerator;

public class OutputHelper
{
	IOutputGenerator outputGenerator;

	public void generateOutput(){
		outputGenerator.generateOutput();
	}

	public void setOutputGenerator(IOutputGenerator outputGenerator){
		this.outputGenerator = outputGenerator;
	}
}</pre>
然后创建一个Spring bean配置文件，并声明所有的Java对象依赖
<pre class="lang:default decode:true">&lt;!-- Spring-Common.xml --&gt;
&lt;beans xmlns="http://www.springframework.org/schema/beans"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xsi:schemaLocation="http://www.springframework.org/schema/beans
http://www.springframework.org/schema/beans/spring-beans-2.5.xsd"&gt;

	&lt;bean id="OutputHelper" class="com.mkyong.output.OutputHelper"&gt;
		&lt;property name="outputGenerator" ref="CsvOutputGenerator" /&gt;
	&lt;/bean&gt;

	&lt;bean id="CsvOutputGenerator" class="com.mkyong.output.impl.CsvOutputGenerator" /&gt;
	&lt;bean id="JsonOutputGenerator" class="com.mkyong.output.impl.JsonOutputGenerator" /&gt;

&lt;/beans&gt;</pre>
然后通过Spring调用
<pre class="lang:default decode:true">package com.mkyong.common;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import com.mkyong.output.OutputHelper;

public class App 
{
    public static void main( String[] args )
    {
    	ApplicationContext context = 
    	   new ClassPathXmlApplicationContext(new String[] {"Spring-Common.xml"});

    	OutputHelper output = (OutputHelper)context.getBean("OutputHelper");
    	output.generateOutput();

    }
}</pre>
以后要改Json格式，直接改一下xml配置文件就行了。能够减少错误

## 结论

通过Spring框架的依赖注入，可以优雅的管理对象依赖，更大的灵活性，尤其是对于Java项目非常好用。