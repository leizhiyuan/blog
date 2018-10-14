---
title: 求整数1-N范围和为N的所有组合
tags:
  - C++
  - windows
  - 大学
  - 算法
id: 3002
categories:
  - 学习笔记
date: 2013-05-27 10:55:49
---

看到的一道题，给出答案

问题：找出整数1~N范围和为N的所有集合，集合里的数不允许重复。

解答：递归吧

代码如下：
<pre class="lang:c++ decode:true">#include "stdafx.h"
#include &lt;iostream&gt;
using namespace std;

void PrintResult(int *log,int index)
{
	for (int i = 0; i &lt;index; ++i)
	{
		cout&lt;&lt;log[i]&lt;&lt;" ";
	}
	cout&lt;&lt;endl;
}

void CalCombination(int* log,int startNum,int N,int &amp;index)
{
	if (N==0)
	{
		PrintResult(log,index);
	}

	else
	{
		for (int i = startNum; i &lt;= N; ++i)
		{
			log[index++]=i;
			CalCombination(log,i+1,N-i,index);
		}
	}
	index--;
}

int _tmain(int argc, _TCHAR* argv[])
{
	cout&lt;&lt;"请输入N:";
	int N=20;
	cin&gt;&gt;N;
	int *log=new int[N];
	int index=0;
	CalCombination(log,1,N,index);
}</pre>
要是允许重复，也简单，将递归中的这句话改为：
<pre>CalCombination(log,i,N-i,index);</pre>
同理，还可以解决类似给定一个数组，让求和为N的元素组合，只需要现将元素排个序，然后思路相同。

&nbsp;