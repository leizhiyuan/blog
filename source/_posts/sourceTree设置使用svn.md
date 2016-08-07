title: sourceTree设置使用svn
date: 2016-08-06 09:28:01
tags:
  - 工具
  - 生产力
  - 记录
categories:
  - 学习笔记
---

mac 下面不想安装多个 GUI 的 svn 客户端.所以使用 sourceTree 来做.

sourceTree 对于 svn 只能从远程 url 拷贝.不能从本地来.所以在 gui 页面进行添加

但是会发现报错

````
Can't locate SVN/Core.pm in @INC (you may need to install the SVN::Core module) (@INC contains:
````

网上搜了一下.原因是 Perl 升级后 版本路径不对.于是执行

````
 sudo ln -s /Applications/Xcode.app/Contents/Developer/Library/Perl/5.18/darwin-thread-multi-2level/SVN /System/Library/Perl/Extras/5.18/SVN
 sudo ln -s /Applications/Xcode.app/Contents/Developer/Library/Perl/5.18/darwin-thread-multi-2level/auto/SVN/ /System/Library/Perl/Extras/5.18/auto/SVN
````

这里就是创建两个软连接.以便 sourceTree 识别.这里注意.如果你装了 Xcode 的 CommandLineTools, 而不是完整的 Xcode.
那么你的目录是没有这个原始文件的所以需要执行的

````
sudo ln -s /Library/Developer/CommandLineTools/Library/Perl/5.18/darwin-thread-multi-2level/SVN /System/Library/Perl/Extras/5.18/SVN
sudo ln -s /Library/Developer/CommandLineTools/Library/Perl/5.18/darwin-thread-multi-2level/auto/SVN/ /System/Library/Perl/Extras/5.18/auto/SVN

````

但是执行的时候还是会报错.因为新版本的 mac系统.已经不允许在 System 目录写文件了.除非关闭安全选项.这就得不偿失了.

但是从stackexchange说法看.


````
mkdir /Library/Perl/5.18/auto

sudo ln -s /Library/Developer/CommandLineTools/Library/Perl/5.18/darwin-thread-multi-2level/SVN /Library/Perl/5.18/SVN

sudo ln -s /Library/Developer/CommandLineTools/Library/Perl/5.18/darwin-thread-multi-2level/auto/SVN /Library/Perl/5.18/auto/SVN
````

这个方法也是可以的.就是使用另一个目录作为软连接的目录.测试通过.同理,上面的真实目录根据你装的是 Xcode 还是 CommandLineTools 来替换.记录备用.



参考:http://apple.stackexchange.com/questions/208300/issue-with-creating-a-symbolic-link-inside-system-folder