---
title: "图的遍历(C#)"
tags:
  - C＃
  - windows
  - 大学
  - 学习
  - 收藏
  - 设计
  - 软件
id: 2561
categories:
  - 文章收藏
date: 2013-01-06 16:35:45
---

讲的非常好的一篇文章。感谢[abatei](http://www.cnblogs.com/abatei/archive/2008/06/06/1215114.html)，直接收藏分享之。

## 图的存储结构

图的存储结构除了要存储图中各个顶点的本身的信息外，同时还要存储顶点与顶点之间的所有关系（边的信息），因此，图的结构比较复杂，很难以数据元素在存储区中的物理位置来表示元素之间的关系，但也正是由于其任意的特性，故物理表示方法很多。常用的图的存储结构有邻接矩阵、邻接表、十字链表和邻接多重表。

### 8.2.1  邻接矩阵表示法

对于一个具有n个顶点的图，可以使用n*n的矩阵（二维数组）来表示它们间的邻接关系。图8.10和图8.11中，矩阵A(i，j)=1表示图中存在一条边(V<sub>i</sub>，V<sub>j</sub>)，而A(i，j)=0表示图中不存在边(V<sub>i</sub>，V<sub>j</sub>)。实际编程时，当图为不带权图时，可以在二维数组中存放bool值，A(i，j)=true表示存在边(V<sub>i</sub>，V<sub>j</sub>)，A(i，j)=false表示不存在边(V<sub>i</sub>，V<sub>j</sub>)；当图带权值时，则可以直接在二维数组中存放权值，A(i，j)=null表示不存在边(V<sub>i</sub>，V<sub>j</sub>)。

[![](/images/34232ee6d725c72cdc277e3b5146680671171e7f.jpg)](http://leaverimage.b0.upaiyun.com/31196_o.jpg)
图8.10所示的是无向图的邻接矩阵表示法，可以观察到，矩阵延对角线对称，即A(i，j)= A(j，i)。无向图邻接矩阵的第i行或第i列非零元素的个数其实就是第i个顶点的度。这表示无向图邻接矩阵存在一定的数据冗余。

图8.11所示的是有向图邻接矩阵表示法，矩阵并不延对角线对称，A(i，j)=1表示顶点V<sub>i</sub>邻接到顶点V<sub>j</sub>；A(j，i)=1则表示顶点V<sub>i</sub>邻接自顶点V<sub>j</sub>。两者并不象无向图邻接矩阵那样表示相同的意思。有向图邻接矩阵的第i行非零元素的个数其实就是第i个顶点的出度，而第i列非零元素的个数是第i个顶点的入度，即第i个顶点的度是第i行和第i列非零元素个数之和。

由于存在n个顶点的图需要n<sup>2</sup>个数组元素进行存储，当图为稀疏图时，使用邻接矩阵存储方法将出现大量零元素，照成极大地空间浪费，这时应该使用邻接表表示法存储图中的数据。

### 8.2.2 邻接表表示法

图的邻接矩阵存储方法跟树的孩子链表示法相类似，是一种顺序分配和链式分配相结合的存储结构。邻接表由表头结点和表结点两部分组成，其中图中每个顶点均对应一个存储在数组中的表头结点。如这个表头结点所对应的顶点存在相邻顶点，则把相邻顶点依次存放于表头结点所指向的单向链表中。如图8.12所示，表结点存放的是邻接顶点在数组中的索引。对于无向图来说，使用邻接表进行存储也会出现数据冗余，表头结点A所指链表中存在一个指向C的表结点的同时，表头结点C所指链表也会存在一个指向A的表结点。
[![](/images/e6366b11df17b7aee6ddd118de3c84bb1c760c37.jpg)](http://leaverimage.b0.upaiyun.com/31197_o.jpg)
有向图的邻接表有出边表和入边表（又称逆邻接表）之分。出边表的表结点存放的是从表头结点出发的有向边所指的尾顶点；入边表的表结点存放的则是指向表头结点的某个头顶点。如图8.13所示，图(b)和(c)分别为有向图(a)的出边表和入边表。
[![](/images/847e7b120ab02fcb88556adaa762a4098e7ba831.jpg)](http://leaverimage.b0.upaiyun.com/31198_o.jpg)
以上所讨论的邻接表所表示的都是不带权的图，如果要表示带权图，可以在表结点中增加一个存放权的字段，其效果如图8.14所示。

[![](/images/a78cf3f8419a98ca2cede25716595c14a30ae2d3.jpg)](http://leaverimage.b0.upaiyun.com/31199_o.jpg)

【注意】：观察图8.14可以发现，当删除存储表头结点的数组中的某一元素，有可能使部分表头结点索引号的改变，从而导致大面积修改表结点的情况发生。可以在表结点中直接存放指向表头结点的指针以解决这个问题（在链表中存放类实例即是存放指针，但必须要保证表头结点是类而不是结构体）。在实际创建邻接表时，甚至可以使用链表代替数组存放表头结点或使用顺序表存代替链表存放表结点。对所学的数据结构知识应当根据实际情况及所使用语言的特点灵活应用，切不可生搬硬套。

【例8-1  AdjacencyList.cs】图的邻接表存储结构
<div>
<pre class="lang:default decode:true">using System;
using System.Collections.Generic;
public class AdjacencyList&lt;T&gt;
{
    List&lt;Vertex&lt;T&gt;&gt; items; //图的顶点集合
    public AdjacencyList() : this(10) { } //构造方法
    public AdjacencyList(int capacity) //指定容量的构造方法
    {
        items = new List&lt;Vertex&lt;T&gt;&gt;(capacity);
    }
    public void AddVertex(T item) //添加一个顶点
    {   //不允许插入重复值
        if (Contains(item))
        {
            throw new ArgumentException("插入了重复顶点！");
        }
        items.Add(new Vertex&lt;T&gt;(item));
    }
    public void AddEdge(T from, T to) //添加无向边
    {
        Vertex&lt;T&gt; fromVer = Find(from); //找到起始顶点
        if (fromVer == null)
        {
            throw new ArgumentException("头顶点并不存在！");
        }
        Vertex&lt;T&gt; toVer = Find(to); //找到结束顶点
        if (toVer == null)
        {
            throw new ArgumentException("尾顶点并不存在！");
        }
        //无向边的两个顶点都需记录边信息
        AddDirectedEdge(fromVer, toVer);
        AddDirectedEdge(toVer, fromVer);
    }
    public bool Contains(T item) //查找图中是否包含某项
    {
        foreach (Vertex&lt;T&gt; v in items)
        {
            if (v.data.Equals(item))
            {
                return true;
            }
        }
        return false;
    }
    private Vertex&lt;T&gt; Find(T item) //查找指定项并返回
    {
        foreach (Vertex&lt;T&gt; v in items)
        {
            if (v.data.Equals(item))
            {
                return v;
            }
        }
        return null;
    }
    //添加有向边
    private void AddDirectedEdge(Vertex&lt;T&gt; fromVer, Vertex&lt;T&gt; toVer)
    {
        if (fromVer.firstEdge == null) //无邻接点时
        {
            fromVer.firstEdge = new Node(toVer);
        }
        else
        {
            Node tmp, node = fromVer.firstEdge;
            do
            {   //检查是否添加了重复边
                if (node.adjvex.data.Equals(toVer.data))
                {
                    throw new ArgumentException("添加了重复的边！");
                }
                tmp = node;
                node = node.next;
            } while (node != null);
            tmp.next = new Node(toVer); //添加到链表未尾
        }
    }
    public override string ToString() //仅用于测试
    {   //打印每个节点和它的邻接点
        string s = string.Empty;
        foreach (Vertex&lt;T&gt; v in items)
        {
            s += v.data.ToString() + ":";
            if (v.firstEdge != null)
            {
                Node tmp = v.firstEdge;
                while (tmp != null)
                {
                    s += tmp.adjvex.data.ToString();
                    tmp = tmp.next;
                }
            }
            s += "\r\n";
        }
        return s;
    }
    //嵌套类，表示链表中的表结点
    public class Node
    {
        public Vertex&lt;T&gt; adjvex; //邻接点域
        public Node next; //下一个邻接点指针域
        public Node(Vertex&lt;T&gt; value)
        {
            adjvex = value;
        }
    }
    //嵌套类，表示存放于数组中的表头结点
    public class Vertex&lt;TValue&gt;
    {
        public TValue data; //数据
        public Node firstEdge; //邻接点链表头指针
        public Boolean visited; //访问标志,遍历时使用
        public Vertex(TValue value) //构造方法
        {
            data = value;
        }
    }
}</pre>
</div>
AdjacencyList&lt;T&gt;类使用泛型实现了图的邻接表存储结构。它包含两个内部类，Vertex&lt;Tvalue&gt;类（109～118行代码）用于表示一个表头结点，Node类（99～107）则用于表示表结点，其中存放着邻接点信息，用来表示表头结点的某条边。多个Node用next指针相连形成一个单链表，表头指针为Vertex类的firstEdge成员，表头结点所代表的顶点的所有边的信息均包含在链表内，其结构如图8.12所示。所不同之处在于：

l         Vertex类中包含了一个visited成员，它的作用是在图遍历时标识当前节点是否被访问过，这一点在稍后会讲到。

l         邻接点指针域adjvex直接指向某个表头结点，而不是表头结点在数组中的索引。

AdjacencyList&lt;T&gt;类中使用了一个泛型List代替数组来保存表头结点信息（第5行代码），从而不再考虑数组存储空间不够的情况发生，简化了操作。

由于一条无向边的信息需要在边的两个顶点分别存储信息，即添加两个有向边，所以58～78行代码的私有方法AddDirectedEdge()方法用于添加一个有向边。新的邻接点信息即可以添加到链表的头部也可以添加到尾部，添加到链表头部可以简化操作，但考虑到要检查是否添加了重复边，需要遍历整个链表，所以最终把邻接点信息添加到链表尾部。

【例8-1  Demo8-1.cs】图的邻接表存储结构测试
<div>
<pre class="lang:default decode:true">using System;
class Demo8_1
{
    static void Main(string[] args)
    {
        AdjacencyList&lt;char&gt; a = new AdjacencyList&lt;char&gt;();
        //添加顶点
        a.AddVertex('A');
        a.AddVertex('B');
        a.AddVertex('C');
        a.AddVertex('D');
        //添加边
        a.AddEdge('A', 'B');
        a.AddEdge('A', 'C');
        a.AddEdge('A', 'D');
        a.AddEdge('B', 'D');
        Console.WriteLine(a.ToString());
    }
}</pre>
&nbsp;

</div>
运行结果：
A：BCD

B：AD

C：A

D：AB

&nbsp;

本例存储的表如图8.12所示，结果中，冒号前面的是表头结点，冒号后面的是链表中的表结点。

## 8.3 图的遍历

和树的遍历类似，在此，我们希望从图中某一顶点出发访遍图中其余顶点，且使每一个顶点仅被访问一次，这一过程就叫做图的遍历(TraversingGraph)。如果只访问图的顶点而不关注边的信息，那么图的遍历十分简单，使用一个foreach语句遍历存放顶点信息的数组即可。但如果为了实现特定算法，就需要根据边的信息按照一定顺序进行遍历。图的遍历算法是求解图的连通性问题、拓扑排序和求关键路径等算法的基础。

图的遍历要比树的遍历复杂得多，由于图的任一顶点都可能和其余顶点相邻接，故在访问了某顶点之后，可能顺着某条边又访问到了已访问过的顶点，因此，在图的遍历过程中，必须记下每个访问过的顶点，以免同一个顶点被访问多次。为此给顶点附设访问标志visited，其初值为false，一旦某个顶点被访问，则其visited标志置为true。

图的遍历方法有两种：一种是深度优先搜索遍历（Depth-First Search 简称DFS）；另一种是广度优先搜索遍历（Breadth_First Search 简称BFS）。

### 8.3.1  深度优先搜索遍历

图的深度优先搜索遍历类似于二叉树的深度优先搜索遍历。其基本思想如下：假定以图中某个顶点V<sub>i</sub>为出发点，首先访问出发点，然后选择一个V<sub>i</sub>的未访问过的邻接点V<sub>j</sub>，以V<sub>j</sub>为新的出发点继续进行深度优先搜索，直至图中所有顶点都被访问过。显然，这是一个递归的搜索过程。

现以图8.15为例说明深度优先搜索过程。假定V<sub>1</sub>是出发点，首先访问V<sub>1</sub>。因V<sub>1</sub>有两个邻接点V<sub>2</sub>、V<sub>3</sub>均末被访问过，可以选择V<sub>2</sub>作为新的出发点，访问V<sub>2</sub>之后，再找V<sub>2</sub>的末访问过的邻接点。同V<sub>2</sub>邻接的有V<sub>1</sub>、V<sub>4</sub>和V<sub>5</sub>，其中V<sub>1</sub>已被访问过，而V<sub>4</sub>、V<sub>5</sub>尚未被访问过，可以选择V<sub>4</sub>作为新的出发点。重复上述搜索过程，继续依次访问V<sub>8</sub>、V<sub>5</sub> 。访问V<sub>5</sub>之后，由于与V<sub>5</sub>相邻的顶点均已被访问过，搜索退回到V<sub>8</sub>，访问V<sub>8</sub>的另一个邻接点V<sub>6</sub>。接下来依次访问V<sub>3</sub>和V<sub>7</sub>，最后得到的的顶点的访问序列为：V<sub>1</sub> → V<sub>2</sub> → V<sub>4</sub> → V<sub>8</sub> → V<sub>5</sub> → V<sub>6</sub> → V<sub>3</sub> →V<sub>7</sub>。
[![](/images/13132121255a94bad1935b0182c903d462a9a856.jpg)](http://leaverimage.b0.upaiyun.com/31200_o.jpg)
下面根据上一节创建的邻接表存储结构添加深度优先搜索遍历代码。

【例8-2  DFSTraverse.cs】深度优先搜索遍历

打开【例8-1  AdjacencyList.cs】，在AdjacencyList&lt;T&gt;类中添加以下代码后，将文件另存为DFSTraverse.cs。
<div>
<pre class="lang:default decode:true">    public void DFSTraverse() //深度优先遍历
    {
        InitVisited(); //将visited标志全部置为false
        DFS(items[0]); //从第一个顶点开始遍历
    }
    private void DFS(Vertex&lt;T&gt; v) //使用递归进行深度优先遍历
    {
        v.visited = true; //将访问标志设为true
        Console.Write(v.data + " "); //访问
        Node node = v.firstEdge;
        while (node != null) //访问此顶点的所有邻接点
        {   //如果邻接点未被访问，则递归访问它的边
            if (!node.adjvex.visited)
            {
                DFS(node.adjvex); //递归
            }
           node = node.next; //访问下一个邻接点
        }
    }

    private void InitVisited() //初始化visited标志
    {
         foreach (Vertex&lt;T&gt; v in items)
        {
             v.visited = false; //全部置为false
         }
     }</pre>
</div>
【例8-2  Demo8-2.cs】深度优先搜索遍历测试
<div>
<pre class="lang:default decode:true">using System;
class Demo8_2
{
    static void Main(string[] args)
    {
        AdjacencyList&lt;string&gt; a = new AdjacencyList&lt;string&gt;();
        a.AddVertex("V1");
        a.AddVertex("V2");
        a.AddVertex("V3");
        a.AddVertex("V4");
        a.AddVertex("V5");
        a.AddVertex("V6");
        a.AddVertex("V7");
        a.AddVertex("V8");
        a.AddEdge("V1", "V2");
        a.AddEdge("V1", "V3");
        a.AddEdge("V2", "V4");
        a.AddEdge("V2", "V5");
        a.AddEdge("V3", "V6");
        a.AddEdge("V3", "V7");
        a.AddEdge("V4", "V8");
        a.AddEdge("V5", "V8");
        a.AddEdge("V6", "V8");
        a.AddEdge("V7", "V8");
        a.DFSTraverse();
    }
}</pre>
&nbsp;

</div>
运行结果：

V1 V2 V4 V8 V5 V6 V3 V7

&nbsp;

本例参照图8-15进行设计，运行过程请参照对图8-15所作的分析。

### 8.3.2  广度优先搜索遍历

图的广度优先搜索遍历算法是一个分层遍历的过程，和二叉树的广度优先搜索遍历类同。它从图的某一顶点V<sub>i</sub>出发，访问此顶点后，依次访问V<sub>i</sub>的各个未曾访问过的邻接点，然后分别从这些邻接点出发，直至图中所有已有已被访问的顶点的邻接点都被访问到。对于图8.15所示的无向连通图，若顶点V<sub>i</sub>为初始访问的顶点，则广度优先搜索遍历顶点访问顺序是：V<sub>1</sub> →V<sub>2</sub> → V<sub>3</sub> → V<sub>4</sub> → V<sub>5</sub> → V<sub>6</sub> → V<sub>7</sub> → V<sub>8</sub>。遍历过程如图8.16的所示。

[![](/images/137d86b71c14e3f0de5f3c57e2f04a6d18a27b94.jpg)](http://leaverimage.b0.upaiyun.com/31201_o.jpg)
和二叉树的广度优先搜索遍历类似，图的广度优先搜索遍历也需要借助队列来完成，例8.3演示了这个过程。

【例8-3  BFSTraverse.cs】广度优先搜索遍历

打开【例8-2  DFSTraverse.cs】，在AdjacencyList&lt;T&gt;类中添加以下代码后，将文件另存为BFSTraverse.cs。
<div>
<pre class="lang:default decode:true">  public void BFSTraverse() //广度优先遍历
  {
      InitVisited(); //将visited标志全部置为false
      BFS(items[0]); //从第一个顶点开始遍历
  }
  private void BFS(Vertex&lt;T&gt; v) //使用队列进行广度优先遍历
   {   //创建一个队列
      Queue&lt;Vertex&lt;T&gt;&gt; queue = new Queue&lt;Vertex&lt;T&gt;&gt;();
      Console.Write(v.data + " "); //访问
      v.visited = true; //设置访问标志
      queue.Enqueue(v); //进队
      while (queue.Count &gt; 0) //只要队不为空就循环
      {
          Vertex&lt;T&gt; w = queue.Dequeue();
           Node node = w.firstEdge;
          while (node != null) //访问此顶点的所有邻接点
          {   //如果邻接点未被访问，则递归访问它的边
              if (!node.adjvex.visited)
              {
                  Console.Write(node.adjvex.data + " "); //访问
                  node.adjvex.visited = true; //设置访问标志
                  queue.Enqueue(node.adjvex); //进队
              }
              node = node.next; //访问下一个邻接点
          }
      }
   }</pre>
&nbsp;

</div>
【例8-3  Demo8-3.cs】广度优先搜索遍历测试
<div>
<pre class="lang:default decode:true">using System;
class Demo8_3
{
    static void Main(string[] args)
    {
        AdjacencyList&lt;string&gt; a = new AdjacencyList&lt;string&gt;();
        a.AddVertex("V1");
        a.AddVertex("V2");
        a.AddVertex("V3");
        a.AddVertex("V4");
        a.AddVertex("V5");
        a.AddVertex("V6");
        a.AddVertex("V7");
        a.AddVertex("V8");
        a.AddEdge("V1", "V2");
        a.AddEdge("V1", "V3");
        a.AddEdge("V2", "V4");
        a.AddEdge("V2", "V5");
        a.AddEdge("V3", "V6");
        a.AddEdge("V3", "V7");
        a.AddEdge("V4", "V8");
        a.AddEdge("V5", "V8");
        a.AddEdge("V6", "V8");
        a.AddEdge("V7", "V8");
        a.BFSTraverse(); //广度优先搜索遍历
    }
}</pre>
</div>
运行结果：

&nbsp;

V1 V2 V3 V4 V5 V6 V7 V8

&nbsp;

运行结果请参照图8.16进行分析。

### 8.3.3  非连通图的遍历

以上讨论的图的两种遍历方法都是相对于无向连通图的，它们都是从一个顶点出发就能访问到图中的所有顶点。若无向图是非连通图，则只能访问到初始点所在连通分量中的所有顶点，其他连通分量中的顶点是不可能访问到的（如图8.17所示）。为此需要从其他每个连通分量中选择初始点，分别进行遍历，才能够访问到图中的所有顶点，否则不能访问到所有顶点。为此同样需要再选初始点，继续进行遍历，直到图中的所有顶点都被访问过为止。

[![](/images/7f0cae03c6b3c9704041e52e0ca4dfa3af5ce5c1.jpg)](http://leaverimage.b0.upaiyun.com/31202_o.jpg)
上例的代码只需对DFSTraverse()方法和BFSTraverse()方法稍作修改，便可以遍历非连通图。
<div>
<pre class="lang:default decode:true ">public void DFSTraverse() //深度优先遍历
    {
        InitVisited(); //将visited标志全部置为false
        foreach (Vertex&lt;T&gt; v in items)
        {
            if (!v.visited) //如果未被访问
            {
                DFS(v); //深度优先遍历
            }
        }
    }
    public void BFSTraverse() //广度优先遍历
    {
        InitVisited(); //将visited标志全部置为false
        foreach (Vertex&lt;T&gt; v in items)
        {
            if (!v.visited) //如果未被访问
            {
                BFS(v); //广度优先遍历
            }
        }
    }</pre>
&nbsp;

</div>