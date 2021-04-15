---
layout: post
title: "（TiDB：TiKV）说存储"
subtitle: "TiDB - Talk about store."
author: "Marshall"
header-img: "img/hero-pc.svg"
header-mask: 0.3
tags:
  - 大数据
  - 存储
  - 笔记
---

### 说存储（TiKV实现原理与如何解决存储相关问题）

#### 存储需要解决哪些问题？

 - (基础功能)不丢不错
 - 支持跨数据中心容灾
 - 写入速度是否快？
 - 数据保存下来，是否方便读？
 - 保存的数据如何修改？如何支持并发修改？
 - 如何原子地修改多条记录？

---

TiKV底层为巨大的K-V结构，key和value都是二进制数组，key按照byte数组原始二进制比特位比较排序
即Key有序链表

##### RocksDB

TiKV采用rocksDB存储（特点 单机高性能），通过raft接口写入rocksDB，每个数据复制都会落地为一条raft复制。这样就有了分布式KV存储了，不用担心节点挂掉了

*（TiKV 通过rocksDB保证单机快速写入速度， 通过raft复制region数据，保证不丢）*

##### Region

TiKV Region概念： 
分布式存储的两种方式： 1 按照key hash，然后存储  2 有序key，分段存储  。 TiKV pick 2
通过startKey-endKey访问，好处自然是适应取批量数。

> 要保证region在节点能够均匀分布。
> 还要保证上层客户端能够访问所需要的数据（寻找region，通过key知道在哪个region，知道region在哪个机器）

Region replica概念：
Region需要replica机制，每组region的replica组叫做raft group, 如下：

```
 --------------------------------
              client
 --------------------------------
   |        |         |       |
   V        V         V       V     
 _____    _____     _____   _____
｜    ｜  ｜    ｜  ｜    ｜ ｜    ｜
｜reg1｜  ｜reg4｜ /｜reg3｜-｜reg3｜----> [raft group(region3)]
｜reg2｜  ｜reg1｜/ ｜reg2｜ ｜reg1｜
｜reg4｜  ｜reg3｜  ｜reg4｜ ｜reg2｜
｜    ｜  ｜    ｜  ｜    ｜ ｜    ｜
 -----    ------    -----   -----
 Node1    Node2     Node3   Node4
```

##### MVCC
有了单机rocksDB高性能写入，和多region replica副本保障。 并发性能还不够，引入MVCC
key+version实现。
```
无mvcc：
	Key1 -> Value
	Key2 -> Value

有mvcc：
	Key1-Version3 -> Value
	Key1-Version2 -> Value
	Key1-Version1 -> Value

```
版本号大的会放在前面，再加上key本身有序，所以再seek时候，可以确定定位到的第一个就是最新版本key。

##### 事务
TiKV的事务采用了percolator模型，并做了大量优化。  
底层乐观锁，执行过程不会写写冲突，只有在提交时候才会检测冲突。在业务的写入冲突比较大时性能不佳。

---

**附录：**

> 如何保持一致性，单节点挂掉？

raft提供能力： 高可用，强一致性，去中心化
提供具体功能： leader选举、成员变更、日志复制
raft细节： leader election、log replication、 membership change、safety

粗略过程：   每个节点启动时都是follower，在election timeout没有收到心跳，转化为
candidate，每个点投票，选举出leader，之后leader在term（任期），负责log replication

timeout后置为candidate，然后发送requestVote,内容大致如下：

```
requestVote:

Node: A
Term: 4
Voted For: B

```

同一个term内一个节点只能投一次
如果出现平票，split vote。会等待下次超时，出现选举时间过长。引入randomized election timeouts 解决。 


 
 
 
 **学习CK、IceBerg、Hive、SPARK、Redis**