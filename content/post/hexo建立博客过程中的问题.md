---
title: hexo建立博客过程中的问题
date: 2015-01-13 23:21:04
tags: 
  - wordpress
categories: 学习笔记
---

最近博客越来越慢了.然后一直也有markdown写文章的想法.于是花了点时间把博客迁到了hexo+github+gitcafe的组合上.
##安装Git##
下载 msysgit 并执行即可完成安装。
##安装Node.js##
在 Windows 环境下安装 Node.js 非常简单，仅须下载安装文件并执行即可完成安装。
##安装hexo##
利用 npm 命令即可安装。（在任意位置点击鼠标右键，选择Git bash）

`npm install -g hexo`

安装完成后,找个目录,比如d:/blog下面.右键,git bash,然后执行
`hexo init`
这样就会自动生成所有的目录结构了然后在安装一下依赖包:
`npm install`
然后执行
`hexo generate` 用来根据md文件生成对应的html文件.执行
`hexo server` 则启动本地服务器.打开
127.0.0.1:4000端口查看

本地查看没啥问题.就需要部署到git上了.先配置一下.
在d:/blog下面的_config.xml中配置远程git的服务器.现在已经支持同时部署到github和gitcafe.在末尾添加
```
deploy:
  type: git
  repo:
    github: git@github.com:yourname/yourname.github.io.git,master
    gitcafe: git@gitcafe.com:yourname/yourname.git,gitcafe-pages

```
其中的yourname就是你的名字

然后执行
`hexo deploy`
即可.让输入啥就输入啥.
下面是命令的简写
`hexo g` == `hexo generate`
`hexo d` == `hexo deploy`
`hexo s` == `hexo server`
`hexo n` == `hexo new`

##网站主题配置##
同样的,在d:/blog下面执行
`git clone https://github.com/wuchong/jacman.git themes/jacman`
即可复制主题.
然后修改_config.xml文件.在里面找到theme 设置为
`theme: jacman`
注意冒号后面有空格

##wordpress迁移##
1. 导出文章
 登录wordpress管理控制台，选择工具->导出，再选择文章。点击下载导出的文件，就可以得到一个名称类似wordpress.2015-01-12.xml的文件。
2. 安装迁移插件
 `npm install hexo-migrator-wordpress --save`执行安装
3. 导入文章
  `hexo migrate wordpress source` 这里source就是刚才那个文件的地址,绝对路径或者相对路径都行.导出的中文可能有编码.手动改一下,或者写个脚本简单转换一下.
4. 导入图片
 经过上面的步骤,会发现图片还是原来的连接.这里看这个[导入图片插件](http://catx.me/2014/03/07/hexo-migrator-image/ "导入图片")使用

##其他问题##
[github连接出现Bad file number问题](http://rangercyh.blog.51cto.com/1444712/749490)
[使用GitHub来管理博客源文件](http://wuchong.me/blog/2014/01/17/use-github-to-manage-hexo-source/)
[Hexo免输入密码部署到github](http://www.foreverpx.cn/2014/09/25/Hexo%E5%85%8D%E8%BE%93%E5%85%A5%E5%AF%86%E7%A0%81%E9%83%A8%E7%BD%B2%E5%88%B0github/)
[教程:github设置多账号](http://never.doubting.me/2013/04/18/2013-04-18-set-multiply-accounts-on-github/)