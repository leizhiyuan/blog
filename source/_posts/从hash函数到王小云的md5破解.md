title: 从hash函数到王小云的MD5破解
tags:
  - 安全
  - 收藏
  - 编程
id: 618
categories:
  - 文章收藏
date: 2012-05-15 13:26:14
---

　　以前一直纳闷不是说md5是不可逆的吗，那王小云怎么能破解呢。看到此文，豁然开朗，所谓的破解只是可以获得一个碰撞，使得两个文件的md5值一样。并不是说给定一个md5，能够得到原文。

　　密码学是理论计算机的一个很大的方向。之前准备先写密码学概论再提在hash函数破解上做出重大贡献的王小云教授的工作，不过前两天[王小云获得求是杰出科学家奖以及100万奖金](http://www.cast.org.cn/n435777/n435799/n928596/n930124/35475.html)，在媒体上又掀起了一轮宣传狂潮，但是有些报道极端弱智，错误百出，所以我趁机纠正一下，并介绍密码学的一个组成部分——hash函数，以及王小云在这上面的工作。

　　王小云的主要工作是关于hash函数的破解工作。她在2005一个密码学会议上宣布破解了SHA-1，震惊了全世界。所以要介绍和理解她的工作，先看一下hash函数具体是怎么回事。

　　简单的说，**hash函数**就是<span style="color: #ff0000;">把任意长的输入字符串变化成固定长的输出字符串</span>的一种函数。通俗得说，hash函数用来生成信息的摘要。输出字符串的长度称为hash函数的**位数**。

　　目前应用最为广泛的hash函数是**[SHA-1](http://en.wikipedia.org/wiki/SHA_hash_functions)**和**[MD5](http://en.wikipedia.org/wiki/Md5)**，大多是128位和更长。

　　hash函数在现实生活中应用十分广泛。很多下载网站都提供下载文件的MD5码校验，可以用来判别文件是否完整。另外，比如在WordPress的数据库，所有密码都是保存的MD5码，这样即使数据库的管理员也无法知道用户的原始密码，避免隐私泄露（很多人在不同地方都是用的同一个密码）。

　　如果两个输入串的hash函数的值一样，则称这两个串是一个**碰撞**(**Collision**)。既然是把任意长度的字符串变成固定长度的字符串，所以，必有一个输出串对应无穷多个输入串，碰撞是必然存在的。

　　一个“优良”的hash函数 _f _应当满足以下三个条件：　

　　1.任意y，找x，使得f(x)=y，非常困难。</li>
　　2.给定x1，找x2，使得f(x1)=f(x2)，非常困难。</li>
　　3.找x1，x2，使得f(x1)=f(x2)，非常困难。</li>

　　上面的“非常困难”的意思是除了枚举外不可能有别的更快的方法。比如第3条，根据[生日定理](http://en.wikipedia.org/wiki/Birthday_paradox)，要想找到这样的x1，x2，理论上需要大约2^(n/2)的枚举次数。

　　几乎所有的hash函数的破解，都是指的破坏上面的第三条性质，即找到一个碰撞（前两条都能被破坏的hash函数也太弱了点，早就被人抛弃了）。在密码学上还有一个概念是**理论破解**，指的是提出一个算法，使得可以用低于理论值得枚举次数找到碰撞。

　　王小云的主要工作是给出了MD5，[SHA-0](http://en.wikipedia.org/wiki/SHA_hash_functions)的碰撞，以及SHA-1的理论破解，她证明了160位SHA-1，只需要大约2^69次计算就能找出来，而理论值是2^80次。她的寻找MD5碰撞的方法是极端高效的。传说王小云当时在会议上把碰撞写出来，结果被下面的人验证发现不对，原来她把MD5算法的一个步骤弄错了。但是她立马联系她的当时留在中国的学生，修正算法，并找到一个新的碰撞。这一个是对的。

　　看到这里，那些认为中国国安局应该将这些结果封存作为秘密武器甚至幻想用这些成果来袭击美国之徒可以停住你们的YY了。这种形式上的破解，在大多数情况下没有实际性的作用。更别提MD5早就被美国人抛弃了。

　　但是，说这种破解一点实际意义都没有，那就侮辱了广大密码学家的智商，密码学家不会无缘无故的弄出碰撞这么一个概念来。下面简单的介绍一下在特定情况下，怎么利用给定的碰撞来做坏事(翻译自[Attacking Hash Functions](http://th.informatik.uni-mannheim.de/people/lucks/HashCollisions/))：

　　Caesar给实习生Alice叫写了一封推荐信(letter)。同一天，Alice叫Caesar在推荐信上数字签名，并提供了一份推荐信的电子板。Caesar打开文件，发现和原件一模一样。所以他在文件上签了名。

　　几个月后，Caesar发现他的秘密文件被非法察看。这到底是怎么回事呢？

[![letter]({{BASE_PATH}}/images/867e9ee66a7ab1aed4342dcb1a98489a6ab17134.png) ](http://www.cits.rub.de/imperia/md/content/magnus/letter_of_rec.ps)[![order]({{BASE_PATH}}/images/1a84f3c37a7c98c13605362aa25991c19b6d2d7b.png)](http://www.cits.rub.de/imperia/md/content/magnus/order.ps)

　　![(apply MD5 to both documents)]({{BASE_PATH}}/images/bf67e539f3da6867fc8a9070b940b83ff6daed5d.png)

　　a25f7f0b 29ee0b39 68c86073 8533a4b9

　　事实上，Alice要求Caesar签名的文件[letter](http://www.cits.rub.de/imperia/md/content/magnus/letter_of_rec.ps)已经被Alice做了手脚，准确地说，Alice还准备了另外一个文件[order](http://www.cits.rub.de/imperia/md/content/magnus/order.ps)，它们的MD5码完全一致。而Caesar的数字签名还依赖于MD5算法，所以Alice用order文件替换Letter文件之后，Caesar的数字签名依然有效。那封order给Alice提供了察看秘密文件的权限。

　　具体的实现方法可见[Hash Functions and the Blind Passenger Attack](http://www.cits.rub.de/imperia/md/content/magnus/rump_ec05.pdf)。我在这里简单的解释一下(只是大致思路，具体实现方式，需要对文件结构信息有所了解)：

　　letter文件的内容是：
　　　　if(x1==x1) show "letter" else show "order"
　　order文件的内容是：　　　
　　　　if(x2==x1) show "letter" else show "order"
　　其中字符串"letter"和"order"代表两封信实际显示的内容。x1，x2是一个MD5的碰撞。

　　上面的方法，只供参考和学术用途，实际使用所引起的后果概不负责。　　
　　参考：
　　１.[Attacking Hash Functions by Poisoned Messages "The Story of Alice and her Boss"](http://th.informatik.uni-mannheim.de/people/lucks/HashCollisions/)
　　２.[Hash function](http://en.wikipedia.org/wiki/Hash_function), wikipedia　
　　３.[SHA](http://en.wikipedia.org/wiki/SHA_hash_functions), wikipedia
　　４.Interview with Yiqun Lisa Yin concerning the attack on SHA-1　

　　原文来自：[http://zhiqiang.org](http://zhiqiang.org/blog/science/computer-science/preliminary-computer-theory-xiao-yun-wang-from-the-hash-function-to-crack-md5.html)