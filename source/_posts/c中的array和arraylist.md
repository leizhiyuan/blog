title: "C#中的Array和ArrayList"
tags:
  - C＃
  - 开发
  - 总结
  - 软件
id: 1118
categories:
  - 学习笔记
date: 2012-07-11 12:51:03
---

数组是最基础的数据结构。ArrayList可以看作是Array的复杂版本。下面比较两者的异同

### 比较：

> ### 相同点：
> 
> > Array和ArrayList均实现了相同的接口，因此具有许多相同的操作方法，例如对自身进行枚举，能够以foreach语句遍历。
> > Array和ArrayList创建的对象均保存在托管堆中。
> 
> ### 不同点：
> 
> > 1.  Array只能存储同构对象，当然，声明为Object类型的数组除外，因为任何类型都可以隐式转换成Object类型。ArrayList可以存储异构对象，这是因为本质上ArrayList内部维护着一个object[] items类型的字段，在应用ArrayList时，应该考虑装箱和拆箱所带来的性能损失。一般情况下，Array的性能高于ArrayList。
> > 2.  Array可以是一维的，也可以是多维的，而ArrayList只能是一维的。
> > 3.  Array的容量是固定的。一旦声明，不可更改，而ArrayList容量动态增加，当添加元素超过初始容量时，ArrayList会根据需要重新分配。而且可以通过TrimToSize删除空项来压缩体积。其实，除了Array外，其他集合类都是可以动态增加的。
> > 4.  Array的下限可以设置，而ArrayList下限只能是0.
> > 5.  Array只有简单的方法来获取或设置元素值，不能随意增加或删除数组元素，而ArrayList提供了更多的方法来操作元素，可以方便的插入或删除指定位置上的元素。一般可以用ArrayList代替Array.
tip：List泛型类对应于ArrayList。