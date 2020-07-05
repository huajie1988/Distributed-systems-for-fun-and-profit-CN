# 引言

我期望能写一篇文章，它可以将许多最新的分布式系统(诸如Amazon的Dynamo，谷歌的BigTable和MapReduce, 还有Apache的Hadoop等等)的思想整合在一起。

在这篇文章中我试图提供一种更简单的方式来介绍分布式系统。对我来说，这意味着两件事：介绍你将需要的关键性的概念，以便当你需要阅读更高深的文章时能有一个[良好的体验](https://www.google.com/search?q=super+cool+ski+instructor)。同时也必须提供足够详实的叙述让你明白这究竟是怎么一回事，而又不至于陷入细枝末节之中。在2013年，你可以通过互联网更有选择性地阅读那些你觉得最有趣的主题。

在我看来，大多数分布式编程都是关于处理分布后两个因素所带来的影响：

- 信息是以光速传播的
- 独立事物，独立失败*

换句话说，分布式编程的核心就是处理距离（废话！）和处理更多的事（废话！）。因此这些约束定义了一个可能的系统设计空间, 我希望在阅读完这篇文章之后，您会对距离、时间和一致性模型之间的相互作用有更好的理解。

本文主要讨论分布式编程和系统概念，在此之前你需要了解数据中心里的商业系统是如何运作的。尝试隐藏所有的一切显然是愚蠢的，因此您将学习许多关键的协议和算法（例如，在本学科中被引用次数最多的论文），其中包含一些新的令人兴奋而且还尚未被收录在大学教科书中的方法来研究最终的一致性,例如CRDTs和CALM定理。

我希望你喜欢这篇文章。如果你想表示感谢请在[Github](https://github.com/mixu/)或者[Twitter](http://twitter.com/mikitotakada)上关注我。当然，如果你发现了错误，也可以在[Github上pull request](https://github.com/mixu/distsysbook/issues)

------

# 1. 基础

[第一章](http://book.mixu.net/distsys/single-page.html#intro)通过一些重要的术语和概念来介绍高层次的分布式系统。它包括了一些需要实现的目标，如可扩展性、可用性、性能、延迟和容错能力；同时也介绍了其实现的难点以及抽象和模型，除此之外还介绍了分区和副本（replication）它们是如何发挥作用的。

# 2. 上下层的抽象

[第二章](http://book.mixu.net/distsys/single-page.html#abstractions)深入探讨了抽象与不可能性。首先我们将从尼采的引言开始，然后介绍系统模型以及在典型系统模型中所做的诸多假设。接下来我们将讨论CAP定理并总结了FLP不可能性的结论。然后转向CAP定理的含义，其中之一就是应该探索其他的一致性模型。最后讨论一些一致性模型。

# 3. 时间与序列

理解分布式系统的很大一部分就是能充分的理解时间和序列。如果我们不能很好的理解这一点我们的系统将面临失败。 [第三章](http://book.mixu.net/distsys/single-page.html#time)集中讨论了时间和序列以及时钟（如向量钟和失效检测器）之间的关系。

# 4. 复制: 防止差异

[第四章](http://book.mixu.net/distsys/single-page.html#replication) 介绍了复制问题及其实现的两种基本方式。事实证明，大部分的相关特性都可以用这个简单的描述来进行讨论。然后，从最小容错（2PC）到Paxos，讨论了维护单副本一致性的复制方法。

# 5. 复制: 允许差异

[第五章](http://book.mixu.net/distsys/single-page.html#eventual)讨论了复制在弱一致性时的保证。它引入了一个基本的协调方案，即利用分区副本尝试达成协议。然后以Amazon的Dynamo为例讨论了系统设计时如何保证弱一致性。最后，讨论了两个关于无序编程的观点: CRDTs 和CALM定理。

# 附录

[附录](http://book.mixu.net/distsys/single-page.html#appendix) 涵盖了进一步阅读的建议。

------

*: 这是一个[谎言](http://en.wikipedia.org/wiki/Statistical_independence). [Jay Kreps的这篇文章详细阐述了这一点](http://blog.empathybox.com/post/19574936361/getting-real-about-distributed-system-reliability)。