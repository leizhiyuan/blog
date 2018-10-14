---
title: mac日志批量查询配置
date: 2016-10-05 10:13:05
tags: 
 - java 
 - 学习 
 - mac
categories: 学习笔记
---

由于公司线下机器非常多,导致每次查日志变得非常痛苦.线下的trace平台大部分时候还是可用的.但是有时候需要本机来批量查询.方案就是批量分发ssh key,实现免登.然后luit实现编码转换,这个主要是公司的机器编码有差异.历史原因.


## 0. 准备

   先要安装pssh,expect,ssh-copy-id.
   ````  	
	brew install pssh
	brew install homebrew/dupes/expect
	brew install ssh-copy-id
   ````

   另外安装luit的安装参考这里[luit安装](http://www.jianshu.com/p/69382cb499db)
## 1. 生成ssh key ,并批量copy

   生成ssh key比较简单.`ssh-keygen -t rsa -C "your_email@example.com"`,直接使用git的ssh key也是可以的.然后保存下面这个脚本为pscopy.sh,
 ````
#!/bin/bash
FILE=`cat ~/host.txt`
for ip in $FILE;do
expect -c "
spawn ssh-copy-id $ip
    expect {
        \"*yes/no*\" {send \"yes\r\";exp_continue}
        \"*password*\" {send \"pass\r\";exp_continue}
        \"*password*\" {send \"pass\r\";}
}
"
done

 ````

   然后执行一下sh pscopy.sh,注意,host.txt要保证存在.格式是user@address.一行一个,中间的paas要改成user的密码,这样就会使用指定的用户密码,自动copy ssh key了.
完成上面的步骤之后,ssh user@address 就可以免登了.


## 2. 写一个简单的pssh脚本

````
#!/bin/bash

encoding=
key=
command=
file=
usage()
{
    echo "Usage: `basename $0` [-f filename]  [-c encoding] [-k keyword]"
    exit 1
}

while getopts :f:c:k: opt
do  
    case $opt in
        c)  
            encoding=$OPTARG
            ;;
        :)
            echo "-$OPTARG needs an argument"
            ;;
        k)  
            key=$OPTARG
            ;;
        f)
            file=$OPTARG
            ;;
        *)  
            echo "-$opt not recognized"
            usage
            ;;
    esac
done

if [ -z "$encoding" ]; then   #该脚本必须提供-d选项
    encoding="gbk"
fi

if [ -z "$file" ]; then   #该脚本必须提供-d选项
    file="~/hosts.txt"
fi
if [ -z "$key" ]; then   #该脚本必须提供-d选项
  usage
fi
command="pssh -h $file -P \"find /home/admin/logs/ -name '*.log'|xargs grep $key --col\""
luit -encoding "${encoding}" "${command}"

````

保存这个脚本为pssh.sh,然后用法如上.之后就可以通过 pssh.sh -f host.txt -c gbk -k keyword来批量查询了