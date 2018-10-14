---
title: 模板优先级队列及堆排序(C++实现)
tags:
  - C++
  - 大学
  - 学习
  - 算法
  - 编程
id: 2873
categories:
  - 学习笔记
date: 2013-04-16 08:42:58
---

模板优先级队列，数组实现，再熟悉一下常用算法，同时简单的堆排序应用

写了一个是队列自增长，另一个为了演示我还添加了一个叫做FillPq的方法，这个方法可以使用一个数组直接填充到优先级队列里，此时，优先级队列并不优先，然后进行下滤调整，之后建堆完成，输出即可
<pre class="lang:default decode:true">#include "stdafx.h"

template&lt; class T&gt;
class PriorityQueue
{
private:
	 T *pq;
	 int N;
	 int capacity;
public:
	PriorityQueue(void);
	~PriorityQueue(void);
	void Insert(T x);
	T DelTop();
	void Swim(int k);
	void Sink(int k);
	bool Less(int i,int j);
	void Swap(int i,int j);
	bool Resize();
	void FillPq(T arr[],int size);
};

template&lt; class T&gt;
void PriorityQueue&lt;T&gt;::FillPq( T arr[],int size )
{
	N=size;
	capacity=2*size;
	for (int i=0;i&lt;size;i++)
	{
		pq[i+1]=arr[i];
	}
}

template&lt; class T&gt;
PriorityQueue&lt;T&gt;::PriorityQueue(void)
{
	pq=new T[10];
	N=0;
	capacity=10;
}

template&lt; class T&gt;
void PriorityQueue&lt;T&gt;::Insert( T x )
{
	if (N==capacity)
	{
		Resize();
	}
	pq[++N]=x;
	Swim(N);
}

template&lt; class T&gt;
T PriorityQueue&lt;T&gt;::DelTop()
{
	T max=pq[1];
	Swap(1,N--);
	Sink(1);
	pq[N+1]=NULL;
	return max;
}
//下滤
template&lt; class T&gt;
void PriorityQueue&lt;T&gt;::Sink( int k )
{
	while (2*k&lt;=N)
	{
		int j=2*k;
		if (j&lt;N &amp;&amp; Less(j,j+1))
		{
			j++;
		}
		if (!Less(k,j))
		{
			break;
		}
		Swap(k,j);
		k=j;
	}
}
//上浮
template&lt; class T&gt;
void PriorityQueue&lt;T&gt;::Swim( int k )
{
	while (k&gt;1 &amp;&amp; Less(k/2,k))
	{
		Swap(k,k/2);
	}
}

template&lt; class T&gt;
void PriorityQueue&lt;T&gt;::Swap( int i,int j )
{
	T temp=pq[i];
	pq[i]=pq[j];
	pq[j]=temp;
}

template&lt; class T&gt;
bool PriorityQueue&lt;T&gt;::Less( int i,int j )
{
	return pq[i]&lt;pq[j];
}

template&lt; class T&gt;
bool PriorityQueue&lt;T&gt;::Resize()
{
	T *newPq=new T[capacity*2];
	capacity=capacity*2;
	for (int i=1;i&lt;=N;i++)
	{
		newPq[i]=pq[i];
	}
	pq=newPq;
	return true;
}

template&lt; class T&gt;
PriorityQueue&lt;T&gt;::~PriorityQueue(void)
{
}</pre>
然后是堆排序
<pre class="lang:default decode:true">#include "stdafx.h"
#include &lt;iostream&gt;
#include &lt;string&gt;
#include "PriorityQueue.cpp"
using namespace std;

int _tmain(int argc, _TCHAR* argv[])
{
        PriorityQueue&lt;int&gt; maxPq;

	int arr[8]={1,2,4,3,6,8,7,5};
	maxPq.FillPq(arr,8);
	//建堆
	for (int i=4;i&gt;0;i--)
	{
	maxPq.Sink(i);
	}
	//输出
	for (int i=1;i&lt;=8;i++)
	{
	cout&lt;&lt;maxPq.DelTop();
	}
}</pre>
&nbsp;