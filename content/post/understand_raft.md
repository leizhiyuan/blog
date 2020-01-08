---
title: "深入理解Raft协议"
date: 2019-12-30T14:42:42+08:00
draft: false
categories: 
  - 学习笔记
tags: ["分布式","理论"]
---

本文部分以JRaft为例，来详细介绍Raft。

# Raft 来源
首先，我们介绍 Raft 问题的来源，Raft 实际上是一个一致性算法的一种实现，和Paxos等价，但是在实现上，简化了一些，并且更加易用。

这里面又引入了两个名字。一个是一致性，一个是Paxos，我们先说一致性，

一致性是一个可容错的分布式系统重的最基本的一个问题，一致性包含了“多个服务器对同一个值达成共识，一旦对某个值达成共识，这个决定就是不可变了”，通常，一致性算法，当多数服务器可用的时候，才有效，比如5个server，那么2个挂了，是没问题的，但是再挂一个，超过一半，就不能提供服务了。这句话也说明，他不会返回错误的值，因为都不提供服务了。

一致性通常和 Replicated State Machines（后面简称RSM）相关，最早提出是在图灵奖得主Leslie Lamport的著名论文"Time, clocks, and the ordering of events in a distributed system(1978)"论文中，比较系统性的阐述是在Fred Schneider的论文"  Implementing fault-tolerant services using the state machine approach(1990)"中。

它的基本思想是一个分布式的RSM系统由很多个replica组成，每个replica是一个状态机，它的状态保存在一组状态变量中。状态机的状态通过并且只能通过外部命令（commands)来改变。比如你可以把MySQL服务器想像成一个状态机。它每接收到一条带修改功能的SQL语句（比如update/insert)就会改变它的状态。一组配置好replication的MySQL servers就是典型的RSM。

RSM能够工作基于这样的假设：

如果一些状态机具有相同的初始状态，并且他们接收到的命令也相同，处理这些命令的顺序也相同，那么它们处理完这些命令后的状态也应该相同。
因为replica都具有相同的状态，所以坏掉任何一个也没有关系。有了RSM之后理论上可以做到永远不会因为机器的物理故障而丢失数据。

也就是说，根据论文的指导，比较普遍的构建容错系统的方法是，每个服务器都维持一个状态机和一个Log，状态机就是我们想要实现容错的一个组件实现，比如想实现一个分布式环境下可容错的 Hash Table，
客户端会和这个状态机交互，每个状态机从log中获取input命令，在这个Hash Table 的例子中，这个log 可能是类似 把X 设置成3这样的命令，一致性算法必须确保，如果任何一个状态机在第N个命令中认可了n被设置为了3，那么其他机器上的状态机器就绝对不应该设置为其他值，这就能保证其他所有的机器总是处理相同的命令序列，最终大家都是同样的状态。

至于Paxos，这里可以先简单理解就是个一致性算法的实现方式，和 Raft 类似。

总结就一致性是为了解决分布式环境下的容错问题，而Raft 和 Paxos 是其中的一种实现。

# 核心怎么实现呢

要实现Raft，根据作者的表述，通过对状态空间的简化，以及问题的分解，实现方只需要实现的就是各个子问题

状态空间:
状态太多就会增加理解的困难程度。Raft 算法尽可能地确定各个环节的状态。典型地，Raft 算法采用 strong leader 的模型，每个日志的读写均由 Leader 从中主动协调，这样一来，整体系统的数据流将非常简单：从 Leader 流行 Follower。而且每个节点的状态也只有 3 种：Leader，Candidate 和 Follower。

子问题:
Leader election：描述如何从集群的几个节点中选举出 Leader；
Log Replication：描述如何将日志同步到各个节点从而达成一致；
Safety：定义了一组约束条件来保证 Raft 算法的强一致性；
Membership changes：描述如何变更集群关系（增加或者减少节点）；


## Leader election

Raft的节点被称为peer，节点的状态是Raft算法的关键属性，在任何时候，Raft节点可能处于以下三种状态：

Leader：Leader负责处理客户端的请求，同时还需要协调日志的复制。在任意时刻，最多允许存在1个Leader，其他节点都是Follower。注意，集群在选举期间可能短暂处于存在0个Leader的场景。

Follower：Follower是被动的，它们不主动提出请求，只是响应Leader和Candidate的请求。注意，节点之间的通信是通过RPC进行的。

Candidate：Candidate是节点从Follower转变为Leader的过渡状态。因为Follower是一个完全被动的状态，所以当需要重新选举时，Follower需要将自己提升为Candidate，然后发起选举。

![leader选举](/images/2019-12-30-16-55-34.png)

但是这种机制也带来一个麻烦，如果一个节点 因为自己的原因没有看到 Leader 发出的通知，他就会自以为是的试图竞选成为新的Leader，虽然不断发起选举且一直未能当选（因为Leader和其他船都正常通信），但是它却通过自己的投票请求实际抬升了全局的 Term

为了阻止这种“捣乱”，可以设计一个预投票 (pre-vote) 环节。候选者在发起投票之前，先发起预投票，如果没有得到半数以上节点的反馈，则候选者就会放弃参选，也就不会提升全局的 Term。

1. Candidate 被 ET(Election Timeout) 触发
2. Candidate 开始尝试发起 pre-vote 预投票
3. Follower 判断是否认可该 pre-vote request
4. Candidate 根据 pre-vote response 来决定是否发起 RequestVoteRequest
5. Follower 判断是否认可该 RequestVoteRequest
6. Candidate 根据 Response 来判断自己是否当选

![选举](/images/2019-12-31-16-39-02.png)


## 线性一致性

线性一致读是在分布式系统中实现 Java volatile 语义，当客户端向集群发起写操作的请求并且获得成功响应之后，该写操作的结果要对所有后来的读请求可见。其实就是CAP里面的C，


### Raft log read

实际上如果基于Raft本身的设计，因为每次 Read 都需要走 Raft 流程，Raft Log 存储、复制带来刷盘开销、存储开销、网络开销，走 Raft Log不仅仅有日志落盘的开销，还有日志复制的网络开销，另外还有一堆的 Raft “读日志” 造成的磁盘占用开销，导致 Read 操作性能是非常低效的，所以在读操作很多的场景下对性能影响很大，在读比重很大的系统中是无法被接受的，通常都不会使用。

读请求的处理则没有这种限制：所有的节点（Leader与Followers）都可以处理用户的读请求，但是由于以下几种原因，导致从不同的节点读数据可能会出现不一致：

Leader和Follower之间存在状态差：这是因为更新总是从Leader复制到Follower，因此，Follower的状态总的落后于Leader，不仅于此，Follower之间的状态也可能存在差异。因此，如果不作特殊处理，从集群不同的节点上读数据，读出的结果可能不相同；假如限制总是从某个特点节点读数据，一般是Leader，但是如果旧的Leader和集群其他节点出现了网络分区，其他节点选出了新的Leader，但是旧Leader并没有感知到新的Leader，于是出现了所谓的脑裂现象，旧的Leader依然认为自己是主，但是它上面的数据已经是过时的了，如果客户端的请求分别被旧的和新的Leader分别处理，其得到的结果也会不一致。


### ReadIndex Read

第一种是 ReadIndex Read，当 Leader 需要处理 Read 请求时，Leader 与过半机器交换心跳信息确定自己仍然是 Leader 后可提供线性一致读：
1. Leader 将自己当前 Log 的 commitIndex 记录到一个 Local 变量 ReadIndex 里面；
2. 接着向 Followers 节点发起一轮 Heartbeat，如果半数以上节点返回对应的 Heartbeat Response，那么 Leader就能够确定现在自己仍然是 Leader；
3. Leader 等待自己的 StateMachine 状态机执行，至少应用到 ReadIndex 记录的 Log，直到 applyIndex 超过 ReadIndex，这样就能够安全提供 Linearizable Read，也不必管读的时刻是否 Leader 已飘走；
4. Leader 执行 Read 请求，将结果返回给 Client。


使用 ReadIndex Read 提供 Follower Read 的功能，很容易在 Followers 节点上面提供线性一致读，Follower 收到 Read 请求之后：
1. Follower 节点向 Leader 请求最新的 ReadIndex；
2. Leader 仍然走一遍之前的流程，执行上面前 3 步的过程(确定自己真的是 Leader)，并且返回 ReadIndex 给 Follower；
3. Follower 等待当前的状态机的 applyIndex 超过 ReadIndex；
4. Follower 执行 Read 请求，将结果返回给 Client。
不同于通过 Raft Log 的 Read，ReadIndex Read 使用 Heartbeat 方式来让 Leader 确认自己是 Leader，省去 Raft Log 流程。相比较于走 Raft Log 方式，ReadIndex Read 省去磁盘的开销，能够大幅度提升吞吐量。虽然仍然会有网络开销，但是 Heartbeat 本来就很小，所以性能还是非常好的。

### Lease Read

虽然 ReadIndex Read 比原来的 Raft Log Read 快很多，但毕竟还是存在 Heartbeat 网络开销，所以考虑做更进一步的优化。

Raft 论文里面提及一种通过 Clock + Heartbeat 的 Lease Read 优化方法，也就是 Leader 发送 Heartbeat 的时候首先记录一个时间点 Start，当系统大部分节点都回复 Heartbeat Response，由于 Raft 的选举机制，Follower 会在 Election Timeout 的时间之后才重新发生选举，下一个 Leader 选举出来的时间保证大于 Start+Election Timeout/Clock Drift Bound，所以可以认为 Leader 的 Lease 有效期可以到 Start+Election Timeout/Clock Drift Bound 时间点。Lease Read 与 ReadIndex 类似但更进一步优化，不仅节省 Log，而且省掉网络交互，大幅提升读的吞吐量并且能够显著降低延时。


Lease Read 基本思路是 Leader 取一个比 Election Timeout 小的租期（最好小一个数量级），在租约期内不会发生选举，确保 Leader 不会变化，所以跳过 ReadIndex 的第二步也就降低延时。由此可见 Lease Read 的正确性和时间是挂钩的，依赖本地时钟的准确性，因此虽然采用 Lease Read 做法非常高效，但是仍然面临风险问题，也就是存在预设的前提即各个服务器的 CPU Clock 的时间是准的，即使有误差，也会在一个非常小的 Bound 范围里面，时间的实现至关重要，如果时钟漂移严重，各个服务器之间 Clock 走的频率不一样，这套 Lease 机制可能出问题。


Lease Read 实现方式包括：
1. 定时 Heartbeat 获得多数派响应，确认 Leader 的有效性；
2. 在租约有效时间内，可以认为当前 Leader 是 Raft Group 内的唯一有效 Leader，可忽略 ReadIndex 中的 Heartbeat 确认步骤(2)；
3. Leader 等待自己的状态机执行，直到 applyIndex 超过 ReadIndex，这样就能够安全的提供 Linearizable Read。


### 日志复制

既然是共识算法，就不可避免的要对需要达成共识的内容在多个服务器节点之间进行传输，我们将这些内容封装成一个个日志块 (LogEntry)，这种服务器节点间的日志传输行为也就有了专门的术语：日志复制。

日志复制核心需要遵守的，就是从Leader到Follower，被复制的日志是有序且连续的。不能乱，也不能少。


快照是对日志复制的一个优化。日志复制只是基础概念，实际操作中，会遇到一些新问题：
1. 当对集群以新增节点方式来扩容，新节点需要从当前的 Leader 中获取所有的日志并重放到本身的状态机中，这对 Leader 和网络带宽都会带来不小的开销。
2. 因为服务器节点需要存储的日志不断增加，但是磁盘空间有限。

因此，需要引入快照，快照有点像电脑的ghost快照。就是合并命令，直接推进。快照的是当时的状态， 比如有一系列的命令，X<3,X<4,X<5,那么我们可以直接写X<5。这样既保留了状态，由不至于有很多无用的状态。

### Safety 和约束


在非拜占庭的场景下，绝对不会返回错误的结果。这些场景包括网络延迟，分区，包丢失，重复发包，乱序

约束

Leader选举约束 – 每个Term内最多一个Leader
日志匹配约束 - 如果多个日志，有相同的index和term，在到达指定的index时，他们的日志应该是完全一样的。
Leader 完整性 –  某个term内提交的日志，总是会出现在Leader的日志中
状态机约束 – 如果一个Server已经在自己的状态机里接受了一个index的命令，这种情况下，其他机器绝对不能接受在相同的index里不同的commnad。
Leader 是 Append-only – Leader 节点只能Append 命令，不能修改，删除，更新。
Follower 节点Crash – Follower Crash的时候，发给他的所有请求都被ignore了。也肯定不能参与选举，一定他重启成功，需要从Leader里面同步log。


### 节点变更

这个比较好理解，就是要支持新增/删除/替换节点，但是，在这个过程中，不是安全的，很有可能产生两个子集群。

![节点变更](/images/2020-01-02-08-33-13.png)

因此，为了安全的做节点变更，一般采用两步操作。    
1. leader先将C-old和C-new的并集发给新老followers，进入joint consensus
2. 一旦上一步做完，将新的使用新的集群配置发给C-new 集群

leader接收到一个改变配置从 C-old 到 C-new 的请求，会将并集的配置（C-old,C-new）以日志条目的形式存储并发送到新老所有节点。一旦一个服务器将（C-old,C-new）配置日志条目提交后，该节点就会用这个配置（C-old,C-new）来做出未来所有的决定。raft的机制保证只有拥有 C-old,C-new 日志条目的服务器才有可能被选举为领导人。当C-old,C-new日志条目被提交以后，leader在使用相同的策略提交C-new，如上图所示，C-old 和 C-new 没有任何机会同时做出单方面的决定，这就保证了安全性。

![节点变更2](/images/2020-01-02-08-37-58.png)

这里面有三个要特殊注意的点

1.新节点要加入的时候没有存储任何日志条目，如果该节点直接加入，可能要花一段时间来追赶日志。而这段时间可能无法响应client的请求。Raft解决该问题的方法是增加一个新的阶段，先将新的节点作为不计票的成员加入到集群。等到该新节点日志一致后再开始配置的更新。

2.leader节点有可能不在新的配置中。这种情况下，leader一旦提交了C-new，就会转变成follower状态。会触发重新选举

3.删除节点有可能导致集群崩溃。这些被删除掉的节点将会收不到心跳，从而他们会反复地开启新的选举。raft中采取当follower确定目前有leader的时候拒绝掉RequestVote RPCs来解决该问题。确定目前有leader的方式是设置了一个最小的election timeout时间，在该时间内收到RequestVote RPCs时拒绝。由于正常选举，节点的时间都会被该时间要大，则该优化不会影响到正常选举。


# 我用Paxos 不行吗？

1. 太难理解
2. 没有提供比较好的工程实践，尤其是multi-Paxos，很多理论上没补全。

# 参考文献

Raft 官网 : https://raft.github.io/

In Search of an Understandable Consensus Algorithm : https://raft.github.io/raft.pdf
