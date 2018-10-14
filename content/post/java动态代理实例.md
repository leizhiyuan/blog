---
title: Java动态代理实例
tags:
  - java
  - 上海
  - 学习
  - 工作
id: 3350
categories:
  - 学习笔记
date: 2013-11-24 18:16:31
---

首先什么是代理？
<div>所谓代理呢也就是在调用实现类的方法时，可以在方法执行前后做额外的工作，这个就是代理。</div>
<div></div>
<div>那动态代理呢，官方解释是：</div>
<div>
<pre class="lang:default decode:true ">Java 动态代理机制的出现，使得 Java 开发人员不用手工编写代理类，只要简单地指定一组接口及委托类对象，便能动态地获得代理类。代理类会负责将所有的方法调用分派到委托对象上反射执行，在分派执行的过程中，开发人员还可以按需调整委托类对象及其功能，这是一套非常灵活有弹性的代理框架。</pre>
老湿，你说的是个毛啊，完全没看懂啊！

我更喜欢另一种通俗的解释，官方的解释总是高度抽象的，等用了一段时间才能理解体会
<div>
<div>动态代理实现了日志和业务的分开，也就是某个类只是要提供了某些业务，比如银行取款业务。这个类实现了取款业务的同时也需要实现日志功能，如果不用动态代理的话，那么由此一来该类代码里面已经额外地添加了自己不该添加的日志功能能代码。所以我们就得使用动态代理把它的业务代码和日志功能代码分开。所以用到了动态代理概念，spring里面的AOP就是一个很好的例子。</div>
<div></div>
<div>不直观啊，老湿，能再给力一点不？</div>
<div>额，这样的话，我们来看一个例子，要用到的两个类
<div>实现java.lang.reflect.InvocationHandler接口提供一个执行处理器，也就是真正做事的，然后通过java.lang.reflect.Proxy得到一个代理对象，通过这个代理对象来执行业务方法,在业务方法被调用的同时，执行处理器会被自动调用。   记住，动态代理只能对接口</div>
<div>首先业务接口：</div>
<div>
<pre class="lang:default decode:true ">public interface HelloWorld {
	public void sayHelloWorld();
}</pre>
然后我们是这样写的实现
<pre class="lang:default decode:true">public class HelloWorldImpl implements HelloWorld {
	public void sayHelloWorld() {
		System.out.println("Hello World!");
	}
}</pre>
后来我们觉得执行这个方法前能不能做点其他啥事呢，比如写个日志？见个妹子？啥，这段代码不让改了，改了的话，业务方法和日志混合的一塌糊涂啊，以后想改个日志格式你来写啊

那我们就得定义一个拦截器/执行处理器了。
<pre class="lang:default decode:true">public class HelloWorldHandler implements InvocationHandler {
	//目标对象
	private Object targetObject;

	public HelloWorldHandler(Object targetObject){
		this.targetObject = targetObject;
	}

	public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
		System.out.println("方法调用前");

		Object result = method.invoke(this.targetObject, args);

		System.out.println("方法调用结束");

		return result;
	}
}</pre>
这客户端咋用啊，老湿
<pre class="lang:default decode:true">public class HelloWorldTest {
	public static void main(String[] args) {
		//业务对象
		HelloWorld obj = new HelloWorldImpl();

		//拦截器对象
		HelloWorldHandler handler = new HelloWorldHandler(obj);

		//返回业务对象的代理对象
		HelloWorld proxy = (HelloWorld)Proxy.newProxyInstance(
				obj.getClass().getClassLoader(), 
				obj.getClass().getInterfaces(), 
				handler);

		//通过代理对象执行业务对象的方法
		proxy.sayHelloWorld();
	}
}</pre>
看到没，通过Proxy类的newProxyInstance方法，传入类加载器，类接口，和这个处理器，我们就获得一个代理

执行结果是这样的
<pre class="lang:default decode:true">方法调用前
Hello World!
方法调用结束</pre>
恩，电脑没死机，是这样的

&nbsp;

</div>
<div></div>
</div>
<div></div>
<div></div>
</div>
</div>