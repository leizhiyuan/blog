title: C++回调函数
tags:
  - C++
  - 原创
  - 学习
  - 总结
  - 软件
id: 535
categories:
  - 学习笔记
date: 2012-05-09 21:04:37
---

　　回调函数就是一个通过函数指针调用的函数。如果你把函数的指针(地址)作为参数传递给另一个函数，当这个指针被用为调用它所指向的函数时，我们就说这是回调函数。

　　也就是说，回调函数它首先是一个函数，然后有一个指针指向它（该指针称为函数指针），在别的代码块中，通过这个函数指针调用了这个函数，仅此而已。

　　下面给出一个例子，我写出了比较详细的注释。希望足够清晰。这个例子说明了，回调函数可以把调用者和被调用者分开，对于调用者来说，只需要知道自己要调用一个函数，该函数有一个string类型的参数，至于具体调用哪个，被调用的函数到底怎么执行，怎么解释该参数，是完全不用关心的。
<pre lang="c">
#include <iostream>  
#include <string>  
using namespace std;  

typedef void (*PF)(string s);  //定义一个名为PF的函数指针，该指针指向一类函数，该类函数有一个string类型的参数，返回值为void。  

void funcOne(string s)  //回调函数1  
{  
    cout << s+" One"<< endl;  
}  

void funcTwo(string s)  //回调函数2 
{  
    cout << s+" Two"<< endl;  
}  

void caller( PF pf, string s) //调用函数  
{  
    cout << "I am Caller Function" << endl;  
    pf(s);  
}  

int main()  
{  
    string str = "Test CallBack Function";  
    PF pf1 = funcOne;  //实例化一个函数指针，指向func函数      
    caller(pf1, str);  
    pf1=funcTwo; 
    caller(pf1, str);  
    system("pause");  
    return 0;  
}  

</pre>

　　回调函数的一个很实用的例子就是泛型算法中的max算法，具体可以参见本文末尾的第一篇参考文档，
如果你在自行写代码中发生了
error C2679: 二进制“<<”: 没有找到接受“std::string”类型的右操作数的运算符(或没有可接受的转换)错误
　　说明你没有把string头文件导入，导入即可
　　个人能力有限，错误之处请留言指正，不胜感激。
参考：
[http://learn.akae.cn/media/ch24s05.html](http://learn.akae.cn/media/ch24s05.html)
[http://baike.baidu.com/view/414773.htm](http://baike.baidu.com/view/414773.htm)