---
title: LR(0)和SLR分析表的构造
tags:
  - 大学
  - 总结
  - 生活
  - 编程
  - 编译
id: 574
categories:
  - 学习笔记
date: 2012-05-13 10:45:13
---

　　[上篇文章中](http://leaver.me/archives/548.html)，我已经说到了，LR(0)分析表是LR(0)分析器的重要组成部分，它是总控程序分析动作的依据，他是由LR(0)项目集规范族来进行构造的。他的结构主要有两个部分ACTION 和GOTO

　　先看看指导原则，可以直接跳过，看例题的时候可以返回来对照参考。

　　假设已构造出LR(0)项目集规范族为：C={I0,I1, … , In}，其中Ik为项目集的名字，k为状态名，令包含S′→·S项目的集合Ik的下标k为分析器的初始状态。那么分析表的ACTION表和GOTO表构造步骤为：

　　① 若项目A→α·aβ属于Ik且转换函数GO(Ik,a)= Ij，当a为终结符时则置ACTION[k,a]为Sj。　
　　② 若项目A→α· 属于Ik，则对任何终结符a 和'#'号置ACTION[k,a]和ACTION[k,#]为"rj"，j为在文法G′中某产生式A→α的序号。
　　③ 若GO(Ik,A)＝Ij，则置GOTO[k,A]为"j"，其中A为非终结符。　
　　④ 若项目S′→S·属于Ik，则置ACTION[k,#]为"acc"，表示接受。
　　⑤ 凡不能用上述方法填入的分析表的元素，均应填上"报错标志"。为了表的清晰我们仅用空白表示错误标志。

　　上篇文章的例题是这样的：LR（0）项目集规范簇也已经算出来了，共有6个I，从I0-I5，最终构造的LR(0)的分析表共7行，包括标题行，也就是ACTION和GOTO，然后是状态行，状态行和ACTION的交处分割成三列，分别是终结符号，和#终结符。也就是分割多少列取决于终结符的数目，GOTO列是非终结符，分割多少列也取决于非终结符的数目。，然后就是具体的6个状态了，画出表的结构后，如下，先不用管表的内容怎么写。

　[![](/images/0f0ac59f4cab73039ae7f5aecb327def5665da13.jpg)](http://leaverimage.b0.upaiyun.com/21422_o.jpg)

　　然后对照构造原则来填写表，这时你会发现要一个个从那么多的GO函数和I项目组中找对应的式子实在太难了，看不清楚，这时候，我们用GO函数把LR(0)项目集规范族连成一个识别该文法所产生的活前缀的DFA，有点像流程图了，首先把各个I项目画出来，然后需要把他们的关系表示出来，关系由GO函数确定，比如I5=GO(I2, S)，则在I2和I5之间画一个箭头，由I2指向I5，线上写上S，由括号里的第二个值确定，此题构造的DFA如下图，很简单吧。

　　[![](/images/ "识别活前缀的DFA")](http://leaverimage.b0.upaiyun.com/21397_o.jpg)

　　<span style="font-family: Verdana, Arial, Helvetica, sans-serif;"><span style="font-size: 12px; line-height: normal;">然后我们正式开始吧。第一条指导规则说到， 若项目A→α·aβ属于Ik且转换函数GO(Ik,a)= Ij，当a为终结符时则置ACTION[k,a]为Sj，我们先考察对于I0，发现S-&gt;·aS属于I0，且GO(I0,a)=I1,所有我们ACTION[0,a]置为S1.同理S-&gt;·bS属于I0，GO(I0,b)=I2，所以ACTION[0,b]置为S2。</span></span>

　　再来看第二条规则，若项目A→α· 属于Ik，则对任何终结符a 和'#'号置ACTION[k,a]和ACTION[k,#]为"rj"，j为在文法G′中某产生式A→α的序号，也就是说这里的j可不是I项目的标号，而是增广文法

　　(0)S’→S　
　　(1)S→aS　
　　(2)S→bS
　　(3)S→a

　　的标号，从0-3啦。我们考察I1，发现S→·aS属于I1，且GO(I1,a)=I1，所以应该置1和a的交的格子为S1，但是此时运用第二条规则会发现S-&gt;a·也属于I1，则又应该置ACTION[1,a]为=r3，ACTION[1,#]为r3，这样就发生了冲突。这是因为大多数文法不能满足LR(0)文法的条件，对于此冲突，我们不能确定看到S-&gt;a的时候是规约还是移进，有些文法是可以直接构造的，为此，此处不能够早LR(0)分析表了，我们构造经过改进后得到了一种新的SLR(1)文法，并没有什么太大差别，主要就是解决冲突。

　　解决冲突的指导原则如下：

　* 假设一个LR（0）项目集规范族中有如下项目集合：

　　{X → α.bβ，A → γ.，B → δ.}

　　即存在移进-归约冲突和归约-归约冲突

　　* 如果FOLLOW（A）∩ FOLLOW（B）∩ {b} =ф，则可以如下来解决冲突（假设当前符号是 a ）：　
　　1、若 a = b，则移进
　　2、若 a∈ FOLLOW（A），则用产生式 A → γ归约　
　　3、若 a∈ FOLLOW（B），则用产生式 B → δ归约　
　　4、否则，报错

　　此处的冲突发生时，当前符号是a，并且此时项目集中无B推导式，且指导规则中的b在此处其实是S-&gt;.aS中的a，所以计算Follow(S)∩ {a} ，发现为空，所以可以解决冲突，因为此时，当前符号是a，此处规则中的b也是a，所以，移进，也就是置ACTION[1,a]为=S1，运用分析表的ACTION表和GOTO表构造步骤的第一步，而不是置为r3，所以冲突解决。

　　然后再看构造步骤中的第三步，若GO(Ik,A)＝Ij，则置GOTO[k,A]为"j"，其中A为非终结符。此题中，只有S为非终结符，看DFA中的I0，发现GO(I0,S)＝I3，所以置GOTO[0,S]为3，ok

　　第四个步骤，若项目S′→S·属于Ik，则置ACTION[k,#]为"acc"，很简单，DFA中，I3符合，所以置ACTION[3,#]为"acc"。到此解释完了

　　反复运用，直到填完表。

　　完成后的表如图一所示。太复杂了。脑子烧糊了都，下篇有机会的话介绍如何使用来进行分析。其实剩下的部分不怎么难了。应该可以看得懂了

　　参考：

　　[http://metc.gdut.edu.cn/compile/nandian/n-7.htm](http://metc.gdut.edu.cn/compile/nandian/n-7.htm)

　　[http://jpkc.hdu.edu.cn/computer/byyl/online/4-3.htm](http://jpkc.hdu.edu.cn/computer/byyl/online/4-3.htm)