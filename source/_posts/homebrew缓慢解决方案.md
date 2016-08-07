title: homebrew缓慢解决方案
date: 2016-08-07 17:29:55
tags:
  - 工具
  - 生产力
  - 记录
categories:
  - 学习笔记
---

mac 下使用 homebrew 作为包管理工具是非常好的. brew 用来安装非 gui 界面的程序. cask 用来安装 gui 界面的程序.但是这两个是使用的源在国外.所以你懂得..


1.替换 homebrew 默认源

````
cd /usr/local
git remote set-url origin git://mirrors.ustc.edu.cn/brew.git
````
这里注意记一下以前的默认源.防止以后想换回来..

默认源是
`https://github.com/Homebrew/brew`


2.替换homebrew bottles默认源
````
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bashrc
````
这里的.bashrc根据自己的情况替换.我是 zsh,就写到.zshrc 文件.
