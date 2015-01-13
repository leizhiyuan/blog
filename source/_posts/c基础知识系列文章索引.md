title: "C# 基础知识系列文章索引"
tags:
  - C＃
  - windows
  - 大学
  - 开发
  - 收藏
  - 设计
  - 软件
id: 2634
categories:
  - 文章收藏
date: 2013-01-27 20:50:08
---

清理GR的加星标项目。分享来自博客园 [zhili](http://www.cnblogs.com/zhili) 的C#基础系列文章。

C#基础知识系列终于告了一个段落了, 本系列中主要和大家介绍了C#1.0到C# 4.0中一些重要的特性，刚开始写这个专题的初衷主要是我觉得掌握了C#这些基础知识之后，对于其他任何的一门语言都是差不多的，这样可以提高朋友们对其他语言的掌握，以及可以让大家更加迅速地掌握.NET的新特性， 并且相信这个系列对于找工作的朋友也是很有帮助的，因为很多公司面试都很看重基础知识是否扎实，是否对C#有一个全面的认识和理解，所以很多公司面试都会问到一些C#基础概念的问题，例如，经常面试会问：你是如何理解委托的，如何理解匿名函数等问题。

然而这个系列中并没有介绍COM互操作性的内容以及.Net 4.5中的一些新特性，所以后面将会对这两个方面的内容进行补充，由于这个系列托的太久了(大概也有3个月吧)，所以就先告一段落的，后面将会带来.NET互操作性系列的介绍。下面就为这个系列文章做一个索引，方便大家收藏和查找。

C#基础知识系列索引

C#1.0

1. [深入解析委托——C#中为什么要引入委托](http://www.cnblogs.com/zhili/archive/2012/10/22/Delegate.html)

2. [委托本质论](http://www.cnblogs.com/zhili/archive/2012/10/25/DeepDelegate.html)

3. [如何用委托包装多个方法——委托链](http://www.cnblogs.com/zhili/archive/2012/10/27/MulticastDelegate.html)

4. [事件揭秘](http://www.cnblogs.com/zhili/archive/2012/10/27/Event.html)

5. [当点击按钮时触发Click事件背后发生的事情](http://www.cnblogs.com/zhili/archive/2012/10/29/ButtonClickEvent.html)

C# 2.0

6. [泛型基础篇——为什么引入泛型](http://www.cnblogs.com/zhili/archive/2012/11/03/GenericType.html)

7. [泛型深入理解(一)](http://www.cnblogs.com/zhili/archive/2012/11/08/Generic_1.html)

8. [泛型深入理解(二)](http://www.cnblogs.com/zhili/archive/2012/11/08/Generic_2.html)

9. [深入理解泛型可变性](http://www.cnblogs.com/zhili/archive/2012/11/12/GenericVari.html)

10. [全面解析可空类型](http://www.cnblogs.com/zhili/archive/2012/11/23/Nullable.html)

11. [匿名方法解析](http://www.cnblogs.com/zhili/archive/2012/12/01/anonymousmethod.html)

12. [迭代器](http://www.cnblogs.com/zhili/archive/2012/12/02/Interator.html)

C# 3.0

13. [全面解析对象集合初始化器、匿名类型和隐式类型](http://www.cnblogs.com/zhili/archive/2012/12/11/basicfeatures.html)

14. [深入理解Lambda表达式](http://www.cnblogs.com/zhili/archive/2012/12/12/LambdaExpression.html)

15. [全面解析扩展方法](http://www.cnblogs.com/zhili/archive/2012/12/17/ExtensionMethod.html)

16. [Linq介绍](http://www.cnblogs.com/zhili/archive/2012/12/24/Linq.html)

C# 4.0

17. [深入理解动态类型](http://www.cnblogs.com/zhili/archive/2013/01/07/DynamicType.html)

&nbsp;

从C#的所有特性可以看出,C#中提出的每个新特性都是建立在原来特性的基础上,并且是对原来特性的一个改进, 做这么多的改进主要是为了方便开发人员更好地使用C#来编写程序,是让我们写更少的代码来实现我们的程序,把一些额外的工作交给编译器去帮我们做,也就是很多人说微软很喜欢搞语法糖的意思(语法糖即让编译器帮我们做一些额外的事情，减少开发人员所考虑的事情，使开发人员放更多的精力放在系统的业务逻辑上面。)，大家从C# 3中提出的特性中可以很好的看出这点(指的是玩语法糖)，C#3中几乎大部分特性都是C#提供的语法糖，从CLR层面来说(指的是增加新的IL指令)，C# 3并没有更新什么，C# 4中提出的动态类型又是建立在表达式树的基础上，包括Linq也是建立在表达式树的基础上，所以每个特性都是层层递进的一个关系。相信C#后面提出的新特性将会更加方便我们开发程序，感觉所有语言的一个统一的思想都是——写更少的代码，却可以做更多的事情。但是我们不能仅仅停住于知道怎么使用它，我们还应该深入研究它的背后的故事，知道新特性是如何实现的和原理。用一句说就是——我们要知其然之气所以然，学习知识应该抱着刨根问底的态度去学习,**相信这样的学习方式也可以让大家不感到心虚,写出的程序将会更加自信。**