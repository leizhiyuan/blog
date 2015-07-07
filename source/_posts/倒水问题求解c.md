title: 倒水问题求解(C++)
tags:
  - C++
  - 大学
  - 学习
  - 算法
  - 编程
id: 2966
categories:
  - 学习笔记
date: 2013-05-15 18:37:24
---

明天要去参加微软面试，不求顺利，但求体验。

这个题目答题的意思是:

给你一个容量为A升的桶和一个容量为B升的桶，水不限使用，要求精确得到Q升水.请说明步骤

当数字比较小的时候,我们可以通过大脑穷举来得到结果,但这里有两个问题,当数字很大的时候怎么解?题目给定的数据是否有解?

首先判断是否有解?

题目可以理解为,x为用A的次数,y为用B的次数,Q为目标值

Q = A * x + B * y
Q =目标值.

Q必须是 Gcd(A,B)(也就是A,B的最大公约数)的倍数,否则无解,如果 Gcd(A,B) == 1, 任何Q都是可解的

最简单的方法就是把A的水不断的向B中倒(B向A中倒也行),知道得到最终结果,如果桶满了,就清空该桶.举个例子

A = 3, B = 4 并且 Q = 2
重复得从 A-&gt;B

A B
######
0 0
4 0
1 3
1 0
0 1
4 1
2 3 &lt;-A桶中得到2了

试试从  B-&gt;A

A B
#####
0 0
0 3
3 0
3 3
4 2 &lt;- B中也得到了2
但是注意,从 B-&gt;A 比从 A-&gt;B快哦

然后我贴出代码
<pre class="lang:default decode:true">#include "stdafx.h"
#include &lt;iostream&gt;
#include &lt;vector&gt;
#include&lt;string&gt;

using namespace std;

//热门智力题 - 打水问题
//基本思想：用A桶容量的倍数对B桶的容量进行取余。
//指导方针：不断用A桶装水倒入B桶，B桶满了立即清空
//每次判断下二个桶中水的容量是否等于指定容量。
const string OPERATOR_NAME[7] = {
	"装满A桶",
	"装满B桶",
	"将A桶清空",
	"将B桶清空",
	"A桶中水倒入B桶",
	"B桶中水倒入A桶",
	"成功得到结果"
};
int max(int a,int b)
{
	return a&gt;b?a:b;
}
int gcd(int m,int n)
{
	int temp,p,r;
	//n存放最小数，m存放最大数
	if(n&gt;m){
		temp = n;
		n = m;
		m = temp;
	}
	p=n*m;//先取得两个数的积
	while(n!=0){
		r=m%n;
		m=n;
		n=r;
	}
return m;
}
int _tmain(int argc, _TCHAR* argv[])
{
	int    a_capacity, b_capacity, goal_capacity;
	vector&lt;string&gt; record;       //记录操作过程
	int a_water, b_water;

	cout&lt;&lt;"请输入A桶容量，B桶容量，目标容量Q：";
	cin&gt;&gt;a_capacity&gt;&gt;b_capacity&gt;&gt;goal_capacity;
	a_water = b_water = 0; //A桶，B桶中有多少升水

	//判断是否一定可解
	if (a_capacity&lt;=0 || b_capacity&lt;=0 || goal_capacity&lt;=0)
	{
		cout&lt;&lt;"请保证所以容量大于0"&lt;&lt;endl;
		return -1;
	}
	if (goal_capacity&gt;max(a_capacity,b_capacity))
	{
		cout&lt;&lt;"要量出的容量应该小于其中桶容量"&lt;&lt;endl;
		return -2;
	}
	//如果可解则解之
	if(goal_capacity%gcd(a_capacity,b_capacity)==0)
	{
		//存放临时的字符串构造
		char szTemp[30];
		while (true)
		{
			if (a_water == 0)//A桶没水,就装满水
			{
				a_water = a_capacity;
				sprintf(szTemp, "         A:%d  B:%d", a_water, b_water);
				record.push_back(OPERATOR_NAME[0] + szTemp);//先填满 A桶
			}
			else
			{
				//如果A桶的水比(B桶容量-B桶的水)要多，也就是多于没装满的B桶空出来的部分，A桶会剩下
				if (a_water &gt; b_capacity - b_water)
				{
					//A桶向B桶倒水直到B桶满，此时A桶的水==A桶的水+B桶的水-B桶容量
					a_water = a_water + b_water- b_capacity;
					b_water = b_capacity;		 //B桶的水装满了
					sprintf(szTemp, "  A:%d  B:%d", a_water, b_water); 
					record.push_back(OPERATOR_NAME[4] + szTemp);//A-&gt;B	
					if (a_water == goal_capacity)
						break;
					b_water = 0;			//将B桶清空
					sprintf(szTemp, "       A:%d  B:%d", a_water, b_water); 
					record.push_back(OPERATOR_NAME[3] + szTemp);
				}
				else
				{
					//A桶小于装满B桶需要的水，此时B桶的水==A桶的水+B桶的水
					b_water += a_water;	
					a_water = 0;
					sprintf(szTemp, "  A:%d  B:%d", a_water, b_water);
					record.push_back(OPERATOR_NAME[4] + szTemp);//A-&gt;B
					if (b_water == goal_capacity) 
						break;
				}
			}
		}
		record.push_back(OPERATOR_NAME[6]);	//break出来说明成功了
		cout&lt;&lt;"\n---------------------------------------------------"&lt;&lt;endl;
		cout&lt;&lt;"以下下是一种方案"&lt;&lt;endl;
		vector&lt;string&gt;::iterator pos;
		for (pos = record.begin(); pos != record.end(); pos++)
			cout&lt;&lt;*pos&lt;&lt;endl;
		cout&lt;&lt;"---------------------------------------------------"&lt;&lt;endl;
	}
	else
	{
		cout&lt;&lt;"此情况下无解"&lt;&lt;endl;
	}

	return 0;
}</pre>
运行结果如下：

[![](/images/9b3722c45b95afd3637b0726db13912e2f203ff3.png)](http://leaverimage.b0.upaiyun.com/36055_o.png)

关于如何求最少的步数来求解这个问题，希望有朋友能够留言指教。