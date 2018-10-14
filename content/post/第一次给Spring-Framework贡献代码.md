---
title: 第一次给Spring-Framework贡献代码
tags:
  - spring
  - 工作
  - 软件
id: 3732
categories:
  - 学习笔记
date: 2014-08-21 07:46:26
---

鲁肃说建议去看Spring框架的代码,之前其实我想看来着,不过一看到还要gradlew,换jdk就好麻烦.这次各种折腾把代码fork下来,然后安装`gradlew `,然后转换成eclipse支持的,期间升级了eclipse版本和jdk版本到8.否则会有个方法不支持,

流程很简单,先fork一下代码,然后自己改好提交上去,再去Spring框架的pull request请求一下.等大牛合并就行了.

刚开始看测试用例,我当时看到这个方法调用了一个下线的方法.于是改了一下.提交上去之后,**[sbrannen](https://github.com/sbrannen) ** 回复说:
> while you're at it, why don't you go ahead fix the related deprecated issues in all of the test classes in `spring-jdbc`
于是我把spring-jbdc包下面的几个方法调用都改掉了.记得谁说过,任何事情都不是别人的事情,你发现了你就要去做,不要等着别人去做,不会就去学.于是再次修改提交,sbrannen 问我有没有签[CLA](https://support.springsource.com/spring_committer_signup.),这个坑爹的网站挂了.等了几天,昨天终于ok了.今天代码已经合并.

截图留念:

[![spring](/images/56d25e223f79e6ffdb3162ac7658124363f1d82e.png)](http://leaverimage.b0.upaiyun.com/2014/08/spring.png)

https://github.com/spring-projects/spring-framework/commits?author=leizhiyuan

今天要吃两个煎饼果子.