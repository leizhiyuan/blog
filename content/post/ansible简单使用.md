---
title: ansible简单使用
date: 2016-03-25 19:51:07
tags:
  - 运维
  - python
  - 学习
categories:
  - 学习笔记
---

由于线下机器太多.有没有日志平台,所以查询日志比较麻烦.发现了ansible,按照官方文档(ubuntu)
````
$ sudo apt-get install software-properties-common
$ sudo apt-add-repository ppa:ansible/ansible
$ sudo apt-get update
$ sudo apt-get install ansible
````

一步步执行,第二步执行的时候,可能会报错
````
sudo: add-apt-repository: command not found
````

这时候.先执行
````
$ sudo add-apt-repository ppa:git-core/ppa
$ sudo apt-get update
````

然后接着执行上面的第二步就行了..


安装完成后,配置集群
````
cat /etc/ansible/hosts
````
````
[servergroup1]
192.168.1.1 ansible_ssh_user=root ansible_ssh_pass=root
[servergroup2]
192.168.2.1 ansible_ssh_user=root ansible_ssh_pass=root
````

后面的账号和密码,如果你使用ssh key登陆的话就不需要了.但是如果有很多机器,需要加到known_hosts就太多了.
这时候可以参考[Batch_key](https://github.com/linuxyan/linuxyan/blob/master/python/Batch_create_pub_key/Batch_key.py)
这个脚本.稍微修改一下,就能批量生成了


然后就是执行命令了

ansible常见用法为ansible host-pattern -m 模块 -a 命令，host-pattern类似于简化的正则表达式，而模块可以通过ansible-doc -l命令来查询。下面是一些常用模块的使用方法：

安装软件：`ansible servergroup1 -m apt -a 'name=gcc state=present' 或者ansible local -m yum -a "name=nmap state=installed"`

执行命令：`ansible servergroup1 -m shell -a 'uptime'`

拷贝文件：`ansible servergroup1 -m copy -a  'src=/tmp/server dest=/tmp/server'`

文件属性：`ansible servergroup1 -m file -a  'dest=/tmp/server mode=755 owner=root group=root'`

还有一个playbook的,看上去就是一个任务定义.我也暂时用不上..

参考文档:

1. http://docs.ansible.com/ansible/intro_installation.html

2. http://lifeonubuntu.com/ubuntu-missing-add-apt-repository-command/

3. http://www.cnblogs.com/feisky/p/4102613.html