[本文地址](https://github.com/Everything2CN/Zookeeper-CN/blob/master/2.%20Getting%20Started/README.md) 
[原文地址](http://zookeeper.apache.org/doc/current/zookeeperStarted.html)

# 入门：使用ZooKeeper协调分布式应用程序
本文档包含有助于您快速使用ZooKeeper的信息。它主要针对希望尝试它的开发人员，包含一个ZooKeeper服务器的简单安装说明，一些验证它正在运行的命令，以及一个简单的编程示例。最后，为方便起见，有一些部分涉及更复杂的安装，例如运行复制部署和优化事务日志。但是，有关商业部署的完整说明，请参阅[ZooKeeper管理员指南](http://zookeeper.apache.org/doc/current/zookeeperAdmin.html)。

# 先决条件
请参阅[管理指南](http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_systemReq)中的系统要求。

# 下载
要获取ZooKeeper发行版，请从其中一个Apache下载镜像下载最新的稳定版本。


# 独立操作
在独立模式下设置ZooKeeper服务器非常简单。 服务器包含在单个JAR文件中，因此安装包括创建配置。

一旦你下载了一个稳定的ZooKeeper版本，就解压缩并cd到root

要启动ZooKeeper，您需要一个配置文件。 这是一个示例，在conf / zoo.cfg中创建它：
```
tickTime=2000
dataDir=/var/lib/zookeeper
clientPort=2181
```
这个文件可以被称为任何东西，但为了讨论清楚起见，请将其命名为conf / zoo.cfg。 更改dataDir的值以指定现有（从空开始）目录。   以下是每个字段的含义：
- ***tickTime***  ：ZooKeeper使用的基本时间单位（以毫秒为单位）。它用于做心跳，最小会话超时将是tickTime的两倍。

- ***dataDir***  ：存储内存数据库快照的位置，除非另有说明，否则指向数据库更新的事务日志。

- ***clientPort***  :侦听客户端连接的端口

现在您已创建配置文件，您可以启动

`bin/zkServer.sh start`

ZooKeeper使用log4j记录消息 - 更多详细信息，请参阅“程序员指南”的“[日志](http://zookeeper.apache.org/doc/current/zookeeperProgrammers.html#Logging)”部分。 您将看到日志消息进入控制台（默认）和/或日志文件，具体取决于log4j配置。

此处概述的步骤以独立模式运行ZooKeeper。 没有复制，因此如果ZooKeeper进程失败，服务将会关闭。 这适用于大多数开发情况，但要以复制模式运行ZooKeeper，请参阅运行[Replicated ZooKeeper](http://zookeeper.apache.org/doc/current/zookeeperStarted.html#sc_RunningReplicatedZooKeeper)。


# 管理ZooKeeper存储

对于长期运行的生产系统，必须在外部管理ZooKeeper存储（dataDir和logs）。有关详细信息，请参阅[维护](http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance)部分。

# 连接到ZooKeeper
```
$ bin/zkCli.sh -server 127.0.0.1:2181
```
这使您可以执行简单的文件操作。 连接后，您应该看到以下内容：
```
Connecting to localhost:2181
log4j:WARN No appenders could be found for logger (org.apache.zookeeper.ZooKeeper).
log4j:WARN Please initialize the log4j system properly.
Welcome to ZooKeeper!
JLine support is enabled
[zkshell: 0]
```

在shell中，键入help以获取可从客户端执行的命令列表，如下所示
```
[zkshell: 0] help
ZooKeeper host:port cmd args
        get path [watch]
        ls path [watch]
        set path data [version]
        delquota [-n|-b] path
        quit
        printwatches on|off
        createpath data acl
        stat path [watch]
        listquota path
        history
        setAcl path acl
        getAcl path
        sync path
        redo cmdno
        addauth scheme auth
        delete path [version]
        setquota -n|-b val path
```

从这里，您可以尝试一些简单的命令来感受这个简单的命令行界面。首先，从发出list命令开始，如在ls中，产生：
```

[zkshell: 8] ls /
[zookeeper]

```

接下来，通过运行`create /zk_test my_data`创建一个新的znode。这将创建一个新的znode，并将字符串“my_data”与节点相关联。你应该看到：
```

[zkshell: 9] create /zk_test my_data
Created /zk_test

```

发出另一个`ls /`命令来查看目录的样子：
```
[zkshell: 11] ls /
[zookeeper, zk_test]
```

请注意，现在已创建`zk_test`目录。

接下来，通过运行`get`命令验证数据是否与znode相关联，如下所示：
```
[zkshell: 12] get /zk_test
my_data
cZxid = 5
ctime = Fri Jun 05 13:57:06 PDT 2009
mZxid = 5
mtime = Fri Jun 05 13:57:06 PDT 2009
pZxid = 5
cversion = 0
dataVersion = 0
aclVersion = 0
ephemeralOwner = 0
dataLength = 7
numChildren = 0       
```

我们可以通过发出set命令来更改与zk_test相关的数据，如下所示：
```
[zkshell: 14] set /zk_test junk
cZxid = 5
ctime = Fri Jun 05 13:57:06 PDT 2009
mZxid = 6
mtime = Fri Jun 05 14:01:52 PDT 2009
pZxid = 5
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0
dataLength = 4
numChildren = 0
[zkshell: 15] get /zk_test
junk
cZxid = 5
ctime = Fri Jun 05 13:57:06 PDT 2009
mZxid = 6
mtime = Fri Jun 05 14:01:52 PDT 2009
pZxid = 5
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0
dataLength = 4
numChildren = 0
```

注意我们在设置数据之后做了一个get，确实确实改变了。 

最后，让我们通过发出以下命令来删除节点：
```
[zkshell: 16] delete /zk_test
[zkshell: 17] ls /
[zookeeper]
[zkshell: 18]
```

这就是现在。要了解更多信息，请继续阅读本文档的其余部分，并参阅“[程序员指南](http://zookeeper.apache.org/doc/current/zookeeperProgrammers.html)”。

# ZooKeeper编程
ZooKeeper具有Java绑定和C绑定。它们在功能上是等价的。C绑定有两种变体：单线程和多线程。这些仅在消息传递循环的完成方式上有所不同。有关更多信息，请参阅[ZooKeeper程序员指南](http://zookeeper.apache.org/doc/current/zookeeperProgrammers.html#ch_programStructureWithExample)中的编程示例，了解使用不同API的示例代码。


# 运行复制的ZooKeeper
在独立模式下运行ZooKeeper便于评估，开发和测试。但在生产中，您应该以复制模式运行ZooKeeper。同一应用程序中的复制服务器组称为仲裁，在复制模式下，仲裁中的所有服务器都具有相同配置文件的副本。

注意：
对于复制模式，至少需要三台服务器，强烈建议您使用奇数个服务器。如果您只有两台服务器，那么您处于这样的情况：如果其中一台服务器出现故障，则没有足够的机器来构成多数仲裁。两台服务器本质上不如单一服务器稳定，因为有两个单点故障。

复制模式所需的`conf/zoo.cfg`文件类似于独立模式中使用的文件，但有一些差异。这是一个例子：
```
tickTime=2000
dataDir=/var/lib/zookeeper
clientPort=2181
initLimit=5
syncLimit=2
server.1=zoo1:2888:3888
server.2=zoo2:2888:3888
server.3=zoo3:2888:3888
```
新条目initLimit是暂停ZooKeeper用于限制仲裁中ZooKeeper服务器连接到领导者的时间长度。条目syncLimit限制服务器与领导者的过期时间。

使用这两个超时，您可以使用tickTime指定时间单位。在这个例子中，initLimit的超时是2000个milleseconds的5个滴答，或者是10秒。

表单server.X的条目列出了构成ZooKeeper服务的服务器。当服务器启动时，它通过在数据目录（dataDir ？）中查找文件myid来知道它是哪个服务器。该文件包含服务器编号，ASCII格式。

最后，记下每个服务器名称后面的两个端口号：“2888”和“3888”。 对等方使用以前的端口连接到其他对等方。 这种连接是必要的，以便对等方可以进行通信，例如，就更新的顺序达成一致。 更具体地说，ZooKeeper服务器使用此端口将关注者连接到领导者。 当新的领导者出现时，跟随者使用此端口打开与领导者的TCP连接。 由于默认的领导者选举也使用TCP，我们目前需要另一个端口进行领导者选举。 这是服务器条目中的第二个端口。

注意：
	如果要在一台计算机上测试多个服务器，请将servername指定为localhost，并为该服务器中的每个server.X指定唯一的仲裁和领导者选举端口（即上例中的2888：3888,2889：3889,2890：3890） 配置文件。 当然，单独的dataDirs和不同的clientPorts也是必需的（在上面复制的示例中，在单个localhost上运行，您仍然会有三个配置文件）。 
	请注意，在一台计算机上设置多台服务器不会产生任何冗余。如果发生导致机器死机的事情，所有zookeeper服务器都将脱机。完全冗余要求每台服务器都有自己的机器。它必须是完全独立的物理服务器。同一物理主机上的多个虚拟机仍然容易受到该主机的完全故障的影响。

# 其他优化
还有一些其他配置参数可以大大提高性能：
- 要在更新时获得较低的延迟，请务必拥有专用的事务日志目录。 默认情况下，事务日志与数据快照和myid文件放在同一目录中。 dataLogDir参数指示用于事务日志的不同目录。
- [tbd：其他配置参数是什么？]


# 目录
- [概述](../1.%20Overview/README.md)
- [入门指南](../2.%20Getting%20Started/README.md)
- [程序员指南](../3.%20Programmer's%20Guide/README.md)
- [食谱](../4.%20Recipes/README.md)