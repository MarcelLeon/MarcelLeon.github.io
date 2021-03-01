---
layout: post
title: "【java基础-并发编程】关于并发的几个关键字volatile、synchronized、final"
subtitle: ""
author: "Marshall"
header-img: "img/post-bg-halting.jpg"
header-mask: 0.3
tags:
  - 并发编程
  - jvm
  - java
---

*java基础-并发编程*

## 本文将回答哪些问题：
**volatile、synchronized、final这几个关键字的作用？**

**volatile、synchronized、final底层原理是什么？**

**面试中会怎么问这些关键字？**

## 背景简介
自学+面试心得

## 正文
### 【volatile】
#### 定义
是java提供的关键字，能够实现可见性和有序性
#### 如何实现可见性
在写volatile修饰的变量时，向cpu请求前添加lock前缀，这样可以达到写完该变量值后，立即刷新到内存（而不是存在cpu的L1 L2缓存中）。此时各个cpu（我们默认是多核cpu场景下）缓存不一致，当任意其它线程去访问该变量时，都将从内存中重新拿该值。以此实现可见性。(附：涉及到cpu的lock指令和MESI缓存一致性协议，如果对于你是新概念建议查阅一下) (附2：之前面试被问到为什么不能实现原子性，读者也可以思考一下。)
#### 如何实现有序性
程序员总想让计算机按照自己代码的顺序去执行，而计算机总想把指令重排序按照自己流水线去高效执行。 高效执行代码是好的，但全依着cpu和编译器排序是不行的（排序包括cpu和编译器）。这里就是怕它不来（影响性能），又怕它乱来（重排序后影响程序预期结果）。所以定义了一些规定，引申出了两个概念 JMM + happen-before，这里说的volatile修饰的变量，编译器会为其加上StoreStore屏障，对应两种限制(限制这个词可能不够准确哈)Store、Load，举个例子  StoreStore的意思就是先进来线程1对X的写要先于后进来线程2的写。这个语义能保障cpu不会对其重排序，最终保障执行有序性。（附 jsr-133对volatile的有序性有了修改，在volatile后面的变量与volatile的读写也是不可重排序的。）
### 【synchronized】
#### 定义
是一种隐式锁，提供了同步语义，一个线程进入后，其它线程将等待线程释放后才可进入。（jdk1.6后）在刚创建时synchronized是无锁的，有单个线程是偏向锁的，多个线程竞争时是轻量级锁，高并发时将会升级为重量级锁（mutex）。
#### 如何实现锁
与lock的不同是，它只需要写一个由synchronized包括的区间就可以实现锁。所以叫隐式锁。那总要有人实现锁吧，其实是JVM在编译器就对它动了手脚。会在字节码中编译成monitorenter和monitorexit对应入和出synchronized（x）{ **** } 区间。值得注意的是monitorexit有两个啊，第二个对应的是error发生时的释放锁。
具体monitorenter的实现则是基于操作系统(linux)的pthread_mutex_lock，这个方法在linux内核代码的pthread.hpp里，感兴趣的可以找找，反正我找到了没看懂。
#### 还有啥？
其实synchronized的知识点还是很多的。比如锁升级锁降级，锁粗化。会不会响应中断，锁升级的详细过程。建议大家找找相关文章。我这里就不详写了，太多了，而且网上写的好很多。
### 【final】
#### 定义
final定义的变量第一次赋值后不可变，定义的方法不可重写，定义的类不可继承，还有个功能就是局部的有序性。
#### 如何实现
与volatile类型，将会对final修饰的变量添加禁止重排序。具体是为其和构造函数间添加一个StoreStore屏障，即先发生赋值final变量之后再return构造。
（附 把变量声明成final是个好的编程习惯）
## 总结
java面试官会很中意问这些关键字相关的问题。

比如：volatile的作用？ 怎么实现的xx？ 怎么实现的xxx？

synchronized的锁升级流程是什么样的？

手写个DCL单例模式？


