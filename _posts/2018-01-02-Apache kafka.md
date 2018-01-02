# Apache kafka

标签（空格分隔）： Apache_kafka

---
#kafka 简介
=========

>* Apache kafka是一种分布式的基于**发布/订阅**的消息系统。
>* 快速、可持久化、可扩展
>* 可与Apache Strom、Spark等分布式系统集成

> ## kafka的关键特性
> 1.   kafka将消息保存在磁盘中，以**顺序读写**的方式访问磁盘、避免随机读写带来的性能消耗。
> 2.   kafka支持批量读写信息，并对消息进行批量压缩。提高了**网络利用率、压缩利用率**
> 3.   kafak支持消息分区，每个分区中的消息保证顺序传输，而分区之间可以并发操作，这样就提高了kafak的**并发能力**
>4.    kafka 支持在线分区、支持水平扩展
>5.    kafka支持多分区、Leader副本支持读写其他副本负责与Leader副本进行同步，提高了数据的**容灾**能力

>## Kafka的应用场景
>1. 消息中间件（实现消息队列、消息的发布和订阅）
>2. 数据总线、将其接入到多个子系统中
>3. 日志的收集中心，多个系统产生的日志统一收集到Kafka，然后由数据分析平台统一进行处理

![QQ截图20180102153843.png-526.5kB][1]

>* 解耦合   知道消息的topic和消息的存放格式即可
>* 数据持久化 网络不可靠、保存到磁盘、提供了日志清理与日志压缩功能。
>* 扩展与容灾 topic有多个partition，partion有多个replic，每个分区中的消息是不同的。
>* 顺序保证 每个partition中的消息是有序的，并不能保证多个partition之间的数据有序。
>* 异步通信

##Kafka核心概念
>*    1.消息 Kafka中最基本的单元。key、value组成字节数组，key主要是根据一定的celue将消息路由到指定的分区中，同样的key的消息全部写入到同一分区中。消息的真正负载是value部分的数据。为了提高网络和存储的利用率，生产者会批量发送消息到kafka、并在发送之前对消息进行压缩。
>*    2.Topic 存储消息的逻辑概念、生产者向其推送消息，消费者消费其中的信息
>*    3.partition 同一topic下包含多个分区、不同分区的消息是不同的，通过offset保存消息在分区内的顺序，同一Topic的不同分区会分配到不同的Broker上，分区是kafka水平扩展的基础。
![log_anatomy.png-19.1kB][2]
>*    4.分区在逻辑上对应一个Log。Log是一个逻辑概念、对应磁盘上的一个文件夹。Log由多个segement组成，每个segement对应一个日志文件和一个索引文件。因为Kafka采用顺序存储，所以指向最新的segement中加入数据，为了权衡文件的大小，索引速度、占用内存大小。索引文件采用稀疏索引，大小并不会很大，在运行时会将内容映射到内存，提高索引速度。
![1.png-168.2kB][3]
![2.jpg-78.1kB][4]
![3.png-212.5kB][5]
>*    5.保留策略&日志压缩 
>*    6.Broker 一个单独的Kafka Server就是一个Broker。Broker的主要工作是接收producer发过来的消息，分配offset，之后保存到磁盘中，同时接收consumer和其他broker的请求。
>*    7.副本 Leader副本、Follwer副本
>*    8.ISR集合 （In-sync Replica）表示目前“可用”且消息量与leader差不多的副本集合，这是整个副本集合的一个子集。副本节点必须直接与Zookeeper相连接、副本最后一条offset与leader最后一条消息的offset之间的差值不能超过指定的值。Leader副本维护此分区
>*    9.HW&LEO HW标记了一个特殊的offset，消费者处理消息的时候只能拉取到HW之前的消息，之后的消息对消费者来说不可见。HW是有leader副本管理的。当ISR集合中全部的follwer副本都拉取了HW指定消息进行同步后，Leader副本会递增HW的值。HW之前的消息称为commit
>*    10.LEO(lOG End Offset)是所有副本都会有一个offset标记，它指向当前副本的最后一个消息的offset
>*    11.生产者 主要工作是生产消息，将消息按照一定的规则推送到Topic的分区中
>*    12.消费者 主要是从topic中拉取消息，对消费者进行消费。某个消费者消费到partion的那个offset的相关信息是由自己维护的。
>*    13.Consumer Group 多个consumer组成一个Consumer Group，一个consumer只能属于一个Consumer Group。consumer Group保证其订阅的Topic的每个分区只能被一个consumer去消费。kafka还通过Consumer Group实现了消费者的水平扩展和故障转移。可以向Consumer Group添加consumer，触发Rebanlance操作重新分配分区与消费者的关系。


  [1]: http://static.zybuluo.com/chaohan/3pnvwhgorwnto2nghhb1330x/QQ%E6%88%AA%E5%9B%BE20180102153843.png
  [2]: http://static.zybuluo.com/chaohan/zqanmfv7uu8lv06rn86t39cw/log_anatomy.png
  [3]: http://static.zybuluo.com/chaohan/muljy12t6gr4cta6mo6emowx/1.png
  [4]: http://static.zybuluo.com/chaohan/9zbap57ylr9ou41hv5d0i8og/2.jpg
  [5]: http://static.zybuluo.com/chaohan/o0h95ffrq9k8i5a062jteqqn/3.png