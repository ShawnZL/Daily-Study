# Lambda

Robust and fault-tolerant（容错性和鲁棒性）

Low latency reads and updates（低延时）

Scalable（横向扩容）

Extensible（可扩展）

Allows ad hoc queries（方便查询）

Minimal maintenance（易于维护）

Debuggable（易调试）

## 三层架构

查询都可以表示为：Query = Function(All data)，如果在数据量非常大的时候，且还要支持实时查询，就会消耗巨大的系统资源，或者难以达到。

### 预运算查询函数（precomputed query function）

预运算查询函数可以在系统空闲的时候根据业务需要的设计，去运行查询分析作业，然后生成结果我们称之为Batch view （批视图），此时查询就变为了：

> Batch view = Query Function(All data)
>
> Query = Function(Batch view)

从表达式中我们可以看出，真正的业务查询实际上是查询的 批处理视图，也即是我们预先准备好的数据内容。我们也可以把这一步称之为：**中间数据生成。**

那么在Lambda架构中，把Batch view生成称之为Batch Layer 批处理层

### Batch Layer

存储Master Dataset，这是一个持续增长的数据集，

在Master Dataset上执行预计算函数，构建查询所需要的对应的view

我们把预处理结果称之为view，通过view可以快速得到结果（或者说对比query All data 简单太多）

可以看出，预计算函数，本质上就是一个批处理，那么就比较适合使用MR、Spark等计算引擎进行处理

### Speed Layer

Batch Layer可以很好的处理离线数据，但是在我们的小组中，有许多存在的实时增量数据，而Speed Layer这一层就是用来处理实时增量数据的。Speed Layer层是对数据进行计算并生成一个Realtime View

与`Batch Layer`相比

> Speed Layer处理的数据是最近的增量数据流，Batch Layer处理的全体数据集
>
> Speed Layer会对接受的新数据不断跟新`Realtime View`，而Batch Layer是根据离线数据集直接得到Batch View
>
> Speed Layer是一种增量计算，而非从新计算（recomputation）
>
> Speed Layer因为采用增量计算，所以延迟小，而Batch Layer是全数据集的计算，耗时比较长

**realtime view＝function(realtime view，new data)**

#### 容错性

Speed Layer中处理的数据也不断写入Batch Layer，当Batch Layer中重新计算的数据集包含Speed Layer处理的数据集后，当前的Realtime View就可以丢弃，这也就意味着Speed Layer处理中引入的错误，在Batch Layer重新计算时都可以得到修正。

#### 复杂性隔离

Speed Layer采用增量算法处理实时数据，复杂性比Batch Layer要高很多。通过分开Batch Layer和Speed Layer，把复杂性隔离到Speed Layer，可以很好的提高整个系统的鲁棒性和可靠性。也就是Batch Layer根据其执行的时间间隔，不断的将Batch View 涵盖的范围覆盖到新数据上。而一旦Batch View执行到了某个时间点，这个时间点之前的Realtime View就会被丢弃。也就是说，由于Batch View的高延迟，在需要得到最新数据结果的时候，由Realtime View做补充，然后再后方，Batch View不断的追赶进度。

### Serving Layer

Lambda架构中的最高层：Serving Layer层，可以理解为用户层，即响应用户的查询需求的层。

> Query = Function(Batch View + Realtime View)

## 问题

**实时和批量结果不一致引起的冲突**

**批量计算无法在时限内计算完成**

**开发和维护的问题**

**服务器存储开销大**

# kappa

Kappa架构的核心思想是通过改进流计算系统来解决数据全量处理的问题，使得实时计算和批处理过程使用同一套代码。

此外Kappa架构认为只有在有必要的时候才会对历史数据进行重复计算，而如果需要重复计算时，Kappa架构下可以启动很多个实例进行重复计算。

<img src="https://img-blog.csdnimg.cn/20210623211758478.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80ODE0Mzk5Ng==,size_16,color_FFFFFF,t_70" style="zoom:67%;" />

Kappa架构的核心思想，包括以下三点：

1.用Kafka或者类似MQ队列系统收集各种各样的数据，你需要几天的数据量就保存几天。

2.当需要全量重新计算时，重新起一个流计算实例，从头开始读取数据进行处理，并输出到一个新的结果存储中。

3.当新的实例做完后，停止老的流计算实例，并把老的一些结果删除。

## 缺点

流式处理对于历史数据的高吞吐量力不从心：所有的数据都通过流式计算，即便通过加大并发实例数亦很难适应IOT时代对数据查询响应的即时性要求。

开发周期长：此外Kappa架构下由于采集的数据格式的不统一，每次都需要开发不同的Streaming程序，导致开发周期长。

服务器成本浪费：Kappa架构的核心原理依赖于外部高性能存储redis,hbase服务。但是这2种系统组件，又并非设计来满足全量数据存储设计，对服务器成本严重浪费。

# IOTA