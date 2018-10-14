---
title: kafka快速上手
date: 2015-09-03 22:29:57
tags:
 - 中间件
 - 学习
categories: 学习笔记
---

## 简单介绍 ##
kafka是一个分布式消息中间件,在kafka中主要涉及到四个基本名词:
**Topic**
Kafka将消息种子分门别类， 每一类的消息称之为一个主题(Topic).

**Producer**
发布消息的对象称之为主题生产者.

**Consumer**
订阅消息并处理消息的对象称之为主题消费者

**Broker**
已发布的消息保存在一组服务器中，称之为Kafka集群。集群中的每一个服务器称为一个代理(Broker). 消费者可以订阅一个或多个主题，并从Broker拉数据(注意是拉,不是pull,)，从而消费这些已发布的消息。

## 安装(以windows为例) ##
安装非常简单,从这里[下载](http://kafka.apache.org/downloads.html),下载完成后解压到一个目录就好了.

## 简单使用 ##
首先使用kafka的一个流程就是生产者生产消息,发送给kafka集群,然后消费者从kafka集群中获取消息进行消费.
要启动kafka需要先启动zookeeper,因为ZooKeeper是通过冗余服务实现高可用性的,也就是说在分布式环境中,如何保证kafka集群的高可用.zookeeper会来做leader选取,当消费者准备发消息时,会从zookeeper中获取一个可用的消息服务器地址,然后连接进行发送,保证党集群内有服务器宕机并不影响整体的使用.
![来自slideshare的一张图](/images/kafka_transfer.jpg)

1.启动自带的简易zookeeper.
进行解压目录的bin/windows目录
`zookeeper-server-start.bat ../../config/zookeeper.properties`

执行命令启动,从zookeeper.properties中会看到.zookeeper会开发一个clientPort=2181,2181的端口给消费者使用,其实也可以给生产者使用,但是在0.8.0版本后，producer不再通过zookeeper连接broker, 而是通过brokerlist（192.168.0.1:9092,192.168.0.2:9092,192.168.0.3:9092配置,直接和broker连接，只要能和一个broker连接上就能够获取到集群中其他broker上的信息,绕过了zookeeper.

2.启动kafka服务
`kafka-server-start.bat ../../config/server.properties` 执行启动,另一个命令行窗口,同样的.查看配置问题,会知道kafka的服务会在port=9092 ,9092端口打开.

3.注册一个topic
 `kafka-topics.bat --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test`
 这个命令中,create表示创建.zookeeper 和后面的地址表示kafka使用本机2181端口开放的zookeeper保持高可用.replication-factor表示消息只冗余一份,目前我们只有一个kafka机器,broker,partitions 表示一份分区,分区是kafka的另一个概念,大致是说,同一topic内部的消息按照一定的key和算法被分区(partition)存储在不同的位置上，这个下次写好了.这样已经在kafka注册了一个名为test的消息topic了.

4.使用简易的控制台生产者模拟
`kafka-console-producer.bat --broker-list localhost:9092 --topic test`
前面说过了.新版本生产者直接通过brokerlist来连接kafka,目前只有一台,所以就一个地址,准备向test这个topic发送消息.

5.使用简易的控制台消费者模拟
`kafka-console-consumer.bat --zookeeper localhost:2181 --topic test`
这个前面也说过了.消费者使用zookeeper获取可用的broker列表,然后拉去消息,并且还有一些offset同步的问题.和分区,文件存储一起的一个概念,下次写.

6.开始生产和消费消息
至此,已经开了四个控制台窗口了..在producer窗口里,随便打几个字,然后enter,在消费者的窗口里将会显示出来.
![实际测试图](/images/kafka_demo.jpg)

## 其他问题 ##
实际可能不那么顺利,如果你启动kafka或者其他应用的时候,有错误提示,提示无法创建虚拟机vm这样的.那么修改一下对应的bat脚本.就好了
![启动错误](/images/kafka_start_error.jpg),vm的heap申请是1G,如果你机器内存不够,改成512M,或者更小的就好了.

## 更多文档 ##
1. [官方文档](http://kafka.apache.org/documentation.html)
2. [kafka快速入门](http://colobu.com/2014/08/06/kafka-quickstart)
