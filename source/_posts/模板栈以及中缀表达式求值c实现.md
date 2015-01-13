title: 模板栈以及中缀表达式求值(C++实现)
tags:
  - C++
  - 大学
  - 学习
  - 算法
  - 编程
  - 软件
id: 2865
categories:
  - 学习笔记
date: 2013-04-11 18:50:25
---

栈直接用链表实现，这个比较简单，不多说，不过C++写程序，IDE的错误检测不是很给力。

至于给定一个中缀表达式，如何不转换成后缀表达式，直接求值，方法就是使用两个栈，一个操作符栈，一个操作数栈，然后从左到右扫描表达式，我这里中缀表达式计算实现的很简单，不完整，大家可以扩展。栈的实现是我想写的，思路如下：

1.如何是操作数，压入操作数栈

2.如果是操作符，压入操作符栈

3.如果是左括号，直接忽略

4.如果是有括号，弹出操作符栈栈顶元素，然后弹出操作数栈两个元素，进行操作以后结果压入操作数栈

&nbsp;

看个图就好了

[![]({{BASE_PATH}}/images/d0b2eed86d2ba2b987d3e5db380b9a452e8f5f87.png)](http://leaverimage.b0.upaiyun.com/34863_o.png)

&nbsp;

最后给出栈顶实现代码
<pre class="lang:default decode:true">#include "stdafx.h"
#pragma region Node定义

template &lt;class T&gt;
class Node
{
	template&lt;class T&gt; 
	friend class Stack;
private:
	T m_data;
	Node *pNextNode;
public:
	Node();
	Node(T d);
};

template &lt;class T&gt;
Node&lt;T&gt;::Node()
{
	m_data=default(T);
	pNextNode=NULL;
}
template &lt;class T&gt;
Node&lt;T&gt;::Node(T d)
{
	m_data=d;
	pNextNode=NULL;
}
#pragma endregion 

#pragma region Stack定义

template &lt;class T&gt;
class Stack
{

private:
	Node&lt;T&gt; *m_pTopNode;
	int m_nNodeCount;
public:
	Stack();
	~Stack();
	bool IsEmpty();
	bool Push(T e);
	T Pop();
	int Size();
};

template &lt;class T&gt;
Stack&lt;T&gt;::Stack() : m_pTopNode(NULL),m_nNodeCount(0)
{
}

template &lt;class T&gt;
Stack&lt;T&gt;::~Stack()
{
	while (!IsEmpty())
	{
		Node&lt;T&gt; *pTempNode = m_pTopNode;
		m_pTopNode = m_pTopNode-&gt;pNextNode;
		delete (pTempNode);
		pTempNode = NULL;
	}
	m_nNodeCount = 0;
	m_pTopNode = NULL;
}

template &lt;class T&gt;
bool Stack&lt;T&gt;::IsEmpty()
{
	return (m_pTopNode == NULL);
}

template &lt;class T&gt;
bool Stack&lt;T&gt;::Push(T e )
{
	Node&lt;T&gt; *pNewNode = new Node&lt;T&gt;(e);
	if (NULL == pNewNode)     {
		return false;
	}

	if(! IsEmpty()) {
		pNewNode-&gt;pNextNode = m_pTopNode;
	}
	m_pTopNode = pNewNode;
	m_nNodeCount ++;

	return true;
}

template &lt;class T&gt;
T Stack&lt;T&gt;::Pop()
{
	if(IsEmpty()) {
		return T(-1);
	}
	T e;
	e = m_pTopNode-&gt;m_data;
	Node&lt;T&gt; *pNode = m_pTopNode;
	m_pTopNode = m_pTopNode-&gt;pNextNode;
	delete (pNode);
	m_nNodeCount--;

	return e;
}

template &lt;class T&gt;
int Stack&lt;T&gt;::Size()
{
	return m_nNodeCount;
}
#pragma endregion</pre>
然后是main函数代码
<pre class="lang:default decode:true">int _tmain(int argc, _TCHAR* argv[])
{
	string str;
	str="(1+((2+3)*(4*5)))";
	for (int i=0;i&lt;str.length();i++)
	{
		char s=str[i];
		if (s=='(') ;
		else if (s=='+') ops.Push(s);
		else if (s=='*') ops.Push(s);
		else if (s==')') 
		{
			char op = ops.Pop();
			if (op=='+') 
				vals.Push(vals.Pop() + vals.Pop());
			else if (op=='*') 
				vals.Push(vals.Pop() * vals.Pop());
		}
		else 
			vals.Push(s-'0');
	}
	cout&lt;&lt;(vals.Pop());
	return 0;
}</pre>
另外，今天是博客建站一周年.加油！