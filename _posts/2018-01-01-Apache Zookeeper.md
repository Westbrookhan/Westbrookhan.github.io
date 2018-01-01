# Apache Zookeeper

------

<a href="https://zookeeper.apache.org/">Zookeeper简介：</a>

> * Zookeeper 分布式服务框架是 Apache Hadoop 的一个子项目，它主要是用来解决分布式应用中经常遇到的一些数据管理问题，如：统一命名服务、状态同步服务、集群管理、分布式应用配置项的管理等等




Zookeeper主要是为了满足：如何让一个应用中多个独立的程序协同工作


>* Zookeeper从文件系统出发就，提供了一组简单的API，使得开发人员可以实现通用的协作任务。
>* 包括Leader选举、管理组内成员、管理元数据等
>* 运行在一组服务器上，保证了高容错性和可扩展性
>* 使用Zookeeper时需要将协同数据和应用数据分别开来，zk主要管理的是协同数据及元数据
>* 一个协作任务是由多个进程组成的，这个任务可以是为了协作也可以是为了竞争。协作：从节点空闲时通知主节点可以工作。竞争：多个节点都想成为主节点。



------

## Zookeeper中对分布式文件系统的定义

>* 同时跨越多个物理主机、独立运行的多个软件组件所组成的系统。
>* 分布式系统中信息交换的方式有两种：
>* 1.通过网络直接进行信息交换
>* 2.或者读写某些存储
>* zk采用第二种方式 对于共享本身又需要在进程和存储之间进行网络通信

###  CAP 

- [x] Consistency 一致性
- [x] Availability 可用性
- [x] Partiton-tolerance 分区容错性



### Zookeeper数据树结构图
![5321750-b269f41e3fce5cea.png-29.8kB][1]
>* Zookeeper API暴露了以下的方法
>* create /path data 创建节点并包含数据
>* delete /path 删除节点
>* exists /path 检查节点是否存在
>* setData /path data 设置名为/path节点的数据为data
>* getData /path 返回名为/path节点的信息
>* getChildren /path /返回/path的所有子节点列表
</br>





### ZNode的不同类型
>* 持久节点  只能调用delete来删除  如保存从节点任务的分配情况
>* 临时节点  连接断开时将被删除    如主节点为临时节点
>* 有序节点  会分配一个自增ID 如分配一个Task-00001  Task-00002


### 监视与通知
>* 每次访问ZNode时，客户端都要获取节点内容，这样代价就非常的大。
>* 转轮训为通知 客户端提交要监视的ZNode、监视点是一个单独的出发操作。收到消息后需要再次设置监视
>* 设置Watch事件

### 版本
>* 每一个ZNode都有一个版本号、它随着数据的变化而自增

### Zookeeper架构
>* 独立模式
>* 仲裁模式     n/2 -1

### 会话


>* 客户端通过Tcp协议与服务器进行连接并通信，但当前会话无法与服务器连接时可能会连接到不同的服务器上。会话提供了顺序保证及FIFO

---

## 开始使用Zookeeper
![1.png-104.9kB][2]
>* 尝试连接到localhost：2181
>* 客户端初始成功连接新的会话



### 创建临时主节点
![2.png-5.9kB][3]
</br>

### 从节点、任务、和分配
![3.png-11.9kB][4]
>* 创建从节点、任务和分配

>* 创建一个从节点
![4.png-10.3kB][5]

>* 从节点创建一个ZNode接受任务分配
![6.png-9.6kB][6]
>* 从节点现在已经准备就绪、可以接受任务的分配

### 创建客户端角色

>* 要求主系统执行cmd命令
![9.png-5.1kB][7]
![10.png-4.4kB][8]
>* 执行任务的从节点会在task-0000000000下创建状态的Znode

### 主节点检查新任务并分配到从节点
![12.png-3.8kB][9]
![13.png-4.2kB][10]
![11.png-5.8kB][11]
>* 一旦从节点完成任务 它就会在从节点下面创建状态ZNode
![15.png-18.9kB][12]

作者 [@han][13]     
2018 年 01月 01日    


  [1]: http://static.zybuluo.com/chaohan/if1yh2o14hwis12hn0ym36je/5321750-b269f41e3fce5cea.png
  [2]: http://static.zybuluo.com/chaohan/zm1m5fc2xgwmakwgpydii665/1.png
  [3]: http://static.zybuluo.com/chaohan/uovrpc56dyohm9nj4q4w0hxr/2.png
  [4]: http://static.zybuluo.com/chaohan/ayx2i3ja1pouuj02bwai6o2w/3.png
  [5]: http://static.zybuluo.com/chaohan/67rpwoulwvgnih7wbmcih8mo/4.png
  [6]: http://static.zybuluo.com/chaohan/4mus8muoqxhoyu4xqzjo7e4m/6.png
  [7]: http://static.zybuluo.com/chaohan/sq2hqe0le77dpoya9s85lgze/9.png
  [8]: http://static.zybuluo.com/chaohan/7d9jz43tb110cejzebaic880/10.png
  [9]: http://static.zybuluo.com/chaohan/ii88erab3y2rxtn1mdt4cos0/12.png
  [10]: http://static.zybuluo.com/chaohan/z3e2mzkmptev77e3fhujomnu/13.png
  [11]: http://static.zybuluo.com/chaohan/s94gyp0q2d9yd7203dsmo0g9/11.png
  [12]: http://static.zybuluo.com/chaohan/mh5n6v7pc5zppebdju1mof1k/15.png
  [13]: http://static.zybuluo.com/chaohan/ayx2i3ja1pouuj02bwai6o2w/3.png