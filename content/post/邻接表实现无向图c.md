---
title: 邻接表实现无向图(C++)
tags:
  - C++
  - windows
  - 算法
id: 3008
categories:
  - 学习笔记
date: 2013-05-31 15:05:29
---

很早以前写的代码了，贴出来做个备份吧。用向量来存储一条邻接链表，存储可连通值。实现了判断是否连通，添加边，添加顶点的功能。

UnDirectGraph.h
<pre class="show-lang:1 lang:default decode:true">#pragma once

#include "stdafx.h"
#include &lt;vector&gt;
using namespace std;
class UnDirectGraph
{
private:
	int vCount;
	vector&lt;int&gt; *adj;
public:
	int GetVCount();
	UnDirectGraph(int vCount);
	void AddEdge(int v,int w);
	vector&lt;int&gt; &amp;Vadj(int v);
	bool IsConnected(int v,int w);
};</pre>
UnDirectGraph.cpp
<pre class="lang:default decode:true">#pragma once

#include "stdafx.h"
#include "UnDirectGraph.h"
using namespace std;
UnDirectGraph::UnDirectGraph(int _vCount)
{
	this-&gt;vCount=_vCount;
	adj=new vector&lt;int&gt;[vCount];

	for (int i=0;i&lt;vCount;i++)
	{
			adj[i].clear();
	}

}
void UnDirectGraph::AddEdge(int v,int w)
{
	adj[v].push_back(w);
	adj[w].push_back(v);
}

vector&lt;int&gt;&amp; UnDirectGraph::Vadj(int v)
{
	return adj[v];
}

bool UnDirectGraph::IsConnected(int v,int w)
{
	for (vector&lt;int&gt;::iterator iter=adj[v].begin();iter!=adj[v].end();iter++)
	{
		if (*iter==w)
		{
			return true;
		}
	}
	return false;
}

int UnDirectGraph::GetVCount()
{
	return vCount;
}</pre>
代码还算清晰，就不解释了，有问题留言反馈。谢谢。