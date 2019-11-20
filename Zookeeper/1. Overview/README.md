[本文地址](https://github.com/Everything2CN/Zookeeper-CN/blob/master/1.%20Overview/README.md)    [原文地址](http://zookeeper.apache.org/doc/current/zookeeperOver.html)

# Zookeeper:分布式应用程序的分布式协调服务
ZooKeeper是一种用于分布式应用程序的分布式开源协调服务。 它公开了一组简单的原语，分布式应用程序可以构建这些原语，以实现更高级别的服务，以实现同步，配置维护以及组和命名。 它的设计易于编程，并使用在熟悉的文件系统目录树结构之后设计的数据模型。 它在Java中运行，并且具有Java和C的绑定。

众所周知，协调服务很难做到。 他们特别容易出现诸如竞争条件和死锁等错误。 ZooKeeper背后的动机是减轻分布式应用程序从头开始实施协调服务的责任。

# 设计目标
***ZooKeeper很简单***。 ZooKeeper允许分布式进程通过共享的层级命名空间相互协调，该命名空间与标准文件系统类似地组织。名称空间由数据寄存器组成 - 在ZooKeeper用语中称为znodes - 这些与文件和目录类似。与专为存储而设计的典型文件系统不同，ZooKeeper数据保存在内存中，这意味着ZooKeeper可以实现高吞吐量和低延迟数量。

ZooKeeper实现非常重视高性能，高可用性，严格有序的访问。 ZooKeeper的性能方面意味着它可以在大型分布式系统中使用。可靠性方面使其不会成为单点故障。严格的排序意味着可以在客户端实现复杂的同步原语。

***ZooKeeper被复制***。与它协调的分布式进程一样，ZooKeeper本身也可以在称为集合的一组主机上进行复制。

![ZooKeeper Service](./img/zkservice.jpg)

组成ZooKeeper服务的服务器必须彼此了解。 它们维护内存中的状态图像，以及持久性存储中的事务日志和快照。 只要大多数服务器可用，ZooKeeper服务就可用。

客户端连接到单个ZooKeeper服务器。 客户端维护TCP连接，通过该连接发送请求，获取响应，获取监视事件以及发送心跳。 如果与服务器的TCP连接中断，则客户端将连接到其他服务器。

***ZooKeeper是有序的***。 ZooKeeper使用反映所有ZooKeeper事务顺序的数字标记每个更新。 后续操作可以使用该顺序来实现更高级别的抽象，例如同步原语。

***ZooKeeper很快***。 它在“读取主导”工作负载中特别快。 ZooKeeper应用程序在数千台计算机上运行，并且在读取比写入更常见的情况下表现最佳，比率大约为10：1。

# 数据模型和分层命名空间
ZooKeeper提供的名称空间非常类似于标准文件系统。 名称是由斜杠（/）分隔的路径元素序列。 ZooKeeper名称空间中的每个节点都由路径标识。

![ZooKeeper's Hierarchical Namespace](./img/zknamespace.jpg)

# 节点和短暂节点
与标准文件系统不同，ZooKeeper命名空间中的每个节点都可以包含与之关联的数据以及子项。这就像拥有一个允许文件、目录的文件系统。 （ZooKeeper旨在存储协调数据：状态信息，配置，位置信息等，因此存储在每个节点的数据通常很小，在字节到千字节范围内。）我们使用术语znode来表明我们正在谈论ZooKeeper数据节点。

Znodes维护一个stat结构，包括数据更改，ACL更改和时间戳的版本号，以允许缓存验证和协调更新。每次znode的数据更改时，版本号都会增加。例如，每当客户端检索数据时，它也接收数据的版本。

存储在命名空间中每个znode的数据以原子方式读取和写入。读取获取与znode关联的所有数据字节，写入替换所有数据。每个节点都有一个访问控制列表（ACL），限制谁可以做什么。

ZooKeeper也有短暂节点的概念。只要创建znode的会话处于活动状态，就会存在这些znode。会话结束时，短暂znode将被删除。当您想要实现[tbd]时，短暂节点很有用。

# 有条件的更新和监视
ZooKeeper支持监视的概念。客户端可以在znodes上设置监视。当znode更改时，将触发并删除监视。当触发监视时，客户端会收到一个数据包，说明znode已更改。如果客户端与其中一个Zoo Keeper服务器之间的连接中断，客户端将收到本地通知。这些可以用于[tbd]。

# 担保
ZooKeeper非常快速且非常简单。 但是，由于其目标是构建更复杂的服务（如同步）的基础，因此它提供了一系列保证。 这些是：

- 顺序一致性 - 客户端的更新将按发送顺序应用。
- 原子性 - 更新成功或失败。 没有部分结果。
- 单系统映像 - 无论服务器连接到哪个服务器，客户端都将看到相同的服务视图。
- 可靠性 - 一旦应用了更新，它将从那时起持续到客户端覆盖更新。
- 及时性 - 系统的客户视图保证在特定时间范围内是最新的。

有关这些以及如何使用它们的更多信息，请参阅[tbd]

# 简单的API
ZooKeeper的设计目标之一是提供一个非常简单的编程接口。 因此，它仅支持以下操作：

create
在树中的某个位置创建一个节点

delete
删除节点

exists
测试某个位置是否存在节点

get data
从节点读取数据

set data
将数据写入节点

get children
检索节点的子节点列表

sync
等待数据传播

有关这些内容的更深入讨论，以及如何使用它们来实现更高级别的操作，请参阅[tbd]

# 实现
ZooKeeper Components显示ZooKeeper服务的高级组件。 除请求处理器外，构成ZooKeeper服务的每个服务器都复制其自己的每个组件的副本。

![ZooKeeper Components](./img/zkcomponents.jpg)

复制数据库是包含整个数据树的内存数据库。更新将记录到磁盘以获取可恢复性，并且写入在应用于内存数据库之前会序列化到磁盘。

每个ZooKeeper服务器都为客户端服务。客户端只连接到一台服务器以提交请求。读取请求由每个服务器数据库的本地副本提供服务。更改服务状态，写请求的请求由协议协议处理。

作为协议协议的一部分，来自客户端的所有写入请求都被转发到称为领导者的单个服务器。其余的ZooKeeper服务器（称为关注者）接收来自领导者的消息提议并同意消息传递。消息传递层负责替换失败的领导者并将关注者与领导者同步。

ZooKeeper使用自定义原子消息传递协议。由于消息传递层是原子的，因此ZooKeeper可以保证本地副本永远不会发散。当领导者收到写入请求时，它会计算应用写入时系统的状态，并将其转换为捕获此新状态的事务。

# 用途
ZooKeeper的编程接口非常简单。但是，使用它，您可以实现更高阶的操作，例如同步原语，组成员资格（group membership），所有权（ownership）等。某些分布式应用程序已将其用于：[tbd：从白皮书和视频演示添加用途。]有关详细信息，请参阅[TBD]

# 性能
ZooKeeper旨在提供高性能。 不就是这样吗？ ZooKeeper雅虎开发团队的成果！ 研究表明它是。（请参阅ZooKeeper吞吐量作为读写比率变化。）在读取数量超过写入的应用程序中，它的性能尤其高，因为写入涉及同步所有服务器的状态。 （读取数量超过写入通常是协调服务的情况。）

![ZooKeeper吞吐量作为读写比率变化](./img/zkperfRW-3.2.jpg)

图中ZooKeeper吞吐量作为读写比率变化是ZooKeeper版本3.2的吞吐量图，运行在具有双2Ghz Xeon和两个SATA 15K RPM驱动器的服务器上。 一个驱动器用作专用的ZooKeeper日志设备。 快照已写入OS驱动器。 写请求是1K写入，读取是1K读取。 “服务器”表示ZooKeeper集合的大小，即构成服务的服务器数量。大约30个其他服务器用于模拟客户端。ZooKeeper集合的配置使得领导者不允许来自客户端的连接。

提示：
在版本3.2中，与之前的3.1版本相比，r / w性能提高了约2倍。

基准也表明它也是可靠的。 存在错误时的可靠性显示了部署如何响应各种故障。 图中标记的事件如下：

1. 追随者的失败和恢复
1. 失败和恢复不同的追随者
1. 领导者的失败
1. 两名追随者的失败和恢复
1. 另一位领导失败了

# 可靠性
为了在注入故障时显示系统随时间的行为，我们运行了由7台机器组成的ZooKeeper服务。我们运行与之前相同的饱和度基准，但这次我们将写入百分比保持在恒定的30％，这是我们预期工作负载的保守比率。

![存在错误时的可靠性](./img/zkperfreliability.jpg)

这是图表中的一些重要观察结果。 首先，如果追随者失败并迅速恢复，那么即使失败，ZooKeeper也能够维持高吞吐量。但也许更重要的是，领导者选举算法允许系统足够快地恢复以防止吞吐量大幅下降。在我们的观察中，ZooKeeper选择新领导者的时间不到200毫秒。第三，随着追随者的恢复，ZooKeeper能够在开始处理请求后再次提高吞吐量。

# ZooKeeper项目
ZooKeeper已成功应用于许多工业应用。 它用于Yahoo! 作为Yahoo!的协调和故障恢复服务 Message Broker，是一个高度可扩展的发布 - 订阅系统，可管理数千个主题以进行复制和数据传递。 雅虎的获取服务使用它！ crawler，它还管理故障恢复。 一些雅虎！ 广告系统也使用ZooKeeper来实现可靠的服务。

鼓励所有用户和开发人员加入社区并贡献他们的专业知识。 有关更多信息，请参阅Apache上的Zookeeper项目。

# 目录
- [概述](../1.%20Overview/README.md)
- [入门指南](../2.%20Getting%20Started/README.md)
- [程序员指南](../3.%20Programmer's%20Guide/README.md)
- [食谱](../4.%20Recipes/README.md)