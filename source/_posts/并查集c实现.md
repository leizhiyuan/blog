title: 并查集(C++实现)
tags:
  - C++
  - windows
  - 大学
  - 学习
  - 编程
id: 2857
categories:
  - 学习笔记
date: 2013-04-11 08:45:38
---

并查集这个很有意思，并查集是一种树型的数据结构，用于处理一些不相交集合（Disjoint Sets）的合并及查询问题。昨天看书看到了，然后用C++简单实现了下。在Dijkstra算法中，用来判断两个顶点是否在同一个集合里。

里面定义了两个类，都是并查集，一个是QuickFind，查找很快，一个是QuickUnion，合并较快。写了一些注释，有一些优化的提示.看代码吧，有什么问题指出来吧。

QuickFind的实现
<pre class="lang:default decode:true">#include "QuickFind.h"

QuickFind::QuickFind(int N)
{
	size=N;
	id=new int[N];
	for(int i=0;i&lt;N;i++)
	{
		id[i]=i;
	}
}

bool QuickFind::Find(int p,int q)
{
	return id[p]==id[q];
}

void QuickFind::Unite(int p,int q)
{
	int pid=id[p];
	for(int i=0;i&lt;size;i++)
		if(id[i]==pid)
			id[i]=id[q];

}
QuickFind::~QuickFind(void)
{
	delete []id;
}</pre>
QuickUnion的实现
<pre class="lang:default decode:true">#include "QuickUnion.h"

QuickUnion::QuickUnion(int N)
{
	size=N;
	id=new int[N];
	for(int i=0;i&lt;N;i++)
	{
		id[i]=i;
	}
}
int QuickUnion::root(int i)
{
	while (i!=id[i])
	{
		//id[i]=id[id[i]]; 若添加这句话则为压缩路径
		i=id[i];
	}
	return i;
}
bool QuickUnion::Find(int p,int q)
{
	return root(p)==root(q);
}

void QuickUnion::Unite(int p,int q)
{
	//将p的根挂在q的根上，
	//这样会导此数变高，若需要优化，需要设置另一个
	//数组sz[]，sz[i]表示所以根为i的节点的数目，然后将
	//小树靠在大树上

	/*
	int i=root(p);
	int j=root(q);
	if(sz[i]&lt;sz[j])
	{
		id[i]=j;sz[j]+=sz[i];
	}
	else
	{
		id[j]=i;sz[i]+=sz[j];
	}*/
	int rootp=root(p);
	int rootq=root(q);
	id[rootp]=rootq;
}
QuickUnion::~QuickUnion(void)
{
	delete []id;
}</pre>
参考文档(英文)：[UnionFind.pdf](http://pan.baidu.com/share/link?shareid=436984&amp;uk=1493685990)

工程代码下载：[并查集Demo](http://pan.baidu.com/share/link?shareid=436978&amp;uk=1493685990)