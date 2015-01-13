title: "C#模拟手工洗牌(附测试)"
tags:
  - C＃
  - windows
  - 学习
  - 总结
  - 设计
id: 2339
categories:
  - 学习笔记
date: 2012-11-25 09:24:20
---

洗牌大家都知道，代码实现最广泛的一种就是产生两个随机数，然后交换这两个随机数为下标的牌，但是这种的洗牌并不能保证同概率，你可以参考本文做一些测试，本文代码没啥可说的。我写出了非常详细的注释

ps:刚开始写那个随机数的时候，我随便给了个种子2012.。结果你懂的。。意外意外。这个全局的result数组让我很疼，代码有什么可以改进的，欢迎留言指出。不胜感激。
<pre class="lang:default decode:true">/*Author:Bystander
 *Blog:http://leaver.me 
 *Date:2012/11/24*/
using System;

class Program
{
    static int[,] result;
    static void Main()
    {
        //初始牌的顺序，我只测试10张牌的情况
        char[] _arr = { 'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I', 'J' };
        result = new int[_arr.Length, _arr.Length];
        //进行1000次，来统计结果的数字.

        for (int i = 0; i &lt; 10000; i++)
        {
            ShuffleArray_Manual(_arr);
            SumCount(_arr);
        }

        int j = 0;
        foreach (int i in result)
        {
            if (j % result.GetLength(1) == 0)
            {
                Console.WriteLine();
            }
            Console.Write(i + "\t");
            j++;
        }

    }

    //模拟洗牌
    static void ShuffleArray_Manual(char[] arr)
    {

        Random rand = new Random(Guid.NewGuid().GetHashCode());
        int len = arr.Length;
        int mid = len / 2;

        /*
         * 洗牌的过程重复进行5次，为了洗的均匀.每次都是先平分扑克，然后完全交叉，然后从牌中拿出一部分，放在牌的最前面，也就是切牌，然后再进行下次平分扑克。
         */
        //双手洗牌5次，默认认为是平分扑克
        for (int n = 0; n &lt; 5; n++)
        {

            //两手洗牌
            for (int i = 1; i &lt; mid; i += 2)
            {
                char tmp = arr[i];
                arr[i] = arr[mid + i];
                arr[mid + i] = tmp;
            }

            //随机切牌
            //注意切牌指的是从中抽出n张牌放到扑克牌的前面去
            char[] buf = new char[len];

            for (int j = 0; j &lt; 5; j++)
            {
                //产生从大于等于1小于len的数
                int start = rand.Next() % (len - 1) + 1;

                //产生大于等于0小于等于一半的数
                int numCards = rand.Next() % (len / 2) + 1;

                if (start + numCards &gt; len)
                {
                    numCards = len - start;
                }

                //把扑克牌arr的前start张牌复制到buf里
                Array.ConstrainedCopy(arr, 0, buf, 0, start);

                ///然后把切出来的numCards张牌，起始下标为start的移动到扑克牌arr的最前面
                Array.ConstrainedCopy(arr, start, arr, 0, numCards);

                ///最后把切出去的buf牌（numCards张）复制回扑克牌arr的numCards之后的元素
                Array.ConstrainedCopy(buf, 0, arr, numCards, start);
            }

        }
    }

    //统计一次结果的次数，存入结果数组
    static void SumCount(char[] arr)
    {
        for (int i = 0; i &lt; arr.Length; i++)
        {
            result[arr[i] - 'A', i] += 1;
        }
    }
}</pre>
&nbsp;