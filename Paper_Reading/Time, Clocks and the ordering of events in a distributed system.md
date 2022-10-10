# Introduction

the partial ordering defined by the "happened before"

# The Partial Ordering(部分排序)

the real clocks are not perfectly accurate and do not keep precise physical time. **Thus, we want the "happened before" relation without using physical time.**

> ```
> -> define:
> (1)If a and b are events in the same process, and a comes before b, then a -> b. 
> (2) If a is the sending of a message by one process and b is the receipt of the same message by another process, then a -> b. 
> (3) If a -> b and b -> c the a -> c.Two distinct events a and b are said to be concurrent if a -/> b and b -/> a.
> 简单一句话：a发生在b之前，关系定义为 ->
> ```

# Logical Clocks

我们为进程Pi定义一个时钟Ci为进程中的每个事件分配一个编号`Ci<a>`。系统的全局时钟为C，对任意事件b，它的时间为C`<b>`，如果b是进程j中的事件，那么`C<b>`= `Cj<b>`。Ci是逻辑上的时钟，和物理时钟无关。它可以采用计数器的方式实现而不需要任何计时机制。

clok is just a way of assigning a number to an event, where the number is thought of as the time at which the event occurred.

> ```
> Clock Condition. For any events a,b:
> 						if a -> b then C(a) < C(b) C = clock
> ```

### Two conditions hold

C1. If a and b are events in process Pi, and a comes before b, then Ci(a) < Ci(b).

C2. If a is the sending of a message by process Pi and b is the receipt of that message by process Pj, then Ci(a) < Cj(b).



**The value of C(i) will change between events, so changing C(i) does not itself constitute an event.**(C(i) 的值会在事件之间发生变化，因此改变 C(i) 本身并不构成事件。)

to guarantee that the system of clocks satisfies C1 and C2

C1: **IR1**: `Each process Pi increments Ci between any two successive events.`

**任何一个进程Pi在两个成功事件之间递增Ci**

C2: message `m` carry the Timestamp `Tm` which equals the time at which the message was sent. **IR2**: `(a) If event a is the sending of a message m by process Pi,then the message m contains a timestamp Tm = Ci(a). (b) Upon receiving a message m, process Pi sets Ci greater than or equal to its present value and greater than Tm.`

**Ci+1 = max(Ci, Tm) + 1**

# Ordering the Events Totally

> ```
> => define:
> if a is an event in process Pi and b is an event in process Pj, the a => b if and only if either 
> (i) Ci(a) < Cj(b) 
> (ii) Ci(a) = Cj(b) and Pi < pj 
> ```

In other words, the relation => is a way of completing the "happened before" partial ordering to a total ordering

显而易见的，这是一个全序关系，同时如果a->b，那么a=>b。换言之，=>将“happened before”的偏序关系变成了全序关系。=>的顺序关系依赖于系统的时钟Ci，并且不是唯一的。选择满足Clock Condition的不同系统时钟将得到不同的全序关系。对于给定的全序关系，一定会有一个满足Clock Condition的系统时钟。只有偏序关系是由系统的事件唯一决定的。

Five rules:

1. Pi send message (Tm:Pi) requests resource to **every other process** and and puts that message **on its request queue**, where Tm is the timestamp of the message;

2. Pj receives the message (Tm:Pi), then places it on its request queue and sends a ack to Pi;

3. To release the resources, **process Pi remove any (Tm:Pi) requests resource message from its request queue** and **send a (Timestamped) Pi releases resource  message to every other process.**

4. When process Pj receives a Pi releases resource message, it removes any (Tm:Pi) requests resource message from its request queue.

5. Process Pi is granted the resource when the following two conditions are satisfied: **(i) There is a (Tm:Pi) requests resource message in its request queue which is ordered before any other request in its queue by the relation =>. (To define the relation "=>" for messages, we identify a message with the event of sending it.)** **(ii) Pi has received a message from every other process time- stamped later than Tm**

   Note that conditions (i) and (ii) of rule 5 are tested locally by Pi.

Condition(ii) of rule 5, together with the assumption that **messages are received in order**, guarantees that Pi has learned about all requests which preceded it current request.

Rule2 guarantees that after Pi requests the resource, condition (ii) of rule 5 will eventually hold. 

Rules 3 and 4 imply that if each process which is granted the resource eventually releases it, then condition (i) of rule 5 will eventually hold, thus proving condition III.

C*S->S e(C,S) = S\` means that executing the command C with the machine in state S causes the machine state to change to S'. **set c consists of all the commands Pi requests resource and Pi releases resource, S consist of the queue of waiting request commands, where the request at the head of the queue is the currently granted one.Executing a request com- mand adds the request to the tail of the queue, and executing a release command removes a command from the queue.**

**Because they receive the same Timestamp which was send to all process.** Synchronization is achieved because all processes order the commands according to their time- stamps (using the relation =>), so each process uses the same sequence of commands. 

Problem: one runs while the other must be active at the same time. **so that the failure of a single process will make it impossible for any other process to execute State Machine com- mands, thereby halting the system.** 容错性超级垃圾

# Anomalous Behavior(反常)

request according to the total ordering =>

problem: request `a` and computer `A`, tell computer `B` to issue a request `b`, It is quite possible for request `b` to receive a lower timestamp and be ordered before request `a`. In other word, this suppose to be `a -> b` but the total ordering receive `b` earlier than `a` which cause the wrong ordering  `b -> a`.

reason: **The precedence information is based on messages external to the system.**



how to deal with:

first way: Process A and receive the timestamp TA of the request from the system. When issuing request B, B will be given a timestamp later than TA.

Second way: 

> ```
> Strong Clock Condition: for any events a,b in ƒ:
>  if a --> b then C(a) < C(b) 注：--等于重-
> ```

This is stronger than the ordinary Clock Condition because `-->` is a stronger relation than `->`. It is not in general satisfied by our logical clocks.

We can therefore use physical clocks to eliminate anomalous behavior. We now turn our attention to such clocks. `-->` 依赖物理时钟帮助实现

# Physical Clocks

> ```
> Ci(t) denote the reading of the clock Ci at physical time t.
> ```

discrete clock which can be thought of as a continuous one in which there is an error of up to 1/2 "tick" in reading it.

More precisely, we assume that Ci(t) is a continuous, differentiable function of t except for isolated jump discontinuities where the clock is reset(我们假设 Ci(t) 是 t 的连续、可微函数，除了时钟被重置的孤立跳跃不连续性). Then dCi(t)/dt represents the rate at which the clock is running at time t.

In order for the clock Ci to be a true physical clock, it must run at approximately the `correct rate`. That is, we must have dCi(t)/dt `约等于` 1 for all t. More precisely, we will assume that the following condition is satisfied:

```
PC1: There exists a constant k << 1 （∞小）
			such that for all i:|dCi(t)/dt - 1| < k.
PC2: For all i,j |Ci(t) - Cj(t)| < e
```

The two clock never run at exactly the same rate, they will tend to drift further and further. We must therefore devise an algorithm to insure that PC2 always holds.

Let u be a number such that if event a occurs at physical time t and event b in another process satisfies `a -->` b, then b occurs later than physical time `t + u`. In other words, `u` is less than the shortest transmission time for interprocess messages. We can always choose `u` equal to the shortest distance between processes divided by the speed of light.(距离 / 光速)

To avoid anomalous behavior, we must make sure that for any i,j, and t: `Ci(t + u) - Cj(t) > 0`. Combining this with PC1 and 2 allows us to relate the required smallness of k and e to the value of u as follows. We assume that when a clock is reset, it is always set forward and never back. (Setting it back could cause CI to be violated.) PCI then implies that `Ci(t + u) - Ci(t) > (1 - k)u`. Using PC2, it is then easy to deduce that `Ci(t + u) - Cj(t) > 0` if the following inequality holds:
$$
e/(1 - k) <= u
$$
修改规则

> ```
> IR1’.对于每个i，如果Pi在物理时间t未收到消息，那么Ci是在时间t就是可微分的并且dCi(t)/dt>0.
> IR2’.(a)如果Pi在物理时间t发送了一个消息m，那么m将包含一个时间戳Tm=Ci(t).
> 		 (b)当在时间t’接收到消息m时，进程Pj将设置Cj(t’)等于max(Cj(t’-0),Tm+Um).(注：Cj(t’-0)=[limCj(t’-|&|),&->0])
> ```

## Physical Clocks 物理时钟

如果将时钟Ci作为一个真实的物理时钟，那么它还必须以一个近似正确的速率来运行。也就是说，必须要保对于所有的t，dCi(t)/dt≈1。更准确地说，我们要保证满足如下条件：

> PC1.存在一个常数k，对于所有的i：| dCi(t)/dt -1|<k
> 对于典型的由晶体控制的时钟来说，k<=10^-6. 如果时钟只是单单地以近似正确的速率运行是不够的。它们还必须是同步的，即对于所有的i，j，t来说，Ci(t)≈Cj(t)。更准确地说，必须存在一个足够小的常数e，满足如下条件：
> PC2.对于所有的i,j：| Ci(t)-Cj(t)|<e.
> 如果我们让图2中的垂直距离来表示物理时间的话，那么PC2意味着单个滴答线上的高度差异要小于e。

令u表示满足如下条件的值：如果事件a发生在物理时间t，同时b是发生在另一个进程中的满足a->b事件，那么b肯定发生在物理时间t+u之后。换句话说，u需要小于进程间消息传输的最短时间。我们可以用进程间的距离除以光速的值作为u的值。当然，这取决于∮’中消息是如何传输的，u的值也可以很大。

现在我们将规则IR1和IR2针对物理时钟修改如下：

> IR1’.对于每个i，如果Pi在物理时间t未收到消息，那么Ci是在时间t就是可微分的并且dCi(t)/dt>0.
> IR2’.(a)如果Pi在物理时间t发送了一个消息m，那么m将包含一个时间戳Tm=Ci(t).(b)当在时间t’接收到消息m时，进程Pj将设置Cj(t’)等于max(Cj(t’-0),Tm+Um).(注：Cj(t’-0)=[limCj(t’-|&|),&->0])

定理：

> 假设一个半径为d的由多个进程组成的强连通图始终满足规则IR1’和IR2’。同时对于任意消息m，Um<=某常数U,以及任意时刻t>=t0来说：
> (a) PC1成立.
> (b) 存在常数τ和ε，每τ秒都会有一个消息在不可预测的延迟部分小于ε的情况下在每条边上传送。那么PC2就能被满足，同时对于所有的t>t0+τd，e≈d(2kτ+ε)，这里我们假设U+ε<<τ。

# 感悟

递进解释了偏序与全序的作用。在偏序的关系中将因果与并发的概念深入进行了分析。

## 偏序和全序背景知识

### 离散数学

**偏序： 设R是非空集合A上的关系，如果R是自反的，反对称的，和传递的，则称R是A上的偏序关系。**

> ```
> 偏序的定义：设R是集合A上的一个二元关系，若R满足：
> Ⅰ 自反性：对任意x∈A，有xRx；
> 假设A是一个集合 {1,2,3} ；R是集合A上的关系，例如{<1,1>,<2,2>,< 3,3>,<1,2>,<1,3>,<2,3>} 因为包含<1,1> <2,2> <3,3>全部<a,a>序列对，反自反不包括任何形如<a，a>的序对。仅仅包含<1,1> 不能成为自反，也不能称为非自反
> Ⅱ 反对称性（即反对称关系）：对任意x,y∈A，若xRy，且yRx，则x=y；
> 反对称性：任取两个A中元素x,y(x!=y)，如果<x,y> 在关系R上,那么<y,x> 不在关系R上，那么R是反对称的。
> Ⅲ 传递性：对任意x, y,z∈A，若xRy，且yRz，则xRz。 则称R为A上的偏序关系。
> 传递性：任取三个A中元素x,y,z，如果<x,y>,<y,z> 在关系R上，那么 <x,z> 也在关系R上，那么R是传递的。
> ```

**全序：如果R是A上的偏序关系，那么对于任意的A集合上的 x,y，都有 x <= y，或者 y <= x，二者必居其一，那么则称R是A上的全序关系。**重点是有一个全部的关系，**任意的**条件要满足

{1,2,3} ->      <1,1><2,2><3,3><1,2><2,3><1,3> 全序

全序的定义：设集合X上有一全序关系，如果我们把这种关系用 ≤ 表述，则下列陈述对于 X 中的所有 a, b 和 c 成立：
如果 a ≤ b 且 b ≤ a 则 a = b (反对称性)
如果 a ≤ b 且 b ≤ c 则 a ≤ c (传递性) a ≤ b 或 b ≤ a (完全性)
注意：完全性本身也包括了自反性。 所以，全序关系必是偏序关系

所以可以看到，全序也是一种偏序。偏序究竟在说啥，关键在于**反对称性**上，就是说，<x,y> 在关系R上，那么 <y,x> 不在关系R上，那我问你，<y,x> 关系是啥，就是未知。所以说**偏序就在于你的集合A={1,2,3,4}，有一些元素的关系根据R你是得不出的**。那么既然你不知道这个<y,x>，那么全序关系上，就多加一个条件，都有 x <= y，或者 y <= x，二者必居其一。

## 偏序

分布式系统中如果没有实际的时钟，那么我们能依赖的就只有Happened Before了，即在某些条件下，我们可以确定event A一定在event B之前发生。这里有一个非常重要的理念：**在分布式系统中，我们能依赖的只有这种部分事件之间的Happened Before关系，也就是Partial Ordering**。在分布式系统中有2种情况是可以建立起Happened Before关系的:

- 同一个process先后发生的event A和event B
- 同一个message从event A发送，由event B接收

Figure 1中由下往上是时间的流逝，自左往右是不同的空间中的process。Figure 1中的有些event之间是有直接（p1 -> q2）或者间接（p1 -> r3）的happened before关系的；而有些event之间是没有的（p3和q3），因此我们无法确定p3和q3之间的顺序，所以我们说p3和q3是并发的。

Happened Before的理论和**狭义相对论**中的物理世界中的观念十分的类似，即event的先后是相对的，在实际世界中的不同惯性坐标系下，2个event的先后关系可能发生变化。不过Paper中没有深入的对比，我会在**感想**部分中深入讨论下

## 逻辑时钟

为了能显式地表示分布式系统中事件的偏序，我们需要引入逻辑时钟，顾名思义，这种时钟与物理时钟没有任何关系。假设每个进程 `Pi` 的逻辑时钟为 `Ci`，那么对于 `Pi` 中的任意事件 a，其发生的时刻即为 `Ci<a>` 。

根据上文中偏序的讨论，一个正确的逻辑时钟需要满足：

> ```
> Clock Condition. For any events a,b:
> 						if a -> b then C(a) < C(b) C = clock
> ```

需要注意的是，Clock Condition 的逆命题 "如果 `Ci<a>`  <  `Ci<b>` ，那么 a → b" 并不成立，若后者成立则意味着系统中所有并发事件必须在相同的逻辑时刻发生，事实并非如此。

如果以下两个条件成立：

1. 如果a和b是进城Pi中的两个事件，且 a 先于 b 发生，那么`Ci<a>` < `Ci<b>`
2. C2：如果进程 Pi 向进程 Pj 发送消息，a 表示发送事件，b 表示接受事件，那么`Ci<a>` < `Cj<b>`。

那么我们可以说服自己 “Clock Condition 成立”。顺着思路，我们可以构建这样一个逻辑时钟

1. 在进程内部，每发生一个事件，逻辑时钟绝对值自增一次

2. (1)如果 a  事件是 "Pi 发送消息 m 到 Pj "，那么 m 需要包含 Pi 发送消息时的逻辑时间戳 Tm

   (2)当 Pj 接收到消息 m 时，必须将自身的逻辑时钟与 Tm 对齐，即 Cj+1 = max(Tm , Cj) + 1



**注意：我这里说的是判定，虽然我们有了一致的选择标准，但是，由于异步时间模型，消息的到来是不可知的，所以，每个机器在同一物理时间上看到的可选事件是不同的，这就是并行冲突发生的原因，由此，引出了两种解决并行冲突的方式，一种是悲观（阻塞）的方式，一种是乐观（回滚）的方式。**

**lamport使用了阻塞的方式，这种方式实现简单，但是代价太高，容易造成一台当机，整个集群不可用的问题，而且使得集群串行化。不过，这个方式确实解决了数据的一致性问题。**

## 全序

全序, 集合任意两个元素之间都是有序的, 线性关系
偏序, 只有部分元素之间有顺序关系, 非线性, 比如分布式系统中的并发关系

![](https://img-blog.csdnimg.cn/20200527094255406.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2MxODM2NjIxMDE=,size_16,color_FFFFFF,t_70)

只要能够确定 Ex 与 E2、E3 之间的关系，不论是以下哪种情况：

1. Ex -> E2 -> E3
2. E2 -> Ex -> E3
3. E2 -> E3 -> Ex

只要有一种方式能从中稳定的选一种，问题就解决了。

这个问题的解决出奇的简单，我们只要能给系统中的所有进程排个序即可。

下面给出严谨的定义：假设进程间的顺序关系用 “≺ ” 表示，系统中所有事件的全序用 “ ⇒” 表示，那么 “a ⇒ b” 意味着下面两个条件至少有一个成立：

1. `Ci<a>` < `Cj<b>`
2. `Ci<a>` = ` Cj<b>` 且 Pi≺Pj

> 既然知道了我们唯一能确定的就是Happened Before关系，但是我们在解决现实问题的时候，往往会用到全局序（Total Ordering）。那么我们可以预定义一个process之间的顺序，假设当event的逻辑时间相等时，process1的event永远先于process2的，那么我们就可以确定一个全局序。**这里要强调的是Partial Ordering是唯一的，而Total Ordering是不唯一的。**
>
> 针对于这句话的解释，这里的全序和偏序是与线性代数不一致的，从论文给予的定义，如果有逻辑时间一致的情况，那么现在的情况就是要我们自己决定，partial Ordering也无法给予a与b的顺序，但是其他顺序可以严格根据 partial Ordering 决定，所以说 Partial Ordering 是唯一的。但是Total Ordering可以确定为a-b或者b-a两种，就不是唯一的
>
> 本章举了一个在没有Coordinator的情况下的，分布式临界资源算法。**注意这里的分布式临界资源是指没有统一协调者，没有统一等待队列的，需要多个process互相协商的算法。**这个算法非常重要，因为这是一个无中心的分布式算法（2PC是有中心的，才有block问题）。几乎所有的无中心分布式算法都是从这个算法演化而来的。同时我们可以通过状态机（State Machine）的思想，将该算法演变成一个完整的分布式系统。



### 场景举例

实现事件的全序是保障分布式系统正确性的起点，这意味着系统中的多个节点能够达成共识。

想象这样一个具体场景：一组进程需要排他地使用某个资源，如何能保证所有进程以没有分歧的方式轮流使用这个资源？即满足：

1. 当前进程释放资源后，其它进程才能使用资源
2. 资源必须按照请求的先后顺序来赋予其它进程
3. 只要每个进程在使用完资源后会遵守规定释放资源，那么每个请求最终都将被满足

其实这个问题关键就在于 2 中, 如何分布式的环境下确定请求发生的顺序?
使用中央调度进程按照资源请求被收到的顺序, 是否可行? (不考虑安全性, 即单点问题)
论文认为不可行, 因为要求是按照请求发生的顺序, 由于网络问题, 请求被接受顺序可能和发生顺序完全不同

### 调度中心方案

我们能否用唯一的一个调度进程 P0 来满足这个方案？似乎可以？

但想象这样一个场景：

1. P1 先向 P0 发送资源使用请求，然后向 P2 发送一条消息
2. P2 收到消息后向 P0 发送资源使用请求
3. 因为网络传输的延迟问题，P2 的请求先于 P1 的请求到达 P0
4. P0 将资源先分配给 P1，等 P1 释放资源后再分配给 P2

但是根据事件的偏序关系，我们知道 P2 是在收到 P1 消息后才发送资源使用请求，但 P1 在此之前已经发送了资源使用请求，这违背了场景描述的第 2 点要求。

### 全序方案

基于我们构建全序的方案，我们来尝试解决这个问题。为了简化逻辑，我们先制定一个假设：

> 对于任意两个进程 Pi 和 Pj ，从 Pi 发送到 Pj 的消息将按顺序到达

即使没有该假设，我们仍然可以自行通过类似 TCP 的 sequence number 和 ack 机制使该假设成立。

每个进程 Pi 需要维护一个逻辑时钟和收到的请求队列 (Request Queue)，具体实现如下：

> 1. 请求资源时，进程 Pi 需要向所有其它进程 Pj 发送消息 “Tm:Pi 请求使用资源”，消息中包含 Pi 的当前时间戳，同时把请求也放进本地的请求队列中
> 2. 当进程 Pj 收到请求后，将请求放入请求队列后，返回 ack 给 Pi，ack 消息中包含 Pj  当前的最大时间戳
> 3. 当进程 Pi 要释放资源时，从本地的请求队列中移除自己的请求 “Tm:Pi 请求使用资源”，并向其它进程 Pj 发送释放消息 “Tm:Pi 释放资源”
> 4. 当进程 Pj 收到释放请求时，从本地请求队列中移除相应的请求 “Tm: Pi 请求使用资源”
> 5. 当满足以下条件时，进程Pi 被赋予资源的访问权：
>    - 按照逻辑时钟的顺序，消息 “Tm:Pi 请求使用资源” 发生的时间 Tm 最早
>    - Pi 已经收到过其余所有进程的时间戳大于 Tm 的消息

论文上有相关的证明推理过程，这里不再复述。这里想说的是：确定全序的核心在于 **“保证每个进程在做出占用或释放资源的决定之前已经充分了解到其它进程的信息”**。

### 方案对比

如果场景的要求是：“全局上看，所有进程按照一个确定的顺序使用和释放资源”，那么 **调度中心方案** 是满足要求的，这里要求 “资源必须按照请求的先后顺序来赋予其它进程”，主要在于我们要求整个系统中的所有事件存在全序关系。值得关注的是，这里的 **全序方案** 不需要 master 节点，所有节点的身份相同，你可以将它与如今流行的分布式共识算法的选主阶段关联起来理解。



## 外部事件

在上文的讨论中，实际上隐含着一个假设：

> 所有的事件都发生在系统内部

如果系统内部的两个事件 a 和 b 之间没有直接关系，但通过某外部事件产生了因果关系，那么对于系统本身，无论如何也无法通过逻辑时钟来捕捉到这一关系，保证数据一致性也就无从谈起了。

举例如下：假设 A 发送消息给 C ，同时打了个电话给 B，让 B 也发消息给 C ，B 的消息可能在 A 的消息之前到达，且打电话事件属于外部事件，系统并不知晓。

## 物理时钟

### 外部事件

接下来，我们将提出一个分布式系统事件全序算法的改进版本，它将保证 PC2 成立（PC1 通过物理时钟本身决定），从而在分布式系统中确定容忍外部事件的全序。

[link](https://github.com/ZhengHe-MD/distributed-system-experiments/tree/master/total_ordering)

[相对论解释](https://www.jianshu.com/p/0c79d650d13f)

[全部链接](https://blog.csdn.net/dongfang12n/article/details/79970495)