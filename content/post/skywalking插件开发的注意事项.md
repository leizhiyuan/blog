---
title: skywalking插件开发的注意事项
date: 2018-05-13 12:34:32
tags:
  - 开发
categories:
  - 学习笔记
---

最近蚂蚁金服中间件开源了 sofa 相关的部分组件,比如 [rpc](https://github.com/alipay/sofa-rpc),欢迎大家参与讨论贡献,为 rpc 做链路适配的时候,skywalking 现在快到5.0版本了. 已经不支持 h2暂时,开发过程中了,环境的搭建.以下部分字段不能少.否则外面无法连接 es.

```
docker run -d -p 9200:9200 -p 9300:9300 -e "network.host=0.0.0.0" -e "transport.host=0.0.0.0"  -e "network.publish_host=0.0.0.0"  -e "xpack.security.enabled=false" -e "network.bind_host=0.0.0.0" -e "discovery.type=single-node" docker.elastic.co/elasticsearch/elasticsearch:5.5.0
```
这条命令会搞定.


同时 application.yml 文件中

```
storage:
  elasticsearch:
    cluster_name: docker-cluster
    cluster_transport_sniffer: false
    cluster_nodes: localhost:9300
    index_shards_number: 2
    index_replicas_number: 0
    ttl: 70
``` 

这一段改一下.


其他的一些网上已有的文档,可以看看.[SkyWalking 源码分析 —— 调试环境搭建](http://www.iocoder.cn/SkyWalking/build-debugging-environment/)

