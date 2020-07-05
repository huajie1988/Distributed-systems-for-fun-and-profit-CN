# 6. 扩展阅读与附录

If you've made it this far, thank you.

如果你走到了这一步，感谢你的阅读。

If you liked the book, follow me on [Github](https://github.com/mixu/) (or [Twitter](http://twitter.com/mikitotakada)). I love seeing that I've had some kind of positive impact. "Create more value than you capture" and all that.

如果你喜欢这本书，请在[Github](https://github.com/mixu/)（或[Twitter](http://twitter.com/mikitotakada)）上关注我。我喜欢看到我产生了某种积极的影响。"创造的价值远超你所能获得的"。

在此我需要感谢：logpath、alexras、globalcitizen、graue、frankshearar、roryokane、jpfuentes2、eeror、cmeiklejohn、stevenproctor eos2102和stveloughran所提供的帮助！当然，还有文章一些错误和疏漏，是我的错。当然，剩下的任何错误和遗漏都是我的错!

值得注意的是，我关于最终一致性的一章是以伯克利为中心的；我想改变这一点。我还跳过了一个重要的时间用例：一致性快照。还有几个主题我应该扩展一下：即明确讨论安全和可用性，以及更详细地讨论一致性哈希。不过，我要去参加[2013年的Strange Loop](https://thestrangeloop.com/)，随便吧。

如果这本书有第6章的话，可能就是关于如何利用和处理大量数据的方法。似乎最常见的"大数据"计算是通过一个[简单的程序来处理大量数据集](http://en.wikipedia.org/wiki/SPMD)。我不知道后续的章节会是什么（也许是高性能计算，因为目前的重点是可行性），但我可能会在几年后知道。

## 关于分布式系统的书籍

#### 分布式算法(Lynch)

这可能是最常被推荐的关于分布式算法的书。我也会推荐它，但有一个警告。它非常全面，但它是写给研究生读者的，所以你会花很多时间阅读同步系统和共享内存算法，然后才会读到从业者最感兴趣的东西。

#### 可靠和安全的分布式编程导论 (Cachin, Guerraoui & Rodrigues)

对于一个从业者来说，这是一本很有意思的书。它很短，充满了实际的算法实现。

#### 复制：理论与实践

如果你对复制感兴趣，这本书很了不起。关于复制的一章主要是基于此书有趣部分的综合，再加上最近的阅读。

#### 分布式系统：算法 (Ghosh)

#### 分布式算法导论 (Tel)

#### 事务性信息系统：理论、算法和并发控制与恢复实践 (Weikum & Vossen)

本书讲的是传统的事务性信息系统，如本地的RDBMS。最后有两章是关于分布式事务的，但本书的重点是事务处理。

#### 事务处理： 概念与技术 ( Gray & Reuter)

一部经典之作。我觉得Weikum & Vossen更加与时俱进。

## 重要论文

每年，[Edsger W. Dijkstra分布式计算奖](http://en.wikipedia.org/wiki/Dijkstra_Prize)都会颁发给关于分布式计算原理的优秀论文。查看完整名单的链接，其中包括以下经典论文：

- "[Time, Clocks and Ordering of Events in a Distributed System](http://research.microsoft.com/users/lamport/pubs/time-clocks.pdf)" - Leslie Lamport
- "[Impossibility of Distributed Consensus With One Faulty Process](http://theory.lcs.mit.edu/tds/papers/Lynch/jacm85.pdf)" - Fisher, Lynch, Patterson
- "[Unreliable failure detectors and reliable distributed systems](http://scholar.google.com/scholar?q=Unreliable+Failure+Detectors+for+Reliable+Distributed+Systems)" - Chandra and Toueg

微软学术搜索有一个[分布式和并行计算领域的顶级出版物按引用次数排序](http://libra.msra.cn/RankList?entitytype=1&topDomainID=2&subDomainID=16&last=0&start=1&end=100)列表，这可能是一个有趣的列表，可以浏览更多经典。

以下是另外一些推荐的文件清单：

- [Nancy Lynch's recommended reading list](http://courses.csail.mit.edu/6.852/08/handouts/handout3.pdf) from her course on Distributed systems.
- [NoSQL Summer paper list](http://nosqlsummer.org/papers) - a curated list of papers related to this buzzword.
- [A Quora question on seminal papers in distributed systems](http://www.quora.com/What-are-the-seminal-papers-in-distributed-systems-Why).

### 系统

- [The Google File System](http://research.google.com/archive/gfs.html) - Ghemawat, Gobioff and Leung
- [MapReduce: Simplified Data Processing on Large Clusters](http://research.google.com/archive/mapreduce.html) - Dean and Ghemawat
- [Dynamo: Amazon’s Highly Available Key-value Store](http://scholar.google.com/scholar?q=Dynamo%3A+Amazon's+Highly+Available+Key-value+Store) - DeCandia et al.
- [Bigtable: A Distributed Storage System for Structured Data](http://research.google.com/archive/bigtable.html) - Chang et al.
- [The Chubby Lock Service for Loosely-Coupled Distributed Systems](http://research.google.com/archive/chubby.html) - Burrows
- [ZooKeeper: Wait-free coordination for Internet-scale systems](http://labs.yahoo.com/publication/zookeeper-wait-free-coordination-for-internet-scale-systems/) - Hunt, Konar, Junqueira, Reed, 2010