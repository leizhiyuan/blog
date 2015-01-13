title: Unable to read file Data_EGCITest解决方法
tags:
  - C＃
  - matlab
  - 大学
  - 开发
  - 笔记
id: 1169
categories:
  - 学习笔记
date: 2012-07-03 19:50:19
---

　　今天做的是使用matlab进行协整检验，好吧。。听起来似乎很厉害的样子，其实，我也不太清楚协整到底是干嘛呢。不过经管的各位很给力。
想来协整检验matlab是可以做的。所以就去查matlab是不是有这个函数，结果是有的。egcitest，但是具体的参数还是景观的朋友看懂的。

　　早上3点多起床，看matlab的书，然后早上看完了。基本上熟悉了matlab的操作。然后就开始写这个。大致的流程和昨天的那篇文章是一致的。但是matlab生成的dll文件在C#里面调用始终会提示这样一个错误。：
<pre class="lang:default decode:true">MWMCR::EvaluateFunction error ... Error using ==&gt; 
load Unable to read file Data_EGCITest: No such file or directory. Error in =&gt; test.m</pre>
　　组合了几个关键字进行搜索，发现了[这篇文章](http://www.mathworks.cn/matlabcentral/answers/33037-matlab-builder-ne-exception-with-supposedly-toolbox-support)，翻译过来很简单。

　　错误提示说的那个Data_EGCITest是一个名为Data_EGCITest.mat的文件，位于$MATLABROOT/toolbox/econ/econ/Data_EGCITest.mat目录下，$MATLABROOT指的是你matlab的安装路径。在你build 工程的时候，在下图中，记得添加这个文件。
[![]({{BASE_PATH}}/images/9f98cf04debe57b3ec360a6b3ae27c8995579770.jpg "操作")](http://leaverimage.b0.upaiyun.com/24000_o.jpg)

　　然后build生成的dll文件就可以正常在C#里使用了。

　　中间走了很多弯路。因为我想既然matlab都编译生成了dll，那么dll应该没问题。然后我就把精力放在了C#那边。。结果后来折腾了很长时间，才发现是这边的问题。。坑爹。。

　　协整检验的代码就不发了。和题目没关系。。
参考：
　　[http://www.mathworks.cn/help/toolbox/econ/egcitest.html](http://www.mathworks.cn/help/toolbox/econ/egcitest.html)