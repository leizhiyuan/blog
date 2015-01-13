title: VS2010 编译安装boost库
tags:
  - C++
  - 原创
  - 总结
  - 编程
  - 编译
id: 721
categories:
  - 学习笔记
date: 2012-05-24 11:39:55
---

实践是最好的办法。。学习C++，想试试线程，然后打算用boost库，结果boost库编译差点吓到我。。没看到比较完整的安装教程。。一直耽搁。今天动手。完成了。方法记录如下：
1.下载boost
从boost官网( http://www.boost.org )上下载最新的boost版本，现在最新是1.49版本，解压到自定义目录(我解压到了D:/program files,最终的目录结构是D:\Program Files\boost_1_49_0)

2.编译安装

在D:\Program Files\boost_1_49_0的目录下，有一个bootstrap.bat文件，直接双击运行。就会在同目录生成b2.exe；bjam.exe两个文件。

3.设定编译环境
修改user-config.jam (D:\Program Files\boost_1_49_0\tools\build\v2\user-config.jam) 的MSVC configuration
# MSVC configuration
# Configure msvc (default version, searched for in standard locations and PATH).
# using msvc ;
在上面这段的下面直接添加如下的文字。
<pre class="lang:default decode:true">using msvc : 10.0 : :/wd4819/D_CRT_SECURE_NO_DEPRECATE/D_SCL_SECURE_NO_DEPRECATE/D_SECURE_SCL=0 ;</pre>
保存关闭。

4.开始编译
点击开始-&gt;所有程序-&gt;“Microsoft Visual Studio 2010”，指向“Visual Studio tools(工具)”，然后单击“Visual Studio 2010 command prompt（命令提示）” 使用cd切换到D:\Program Files\boost_1_49_0目录。这个就不说了
然后输入如下的代码：
<pre class="lang:sh decode:true  crayon-selected">b2 toolset=msvc-10.0 architecture=x86 instruction-set=i686 address-model=32 link=static 
variant=debug,release threading=multi runtime-link=shared --without-python --without-mpi 
--without-wave --without-graph --without-math --without-serialization stage</pre>
&nbsp;

解释一下命令的意思：
1.toolset：表示编译器工具，我安装的是VS2010，所以是msvc-10(如果你是VS2005，可以使用msvc-8.0 VS2008是msvc-9.0)
2.architecture：表示架构，也就是你的CPU架构，x86,x64，因为我安装的是win7 32位，所以使用了x86的架构
3.instruction-set：表示指令集，依然是8086指令集
4.address-model：表示地址长度为32位
5.link：表示生成动态/静态链接库，动态链接库是shared，静态链接库是static，一般都会编译成静态库，因为给出程序的时候打包boost的库会非常庞大
6.variant：表示生成的Debug或者release版本，一般情况下会两种版本都会编译出来的
7.threading：表示单/多线程编译，一般我们的程序都会用到多线程，所以选择了multi
8.runtime-link：表示动态/静态链接C/C++运行时库(C/C++ Runtime)，我们选择了动态链接
9.without/with：表示不需要编译/需要编译哪些库，一些自己不用的库可以无需编译
10.stage/install：stage表示只生成库文件(DLL和Lib)，install还会生成包含头文件的include目录，推荐使用stage，因为boost_1_49\boost中就是boost库完整的头文件，所以无需再拷贝一份出来。编译出来的库会放在stage文件夹中
这样一份完整的boost库就生成了，剩下就是直接使用到项目中了。

其实编译的具体命令都是可以自己写的。如果你需要编译所有。只需要使用下面的这行代码
b2 --toolset=msvc-10.0 --build-type=complete
就可以了。

不出问题的话。就开始编译了。。登个半个多小时吧。就会完成了。
5.设置vs
打开vs，新建一个工程。然后工程属性。配置属性-&gt;C/C++ ，附加包含目录
填上
D:\Program Files\boost_1_49_0;%(AdditionalIncludeDirectories)
这个是最终的结果，你也可以手动添加

在左侧选择链接器-&gt;附加库目录，填上
D:\Program Files\boost_1_49_0\stage\lib;%(AdditionalLibraryDirectories)
就可以了。

6.测试
在你新建的工程里输入如下的代码。运行成功就说明可以了
<pre class="lang:c++ decode:true ">#include &lt;boost/thread/thread.hpp&gt;
#include &lt;iostream&gt;

void hello()
{
        std::cout &lt;&lt;
        "Hello world, I'm a thread!"
        &lt;&lt; std::endl;
}

int main(int argc, char* argv[])
{
        boost::thread thrd(&amp;hello);
        thrd.join();
        return 0;
}</pre>
&nbsp;

参考：
[http://www.cppfans.org/1317.html](http://www.cppfans.org/1317.html)
[http://www.cnblogs.com/ComputerG/archive/2011/03/10/1979730.html](http://www.cnblogs.com/ComputerG/archive/2011/03/10/1979730.html)
[http://www.cppblog.com/shaker/archive/2011/11/30/33583.html](http://www.cppblog.com/shaker/archive/2011/11/30/33583.html)