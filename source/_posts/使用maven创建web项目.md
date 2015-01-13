title: 使用Maven创建Web项目
tags:
  - java
  - maven
  - 学习
  - 翻译
id: 3266
categories:
  - 我的翻译
date: 2013-09-20 08:44:11
---

本文通过Maven完成一个简单的Web项目（注意，Spring配置不是重点，看看就行）

## 1.从Maven模板创建Web应用程序

命令格式如下：
<pre class="lang:default decode:true">mvn archetype:generate -DgroupId={project-packaging} -DartifactId={project-name} -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false</pre>
这就告诉Maven从**maven-archetype-webapp **这个模板来创建

友情提示：是不是太难记了..好吧，直接输入
<pre>mvn archetype:generate</pre>
根据向导来创建把。。

比如我这样写：
<pre class="lang:default decode:true">&gt; mvn archetype:generate -DgroupId=com.mkyong -DartifactId=CounterWebApp -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building Maven Stub Project (No POM) 1
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] Generating project in Batch mode
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Old (1.x) Archetype: maven-archetype-webapp:1.0
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.mkyong
[INFO] Parameter: packageName, Value: com.mkyong
[INFO] Parameter: package, Value: com.mkyong
[INFO] Parameter: artifactId, Value: CounterWebApp
[INFO] Parameter: basedir, Value: /Users/mkyong/Documents/workspace
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] project created from Old (1.x) Archetype in dir: /Users/mkyong/Documents/workspace/CounterWebApp
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 3.147s
[INFO] Finished at: Thu Dec 20 20:35:19 MYT 2012
[INFO] Final Memory: 12M/128M
[INFO] ------------------------------------------------------------------------</pre>
就创建了一个包名为com.mkyong,类名为CounterWebApp的项目了

## 2.Maven的Web程序目录结构

标准的web.xml部署描述文件生成了
<pre>CounterWebApp
   |-src
   |---main
   |-----resources
   |-----webapp
   |-------index.jsp
   |-------WEB-INF
   |---------web.xml
   |-pom.xml</pre>
对结构有疑问的去[这里](http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)

生成的pom.xml文件如下
<pre class="lang:default decode:true">&lt;project xmlns="http://maven.apache.org/POM/4.0.0" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
	http://maven.apache.org/maven-v4_0_0.xsd"&gt;
  &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
  &lt;groupId&gt;com.mkyong&lt;/groupId&gt;
  &lt;artifactId&gt;CounterWebApp&lt;/artifactId&gt;
  &lt;packaging&gt;war&lt;/packaging&gt;
  &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;
  &lt;name&gt;CounterWebApp Maven Webapp&lt;/name&gt;
  &lt;url&gt;http://maven.apache.org&lt;/url&gt;
  &lt;dependencies&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;junit&lt;/groupId&gt;
      &lt;artifactId&gt;junit&lt;/artifactId&gt;
      &lt;version&gt;3.8.1&lt;/version&gt;
      &lt;scope&gt;test&lt;/scope&gt;
    &lt;/dependency&gt;
  &lt;/dependencies&gt;
  &lt;build&gt;
    &lt;finalName&gt;CounterWebApp&lt;/finalName&gt;
  &lt;/build&gt;
&lt;/project&gt;</pre>
没啥疑问的。web.xml文件则如下：
<pre class="lang:default decode:true" title="web.xml">&lt;!DOCTYPE web-app PUBLIC
 "-//Sun Microsystems, Inc.//DTD Web Application 2.3//EN"
 "http://java.sun.com/dtd/web-app_2_3.dtd" &gt;

&lt;web-app&gt;
  &lt;display-name&gt;Archetype Created Web Application&lt;/display-name&gt;
&lt;/web-app&gt;</pre>
index.jsp内容是：
<pre class="lang:default decode:true" title="index.jsp">&lt;html&gt;
&lt;body&gt;
&lt;h2&gt;Hello World!&lt;/h2&gt;
&lt;/body&gt;
&lt;/html&gt;</pre>

##  3.转换以支持Eclipse

为了使Maven的web项目支持Eclipse，在命令行下，cd到CounterWebApp目录下，然后输入这个命令
<pre class="lang:default decode:true">mvn eclipse:eclipse -Dwtpversion=2.0</pre>
-Dwtpversion=2.0参数是必需的，这个参数指明了是web项目，然后导入到eclipse里就行了。从导入的工程ico可以看出是个web项目

图一

[![eclipse-tomcat-server]({{BASE_PATH}}/images/5c29f69c7cf928d6300dff7c9a176110ddb6c5ab.png)]({{BASE_PATH}}/images/5c29f69c7cf928d6300dff7c9a176110ddb6c5ab.png) [![final-folder]({{BASE_PATH}}/images/4876ca43cc63ce27270db95cc57c40c1e862cbe7.png)]({{BASE_PATH}}/images/4876ca43cc63ce27270db95cc57c40c1e862cbe7.png) [![project-structure]({{BASE_PATH}}/images/beffae62b8bf33f55abebbe65011f8547ea7b91f.png)](http://leaverimage.b0.upaiyun.com/2013/09/project-structure.png)

不要迷惑，如果我们只输入
<pre class="lang:default decode:true">mvn eclipse:eclipse</pre>
会转换成java项目，而不是web项目的。

好了，现在项目已经准备好部署了，eclipse里的tomcat server插件装好，然后启动项目

图二

[![eclipse-tomcat-server]({{BASE_PATH}}/images/5c29f69c7cf928d6300dff7c9a176110ddb6c5ab.png)](http://leaverimage.b0.upaiyun.com/2013/09/eclipse-tomcat-server.png)

可以通过_http://localhost:8080/CounterWebApp/_地址来访问

## 4.更新pom文件

为了使Maven项目支持Spring MVC框架，我们需要修改pom.xml文件

1.添加编译插件指定jdk6来编译

2.添加Spring框架依赖

3.升级JUnit到4.1.1
<pre class="lang:default decode:true" title="pom.xml">&lt;project xmlns="http://maven.apache.org/POM/4.0.0" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
	http://maven.apache.org/maven-v4_0_0.xsd"&gt;
	&lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
	&lt;groupId&gt;com.mkyong&lt;/groupId&gt;
	&lt;artifactId&gt;CounterWebApp&lt;/artifactId&gt;
	&lt;packaging&gt;war&lt;/packaging&gt;
	&lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;
	&lt;name&gt;CounterWebApp Maven Webapp&lt;/name&gt;
	&lt;url&gt;http://maven.apache.org&lt;/url&gt;

	&lt;properties&gt;
		&lt;spring.version&gt;3.0.5.RELEASE&lt;/spring.version&gt;
		&lt;junit.version&gt;4.11&lt;/junit.version&gt;
		&lt;jdk.version&gt;1.6&lt;/jdk.version&gt;
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
			&lt;artifactId&gt;spring-web&lt;/artifactId&gt;
			&lt;version&gt;${spring.version}&lt;/version&gt;
		&lt;/dependency&gt;

		&lt;dependency&gt;
			&lt;groupId&gt;org.springframework&lt;/groupId&gt;
			&lt;artifactId&gt;spring-webmvc&lt;/artifactId&gt;
			&lt;version&gt;${spring.version}&lt;/version&gt;
		&lt;/dependency&gt;

		&lt;dependency&gt;
			&lt;groupId&gt;junit&lt;/groupId&gt;
			&lt;artifactId&gt;junit&lt;/artifactId&gt;
			&lt;version&gt;${junit.version}&lt;/version&gt;
			&lt;scope&gt;test&lt;/scope&gt;
		&lt;/dependency&gt;
	&lt;/dependencies&gt;
	&lt;build&gt;
		&lt;finalName&gt;CounterWebApp&lt;/finalName&gt;
		&lt;plugins&gt;
			&lt;plugin&gt;
				&lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
				&lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;
				&lt;version&gt;3.0&lt;/version&gt;
				&lt;configuration&gt;
					&lt;source&gt;${jdk.version}&lt;/source&gt;
					&lt;target&gt;${jdk.version}&lt;/target&gt;
				&lt;/configuration&gt;
			&lt;/plugin&gt;
		&lt;/plugins&gt;
	&lt;/build&gt;
&lt;/project&gt;</pre>

##  5.Spring MVC 松耦合

创建Spring MVC控制器类，有两个方法，打印信息
<pre class="lang:default decode:true" title="/src/main/java/com/mkyong/controller/BaseController.java">package com.mkyong.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.ModelMap;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
@RequestMapping("/")
public class BaseController {

	@RequestMapping(value="/welcome", method = RequestMethod.GET)
	public String welcome(ModelMap model) {

		model.addAttribute("message", "Maven Web Project + Spring 3 MVC - welcome()");

		//Spring uses InternalResourceViewResolver and return back index.jsp
		return "index";

	}

	@RequestMapping(value="/welcome/{name}", method = RequestMethod.GET)
	public String welcomeName(@PathVariable String name, ModelMap model) {

		model.addAttribute("message", "Maven Web Project + Spring 3 MVC - " + name);
		return "index";

	}

}</pre>
然后，创建Spring配置文件，定义视图解析器
<pre class="lang:default decode:true" title="/src/main/webapp/WEB-INF/mvc-dispatcher-servlet.xml">&lt;beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="
        http://www.springframework.org/schema/beans     
        http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
        http://www.springframework.org/schema/context 
        http://www.springframework.org/schema/context/spring-context-3.0.xsd"&gt;

	&lt;context:component-scan base-package="com.mkyong.controller" /&gt;

	&lt;bean
		class="org.springframework.web.servlet.view.InternalResourceViewResolver"&gt;
		&lt;property name="prefix"&gt;
			&lt;value&gt;/WEB-INF/pages/&lt;/value&gt;
		&lt;/property&gt;
		&lt;property name="suffix"&gt;
			&lt;value&gt;.jsp&lt;/value&gt;
		&lt;/property&gt;
	&lt;/bean&gt;

&lt;/beans&gt;</pre>
然后更新一下web.xml文件，通过Spring的监听器ContextLoaderListener把Spring框架集成进去
<pre class="lang:default decode:true" title="/src/main/webapp/WEB-INF/web.xml">&lt;web-app xmlns="http://java.sun.com/xml/ns/javaee"
	      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	      xsi:schemaLocation="http://java.sun.com/xml/ns/javaee 
	      http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
	      version="2.5"&gt;	

	&lt;display-name&gt;Counter Web Application&lt;/display-name&gt;

	&lt;servlet&gt;
		&lt;servlet-name&gt;mvc-dispatcher&lt;/servlet-name&gt;
		&lt;servlet-class&gt;
			org.springframework.web.servlet.DispatcherServlet
		&lt;/servlet-class&gt;
		&lt;load-on-startup&gt;1&lt;/load-on-startup&gt;
	&lt;/servlet&gt;

	&lt;servlet-mapping&gt;
		&lt;servlet-name&gt;mvc-dispatcher&lt;/servlet-name&gt;
		&lt;url-pattern&gt;/&lt;/url-pattern&gt;
	&lt;/servlet-mapping&gt;

	&lt;context-param&gt;
		&lt;param-name&gt;contextConfigLocation&lt;/param-name&gt;
		&lt;param-value&gt;/WEB-INF/mvc-dispatcher-servlet.xml&lt;/param-value&gt;
	&lt;/context-param&gt;

	&lt;listener&gt;
		&lt;listener-class&gt;
			org.springframework.web.context.ContextLoaderListener
		&lt;/listener-class&gt;
	&lt;/listener&gt;
&lt;/web-app&gt;</pre>
然移动index.jsp到WEB-INF目录里，以防用户直接访问他，同时我们编辑，增加${message}变量，也就是controller控制器传过来的值到页面

最终的页面结构是：

图三

[![final-folder]({{BASE_PATH}}/images/4876ca43cc63ce27270db95cc57c40c1e862cbe7.png)](http://leaverimage.b0.upaiyun.com/2013/09/final-folder.png)

## 6.Eclipse+Tomcat

为了通过Eclipse的Tomcat插件开始/调试项目，需要再执行一次下面的命令，使得所有的依赖附加完成
<pre class="lang:default decode:true">mvn eclipse:eclipse -Dwtpversion=2.0</pre>
在没执行以前，项目依赖是空的

图四

[![web-deployment-assembly]({{BASE_PATH}}/images/73a4412bcfedbc568e2c60c2ec1bf3bca9e1d102.png)](http://leaverimage.b0.upaiyun.com/2013/09/web-deployment-assembly.png)

执行完以后，就都有了

图五

[![web-deployment-assembly-2]({{BASE_PATH}}/images/ce231a20448fb14ef64cbd33f10ce124236af4c4.png)](http://leaverimage.b0.upaiyun.com/2013/09/web-deployment-assembly-2.png)

重要！许多人就是没有第二次执行这个命令导致无法开始/调试，如果有问题，右击你的项目属性，看看依赖是否为空

## 7.Maven打包

<pre class="lang:default decode:true" title="pom.xml">&lt;project ...&gt;
	&lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
	&lt;groupId&gt;com.mkyong&lt;/groupId&gt;
	&lt;artifactId&gt;CounterWebApp&lt;/artifactId&gt;

	&lt;packaging&gt;war&lt;/packaging&gt;
	&lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;</pre>
web项目打包成war。放在target目录

进入项目目录，执行一下就可以了
<pre class="lang:default decode:true">mvn package</pre>
然后把target目录的CountWebApp.war文件拷贝，并部署到你的web容器里就行了

## 8.Demo演示

打开_http://localhost:8080/CounterWebApp/welcome_

可以看到

图六

[![demo1]({{BASE_PATH}}/images/b2247eded5f745ec924642e931b586cd75a1e4e5.png)](http://leaverimage.b0.upaiyun.com/2013/09/demo1.png)

我们传个参数进去

_http://localhost:8080/CounterWebApp/welcome/mkyong_

就可以看到效果了

图七

[![demo2]({{BASE_PATH}}/images/0856171569637fa29d743717f2b75f91913b3dee.png)](http://leaverimage.b0.upaiyun.com/2013/09/demo2.png)

9.Demo下载

[CounterWebApp.zip](http://pan.baidu.com/share/link?shareid=579597800&amp;uk=1493685990)