title: Maven库依赖机制及添加自定义库
tags:
  - java
  - maven
  - 开发
  - 翻译
id: 3264
categories:
  - 我的翻译
date: 2013-09-19 22:49:42
---

# 一.Maven库依赖机制

Maven的库依赖机制可以帮助我们自动下载依赖的库文件，并且还能更新版本。。

考虑一个情境来理解机制的工作原理，假设我们要使用Log4J库作为项目的日志记录。以下是我们要做的

## 1.传统的方式

1）访问Log4J网站[http://logging.apache.org/log4j/2.x/](http://logging.apache.org/log4j/2.x/)

2）下载Log4J的jar包

3）复制进项目的classpath里

4）手工包含到项目的依赖里

看到没，你要从头做到尾，如果Log4J更新了，你得再来一遍。。

## 2.Maven的方式

1）需要知道Log4J的Maven坐标(coordinates,这个暂时没想到好名字)，就是这样的东西
<pre class="lang:default decode:true">&lt;groupId&gt;log4j&lt;/groupId&gt;
	&lt;artifactId&gt;log4j&lt;/artifactId&gt;
	&lt;version&gt;1.2.14&lt;/version&gt;</pre>
然后Maven就会自动下载1.2.14版本的Log4J了。如果version这个元素没有，那么如果有了新版本，Maven会自动下载新版本的。

2）在pom.xml里声明这个Maven坐标
<pre class="lang:default decode:true">&lt;dependencies&gt;
    &lt;dependency&gt;
	&lt;groupId&gt;log4j&lt;/groupId&gt;
	&lt;artifactId&gt;log4j&lt;/artifactId&gt;
	&lt;version&gt;1.2.14&lt;/version&gt;
    &lt;/dependency&gt;
&lt;/dependencies&gt;</pre>
3）当Maven编译或者build的时候，Log4J会自动下载，放入本地仓库里

4）Maven全部接管

## 3.解释

搜索顺序前面说过了。本地-》中央-》远程仓库

Maven坐标咋来的，当然去[中央仓库搜索](http://search.maven.org/)之了，搜索结果清晰的令人发指

# 二.添加自定义库到本地仓库

有两个情况，需要我们包含自定义的库到本地仓库里

1是你想使用的jar文件不再中央仓库

2是你创建了一个自定义的jar包，需要另一个项目使用这个jar

比如，[kaptche](http://code.google.com/p/kaptcha/)，一个第三方的java库，生成验证码，现在中央仓库没有了。

我们想要加到本地仓库里

## 1.mvn安装

下载[kaptche](http://code.google.com/p/kaptcha/)，解压并且拷贝kaptcha-version.jar到其他任何你喜欢的地方，比如c盘，然后输入如下格式的命令
<pre class="lang:default decode:true">mvn install:install-file -Dfile=c:\kaptcha-{version}.jar -DgroupId=com.google.code 
-DartifactId=kaptcha -Dversion={version} -Dpackaging=jar</pre>
这里我这样输入
<pre class="lang:default decode:true">D:\&gt;mvn install:install-file -Dfile=c:\kaptcha-2.3.jar -DgroupId=com.google.code 
-DartifactId=kaptcha -Dversion=2.3 -Dpackaging=jar
[INFO] Scanning for projects...
[INFO] Searching repository for plugin with prefix: 'install'.
[INFO] ------------------------------------------------------------------------
[INFO] Building Maven Default Project
[INFO]    task-segment: [install:install-file] (aggregator-style)
[INFO] ------------------------------------------------------------------------
[INFO] [install:install-file]
[INFO] Installing c:\kaptcha-2.3.jar to 
D:\maven_repo\com\google\code\kaptcha\2.3\kaptcha-2.3.jar
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESSFUL
[INFO] ------------------------------------------------------------------------
[INFO] Total time: &lt; 1 second
[INFO] Finished at: Tue May 12 13:41:42 SGT 2009
[INFO] Final Memory: 3M/6M
[INFO] ------------------------------------------------------------------------</pre>
这样完成了。kaptcha已经存在本地库了

## 2.pom.xml配置一下

<pre>&lt;dependency&gt;
      &lt;groupId&gt;com.google.code&lt;/groupId&gt;
      &lt;artifactId&gt;kaptcha&lt;/artifactId&gt;
      &lt;version&gt;2.3&lt;/version&gt;
 &lt;/dependency&gt;</pre>
这个前面说过了，Maven坐标嘛