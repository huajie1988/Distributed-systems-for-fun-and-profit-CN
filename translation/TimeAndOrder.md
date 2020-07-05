# 3. 时间与序列

什么是顺序？它又为何重要?

那什么又叫 "何为顺序"？

我的意思是，为什么我们一开始就这么纠结于顺序？为什么我们要关心A是否发生在B之前？为什么我们不关心一些其他属性，比如 "颜色"？

好吧，我疯狂的朋友，让我们回到分布式系统的定义来回答这个问题。

你可能还记得，我把分布式编程描述为使用多台计算机使其能像在单台计算机上那样解决问题的艺术。

其实，这也是人们迷恋顺序的核心所在。任何一个系统，如果一次只能做一件事，就会形成一个总的操作顺序。就像人通过一扇门一样，每一个操作都会有一个明确的前身和后继。这基本上就是我们一直努力维护的编程模式。

传统的模式是：一个程序、一个进程、一个内存空间运行在一个CPU上。操作系统抽象掉了可能有多个CPU和多个程序的事实，计算机上的内存实际上是由许多程序共享的。我不是说线程编程和面向事件编程不存在，只是它们是在特定模型之上的抽象。程序的编写是为了以一种有序的方式执行：即自顶向下的方式

顺序作为一种属性之所以受到了如此多的关注，是因为定义“正确性”的最简单的方法是说“它的工作方式与在单台机器上的工作方式相同”。而这通常意味着：a)我们执行相同的操作，b)我们以相同的顺序运行它们——即使我们有多台机器。

分布式系统保持顺序（如为单个系统定义的）的好处是它们是通用的。您无需担心操作是什么，因为它们将完全像在单台计算机上一样执行。 这很棒，因为您知道无论执行什么操作都可以使用同一系统。

在现实中，一个分布式程序运行在多个节点上；有多个CPU和多个操作流进来。你仍然可以分配一个总的顺序，但它需要准确的时钟或某种形式的通信。譬如你可以用一个完全精确的时钟给每个操作打上时间戳，然后用它来计算出总的顺序。或者你可能有某种通信系统，使其可以像分配总顺序一样分配顺序号。

## 全序与偏序

分布式系统在自然状态下是[偏序的](http://en.wikipedia.org/wiki/Partially_ordered_set)。无论是网络还是独立的节点，都不能保证其相对顺序；但是在每个节点上，你都可以观察到其本地顺序。

[全序关系](http://en.wikipedia.org/wiki/Total_order)是一个二元关系，它定义了某个集合中每个元素的顺序。

当其中一个元素大于另一个元素时，两个不同的元素是可以比较的。在偏序集合中，有些元素对不具有可比性，因此偏序关系并不会指定每一项的确切顺序。

全序关系和偏序关系都具有[传递性](http://en.wikipedia.org/wiki/Transitive_relation)和[反对称性](http://en.wikipedia.org/wiki/Antisymmetric_relation)。对于集合X中的所有a、b和c，下列陈述在全序关系和偏序关系中都成立。

```
If a ≤ b and b ≤ a then a = b (反对称性);
If a ≤ b and b ≤ c then a ≤ c (传递性);
```

然而a在全序集合中具有 [完全性](http://en.wikipedia.org/wiki/Total_relation):

```
a ≤ b or b ≤ a (完全性) for all a, b in X
```

而偏序集合中则具有 [自反性](http://en.wikipedia.org/wiki/Reflexive_relation):

```
a ≤ a (自反性) for all a in X
```

需要注意的是，完全性包含自反性；所以偏序关系是全序关系的一个变种。对于偏序关系中的某些元素，完全性不成立----换句话说，有些元素是不可比较的。

> 译者注：全序关系一定是偏序关系，反之不成立

Git分支是一个典型的偏序关系的例子。你可能知道，git版本控制系统允许你从一个基础分支--例如从一个master分支--创建多个分支。每个分支都代表了一个基于共同祖先衍生出来的源代码修改历史。

```
[ branch A (1,2,0)]  [ master (3,0,0) ]  [ branch B (1,0,2) ]
[ branch A (1,1,0)]  [ master (2,0,0) ]  [ branch B (1,0,1) ]
                  \  [ master (1,0,0) ]  /
```

分支A和B都来源于一个共同的祖先分支，但它们之间并没有明确的先后顺序：它们代表了不同的提交记录，如果不做一些额外的工作（合并提交记录），其无法简化为单个线性的历史记录。当然，你可以把所有的提交按某种任意的顺序排列（比如说，先是祖先分支，然后再把A排在B之前或把B排在A之前，从而打破这种状况）--但强行添加全序关系会导致失去部分信息

在一个由单节点组成的系统中，必然会出现全序关系：即在一个程序中，指令的执行和消息的处理都是按照特定的、可观察的顺序执行的。我们已经开始依赖这种全序关系--它使程序的执行变得可以预测。这种顺序也可以在分布式系统上维持，但要付出代价：昂贵的通信，时间同步也很困难且脆弱。

# 何为时间？

时间是顺序的来源 —— 它为我们定义了操作的顺序——巧合的是，它也有一个人们可以理解的解释（一秒、一分钟、一天等等）。

从某种意义上讲，时间就是一个整数计数器。而它恰好足够重要，以至于大多数计算机都有一个专门的时间传感器，也就是所谓的时钟。它如此重要，以至于我们不得不想出如何用一些不完美的物理系统（从蜡烛到铯原子）来合成一个近似相同的计数器。所谓"合成"，就是指我们可以通过一些物理属性来近似地计算出两个物理距离较远的整数计数器的值，而无需直接进行通信。

时间戳其实是代表从宇宙开始到当前时刻的世界状态的简写值——如果某件事在某个时间戳时发生，那么它就有可能受到之前发生的一切事物的影响。因此我们需要将此想法扩展，使其成为因果时钟，该时钟能获取当前事物的前因（依赖关系），而不仅仅是简单地假设该时间戳之前的一切事物都与之相关。当然，通常我们只应该关心具体系统的状态，而非整个世界。

假设时间在任何地方都以同样的速度流逝——这是一个很大的假设，我稍后会回到这个假设——当在程序使用时，时间和时间戳有几种有用的解释。这三种解释是：

- 顺序(*Order*)
- 持续时间(*Duration*)
- 解释(*Interpretation*)

*顺序*：当我说时间是顺序的来源时，我的意思是：

- 我们可以将时间戳附加到无序事件上，使其有序化。
- 我们可以使用时间戳来强制执行特定的操作顺序或消息的传递（例如，如果一个操作没有按顺序到达，我们可以通过延迟操作）。
- 我们可以用时间戳的值来判断某件事情在时间上是否发生在其他事情之前。

*解释*：时间作为一种普遍可比较的价值。时间戳的绝对值可以解释为一个日期，这对人们来说很有用。从日志文件中给定一个宕机时间开始的时间戳，你就可以知道在上周六，当时有一场[雷雨](https://twitter.com/AWSFail/statuses/218915147060752384).

*持续时间*： 用时间衡量的持续时间与现实世界有一定的关系。算法一般不关心时钟的绝对值或将其解释为日期，但它们可能会使用持续时间来进行某些判断。特别是，等待的时间可以提供线索，说明一个系统是否已被分区，或者仅仅是经历了高延迟。

就其本质而言，分布式系统的各个组成部分的行为是不可预测的。它们不能保证任何特定的顺序、推进速度或没有延迟。每个节点都有本地顺序——因为程序执行是（大致）按顺序的——但这些本地顺序是相互独立的。

添加顺序是一种可以有效减少“程序可能执行”生存空间的方法。当事物可以以任何顺序发生时，人类很难对该事物进行推理——毕竟有太多的排列组合需要考虑。

## 所有的时间都以相同的速率流逝?

根据我们个人的经验，我们都有一个直观的时间概念。不幸的是，这种直观的时间概念使我们更容易描绘出全序关系，而非偏序。显然想象一个事情一个接一个发生的顺序，比想象事物同时发生更容易。对单一顺序的信息进行推理，也要比以不同顺序和不同延迟到达的信息进行推理更容易。

然而，在实现分布式系统时，我们希望避免对时间和顺序做出强假设，因为假设越强，系统对"时间传感器"——或者说板载时钟——的问题就越脆弱。此外，添加顺序是有代价的。因此我们能容忍的时间上的不确定性越多，我们就越能利用分布式计算的优势。

对于"时间是否在任何地方都以同样的速率流逝？"这个问题，有三种常见的答案。这三种答案是：

- "全局时钟": 是的
- "本地时钟": 不是, 但
- "没有时钟": 不是!

这些大致对应于我在第二章中提到的三个时序假设：同步系统模型有一个全局时钟，部分同步模型有一个局部时钟，而在异步系统模型中，人们根本不能使用时钟。让我们更详细地看看这些。

### 全局时钟的时间假设

全局时钟假设的是人类有一个完美精度的全球时钟，而且每个人都可以使用这个时钟。这是我们普遍倾向于思考时间的方式，因为在人类的互动中，时间上的微小差异并不重要。

![Global clock](http://book.mixu.net/distsys/images/global-clock.png)

全局时钟基本上是一个总顺序的来源（所有节点上每一个操作都有精确的顺序，即使这些节点从未进行过通信）。

然而，这只是一种理想化的世界：在现实中，时钟只能在有限的精度范围内达到同步。这受制于商用计算机的时钟缺乏准确性，受制于使用[NTP](http://en.wikipedia.org/wiki/Network_Time_Protocol)等时钟同步协议的延迟，以及从根本上讲受制于[时空的性质](http://en.wikipedia.org/wiki/Time_dilation)。

假设分布式节点上的时钟是完全同步的，意味着假设所有时钟都以相同的值开始，并且永不偏移。这是一个很好的假设，因为你可以自由地使用时间戳来确定一个全局的总序--受时钟偏移而不是延迟的约束--但这是一个[不平凡的](http://queue.acm.org/detail.cfm?id=1773943)操作挑战，也是异常情况的潜在来源。在许多不同的情况下，一个简单的故障--比如用户不小心改变了一台机器上的本地时间，或者一台过时的机器加入了一个集群，或者同步时钟以稍有不同的速率偏移等等，都会造成难以追踪的异常。

尽管如此，有一些现实世界的系统还是做出了这种假设。Facebook的[Cassandra](http://en.wikipedia.org/wiki/Apache_Cassandra)是一个假设时钟同步的系统示例。它使用时间戳来解决写入之间的冲突--带有较新时间戳的写入将获胜。这意味着，如果时钟偏移，新数据可能会被旧数据忽略或覆盖；这又是一个操作上的挑战（据我所知，人们都敏锐地意识到这一点）。另一个有趣的例子是谷歌的[Spanner](http://research.google.com/archive/spanner.html)：论文描述了他们的TrueTime API，它可以同步时间，但也可以估计最坏情况下的时钟偏移。

### 本地时钟的时间假设

第二种可能更合理的假设是，每台机器都有自己的时钟，但没有全局时钟。这意味着你不能使用本地时钟来确定远程时间戳是发生在本地时间戳之前还是之后；换句话说，你不能有意义地比较两台不同机器上的时间戳。

![Local clock](http://book.mixu.net/distsys/images/local-clock.png)

本地时钟假设更接近真实世界。它指定了一个偏序关系：在每个系统上的事件都是有序的，但这些事件不能只通过使用时钟来跨系统排序。

但是，你可以使用时间戳在单台机器上对其上的事件进行排序；你也可以在单台机器上使用超时处理，只要你小心不要让时钟反复改变。当然，在一台由终端用户控制的机器上，这可能假设的太多了：例如，用户在使用操作系统的日期控件查找日期时，可能会不小心把日期更改为其他值。

### 没有时钟的时间假设

最后，是逻辑时间的概念。在这里，我们根本不使用时钟，而是以其他方式追踪因果关系。请记住，时间戳只是世界截至当前状态的速记--因此我们可以使用计数器和通信来确定某件事物是在之前、之后又或是与其他事物同时发生。

这样，我们可以确定不同机器之间事件的顺序，但我们无法知道信息延迟，也不能使用超时处理（因为我们假设没有"时间传感器"）。这是一种偏序关系：在单系统上可以使用计数器对其上的事件进行排序，而无需进行通信，但跨系统的事件排序需要信息交换。

When clocks are not used, the maximum precision at which events can be ordered across distant machines is bound by communication latency.

分布式系统中被引用最多的论文之一是Lamport关于[时间、时钟与事件排序](http://research.microsoft.com/users/lamport/pubs/time-clocks.pdf)的论文。向量时钟，是该概念的概括（我将在后面详细介绍），这是一种无需使用时钟即可跟踪因果关系的方法。Cassandra的表兄弟Riak(Basho)和Voldemort(Linkedin)使用向量时钟，而不是假设节点有一个可访问的且完美精确的全局时钟。这使得这些系统可以避免前面提到的时钟精度问题。

当不使用时钟时，在远端的机器上可以进行事件排序的最大精度受到通信延迟的约束。

## 在分布式系统中如何使用时间？

（在分布式系统中使用）时间的优势是什么？

1. 时间可以定义整个系统的顺序（无需沟通）。
2. 时间可以定义算法的边界条件

事件的顺序在分布式系统中是很重要的，因为分布式系统的很多特性都是以操作/事件的顺序来定义的。

- 其中，正确性取决于（对）事件的正确排序，例如分布式数据库中的可序列化。
- 当发生资源争夺时，顺序可以保证资源争夺双方不会僵持不下，例如，当一个组件接收到两条命令时，将履行第一条命令，而取消第二个命令。

全局时钟可以让两台不同机器上的操作有序进行，而无需两台机器间直接通信。在没有全局时钟的情况下，我们需要通过通信才能确定顺序。

时间还可以用来定义算法的边界条件--具体来说，就是区分"高延迟"和"服务器或网络链路故障"。这是一个非常重要的用例；在大多数现实世界的系统中，超时时间被用来确定远程机器是否发生了故障，或者它只是遇到了高网络延迟。做出这种判断的算法称为失效检测器；很快我将讨论它们。

## 向量时钟 (因果序时间)

前面，我们讨论了关于分布式系统中时间进度的不同假设。假设我们无法实现精确的时钟同步--或者说我们的系统从对时间同步不敏感的目标出发，那么我们应该如何进行排序呢？

Lamport时钟和向量时钟是物理时钟的替代品，物理时钟依靠计数器和通信来确定整个分布式系统中事件的顺序。这些时钟提供了一个在不同节点之间可比的计数器。

*Lamport时钟*很简单。每个进程都会使用以下规则来维护一个计数器：

- 每当有一个进程工作时，就增加计数器
- 每当一个进程发送一个消息时，都要包含其计数器。
- 当收到消息时，将计数器设置为`max(local_counter, received_counter)+1`

Expressed as code:

```
function LamportClock() {
  this.value = 1;
}

LamportClock.prototype.get = function() {
  return this.value;
}

LamportClock.prototype.increment = function() {
  this.value++;
}

LamportClock.prototype.merge = function(other) {
  this.value = Math.max(this.value, other.value) + 1;
}
```

[Lamport时钟](http://en.wikipedia.org/wiki/Lamport_timestamps)可以让计数器在不同的系统间进行比较，但要注意的是：Lamport时钟定义了一个偏序关系。如果`timestamp(a) < timestamp(b)`:

- `a` 可能发生在 `b`之前 or
- `a` 可能无法与 `b`比较

这就是所谓的时钟一致性条件：如果一个事件先于另一个事件发生，那么该事件的逻辑时钟就先于其他事件发生。如果`a`和`b`来自同一个因果史，那么要么这两个时间戳值都是在同一个进程中产生的；要么`b`是对`a`中发送的消息的回应，那么我们知道`a`发生在`b`之前。

直观地说，这是因为一个Lamport时钟只能携带一个时间线/历史的信息；因此，比较来自从不相互沟通的系统的Lamport时间戳，可能会导致并发事件看起来也是有序的。

想象一下，在最初的一段时间后，该系统分为两个彼此互不通信的独立子系统。

对于每个独立系统中的所有事件，如果`a`发生在`b`之前，那么`ts(a)<ts(b)`；但如果你从不同的独立系统中抽取两个事件（例如没有因果关系的事件），那么你就不能说它们的相对顺序有什么意义。虽然系统的每个部分都给事件分配了时间戳，但这些时间戳彼此之间没有关系。所以即使两个事件不相关，也可能看起来是有序的。

然而--这仍然是一个有用的属性--从单台机器的角度来看，任何用`ts(a)`发送的消息都会收到一个`ts(b)`的响应，而这个响应`ts(b)>ts(a)`。

*向量时钟*是Lamport时钟的扩展，它维护了N个逻辑时钟的数组`[ t1，t2，...]`——每个节点一个。每个节点不是增加一个共同的计数器，而是在每个内部事件中增加一个在自己向量中的逻辑时钟。因此，更新规则为:

- 每当有进程工作时，就在向量中添加节点的逻辑时钟值
- 每当一个进程发送消息时，都要包括逻辑时钟的完整向量
- 当收到消息时:
  - 更新向量中的每个元素为`max(local, received)`
  - 递增向量中代表当前节点的逻辑时钟值

同样用代码表示:

```
function VectorClock(value) {
  // expressed as a hash keyed by node id: e.g. { node1: 1, node2: 3 }
  this.value = value || {};
}

VectorClock.prototype.get = function() {
  return this.value;
};

VectorClock.prototype.increment = function(nodeId) {
  if(typeof this.value[nodeId] == 'undefined') {
    this.value[nodeId] = 1;
  } else {
    this.value[nodeId]++;
  }
};

VectorClock.prototype.merge = function(other) {
  var result = {}, last,
      a = this.value,
      b = other.value;
  // This filters out duplicate keys in the hash
  (Object.keys(a)
    .concat(Object.keys(b)))	//原代码为.concat(b)),译者觉得有问题，改之
    .sort()
    .filter(function(key) {
      var isDuplicate = (key == last);
      last = key;
      return !isDuplicate;
    }).forEach(function(key) {
      result[key] = Math.max(a[key] || 0, b[key] || 0);
    });
  this.value = result;
};
```

这张插图 ([来源](http://en.wikipedia.org/wiki/Vector_clock)) 显示了一个矢量时钟:

![from http://en.wikipedia.org/wiki/Vector_clock](http://book.mixu.net/distsys/images/vector_clock.svg.png)



三个节点（A、B、C）中的每一个节点都会跟踪向量时钟。当事件发生时，它们会被打上向量时钟的当前值的时间戳。当检查一个向量时钟，如`{ A: 2, B: 4, C: 1 }`，可以让我们准确地识别（潜在）影响该事件的消息。

向量时钟的问题主要是每个节点依赖一条记录，这意味着对于大型系统来说，它们有可能变得非常大。目前已经应用了各种技术来减少向量时钟的大小（或者通过定期进行垃圾收集，或者通过限制大小来降低精度）。

我们已经研究了如何在没有物理时钟的情况下追踪顺序和因果关系。现在，让我们来看看如何将持续时间用于边界条件。

## 失效检测器 (时间用于边界条件)

正如我之前所说，等待的时间可以提供线索，说明系统是否被分区，或者仅仅是经历了高延迟。在这种情况下，我们不需要假设有一个完美准确的全局时钟--只要有一个足够可靠的本地时钟就足够了。

给定一个程序在一个节点上运行，如何判断远程节点已经失效？在缺乏准确信息的情况下，我们可以推断，一个无响应的远程节点在经过一定的合理时间后就已经失效了。

但什么是"合理的时间"呢？这取决于本地节点和远程节点之间的延迟。与其用具体的数值明确规定算法（在某些情况下难免会出错），不如用一个合适的抽象来处理。

失效检测器是一种抽象掉精确时序假设的方法。失效检测器使用心跳消息和定时器来实现。进程间交换心跳消息。如果在消息响应没有收到之前发生超时，那么当前进程就会怀疑与其通信的其他进程已经产生网络分区。

因此基于超时的失效检测器会有过于激进（声明节点已发生故障）或过于保守（花费较长时间来检测崩溃）的风险。那么失效检测器究竟需要多少精度才能适于使用呢？

[Chandra等人](http://www.google.com/search?q=Unreliable Failure Detectors for Reliable Distributed Systems)(1996)在解决共识问题的背景下讨论了失效检测器--这个问题（与失效检测器）格外相关，因为它是大多数复制问题的基础。在有延迟和网络分区的环境中，副本间需要达成一致。

他们用完整性和准确性两个特性来描述失效检测器的特点：

- 强完整性

  每个崩溃的进程最终都会被每个正确的进程所怀疑

- 弱完整性

  每个崩溃的进程最终都会被某些正确的进程所怀疑

- 强准确性

  正确的进程从未被怀疑

- 弱准确性

  某些正确的进程从未被怀疑

完整性比准确性更容易实现；事实上，所有重要的失效检测器都能实现它--你需要做的就是怀疑某些节点而不要等到永远。Chandra等人指出，一个弱完整性的失效检测器可以转化为强完整性的检测器(通过广播可疑进程的信息)，使我们能够更专注于准确性。

避免错误地怀疑非故障进程是很难的，除非你能够假设消息延迟有一个硬性的最大值。这种假设可以在同步系统模型中做出——因此失效检测器在这样的系统中可以有很强的准确性。在不对消息延迟施加硬约束的系统模型下，故障检测最多只能做到最终结果准确。

Chandra等人表明，即使是一个非常弱的失效检测器——最终弱失效检测器⋄W（最终弱准确性+弱完整性），也可以用来解决共识问题。下图（来自论文）说明了系统模型和问题可解决性之间的关系：

![From Chandra and Toueg. Unreliable failure detectors for reliable distributed systems. JACM 43(2):225–267, 1996.](http://book.mixu.net/distsys/images/chandra_failure_detectors.png)

从上面可以看出，在异步系统中，如果没有失效检测器，某些问题是无法解决的。这是因为如果没有失效检测器（或者对时间边界条件有很强的假设，例如同步系统模型），就无法判断远程节点是否已经崩溃，或者仅仅只是遇到了高延迟。这种区别对于任何旨在实现单副本一致性为目标的系统来说都非常重要：失败的节点可以被忽略，因为它们不能引起分歧，但产生网络分区的节点不能被安全地忽略。

如何实现失效检测器呢？从概念上来说，简单的失效检测器并没有什么用，只是在超时过后检测故障。最有趣的部分涉及如何判断远程节点是否发生故障。

理想情况下，我们更希望失效检测器能够适应不断变化的网络环境，避免将超时值硬编码进去。例如，Cassandra使用的是[累积型失效检测器](https://www.google.com/search?q=The+Phi+accrual+failure+detector)，即失效检测器会输出一个怀疑级别（0到1之间的数值），而不是二进制的"上"或"下"的判断。这使得使用失效检测器的应用可以自行决定准确检测和早期检测之间的权衡。

## 时间、顺序与性能

早先我提到添加顺序的代价，那么我是什么意思呢？

如果你要写一个分布式系统，你大概会拥有不止一台计算机。自然的（也是现实的）观点世界是偏序关系的，而不是一个全序关系。你可以将偏序关系转化为全序关系，但这需要通信、等待，并施加一定的约束，以限制在任何特定时间点有多少台计算机可以运行。

所有的时钟都只是受网络延迟（逻辑时间）或物理约束后的近似值。因此即使是让一个简单的整数计数器在多个节点上保持同步也是一个挑战。

虽然时间和顺序经常被放在一起讨论，但时间本身并不是一个如此有用的属性。算法其实并不关心时间，而是关心更抽象的属性：

- 事件的因果顺序
- 故障检测(如信息传递上限的近似值)
- 快照的一致性 (例如，检查系统在某个时间点的状态的能力，这里不讨论)

强制变为全序关系是可能的，但代价昂贵。它要求你以公共（最低）速度进行。通常情况下，确保事件以某种确定的顺序执行的最简单方法是指定一个单一的（瓶颈）节点，而所有操作都要经过这个节点。

时间/顺序/同步性真的有必要吗？这要看情况。在某些用例中，我们希望每个中间操作都能使系统从一个一致的状态转移到另一个一致状态。例如，在许多情况下，我们希望来自数据库的响应代表所有可用信息，同时我们希望避免处理因为系统可能返回不一致的结果而产生的问题。

但在其他情况下，我们可能不需要那么多的时间/顺序/同步性。例如，你正在运行一个长时间的计算，并且直到最后才真正关心系统做了什么--那么只要你能保证答案是正确的，你就不需要太多的同步性。

同步通常作为一种笨拙的工具应用于所有操作，而实际上只有一部分情况与最终结果有关。什么时候需要顺序来保证正确性呢？CALM定理——我将在最后一章讨论——提供了一个答案。

在其他情况下，给出的答案只代表已知最佳的预测也是可以接受的 -- -- 也就是只基于系统中包含的全部信息的一个子集。特别是，在产生网络分区期间，只需要访问部分系统即可响应请求。在其他用例中，最终用户无法真正区分一个可以廉价获得的相对较新的答案和一个保证正确且计算昂贵的答案。例如，某个用户的Twitter粉丝数是X，还是X+1？或者对于电影A、B、C哪个才是最佳答案？做一个比较便宜的、大部分正确的"尽力而为"也是可以接受的。

在接下来的两章中，我们将研究容错性强一致系统的复制--这些系统提供了强有力的保证，同时有强健的故障恢复能力。这些系统为第一种情况提供了解决方案：当你需要保证正确性并且愿意为此付出代价的时候。然后，我们将讨论具有弱一致性保证的系统，这些系统在面对产生网络分区时仍然可用，但这只能给你一个"尽力而为"的答案。

------

## 扩展阅读

### Lamport 时钟, 向量时钟

- [Time, Clocks and Ordering of Events in a Distributed System](http://research.microsoft.com/users/lamport/pubs/time-clocks.pdf) - Leslie Lamport, 1978

### 失效检测器

- [Unreliable failure detectors and reliable distributed systems](http://scholar.google.com/scholar?q=Unreliable+Failure+Detectors+for+Reliable+Distributed+Systems) - Chandra and Toueg
- [Latency- and Bandwidth-Minimizing Optimal Failure Detectors](http://www.cs.cornell.edu/people/egs/sqrt-s/doc/TR2006-2025.pdf) - So & Sirer, 2007
- [The failure detector abstraction](http://scholar.google.com/scholar?q=The+failure+detector+abstraction), Freiling, Guerraoui & Kuznetsov, 2011

### 快照

- [Consistent global states of distributed systems: Fundamental concepts and mechanisms](http://scholar.google.com/scholar?q=Consistent+global+states+of+distributed+systems%3A+Fundamental+concepts+and+mechanisms), Ozalp Babaogly and Keith Marzullo, 1993
- [Distributed snapshots: Determining global states of distributed systems](http://scholar.google.com/scholar?q=Distributed+snapshots%3A+Determining+global+states+of+distributed+systems), K. Mani Chandy and Leslie Lamport, 1985

### 因果性

- [Detecting Causal Relationships in Distributed Computations: In Search of the Holy Grail](http://www.vs.inf.ethz.ch/publ/papers/holygrail.pdf) - Schwarz & Mattern, 1994
- [Understanding the Limitations of Causally and Totally Ordered Communication](http://scholar.google.com/scholar?q=Understanding+the+limitations+of+causally+and+totally+ordered+communication) - Cheriton & Skeen, 1993