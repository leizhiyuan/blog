title: 《Spring揭秘》读书笔记-第一章Spring框架的由来
tags:
  - java
  - spring
  - 设计
  - 软件
id: 3683
categories:
  - 学习笔记
date: 2014-06-30 20:40:40
---

1.框架的由来

倡导J2EE轻量级应用解决方案

框架总结结构

[![Spring Framework](/images/8775d9e40dbc29240061edd2b9b6f76990f39cad.jpg)](http://leaverimage.b0.upaiyun.com/2014/06/Spring-Framework.jpg)
整个Spring架构建立在Core核心模块上，是基础，该模块中，有一个IoC容器的实现，用来以依赖注入的方式管理对象之间的依赖关系。Core中还有一些气筒工具类，比如IO工具类

从图中看到，AOP模块，提供了轻便二强大的AOP框架，一AOP的形式增强POJO的能力，弥补OOP/OOSD的不足，采用Proxy模式，与IoC容器相结合

继续向上看，在Core和AOP之上，提供了完毕的数据访问和事务管理的抽象，其中，对JDBC API的最佳实践简化了API的使用，还未ORM产品提供了统一的支持，

为了简化Java EE的服务，比如JNDI，JMS等等，Spring还提供了这些的集成服务，

最后就是Web模块，提供了一套自己的Web MVC框架，上层模块依赖于下层模块，水平之间的模块彼此基本可以认为独立。

Spring不仅仅是容器，更是开发任何Java应用的框架，

Spring 框架之上衍生的产品包括不限于Spring Web Flow,Spring Web Services,Spring Security,Spring Integration，Spring Rich Client 等等等等