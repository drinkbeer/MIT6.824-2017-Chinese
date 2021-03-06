Introduction
---
Lab2 的目标是建立一个容错的 key/value 存储系统。我们将实现一个一致性算法：[Raft](https://raft.github.io)，它是一个副本状态机协议(Replicated State Machine Protocal)。此后的 Lab 将会在 Raft 的基础上建立一个 key/value 服务，然后我们就能在多台副本状态机之间共享服务，获得更高性能。

上面的说法可能抽象了点。在实际中，假设每个服务器拥有一个状态机以及一个 log。状态机是我们需要容错处理的，例如一个哈希表。对于客户端来讲，他们就只在跟一个单一的，可靠的状态机交互一样，即使有一小部分服务器无法工作。每个状态机从 log 中获取输入命令（例如 `x = 3`）。一致性算法就是用于确保当某个状态机的第 n 条命令是 `x = 3` 时，所有状态机的第 n 条命令也必须是 `x = 3`。这样，所有的状态机都处理同样的命令，产生同样的结果，转移到同样的状态。

一个副本服务 (即，key/value 数据库) 通过将它的数据复制到多个副本服务器上以实现容错。冗余使得即使在某些服务器无法工作的时候仍能继续工作。主要的挑战在于副本之间数据可能不同步。

Raft 管理了服务的状态副本，它能帮助服务从一堆状态中选出当前的正确的状态。它实现了一个副本状态机，将客户端请求按顺序组织成 log，保证所有的副本都持有同样的 log。每个副本都按照 log 的顺序执行客户端请求，并更新自己的状态机。由于在线的副本具有相同的 log，就能按照同样的顺序执行同样的语句，保持同样的状态。如果某个服务器离线了，但是不久又恢复了，Raft 将负责更新它的状态。Raft 运行的基本是多数表决，如果大部分机器都离线了，Raft 将暂停等待。


有一个[动画资料](http://thesecretlivesofdata.com/raft/)，可以参考一下

本章内容较多，报告将分为三个部分：[PartA](https://github.com/double-free/MIT6.824-2017-Chinese/tree/master/Lab2/PartA), [PartB](https://github.com/double-free/MIT6.824-2017-Chinese/tree/master/Lab2/PartB), PartC
