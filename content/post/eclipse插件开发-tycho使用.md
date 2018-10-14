---
title: eclipse插件开发-tycho使用
date: 2016-05-21 18:55:27
tags:
  - 插件
  - 技术
  - 笔记
categories:
  - 学习笔记
---

不说废话，直接上。本文主要包括tycho的使用，版本号的自动更新。

eclipse插件开发中，依赖的管理是个问题。如果采用常规的搞个lib目录，然后加到MF文件中。一旦依赖越来越多。或者要更换版本号就变得非常麻烦。所以要用到tycho

首先说明一下目录结构。一个parent的maven工程，一个plugin工程，。两个features。一个是deps的。一个是plugin的，这个依赖deps是独立的mvn项目。可以先不用管。一个deps依赖工程（这个依赖工程独立）。
在主pom下。

步骤如下。

关于依赖部分

1. 新建一个普通的mvn工程,比如deps。打包类型写成`<packaging>bundle</packaging>`,同时在pom.xml中添加build部分

````
<build>
	<plugins>
		<plugin>
			<groupId>org.apache.felix</groupId>
			<artifactId>maven-bundle-plugin</artifactId>
			<version>3.0.1</version>
			<extensions>true</extensions>
			<configuration>
				<niceManifest>true</niceManifest>
				<manifestLocation>META-INF</manifestLocation>
				<instructions>
					<Bundle-SymbolicName>${project.artifactId}</Bundle-SymbolicName>
					<Embed-Dependency>*</Embed-Dependency>
					<Embed-Transitive>true</Embed-Transitive>
					<Embed-Directory>lib</Embed-Directory>
					<Bundle-ClassPath>{maven-dependencies}</Bundle-ClassPath>
					<_exportcontents>*</_exportcontents>
					<_failok>true</_failok>
					<_nouses>true</_nouses>
					<Import-Package></Import-Package>
				</instructions>
			</configuration>
		</plugin>
	</plugins>
</build>

````
然后正常添加一些依赖到这个工程中。然后执行一下mvn clean install ，你就会发现本地mvn仓库生成了一个jar包，这个jar里直接打包了所有的jar


关于插件部分

1. 新建一个类型为pom的parent工程。用来包含下面的子工程，通过
````
<modules>
	<module>xxx.plugin.1</module>
	<module>xxx.plugin.2</module>
</modules>
````
来管理。同时 添加如下的插件

````
<properties>
	<tycho.version>0.24.0</tycho.version>
</properties>

<build>
	<plugins>
		<plugin>
			<groupId>org.apache.maven.plugins</groupId>
			<artifactId>maven-source-plugin</artifactId>
			<executions>
				<execution>
					<id>attach-sources</id>
					<phase>none</phase>
				</execution>
			</executions>
			<version>2.4</version>
		</plugin>
		<plugin>
			<groupId>org.eclipse.tycho</groupId>
			<artifactId>tycho-maven-plugin</artifactId>
			<version>${tycho.version}</version>
			<extensions>true</extensions>
		</plugin>
		<plugin>
			<groupId>org.eclipse.tycho</groupId>
			<artifactId>target-platform-configuration</artifactId>
			<version>${tycho.version}</version>
			<configuration>
				<pomDependencies>consider</pomDependencies>
			</configuration>
		</plugin>
	</plugins>
</build>
````

2. 新建一个专门用来管理依赖的features，这样用户就可以安装这个features，来把所有的依赖安装到eclipse插件目录了。packing是eclipse-feature

3. 新建一个plugin，新建一个这个插件对应的feature。然后通过右键转换成mvn工程。如图

![mvn转换](/images/convert2mvn.png)

4. 新建一个repository项目，这个项目主要就是为了发布，生成发布相关的文件。新建Update set project。然后添加两个features。转换成mvn。其中packing改成eclipse-repository。
注意，这里的版本号必须对应。比如plugin.xml的版本是1.5.0.qualifier,那么对应pom中必须是1.5.0-SNAPSHOT



然后去根目录下执行一下mvn clean install，就会在repository目录生成需要部署的zip文件或者web site文件

遗留问题是以后要是升级版本号怎么办。。不用担心。。主pom中添加
````
<plugin>
   <groupId>org.eclipse.tycho</groupId>
   <artifactId>tycho-versions-plugin</artifactId>
   <version>${tycho.version}</version>
</plugin>
````
然后 在根目录执行 `mvn -Dtycho.mode=maven org.eclipse.tycho:tycho-versions-plugin:set-version -DnewVersion=1.6.0` 即可全部替换pom和plugin的版本号


另外如果项目导入后在eclipse中报错，一般是因为缺少了上面说的那个依赖。可以直接复制到eclipse的plugin依赖目录。或者在mvn clean install之后，通过安装 依赖feature的方式解决。




附上一些文档

[EclipseTycho](http://www.vogella.com/tutorials/EclipseTycho/article.html)
[Building OSGi project with Maven and tycho](http://o7planning.org/web/fe/default/en/document/9117/building-osgi-project-with-maven-and-tycho)
