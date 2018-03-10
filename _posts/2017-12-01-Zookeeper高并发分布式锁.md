---
layout: post
title: Zookeeper
categories: 数据科学
tags: Zookeeper

---

### 1）基础知识 ###

分布式锁：分布式高并发下，多台微服务机器都要产生唯一订单号，但是接口只能一台访问。

CountDownLatch：多线程并发倒计时抢占，会出现一样的订单号

Lock（JVM，分布式不行）：加在获取订单号上，可以避免出现同时申请订单号
```java
private static Lock lock=new ReetrantLock();
lock.lock();
//....
lock.unlock();
```
### 2）分布式锁技术 ###

- 基于数据库
	- 性能差，数据库容易崩，单点故障
	- 容易死锁
	- 非阻塞
	- 不可重人
- 基于缓存
	- 容易死锁
	- 非阻塞
	- 不可重入
- 基于zookeeper
	- 实现简单
	- 可靠性高
	- 性能好

### 3）ZK ###

分布式、开源分布式应用程序协调服务，Hadoop、Hbase、Kafka、Dubbo中都有。

znode是跟Unix文件系统路径类似的节点可以存储获取数据

通过客户端可以添删改查，还可以注册watcher监控node情况变化


- 节点类型
	- 持久型（PERSISTENT）
	- 持久顺序（PERSISTENT_SEQUENTIAL）
	- EPHEMERAL（临时目录节点）
	- EPHEMERAL_SEQUENTIAL（临时自动编号节点）

实现分布式锁的基础：持久型节点和临时节点，在znode下，编号唯一

- 应用
	- 统一命名服务
	- 配置管理
	- 集群管理
	- <font color=red>共享锁（分布式锁）</font>
	- 队列管理
	- Master选举



