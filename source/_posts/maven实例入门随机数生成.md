title: Maven实例入门-随机数生成
tags:
  - java
  - 学习
  - 总结
  - 翻译
id: 3239
categories:
  - 我的翻译
date: 2013-09-19 20:50:23
---

看了很多个例子，发现这个最好，译文中会带有我的一些了理解，有问题欢迎指出。

## 0.Maven是什么？

Apache Maven，是一个软件（特别是Java软件）项目管理及自动构建工具，由Apache软件基金会所提供。基于项目对象模型（缩写：POM）概念，Maven利用一个中央信息片断能管理一个项目的构建、报告和文档等步骤。

可以看到，核心就是项目管理和自动构建了，从例子中将会体会更深。本例创建一个随机数生成程序。

## 1.从Maven模板创建项目

Maven的环境变量配置和java类似，直接添加系统变量MAVEN_HOME指向你下载的maven目录，然后将bin目录添加到path环境变量里。

在命令行下，进入到你想存储项目的位置，比如，我自己有个叫work的目录，那么我就cd到work目录，然后按下面的格式输入命令
<pre class="lang:default decode:true">mvn archetype:generate -DgroupId={project-packaging} -DartifactId={project-name} -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false</pre>
这是告诉Maven从**maven-archetype-quickstart **创建一个java项目，如果你这个参数不填，那么会列出一个列表，让你选择你想创建什么类型，比如web项目啊，啥的。

友情提示：是不是太难记了..好吧，直接输入
<pre>mvn archetype:generate</pre>
根据向导来创建把。。

比如
<pre class="lang:default decode:true">&gt;mvn archetype:generate -DgroupId=com.mkyong -DartifactId=NumberGenerator -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] -- omitted for readability
[INFO] ----------------------------------------------------------------------------
[INFO] Using following parameters for creating project from Old (1.x) Archetype: maven-archetype-quickstart:1.0
[INFO] ----------------------------------------------------------------------------
[INFO] Parameter: groupId, Value: com.mkyong
[INFO] Parameter: packageName, Value: com.mkyong
[INFO] Parameter: package, Value: com.mkyong
[INFO] Parameter: artifactId, Value: NumberGenerator
[INFO] Parameter: basedir, Value: /Users/mkyong/Documents/workspace
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] project created from Old (1.x) Archetype in dir: /Users/mkyong/Documents/workspace/NumberGenerator
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 3.917s
[INFO] Finished at: Mon Dec 17 18:53:58 MYT 2012
[INFO] Final Memory: 9M/24M
[INFO] ------------------------------------------------------------------------</pre>
这里的groupId就是包名，artifactId就是类名，以后具体的一些其他参数我希望有时间可以跟大家分享。

## 2.Maven目录结构

上面的命令第一次执行的时候会从apache网站下载maven的一些其他东西，所以务必保持联网。执行成功后，会生成一个这样的目录结构
<pre class="lang:default decode:true">NumberGenerator
   |-src
   |---main
   |-----java
   |-------com
   |---------mkyong
   |-----------App.java
   |---test
   |-----java
   |-------com
   |---------mkyong
   |-----------AppTest.java
   |-pom.xml</pre>
这里main目录是我们的程序住代码目录。源代码会放在/src/main/java/包名 目录里，而单元测试代码会放在/src/test/java/包名 目录里。当然还有一个标准的pom.xml文件会生成。这个pom文件其实类似于Ant的build.xml文件，它包含了项目的信息，从目录结构到项目插件到项目依赖，都有了。。
<pre class="lang:default decode:true" title="pom.xml">&lt;project xmlns="http://maven.apache.org/POM/4.0.0" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
	http://maven.apache.org/maven-v4_0_0.xsd"&gt;
  &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
  &lt;groupId&gt;com.mkyong&lt;/groupId&gt;
  &lt;artifactId&gt;NumberGenerator&lt;/artifactId&gt;
  &lt;packaging&gt;jar&lt;/packaging&gt;
  &lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;
  &lt;name&gt;NumberGenerator&lt;/name&gt;
  &lt;url&gt;http://maven.apache.org&lt;/url&gt;
  &lt;dependencies&gt;
    &lt;dependency&gt;
      &lt;groupId&gt;junit&lt;/groupId&gt;
      &lt;artifactId&gt;junit&lt;/artifactId&gt;
      &lt;version&gt;3.8.1&lt;/version&gt;
      &lt;scope&gt;test&lt;/scope&gt;
    &lt;/dependency&gt;
  &lt;/dependencies&gt;
&lt;/project&gt;</pre>

## 3.用Eclipse写代码

maven已经生成了一个完整的工程了，为了能够导入到eclipse里来编辑代码，我们可以把这个项目转换成eclipse可用的。

首先命令行进入NumberGenerator目录，然后输入
<pre class="lang:default decode:true">mvn eclipse:eclipse</pre>
然后就会生成Eclipse需要的所有项目文件里

图1.导入eclipse之后的效果

[![maven-single-java-project](/images/a6c0fbb8d5b17d6afc6d6fb110de27cf98be1715.png)](http://leaverimage.b0.upaiyun.com/2013/09/maven-single-java-project.png)

## 4.更新pom文件

默认的pom文件太简单了。通常我们需要添加编译插件来告诉Maven我们打算使用哪个版本的jdk来编译项目，有时候默认的版本太低了
<pre class="lang:default decode:true">&lt;plugin&gt;
		&lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
		&lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;
		&lt;version&gt;2.3.2&lt;/version&gt;
		&lt;configuration&gt;
			&lt;source&gt;1.6&lt;/source&gt;
			&lt;target&gt;1.6&lt;/target&gt;
		&lt;/configuration&gt;
&lt;/plugin&gt;</pre>
我们把source和target改成了1.6.

然后更新JUnit到4.1.1
<pre class="lang:default decode:true">&lt;dependency&gt;
		&lt;groupId&gt;junit&lt;/groupId&gt;
		&lt;artifactId&gt;junit&lt;/artifactId&gt;
		&lt;version&gt;4.11&lt;/version&gt;
		&lt;scope&gt;test&lt;/scope&gt;
&lt;/dependency&gt;</pre>
最终我们的pom文件如下
<pre class="lang:default decode:true" title="pom.xml">&lt;project xmlns="http://maven.apache.org/POM/4.0.0" 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
	http://maven.apache.org/maven-v4_0_0.xsd"&gt;
	&lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
	&lt;groupId&gt;com.mkyong&lt;/groupId&gt;
	&lt;artifactId&gt;NumberGenerator&lt;/artifactId&gt;
	&lt;packaging&gt;jar&lt;/packaging&gt;
	&lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;
	&lt;name&gt;NumberGenerator&lt;/name&gt;
	&lt;url&gt;http://maven.apache.org&lt;/url&gt;
	&lt;dependencies&gt;
		&lt;dependency&gt;
			&lt;groupId&gt;junit&lt;/groupId&gt;
			&lt;artifactId&gt;junit&lt;/artifactId&gt;
			&lt;version&gt;4.11&lt;/version&gt;
			&lt;scope&gt;test&lt;/scope&gt;
		&lt;/dependency&gt;
	&lt;/dependencies&gt;

	&lt;build&gt;
	  &lt;plugins&gt;
		&lt;plugin&gt;
			&lt;groupId&gt;org.apache.maven.plugins&lt;/groupId&gt;
			&lt;artifactId&gt;maven-compiler-plugin&lt;/artifactId&gt;
			&lt;version&gt;2.3.2&lt;/version&gt;
			&lt;configuration&gt;
				&lt;source&gt;1.6&lt;/source&gt;
				&lt;target&gt;1.6&lt;/target&gt;
			&lt;/configuration&gt;
		&lt;/plugin&gt;
	  &lt;/plugins&gt;
	&lt;/build&gt;

&lt;/project&gt;</pre>

## 5.更新业务逻辑

TDD开发的理念告诉我们先写测试，我们打开AppTest.java文件。
<pre class="lang:default decode:true" title="AppTest.java">package com.mkyong;

import org.junit.Assert;
import org.junit.Test;

public class AppTest {

	@Test
	public void testLengthOfTheUniqueKey() {

		App obj = new App();
		Assert.assertEquals(36, obj.generateUniqueKey().length());

	}
}</pre>
然后修改App.java的代码
<pre class="lang:default decode:true" title="App.java">package com.mkyong;

import java.util.UUID;

/**
 * Generate a unique number
 *
 */
public class App 
{

    public static void main( String[] args )
    {
        App obj = new App();
        System.out.println("Unique ID : " + obj.generateUniqueKey());
    }

    public String generateUniqueKey(){

    	String id = UUID.randomUUID().toString();
    	return id;

    }
}</pre>

## 6.Maven打包

现在，我们要使用Maven来编译我们的项目，并且打包成一个jar文件，记得pom.xml文件吗？在这个文件里我们定义了打包的类型
<pre class="lang:default decode:true" title="pom.xml">&lt;project ...&gt;
	&lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
	&lt;groupId&gt;com.mkyong&lt;/groupId&gt;
	&lt;artifactId&gt;NumberGenerator&lt;/artifactId&gt;

	&lt;packaging&gt;jar&lt;/packaging&gt;
	&lt;version&gt;1.0-SNAPSHOT&lt;/version&gt;
	&lt;!--</pre>
在项目目录(也就是NumberGenerator目录)的命令行下，输入
<pre class="lang:default decode:true">mvn package</pre>
然后就会看到：
<pre class="lang:default decode:true" title="package">&gt; mvn package
[INFO] Scanning for projects...
[INFO]                                                                         
[INFO] ------------------------------------------------------------------------
[INFO] Building NumberGenerator 1.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] ...omitted for readability

-------------------------------------------------------
 T E S T S
-------------------------------------------------------
Running com.mkyong.AppTest
Tests run: 1, Failures: 0, Errors: 0, Skipped: 0, Time elapsed: 0.078 sec

Results :

Tests run: 1, Failures: 0, Errors: 0, Skipped: 0

[INFO] 
[INFO] --- maven-jar-plugin:2.3.1:jar (default-jar) @ NumberGenerator ---
[INFO] Building jar: /Users/mkyong/Documents/workspace/NumberGenerator/target/NumberGenerator-1.0-SNAPSHOT.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 3.064s
[INFO] Finished at: Mon Dec 17 23:38:05 MYT 2012
[INFO] Final Memory: 15M/135M
[INFO] ------------------------------------------------------------------------</pre>
程序开始编译，并且执行了单元测试，然后在target目录下生成了jar包，最后，程序目录结构如下：

[![maven-single-java-project-final](/images/5a515167b36d7c7b74b5643e421cea11097736eb.png)](http://leaverimage.b0.upaiyun.com/2013/09/maven-single-java-project-final.png)

## 7.执行Demo

运行生成的jar文件，还在在NumberGenerator目录下，输入如下命令
<pre class="lang:default decode:true">&gt; java -cp target/NumberGenerator-1.0-SNAPSHOT.jar com.mkyong.App
Unique ID : f1947107-2deb-4926-a635-ea3db61453e8
&gt; java -cp target/NumberGenerator-1.0-SNAPSHOT.jar com.mkyong.App
Unique ID : 98ed9e1c-d1bc-47f1-847d-64db451ce0ff</pre>
后面的com.mkyong.App就是包名+类名，也就是类的全名了

## 8.Demo下载

[ Maven-NumberGenerator.zip](http://pan.baidu.com/share/link?shareid=846280173&amp;uk=1493685990)

&nbsp;

基本上一个完整的的示例就做完了，希望你有所收获，后续会更新一些Maven的的东西。欢迎支持。中秋快乐。

&nbsp;

&nbsp;