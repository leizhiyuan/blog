title: "C#中的const和readonly"
tags:
  - 大学
  - 学习
  - 总结
  - 编程
id: 1056
categories:
  - 学习笔记
date: 2012-06-28 17:29:13
---

　　本文来自《你必须知道的.NET》这本书，是我看书过程中的笔记整理。

　　不变的量是程序设计中的平衡剂，是系统中恒定不变的量，在.NET中提供哦你了两种方式来实现，const和readonly。其中，const是静态常量，readonly是动态常量。

*   const，readonly和static readonly定义的常量，一旦初始值指定，（包括在构造函数内指定初始值），将不可更改，可读不可写。
*   const必须在声明的时候指定初始值，而readonly和static readonly在在声明时可以指定，也可以不指定初始值，同时也可以在构造函数中指定初始值，如果同时在声明时和构造函数中指定了初始值，以构造函数内指定的值为准。
*   const和static readonly定义的常量是静态的，只能由类型来访问，不能和static同时使用，否则可能出现编译错误，而readonly定义的常量是非静态的，只能由实例对象来访问。可以显式使用static定义静态成员
*   static readonly常量，如果在构造函数内指定初始值，那么必须是在静态无参构造函数中。不同的构造函数可以为readonly常量实现不同的初始值。
*   const可以用于定义局部常量和字段常量，而readonly和static readonly不能定义局部变量，只能定义字段常量，实际上，readonly应该被称之为只读字段，因此局限于定义字段，而const才是常量，可以定义字段和局部量。
*   const常量编译后保存在模块的元数据中，无需在托管堆中分配内存，并且const常量只能是百年机器能够识别的基元类型，比如Int32，string等，而readonly需要分配独立的存储空间，并且可以是任意类型。
*   const只能应用在值类型和string类型上，其他引用类型常量只能定义为null，否则以new为const引用类型常量赋值，编译器会引发“只能用null对引用类型（字符串除外）的常量进行初始化”错误提示，原因是构造函数初始化是在运行时，而非编译时，readonly只读字段，可以是任意类型，但是对于引用类型字段来说，readonly不能限制对该对象实例成员的访问控制。
　　总结：尽可能以只读属性来实现对类型读写特性的控制，而不是只读字段，但是在某些情况下，只读字段更简化些。

　　const是编译时常量，readonly是运行时常量，const较高效，readonly更灵活，在应用上，推荐以static readonly代替const，以平衡const在灵活性上的不足，同时克服编译器优化const性能时，所带来的程序集引用不一致问题。

&nbsp;

&nbsp;

&nbsp;