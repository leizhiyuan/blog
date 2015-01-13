title: "[藏]再谈JavaScript中的闭包"
tags:
  - javascript
  - web
  - 学习
  - 收藏
id: 3589
categories:
  - 文章收藏
date: 2014-04-26 16:36:28
---

之前读js的时候总是感觉不清楚，近日决定重新攻读，看到这篇文章之后，我明白了某大神说的那句话，如果你不能向一个6岁的小朋友讲明白。那么这件事情你一定不明白。还有就是如果你必须理解一个闭包才会使用它，那么这个闭包设计本身就是失败的。情赏析本文。相当精彩。

JavaScript中函数的重要性毋庸置疑。在理解了JavaScript中的函数之后，非常重要的地点就是理解我们怎样使用函数来创建闭包。一直以来，闭包都是JavaScript新手学习时的一个难点所在，它位于JavaScript函数与变量作用域交叉的一个灰色地带：

[![1389597690904-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%881.20.17]({{BASE_PATH}}/images/a6b5c488378f9e3aa825f07d0a699f5ba6c72afd.png)](http://leaverimage.b0.upaiyun.com/2014/04/1389597690904-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%881.20.17.png)

本文将尽可能简单的方法讲述关于JavaScript闭包的那些事情，使用的代码也非常的简单。如果一开始就讲述闭包的概念，只会使得你更加的困惑。所以我们就从一个我们熟悉的领域开始，慢慢的向闭包的邪恶领域前进，看看我们在那里能发现什么。

下面开始我们的冒险之旅吧！

# 函数中的函数

我们要做的第一件事情是理解当你在函数中创建了函数并且从函数内部返回一个函数时究竟发生了什么。首先我们来快速的回顾一下函数。

看下面的代码：

    <span class="token keyword" style="color: #0077aa;">function</span> <span class="token function">calculateRectangleArea<span class="token punctuation" style="color: #999999;">(</span></span>length<span class="token punctuation" style="color: #999999;">,</span>width<span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">{</span>
        <span class="token keyword" style="color: #0077aa;">return</span> length<span class="token operator" style="color: #a67f59;">*</span>width<span class="token punctuation" style="color: #999999;">;</span>
    <span class="token punctuation" style="color: #999999;">}</span>   

    <span class="token keyword" style="color: #0077aa;">var</span> roomArea <span class="token operator" style="color: #a67f59;">=</span> <span class="token function">calculateRectangleArea<span class="token punctuation" style="color: #999999;">(</span></span><span class="token number" style="color: #990055;">10</span><span class="token punctuation" style="color: #999999;">,</span><span class="token number" style="color: #990055;">10</span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>  
    <span class="token function">alert<span class="token punctuation" style="color: #999999;">(</span></span>roomArea<span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>   
    `</pre>

    calculateRectangleArea函数接收两个参数并且返回这两个参数的乘积。在这个例子中没我们将返回的数赋值给了变量roomArea。

    当代码运行之后，roomArea变量包含了10乘10的结果，也就是100：

    [![1389597709290-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%881.20.27]({{BASE_PATH}}/images/5465c1b36f5c205fe7fbf69cde8ce0a292c44992.png)](http://leaverimage.b0.upaiyun.com/2014/04/1389597709290-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%881.20.27.png)

    正如你所知道的，一个函数可以返回任何东西。在这个例子中，我们返回了一个数。你可以返回一些文本（也就是字符串），undefined，一个自定义对象等等。只要调用函数的代码知道怎么处理返回的值，你可以做任何你想做的事情。你甚至可以返回另一个函数。我们下面就来看一个这样的例子：

    <pre class=" language-javascript" style="color: black;">`<span class="token keyword" style="color: #0077aa;">function</span> <span class="token function">youSayGoodBye<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">{</span>
        <span class="token function">alert<span class="token punctuation" style="color: #999999;">(</span></span><span class="token string" style="color: #669900;">'Good Bye!'</span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>

        <span class="token keyword" style="color: #0077aa;">function</span> <span class="token function">andISayHello<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">{</span>
            <span class="token function">alert<span class="token punctuation" style="color: #999999;">(</span></span><span class="token string" style="color: #669900;">'Hello!'</span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>
        <span class="token punctuation" style="color: #999999;">}</span>

        <span class="token keyword" style="color: #0077aa;">return</span> andISayHello<span class="token punctuation" style="color: #999999;">;</span> 
    <span class="token punctuation" style="color: #999999;">}</span>
    `</pre>

    你可以在函数内部包括函数。在这个例子中，我们的youSayGoodBye函数包含了一个alert语句以及另一个叫做andTSayHello的函数：

    [![1389597723104-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%881.25.09]({{BASE_PATH}}/images/7dec0726d4e268a59b287fce13db84f2bda5c50e.png)](http://leaverimage.b0.upaiyun.com/2014/04/1389597723104-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%881.25.09.png)

    有趣的地方是当youSayGoodBye函数调用时返回了什么东西。它返回了andISayHello函数：

    <pre class=" language-javascript" style="color: black;">`<span class="token keyword" style="color: #0077aa;">function</span> <span class="token function">youSayGoodBye<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">{</span>
        <span class="token function">alert<span class="token punctuation" style="color: #999999;">(</span></span><span class="token string" style="color: #669900;">'Good Bye!'</span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>

        <span class="token keyword" style="color: #0077aa;">function</span> <span class="token function">andISayHello<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">{</span>
            <span class="token function">alert<span class="token punctuation" style="color: #999999;">(</span></span><span class="token string" style="color: #669900;">'Hello!'</span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>
        <span class="token punctuation" style="color: #999999;">}</span>

        <span class="token keyword" style="color: #0077aa;">return</span> andISayHello<span class="token punctuation" style="color: #999999;">;</span>
    <span class="token punctuation" style="color: #999999;">}</span>   
    `</pre>

    下面我们调用这个函数，并且让一个变量指向这个函数的调用结果：

    <pre class=" language-javascript" style="color: black;">`<span class="token keyword" style="color: #0077aa;">var</span> something <span class="token operator" style="color: #a67f59;">=</span> <span class="token function">youSayGoodBye<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>   
    `</pre>

    在这行代码运行的时候，youSayGoodBye函数中的所有代码同时也在运行。这意味着，你可以看到一个对话框（由于alert）说Good Bye！：

    [![1389597744293-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%881.29.06]({{BASE_PATH}}/images/1826cf206cce86d88a6a324d2624ae277929390d.png)](http://leaverimage.b0.upaiyun.com/2014/04/1389597744293-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%881.29.06.png)

    当运行结束之后，andISayHello函数将会被创建并且返回。在这个时候，变量something只关注一个东西，这个东西就是andISayHello函数：

    [![1389597757035-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%881.40.17]({{BASE_PATH}}/images/907b52e917c6617d2d66519f443e640b61d10472.png)](http://leaverimage.b0.upaiyun.com/2014/04/1389597757035-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%881.40.17.png)

    由于something现在指向一个函数，因此你可以通过括号标示符调用它：

    <pre class=" language-javascript" style="color: black;">`<span class="token keyword" style="color: #0077aa;">var</span> something <span class="token operator" style="color: #a67f59;">=</span> <span class="token function">youSayHello<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>
    <span class="token function">something<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>   
    `</pre>

    当你这么做的时候，返回的内部函数（也就是andISayHello）将会执行。和前面一样，你将会看到一个对话框，但是对话框这次说的是Hello！– 这是由于内部的alert决定的：

    [![1389597771981-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%881.42.50]({{BASE_PATH}}/images/d0674b8248c110e71cbc5c6878af799538446963.png)](http://leaverimage.b0.upaiyun.com/2014/04/1389597771981-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%881.42.50.png)

    上面提到的所有东西都很直观。唯一你可能觉得比较新的地方是一旦一个函数返回一个值，这个函数就不再存在了。唯一存在的东西是返回值。

    现在我们已经接近闭包的邪恶领域了。在下一部分中，我们将扩展前面提到的代码来看看一个变形的例子。

    # 内部函数不是自包含函数的情况

    在前面的例子中，你的andISayHello函数是一个自包含函数并且不依赖于外部函数的任何变量或状态：

    <pre class=" language-javascript" style="color: black;">`<span class="token keyword" style="color: #0077aa;">function</span> <span class="token function">youSayGoodBye<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span> <span class="token punctuation" style="color: #999999;">{</span>

        <span class="token function">alert<span class="token punctuation" style="color: #999999;">(</span></span><span class="token string" style="color: #669900;">"Good Bye!"</span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>

        <span class="token keyword" style="color: #0077aa;">function</span> <span class="token function">andISayHello<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span> <span class="token punctuation" style="color: #999999;">{</span>
            <span class="token function">alert<span class="token punctuation" style="color: #999999;">(</span></span><span class="token string" style="color: #669900;">"Hello!"</span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>
        <span class="token punctuation" style="color: #999999;">}</span>

        <span class="token keyword" style="color: #0077aa;">return</span> andISayHello<span class="token punctuation" style="color: #999999;">;</span>
    <span class="token punctuation" style="color: #999999;">}</span>   
    `</pre>

    在现实的很多场景中，几乎没有这样的自包含函数的例子。你经常会发现需要在内部函数和外部函数之间共享变量和数据。为了强调这一点，我们看看下面的例子：

    <pre class=" language-javascript" style="color: black;">`<span class="token keyword" style="color: #0077aa;">function</span> <span class="token function">stopWatch<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span> <span class="token punctuation" style="color: #999999;">{</span>
        <span class="token keyword" style="color: #0077aa;">var</span> startTime <span class="token operator" style="color: #a67f59;">=</span> Date<span class="token punctuation" style="color: #999999;">.</span><span class="token function">now<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>

        <span class="token keyword" style="color: #0077aa;">function</span> <span class="token function">getDelay<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span> <span class="token punctuation" style="color: #999999;">{</span>
            <span class="token keyword" style="color: #0077aa;">var</span> elapsedTime <span class="token operator" style="color: #a67f59;">=</span> Date<span class="token punctuation" style="color: #999999;">.</span><span class="token function">now<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span> <span class="token operator" style="color: #a67f59;">-</span> startTime<span class="token punctuation" style="color: #999999;">;</span>
            <span class="token function">alert<span class="token punctuation" style="color: #999999;">(</span></span>elapsedTime<span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>
        <span class="token punctuation" style="color: #999999;">}</span>

        <span class="token keyword" style="color: #0077aa;">return</span> getDelay<span class="token punctuation" style="color: #999999;">;</span>
    <span class="token punctuation" style="color: #999999;">}</span>   
    `</pre>

    这个例子展示了一个简单地测量消耗的时间的方式。在stopWatch函数中，你有一个startTime变量来被赋值为Date.now()：

    <pre class=" language-javascript" style="color: black;">` <span class="token keyword" style="color: #0077aa;">var</span> startTime <span class="token operator" style="color: #a67f59;">=</span> Date<span class="token punctuation" style="color: #999999;">.</span><span class="token function">now<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>   
    `</pre>

    你也有一个叫做getDelay的内部函数：

    <pre class=" language-javascript" style="color: black;">`<span class="token keyword" style="color: #0077aa;">function</span> <span class="token function">getDelay<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span> <span class="token punctuation" style="color: #999999;">{</span>
      <span class="token keyword" style="color: #0077aa;">var</span> elapsedTime <span class="token operator" style="color: #a67f59;">=</span> Date<span class="token punctuation" style="color: #999999;">.</span><span class="token function">now<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span> <span class="token operator" style="color: #a67f59;">-</span> startTime<span class="token punctuation" style="color: #999999;">;</span>
      <span class="token function">alert<span class="token punctuation" style="color: #999999;">(</span></span>elapsedTime<span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>
    <span class="token punctuation" style="color: #999999;">}</span>
    `</pre>

    getDelay函数展示了一个包含当前时间Date.now()和前面定义的开始时间startTime之间间隔的对话框。

    回到外部函数stopWatch()，在运行结束之前发生的最户一件事情是返回getDelay函数。正如你所见的，这里的这段代码和先前的例子非常类似。你有一个外部函数，你有一个内部函数，然后外部函数返回了内部函数。

    现在，为了弄清楚，stopWatch函数是怎么运行的，我们添加下面的代码：

    <pre class=" language-javascript" style="color: black;">`<span class="token keyword" style="color: #0077aa;">var</span> timer <span class="token operator" style="color: #a67f59;">=</span> <span class="token function">stopWatch<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>
    <span class="token comment" style="color: #708090;" spellcheck="true">
    // 做一些消耗时间的式
    </span><span class="token keyword" style="color: #0077aa;">for</span> <span class="token punctuation" style="color: #999999;">(</span><span class="token keyword" style="color: #0077aa;">var</span> i <span class="token operator" style="color: #a67f59;">=</span> <span class="token number" style="color: #990055;">0</span><span class="token punctuation" style="color: #999999;">;</span> i <span class="token operator" style="color: #a67f59;">&lt;</span> <span class="token number" style="color: #990055;">1000000</span><span class="token punctuation" style="color: #999999;">;</span> i<span class="token operator" style="color: #a67f59;">++</span><span class="token punctuation" style="color: #999999;">)</span> <span class="token punctuation" style="color: #999999;">{</span>
        <span class="token keyword" style="color: #0077aa;">var</span> foo <span class="token operator" style="color: #a67f59;">=</span> Math<span class="token punctuation" style="color: #999999;">.</span><span class="token function">random<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span> <span class="token operator" style="color: #a67f59;">*</span> <span class="token number" style="color: #990055;">10000</span><span class="token punctuation" style="color: #999999;">;</span>
    <span class="token punctuation" style="color: #999999;">}</span>
    <span class="token comment" style="color: #708090;" spellcheck="true">
    // 调用返回函数
    </span><span class="token function">timer<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>    
    `</pre>

    如果你运行这个例子，你将看到一个对话框展示从初始化到timer函数被调用之间时间间隔的对话框。你的for循环接收时候，timer变量像一个函数一样被调用：

    [![1389597808064-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%882.50.43]({{BASE_PATH}}/images/69023707f410b23b639f61b5118be955427c17b9.png)](http://leaverimage.b0.upaiyun.com/2014/04/1389597808064-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%882.50.43.png)

    基本上，你现在有了一个秒表可以用来计算一个长时间运行的操作花费了多长时间。

    现在你看到我们的简单的秒表例子已经运行起来了，我们回到stopWatch函数看看实际上发生了什么。正如前面所提到的，上面的例子和前面的youSayGoodBye/andISayHello例子很相似。要注意的一点是当getDelay函数返回并赋值给timer变量时发生了什么。

    [![1389597819038-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%882.55.09]({{BASE_PATH}}/images/755cd1da19a41e45c319b357219d99a470d65f44.png)](http://leaverimage.b0.upaiyun.com/2014/04/1389597819038-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%882.55.09.png)

    外部函数stopWatch不再起作用，time人变量被绑定到了getDelay函数。现在，有区别的地方来了。getDelay函数依赖于外部函数stopWatch上下文中的startTime变量：

    <pre class=" language-javascript" style="color: black;">`<span class="token punctuation" style="color: #999999;">.</span><span class="token punctuation" style="color: #999999;">.</span><span class="token punctuation" style="color: #999999;">.</span>

    <span class="token keyword" style="color: #0077aa;">var</span> startTime <span class="token operator" style="color: #a67f59;">=</span> Date<span class="token punctuation" style="color: #999999;">.</span><span class="token function">now<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span><span class="token punctuation" style="color: #999999;">;</span>  

    <span class="token punctuation" style="color: #999999;">.</span><span class="token punctuation" style="color: #999999;">.</span><span class="token punctuation" style="color: #999999;">.</span>

    <span class="token keyword" style="color: #0077aa;">var</span> elapsedTime <span class="token operator" style="color: #a67f59;">=</span> Date<span class="token punctuation" style="color: #999999;">.</span><span class="token function">now<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span> <span class="token operator" style="color: #a67f59;">-</span> startTime<span class="token punctuation" style="color: #999999;">;</span>   

    <span class="token punctuation" style="color: #999999;">.</span><span class="token punctuation" style="color: #999999;">.</span><span class="token punctuation" style="color: #999999;">.</span>  
    `</pre>

    当getDelay函数被返回时外部函数stopWatch函数不再器作用，那么下面的这行代码又发生了什么？

    <pre class=" language-javascript" style="color: black;">`<span class="token keyword" style="color: #0077aa;">var</span> elapsedTime <span class="token operator" style="color: #a67f59;">=</span> Date<span class="token punctuation" style="color: #999999;">.</span><span class="token function">now<span class="token punctuation" style="color: #999999;">(</span></span><span class="token punctuation" style="color: #999999;">)</span> <span class="token operator" style="color: #a67f59;">-</span> startTime<span class="token punctuation" style="color: #999999;">;</span>   

在这个上下文中，看起来startTime变量没有被定义。但是，这段代码显然正常运行了，因此这里存在一些其他的东西。这里提到的“其他的东西”值得就是害羞而神秘的闭包。我们来看看究竟发生了什么似的我们的startTime变量储存了一个实际的值而不是undefined。

JavaScript runtime将会持续跟踪你的变量，内存使用，引用，实际上来说它非常的聪明。在这个例子中，它探测到内部函数（getDelay）依赖于一个来自外部函数（stopWatch）的变量。当这种情况发生时，runtime将会确保任何来自于外部函数的变量仍然在内部函数中可用，即使外部函数已经调用结束了。

为了说明这一点，我们来看一张图：

[![1389597838417-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%883.03.33]({{BASE_PATH}}/images/0771c57bc7c41dfbfaebf8c90b29ebfb3d265bae.png)](http://leaverimage.b0.upaiyun.com/2014/04/1389597838417-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%883.03.33.png)

变量timer依然指向getDelay函数，但是getDelay函数依然可以获取来自于外部函数stopWatch中的startTime变量。这个内部函数 – 由于它将来自于外部函数的相关变量包含进了自己的作用域中 – 因此被称为闭包：

[![1389597858778-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%883.05.32]({{BASE_PATH}}/images/6f0bc551d479b6a9c3b4a7c17f5b28c97a21b146.png)](http://leaverimage.b0.upaiyun.com/2014/04/1389597858778-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%883.05.32.png)

至此，我们可以给闭包下一个定义：闭包就是一个新创建但是依然包含外部作用域变量的函数：

[![1389597875629-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%883.08.29]({{BASE_PATH}}/images/62cc4b99cacf82a71cb306de3365c88adfeb8e85.png)](http://leaverimage.b0.upaiyun.com/2014/04/1389597875629-%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202014-01-13%20%E4%B8%8B%E5%8D%883.08.29.png)

再次回到前面的例子，在timer变量初始化时startTime得到了Date.now()的值。当stopWatch返回了内部桉树getDelay时，stopWatch函数不再起作用。但是内部函数依赖的变量却没有消失。这些共享的变量没有消失。相反，它们被包含进入了内部函数，也就是闭包中。

# 总结

闭包乍看上去很复杂，但是其实很简单，它存在于JavaScript中的各个地方。重要的一点是记住：闭包允许函数保持运行，即使函数的韩静发生了巨大的变化或者小时。当函数被创建时任何包含在作用域中的变量都会被保护起来以确保函数的正常运行。这样的机制对于JavaScript这样的动态语言来说是非常重要的，因为你可以随时创建，修改以及销毁变量。

* * *

# 本文译自Closures in JavaScript，原文地址[http://www.kirupa.com/html5/closures_in_javascript.htm](http://www.kirupa.com/html5/closures_in_javascript.htm)