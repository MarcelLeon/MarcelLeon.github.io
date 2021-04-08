---
layout: post
title: "面试必刷"
subtitle: "Interview Brush"
author: "Marshall"
header-img: "img/squirrel-eat.jpeg"
header-mask: 0.3
hidden: true
tags:
  - 笔记
  - 面试
---

### 算法

1、一个有序数组（从小到大排列），数组中的数据有正有负，求这个数组中的最小绝对值
 [-3,-1,2,3]
 ```
 void minAbs(int[] arr){
     int tmp = 0;
     for(int i = 0; i < arr.length; i ++){
         if(arr[i] > arr[i+1]){
             
         }
     }
 }
 
 void findZero(int[] arr){
     if(a){}
 }
 ```
 
 
 1、1个日志文件，"xfadsdsf\t111"，第二列的含义是服务响应时间，使用linux命令计算平均耗时？
 ```
 cat xfadsdsf\t111 | awk '{print tim=$1;`wc -c`}'  | awk '{total=$0;lines=;print $total/lines}' 
 ```

 
有10个文件，每个文件1G，每个文件的每一行存放的都是用户的query，每个文件的query都可能重复。要求你按照query的频度排序。内存200MB

```
10 * 1G = 10 * 5 * 200

机器 加载 200m ，统计 维护长度为3的最大堆。 query：频次 -》 存储
如果内存不足就删除后续子节点 ---  不重复的多
```

### JAVA
1. JMM 底层原理
> 内存分为共享内存和线程私有内存，（底层  就是更好的发挥cpu和内存）产生了有序性 可见性的问题， 衍生屏障 happened-before
> [JMM底层原理](https://www.jianshu.com/p/8a58d8335270)

2. 线程通信，进程通信？
> 线程通信： a 共享内存（共享对象，比如synchronized(obj) 哪个线程抢到执行） b 消息传递（wait notify） c (TODO)管道通信 java.io.PipedInputStream java.io.PipedOutputStream
> ``` 
> wait notify 要在synchronized(obj){obj.wait(); obj.notify();}中使用，  Condition需要在Lock.lock();  try{ xxxx  }finally{Lock.unlock();}中使用
>  ```
> 进程通信：
 
3. 线程池


4.  jvm对象判活？
> - 引用计数，py和java存在对象互相引用不会生效
> - 可达性分析，GC roots，包含：本地变量，     finalize可以捞一次

5. 垃圾收集器
> 内存规整，不规整   

  serial收集器 : 单线程，开销最小，默认是客户端新生代的gc，停止所有用户线程。标记复制
  
  parnew: 新生代，并发版serial，没改进。能与cms联用。标记复制
  
  parallel scavenge: 新生代，吞吐量优先收集器，trade off，useAdaptiveSize. 自动控制大小。
  
  serial old. 老年代，标记整理，给客户端设计的。可以与serial，ps一起使用。是cms的后备预案。
  
  ps old: 老年代 标记整理，与ps组合成为吞吐量优先。
  
  cms: 标记清除，执行步骤: 初始标记->并发标记->重新标记->并发清除，并发清除时候的用户线程创造floating garbage。
  当清理阶段用户使用内存大于剩余的内存会导致GC失败。失败将改为serial old。XX：CMSInitiatingOccu-pancyFraction配置百分比
  cms三个问题: 1 占用户线程，导致处理变慢。2 清除阶段预留少会导致gc失败改为serial old. 3 内存碎片. 通过参数useCompactAtFullCollection. 
  before设置full gc整理内存
  
  G1: j9默认替换掉了原来的ps组合，j9 cms不推荐使用。面向局部，region内存布局。
   j10 重构垃圾回收接口，把行为和实现分离开来。
  停顿时间模型: m运行时间内停顿n个时间。g1做到了可预测时间。rtsj是什么？
  把region的垃圾集合成csets收集。把region(最小收集单位)看做年轻代老年代，humongous存放大对象＞0.5region. 如果大于regionsize会存放到多个连续humongous. 
  参数G1HeapRegionSize1-32
  参数MaxGCPauseMills 200ms
  region维护了价值(即回收得到的价值) 根据这个优先级gc. 
  g1需要解决的问题: 
  基于region的互相引用？
  memory set. 每个region一个，实现方式是Map(key,index of Map) 卡表的key是其他region的地址，value是卡表的索引号(卡页，原来卡表的实现是1字节->512字节，与region卡表无关)。存储的多所以占了堆10-20%
  整体标记整理，region间标记复制。

6. 对象分配流程
> 尝试栈上分配 > 尝试TLAB分配 > 是否可以直接老年代 > 最后eden

7. 伪共享
几个数值分配到了一个cache line上，修改任意一个都会导致cache line变更， 优化方式@sun.misc.Contended
long = 4byte  
cache line = 64byte 

#### java集合
1. TreeMap
> 1. 节点是红色或者黑色
> 2. 根是黑色
> 3. 所有叶子都是黑色
> 4. 每个红色节点必须有两个黑色子节点
> 5. 从任一节点到每个叶子节点所有简单路径都有相同黑色节点

---

### DataBase
1. MVCC

2. 复合索引

---

### Protocols
1. CAP(Consistency、Availability、Partition Tolerance)


---

### 框架
#### SpringBoot
1. SpringAOP
> spring的aop是基于cglib jdk动态代理实现的，也支持ApsectJ写法
>
> 实现原理：通过BeanPostProcessor对Bean拦截包装：
> 寻找所有@Aspect注解的Bean 解析advice方法，动态构建Advisor，注入代理对象 然后创建拦截链，责任链方式吊用Advisor
>
> spring在两种情况下 使用不同的代理模式： 有接口实现时jdk（如 @service），没有接口实现用cglib（如 @controller）（方便记忆，就是能用jdk就用jdk，但是要求有接口，所以使用cglib补充controller）
>
> AspenctJ 编译时注入，更复杂，但是性能更高   指定-javaagent xxx.jar


#### Netty
1. IO NIO
> IO 基于字节流，阻塞，读写过程阻塞直到完成;
> 
> NIO 基于Buffer，从Channel读取后到Buffer，或者Buffer写到Channel，非阻塞，引入Selector
> 
> NIO 带来了
> - 事件驱动，IO多路复用。
> - 单线程处理多任务 减少线程，变向像纤程 协程
> - no block
> - 基于Buffer传输 高效
> - 更高级的IO函数 zeroCopy
> 
> 适合IM服务器，instant message


#### Flink

---

### 往期面试记录

#### 优选

一面
1. 


#### 团好货
1. checkpoint 意义，优点缺点？

2. checkpoint 流程？

3. 端到端exactly-once，有没有场景？如何实现

4. 服务降级怎么做？

5. 任务如何优化？

6. flink架构演化？

7. 资源优化，节省资源？

8. 任务失败后的恢复？

9. 为什么用rocksdb 不用其他noSql？

10. 项目难点，解决什么问题？

11. sql rank over - 算法 合并+排序

sun xf
1. 实现一个线程池

2. 10T 数值用，16G+20T做全排序？

#### 滴滴

一面：

DD中台1：

```
      1
    2   3 
   4 5 6 7
```

二叉树、叶子节点   n是总节点   n/2 + 1

---

> 思考：
> 查找：
> 二分
> 排序：
> 快排、插入排序、冒泡、堆排序
> 算法：
> 双指针算法、贪心算法、递归算法、

---

项目
java高并发使用
CHM 锁，竞争？
cas  重排序。 对比值
微服务--数据中台--one data one model

数据还是后端开发？   都行，看安排 ----- 为啥离职？个人发展，想去大厂锻炼锻炼，接受更大的挑战

二叉树  层级遍历，维护行号，存储到一个二维数组，之后存储到一个数组中，然后打印。

合并两个单链表，两种方式  递归方式。双指针方式。

多思考。。。

二面：

D2：

DSI 标签 -- 中台
业务
-------------
给定一个二叉树，检查它是否是镜像对称的。


例如，二叉树[1,2,2,3,4,4,3] 是对称的。

```
    1
   / \
  2   2
 / \ / \
3  4 4  3
```

但是下面这个[1,2,2,null,3,null,3] 则不是镜像对称的:

```
    1
   / \
  2   2
   \   \
   3    3
```

反转一个单链表。

示例:

输入: 1->2->3->4->5->NULL
输出: 5->4->3->2->1->NULL

```
class ListNode{
	int val;
	ListNode next;
}

1 2 3 
3 2 1

ListNode reverseListNode(ListNode head){
	
	ListNode pre = null;
	ListNode next = null;

	while(head != null){

		next = head.next;
		head.next = pre;

		pre = head;
		head = next;
	}
}
```


三面：
问题：
快  两个单不能同时
拼   两个单可同时

输入是所有账单

输出有乘客时长

```
1 2
     48
1 - 3
  2 - 8
1 -   8
        12 - 18
```

交集

```
class Order{
	String type;// k,p
	int startHour;  0 - 24
	int endHour;  
}
int getCarryingHours(List<Order> orders){
	
	int result = 0;
	List<Integer> pStartArr = Lists.newArrayList();
	List<Integer> pEndArr = Lists.newArrayList();

	orders.forEach(o -> {
		if("k".equals(o.type)){
			// 需要考虑跨天
			result += o.endHour - o.startHour;
		} else {
			pStartArr.add(o.startHour);
			pEndArr.add(o.endHour);
		}

	});

	result += pEndArr.stream().max() - pStartArr.stream().min();

	return result;
} 
```

#### 快手

ks1:

bloomFilter
无序数组最大的两个
最大的k个
手写最大堆

ks2：
volatile作用
对象都包括什么
cas的问题

1、性能比较高的计数器
counter++ 

1）CAS
2）threadlocal 

2、一个单链表倒数第k个节点

3、二叉树的高度

```
Node {
Node left
Node right

int getHeight(Node root) {

    return max(root->left,root->right) + 1
}
}
```

4、二分搜索算法

#### 京东

JD1

flink 优化，社区贡献 issue 、dashboard、sql-gateway升级1.12  -  个人blog
bf实现  schema
exactly-once 实现
window trigger机制
keyBy

-- StringToInt

JD2 

项目
exactly-once wm et
AtomicInt
phaser 其他同步器、其他juc工具
ABA

-- 线程按顺序打印

-- 链表去重