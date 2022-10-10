[全文翻译](https://zhuanlan.zhihu.com/p/405359833)

[解析](https://zhuanlan.zhihu.com/p/36325917)

# FLP Impossibility

# Introduction

`transaction commit problem`：事务提交问题

我们假设环境是是十分可靠的、健壮的。

> we show the surprising result that no completely asynchronous consensus protocol can tolerate even a single unannounced process death. 
>
>  本文目的就是：我们展示了令人惊讶的结果，不存在一个完全正确的异步一致性协议，能应对只有一个进程突发性地死掉的情况。

## Consensus Protocols(一致性协议)

`consensus protocols P` 是一个有N个进程的异步系统。

N>2 processors ommunicate by sending messages.

A `message` is a pair (p,m) where p is the processor the message is intended for, and m is the contents of the message.(process p 接受 m 然后进行下一步)

> 注意⚠️：If there are no messages in the message system addressed to p then *receive(p)* will return thet a *θ*. However if there are messages for *p* then *receive(p)* will return one of them at random or the a *θ*. **The only requirement is that calling *receive(p)* infinitely many times will ensure that all messages in the message buffer are eventually delivered to *p*. This means that messages may be arbitrarily delayed, but not completely lost.** (可能推迟，但是一定会接受运行)

`configuration` is defined as the internal state of all of the processors.(当前运行算法，内存内容包括消息缓存)

`step` from a configuration to next one, which **consists of a processor *p* performing *receive(p)* and moving to another internal state.**(重点是一个process)

`event`: system state may evolve is by some processor receiving a message, or null, from the message buffer. Each `step` is therefore uniquely defined by the message that is received (possibly the a θ) and the process that received it. That pair is called an event in the paper, so configurations move from one to another through events.(因此，系统状态可能演变的唯一方法是某个处理器从消息缓冲区接收消息或空值。 因此，每个步骤都由接收到的消息（可能是 θ）和接收它的进程唯一地定义。 该对在论文中称为事件，因此配置通过事件从一个移动到另一个。)(重点是多个process一起运行)

`schedule`: a possibly infinite sequence of events from a starting configuration C

`run`: the sequence of steps taken to realise the schedule.(实现这个run)

We say that a run is *deciding* provided that some process eventually decides according to the properties of consensus, and that a consensus protocol is *totally correct* if every admissible run is a deciding run.(我们说运行是决定性的，前提是某个过程最终会根据共识的属性做出决定，并且如果每个可接受的运行都是决定性运行，则共识协议是完全正确的。)

## 引理1

> Lemma 1.(引理1)
>
> 一个从C开始的schedule是一系列从C开始的逐次可应用的事件组成的有限或者无限序列б。一系列相关联step的序列称为run。如果б是有限集，我们令б(C)代表结果configuration，那么我们称该configuration是从C可达的(reachable)。 那些从某些初始configuration可达的configuration称之为可访问的(accessible)。我们下面提到的configuration都是可访问的。
>
> **假设从某个configuration开始的schedule б1 和 б2，分别可以到达 configuration C1和C2。如果 б1 和 б2 里执行各step的进程集合是不相交的，那么б2可以应用于C1，而 б1 可以应用于C2，同时最后他们将到达相同的configuration C3。**
>
> 证明：该结论可以从系统定义中立即得到因为 б1 和 б2 没有交集。
>
> ![Figure 1](https://pic4.zhimg.com/80/v2-430193f2f0f76b85812becb3f84bff6f_1440w.jpg)

其实说白了就是交换率。



如果一个configuration C的某个进程p处于决议状态同时y(p)=v，我们称该configuration具有决议值v。一个一致性协议如果满足如下两个条件，我们就称之为是部分正确(partially correct)的：

> 1. 不存在任何一个可访问的configuration具有超过一个的决议值。
> 2. 对于集合{0,1}中每个值v，总存在某些可访问的configuration具有决议值v。



进程p如果可以执行无限多的step我们就称之为是正常的(nonfaulty)，否则就是出错的。如果在一次运行中最多有一个进程出错，同时发送给正常进程的所有消息最后都能被收到，我们就称此次运行是合法的。

如果一次run中，某个进程可以达到决议状态，我们就称此次run是一次deciding run。**因为它可以完成最终的决议，给出一个大家都讨论的结果**。如果一个一致性协议在即使存在一个出错进程的情况下依然是partially correct的（有且仅有一个出错，可以得到最终的结果），同时每次合法的run都是deciding run，我们就称之为是完全正确(totally correct)的。我们的主要定理表明，对于一致性问题，每个partially correct的协议都有一些合法的run不是deciding run。

# Main Result

> Theorem 1.（结论）
>
>  No consensus protocol is totally correct in spite of one fault
>
> **在有一个进程出错的情况下，不存在一个完全正确的一致性协议。**
>
> 

令C代表一个configuration，V代表那些从C可到达的configuration的决议值。如果|V|=2，**能够到达的所有configuration的所有可能的共识值有两个（0和1）**，称C是bivalent(二价)的。如果|V|=1，**如果只有一个（0或1），分别对0和1记为0-valent和1-valent，**称C是univalent(单价)的，同时根据相应的决议值，称之为是0-valent或者1-valent的。根据P的完全正确性，可知所有合法的运行的V都不等于Φ。

**univalent是指共识值未来（在共识算法执行完毕后）是确定的，bivalent则表示不确定性。**

## 引理2

作者表明，这种配置的决定取决于接收消息的顺序以及是否有任何处理器发生故障，而不仅仅是处理器的初始值是什么。 这是由于异步系统模型固有的非确定性。

> LEMMA 2. P has a bivalent initial configuration.
>
> **P具有一个bivalent的初始configuration。**

证明：假设该定理不成立。

我们的目标就是最终得到一个结果，在一个稳定的状态下，最终态是bivalent。就是矛盾所在。

> 证明！！！
>
> **Cx={0,0,1}，Cy={1,0,1}，Cx和Cy只在p1上不同，那么Cx和Cy是相邻的。所有的初始configuration都可以通过一系列相邻的configuration连接起来（或者他们本身就是相邻的）。现在考虑一个0-valent的初始configuration C0和一个1-valent的初始configuration C1。C0在变化到C1的过程中（千万注意，C1，C0仅仅是一个状态标号），必然可以找到两个相邻的初始configuration，他们的valent正好发生了变化。例如，我们有一个共识算法是基于多数（majority）的，C0={0,0,0}，C1={1,1,1}。那么C0到C1的变化可能是：**
>
> ```
>   C0  ->  Cx  ->  Cy  ->  C1
> {0,0,0} {0,0,1} {1,0,1} {1,1,1}
> ```
>
> **Cx和Cy是相邻的，且Cx是0-valent，Cy是1-valent。**
>
> **取valent发生变化的两个相邻的configuration，C0和C1；令p是那个状态不同的进程。我们的共识算法必须要容忍进程p不可用的情况（论文的标题暗示的大前提）。**
>
> **用上面的例子，Cx和Cy，当p0不可用时，{?,0,1}究竟是0-valent还是1-valent呢？如果是0-valent，那么Cy={1,0,1}在p0可用时是1-valent，在p0不可用时是0-valent，从而Cy是bivalent(2价)；如果{?,0,1}是1-valent，那么类似地Cx是bivalent(2价)。**我们要容忍p，无论它行或是不行，我们在这里Cy这个状态希望得到唯一的一个结果。
>
> **于是我们导出了矛盾：在容忍一个进程不可用的前提下，假设不存在bivalent的初始configuration是不成立的。**

## 引理3

从一个 `bivalent configuration` 开始

If a protocol starts from a bivalent configuration and there is a message *e* that is applicable to that configuration, then the set of configurations reachable through any sequence of messages where *e* is applied last contains a bivalent configuration.

Even more informally, but more intuitively, it says that if you delay a message that is pending any amount from one event to arbitrarily many, there will be one configuration in which you receive that message and end up in a bivalent state. (更非正式但更直观的是，它表示，如果您将一条待处理的消息从一个事件延迟到任意多个，那么您将在一种配置中收到该消息并最终处于二价状态。)

> Lemma 3 (引理3)
>
> **令 C 是 P 的一个 bivalent 的初始 configuration，同时令 e=(p,m) 是一个可以应用于 C 的事件。令 ξ 代表所有从 C 开始的没有应用事件 e 的 configuration 的集合，令 φ = e(ξ) = {e(E)|E ∈ ξ， 然后 e 应用于 E } 那么 φ 也包含一个 bivalent 的 configuration。**

证明：

****

**令C是一个bivalent的初始configuration。e=(p,m)是一个可以应用到C的事件。X是不应用事件e到C的configuration的集合；Y是应用事件e到X的所有configuration的结果集合。那么Y包含一个bivalent的configuration。**

**证明仍然运用了反证法，假设Y中不含有bivalent的configuration。**

**由于C是bivalent的，所以既可能存在0-valent的E0，也可能存在1-valent的E1。如果Ei（i=0或1，下同）存在于X，那么Fi=e(Ei)存在于Y（注意Y是e作用到X中所有configuration之后得到的结果configuration的集合）；如果Ei不存在于X，那么在到达Ei的过程中，取应用事件e之前的configuration Gi，Fi=e(Gi)存在于Y。如下图所示：**

![](https://pic4.zhimg.com/80/v2-7b57a913cd21de037e0b5b68b6e63c9b_1440w.jpg)

Fi在Ei的前后取决于在去往Ei的路程上是否发生了事件e，i可以为0，可以为1。因为C就是一个bivalent。所以Fi可以为`F0`或者`F1`，**因此Y中同时存在0-valent和1-valent的configuration。**



**取Y中存在的0-valent的configuration Y0和1-valent configuration Y1；相应的取X中的configuration X0和X1使得e(X0)=Y0，e(X1)=Y1。（X1与X0只是不同状态的编号，不代表决策值为0或1）类似引理2中的证明方法，我们知道0-valent的X0和1-valent的X1有一系列相邻的configuration相连。如图：**

<img src="https://pic1.zhimg.com/80/v2-54eb80159e9a4fb3f6719bdd4513ed58_1440w.jpg" alt="Figue2" style="zoom:72%;" />

**设e'=(p',m')是使Xa变换到Xb的事件。那么p和p'的关系有两种情况：**

> CASE1：如果p’ ≠ p，那么根据引理 1，得到交换率 -> Yb=e’(Ya)，所以Ya是bivalent的（无法确定自己将来是0-valent还是1-valent）。这和我们的假设“Y中不含有bivalent的configuration”矛盾。
>
> 注意前提条件，Y0是0-valent，Y1是1-valent，但是但是从所有X -> Y，都不改变当前已经有的决议值。只用横向移动才改变。 
>
> 因为对于事件e，作用于(p,m)，即进程p运行m，并不改变当前对于我们需要的决策值的依赖，(0-valent -> 0-valent) (1-valent -> 1-valent)。所以对于相同的event ，因为一个0-valent 任何后继 configuration肯定都是一个0-valen的（见上两张图）。参见figure2。
>
> **Case2:如果p’ = p，考虑p不可用的情况。从Xa开始，假设有事件序列S，使A=S(Xa)。根据引理1（注意S和e作用于不同的进程），S也可以应用于Ya。因此Za=S(e(Xa))=e(A)。同样，S也可以应用于Yb，导致Zb=S(e(e'(Xb)))=e(e'(A))。Za和Zb分别是0-valent和1-valent，都可以通过A变换而来，从而A是bivalent的。但Xa是univalent的，所以A=S(Xa)应该也是univalent。这就导出了矛盾。其中Xa->Xb, Xa->Ya,Xb->Yb都是从上图中得到的，其中状态A是我们估计运行S之后推导出来的状态。**

![](https://pic3.zhimg.com/80/v2-e142b75cb1fac80398a76727420d7c6e_1440w.jpg)

**由引理2，我们知道当一个进程不可用时就可能导致一个bivalent的初始configuration。由引理3，我们知道从一个bivalent的configuration一定能达到另一个bivalent的configuration。因此这个循环可以一直进行下去，集群可能永远无法达成共识。**

# Initially Dead Processes(初始死进程)

在这一节，我们提出一个解决以下情况的一致性协议：总共有N个进程，其中半数以上的是正常进程，在协议执行过程中没有进程会死掉，但是进程不知道初始时到底是哪些进程死掉了。

该协议分为两个步骤。第一个步骤，进程首先构建一个有向图G，每个进程由图中的一个节点表示。每个进程将它自己的进程号广播给其他进程，之后会监听来自其他L-1个进程的消息，L=ceil((N+1)/2)。如果j收到了一条来自i的消息，那么就向图G中加入一条i->j的边。因此，G中节点的入度为L-1。

第二个步骤，进程构建图G+，即图G的传递闭包。在该步骤完成后，除了那些原本就与k相关联的边，每个进程k将能够获知图G+中所有与k相关的边(j,k)。

为了完成该步骤。每个进程会向其他所有进程广播自己的进程号初始值及它在第一个步骤里得到的L-1个进程名称。之后，它就会等待在G中已知的祖先进程发送的阶段2消息。最初，进程只知道在第一个阶段中向它发送消息的L-1个直接祖先进程，但是通过在阶段2收到的消息它知道更多的祖先节点。进程会一直等待，直到收到那些它已知道的所有进程消息为止。

这样，每个进程最后自己的所有祖先以及这些祖先自己的图G的所有边。使用这些信息，它可以计算出图G+中的所有与它的祖先相关联的边。这就使得它可以判定它的哪些祖先属于G+的一个初始闭包，即一个没有入边的闭包。对于节点k来说，如果它本身又是它所有祖先的祖先，那么我们就认为节点k属于一个初始闭包。因为G+的每个节点至少有L-1个祖先，因此只能存在一个初始闭包，同时它至少有L个节点。这样在第二个步骤完成后，每个进程就都知道组成该闭包的所有进程集合了。

最后，每个进程使用一个确定性的规则，在初始闭包中的进程的初始值上做出一个决议。因为所有的进程都知道初始闭包中所有成员的初始值，因此它们最后会得到相同的决议值。

该协议的正确性证明了如下定理：

## 定理2

> **定理 2 .如果在运行过程中没有进程死掉，同时有半数以上的进程是正常的，那么存在一个partially corrent的一致性协议，可以保证所有正常的进程可以达成一个决议。**

# Conclusion

我们已经证明了容错协作计算这个自然而重要的问题不能在完全异步的计算模型中解决。这些结果并不表明这些问题在实践中无法“解决”；相反，他们指出需要更精确的分布式计算模型，这更好地反映了关于处理器和通信时间的现实假设，以及对此类问题解决方案的不太严格的要求。(例如，只有概率为1的情况下才需要终止）这些结果的在这两方面都取得了进展。



# 自己总结

根据解决的是非拜占庭的普通共识问题还是拜占庭共识问题（是否允许系统内节点作恶，以及对完备性的不同要求）

解决这种异常问题有两种方式：`synchrony assumptions` or `non-deterministic consensus algorithm` 同步假设和非确定性共识算法

`synchrony assumption`: It basically means that we need to somehow guarantee **termination** so that we have every non-faulty node decide upon the same value eventually.(以某种方式保证终态) We can do this by implementing timeouts. Famous consensus algorithms like [Raft](https://raft.github.io/raft.pdf) and [Paxos](https://lamport.azurewebsites.net/pubs/lamport-paxos.pdf) implement timeouts for example.

`non-deterministic consensus algorithm`: Consensus algorithms that are non-deterministic are referred to often as probabilistic, meaning that instead of agreeing whether a value is correct. They agree on the probability that some value is correct. (非确定性的共识算法通常被称为概率，这意味着不是同意一个值是否正确。 他们同意某个值是正确的概率。),This is how **Nakomoto consensus** works, it does away with the traditional methods of achieving consensus and instead opts for a more simplified and scalable (with respect to participant inclusion) algorithm. It does away with electing leaders. Instead all nodes try to solve a known computationally hard mathematical problem. When the problem has been solved, the next value can be added. This forms a chain of values. Nodes choose to continue building on the chain of values that have the most combined computational power, preventing from branches with invalid values to be selected.(这就是中本聪共识的工作方式，它摒弃了实现共识的传统方法，而是选择了一种更简化和可扩展（关于参与者包容性）的算法。 它取消了选举领导人。 相反，所有节点都试图解决一个已知的计算困难的数学问题。 问题解决后，可以添加下一个值。 这形成了一个价值链。 节点选择继续在具有最大组合计算能力的值链上构建，防止选择具有无效值的分支。)[**Nakomoto consensus**](https://medium.com/nakamo-to/nakamoto-consensus-21cd304f96ff)



## **termination**, **agreement** and validity

Termination: All non-faulty processes eventually decide on a value(所有正确进程最后都能完成决定。)

agreement: All processes that decide do so on the same value.(所有正确进程决定相同的值。)

validity(有说服力的): The value that has been decided must have proposed by some process.(如果正确的进程都提议同一个值，那么所有正确进程最终决定该值。)

只有当满足这三个条件，才可以称为这个算法解决了consensus problem(共识问题)，The idea behind validity is that we want to exclude trivial solutions that just decide ‘No’ whatever the initial set of values is.

> The FLP proof actually concerns a slightly weaker form of consensus - for termination it is enough only that *some* non-faulty process decides. Note that it’s not enough to have one distinguished process that always decides its own value, as that process might fail, and another will have to take its place for even weak termination to be satisfied.
>
> FLP 证明实际上涉及一种稍微弱一点的共识形式——对于终止，只需要一些非错误的过程来决定就足够了。 请注意，仅拥有一个始终决定自己的值的杰出进程是不够的，因为该进程可能会失败，并且必须有另一个进程取代它才能满足即使是弱终止。



