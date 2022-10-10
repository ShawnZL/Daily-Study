[原文](https://www.preethikasireddy.com/post/lets-take-a-crack-at-understanding-distributed-consensus)

Blockchains have forced engineers and scientists to re-examine and question firmly entrenched paradigms in distributed computing.

# What is a distributed System

> A distributed system is a group of computers working together to achieve a unified goal.

# Properties of a Distributed System

## C) Independent failure of components

> It’s impossible to have a system free of faults.

These failures can be broadly classified into three categories:

- **Crash-fail**: The component stops working without warning (e.g., the computer crashes).
- **Omission**: The component sends a message but it is not received by the other nodes (e.g., the message was dropped).
- **Byzantine**: The component behaves arbitrarily. This type of fault is irrelevant in controlled environments (e.g., Google or Amazon data centers) where there is presumably no malicious behavior. Instead, these faults occur in what’s known as an “adversarial context.” Basically, when a decentralized set of independent actors serve as nodes in the network, these actors may choose to act in a “Byzantine” manner. This means they maliciously choose to alter, block, or not send messages at all.(拜占庭：组件行为任意。 这种类型的故障与可能没有恶意行为的受控环境（例如，谷歌或亚马逊数据中心）无关。 相反，这些错误发生在所谓的“对抗性环境”中。 基本上，当一组分散的独立参与者充当网络中的节点时，这些参与者可能会选择以“拜占庭”方式行事。 这意味着他们恶意选择更改、阻止或根本不发送消息。)

Broadly speaking, there are two types of models to consider when making a distributed system:

**1) Simple fault-tolerance**

**2A) Byzantine fault-tolerance**

**2B) BAR fault-tolerance**

# FLP impossibility result

> Even a single faulty process makes it impossible to reach consensus among deterministic asynchronous processes.

At a high level, there are two ways to circumvent FLP impossibility:

1. Use synchrony assumptions.
2. Use non-determinism.

I know what you’re thinking: What the heck does this even mean?

Let’s revisit our impossibility result. Here’s another way to think about it: the FLP impossibility result essentially shows that, if we cannot make progress in a system, then we cannot reach consensus. In other words, if messages are asynchronously delivered, termination cannot be guaranteed. Recall that termination is a required condition that means every non-faulty node must eventually decide on some output value.

But how can we guarantee every non-faulty process will decide on a value if we don’t know when a message will be delivered due to asynchronous networks?

To be clear, the finding does not state that consensus is unreachable. Rather, due to asynchrony, consensus cannot be reached in a fixed time. Saying that consensus is “impossible” simply means that consensus is “not always possible.” It’s a subtle but crucial detail.

One way to circumvent this is to use timeouts. If no progress is being made on deciding the next value, we wait until a timeout, then start the steps all over again. As we’re about to see, this is what consensus algorithms like Paxos and Raft essentially did.

(使用同步假设

我知道你在想什么：这到底是什么意思？

让我们重新审视我们的不可能结果。这是另一种思考方式：FLP 不可能结果本质上表明，如果我们不能在系统中取得进展，那么我们就无法达成共识。换句话说，如果消息是异步传递的，则无法保证终止。回想一下，终止是一个必需条件，这意味着每个非故障节点最终都必须决定某个输出值。

但是，如果我们不知道由于异步网络何时传递消息，我们如何保证每个非故障进程都会决定一个值呢？

需要明确的是，该发现并未表明无法达成共识。相反，由于异步性，无法在固定时间内达成共识。说共识“不可能”仅仅意味着共识“并不总是可能的”。这是一个微妙但至关重要的细节。

避免这种情况的一种方法是使用超时。如果在决定下一个值时没有取得任何进展，我们会等到超时，然后重新开始这些步骤。正如我们即将看到的，这就是像 Paxos 和 Raft 这样的共识算法本质上所做的。)

# The DLS Algorithm