---
title: python3.5 安装 Paramiko
date: 2015-12-28 19:17:25
tags: 
 - java 
 - 学习 
 - 序列化 
categories: 学习笔记
---

最近由于一些需求,要搞一下python,于是周末搞了搞.要连接服务器,进行一些服务器的操作,于是安装这个Paramiko包,

直接`pip install paramiko` 结果.报错,最关键的一句是:
````
error: Unable to find vcvarsall.bat
````

google一圈.最终找到一种最简单地方法.其他的安装vs.安装MinGW都太复杂了.

1. 安装PyCrypto 第三方版
  因为paramiko依赖PyCrypto,上面那个错就是他报错出来的.安装[PyCrypto第三方版](https://github.com/sfbahr/PyCrypto-Wheels)
````java
pip install --use-wheel --no-index --find-links=https://github.com/sfbahr/PyCrypto-Wheels/raw/master/pycrypto-2.6.1-cp35-none-win_amd64.whl pycrypto
`````
安装完成后,再次安装paramiko即可.
2.修改nt.py

  安装完上面的步骤,写一个简单的程序测试下

````java
#-*- coding: utf-8 -*-
#!/usr/bin/python 
import paramiko
import threading

def ssh2(ip,username,passwd,cmd):
    try:
        ssh = paramiko.SSHClient()
        ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
        ssh.connect(ip,22,username,passwd,timeout=5)
        for m in cmd:
            stdin, stdout, stderr = ssh.exec_command(m)
            out = stdout.readlines()
            #屏幕输出
            for o in out:
                print(o)
        print('%s\tOK\n'%(ip))
        ssh.close()
    except :
        print('%s\tError\n'%(ip))


if __name__=='__main__':
    cmd = ['find /home/admin/logs/ -mtime +3 -name \'*.log.*\' -exec rm -rf {} \;']#你要执行的命令列表
    username = "admin"  #用户名
    passwd = "password"    #密码
    threads = []   #多线程
    ip = "127.0.0.1"
    print("Begin......")
    a=threading.Thread(target=ssh2,args=(ip,username,passwd,cmd))
    a.start() 
    input()

````

运行报错.
`ImportError: No module named 'winrandom'`

so一下..找到一个办法
[http://stackoverflow.com/questions/24804829/another-one-about-pycrypto-and-paramiko](http://stackoverflow.com/questions/24804829/another-one-about-pycrypto-and-paramiko)

找到python3.5的安装目录的
`Lib\site-packages\Crypto\Random\OSRNG`的nt.py文件将

````java
import winrandom
````

改成
````java
from . import winrandom

````

再次运行ok.非常简单