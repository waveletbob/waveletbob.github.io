---
layout: post
title: 精通Hadoop
categories: 数据科学
tags: 大数据

---

### 1）2.X ###

- 历史
- 加强特性
- 选型

### 2）MR进阶 ###

- MR输入
	- InputSplit块数据
	- InputFormat
	- RecordReader
	- 大量小文件问题
	- 输入过滤（Map）
- Map
	- dfs.blocksize
	- 中间输出结果排序与溢出


	Map输出的中间结果并不是直接写入磁盘，而是使用环形缓冲区的方式缓存到内存，缓冲区满后才可写入磁盘，缓冲区大小由mapreduce.task.io.sort.mb配置，还有一个缓冲的阈值mapreduce.task.io.sort.spill.percent


	- 本地reducer与combiner
	- 获取中间输出结果——Map侧
- Reduce
	- 获取中间输出结果——Reduce侧
	- 中间输出结果的合并和溢出
- MR输出
- MR作业计数器
- 数据连接处理
	- Reduce侧的连接  
	- Map侧的连接

### 3）Pig进阶 ###
果断放弃，没学习价值
### 4）Hive进阶 ###
- Hive架构
	- Hive元存储
	- Hive编译器
	- Hive执行引擎
	- Hive支持组件
- 数据类型
- 文件格式
	- 压缩文件
	- ORC文件
	- Parquet文件
- 数据模型
	- 动态分区
	- Hive表索引
- Hive查询优化器
- DML进阶
	- group by
	- order by与sort by
	- JOIN类型
	- 高级聚合
	- 其他高级语句
		- EXPLODE
		- TABLESAMPLE
		- EXPLAIN
- UDF、UDAF、UDTF
### 5）序列化与Hadoop I/O ###

- Hadoop数据序列化
	- Writable和WritableComparable
	- Hadoop与Java的序列化区别
		主要是因为Hadoop做了精简，这样网络传输的序列化对象就小了
- Avro序列化
	- Avro与MapReduce
	- Avro与Pig
	- Avro与Hive
	- 比较Avro与protocol Buffers/Thrift
- 文件格式
	- Sequence文件
	- MapFile文件
	- 其他数据结构
- 压缩
	- 分片与压缩
	- 压缩范围  
### 6）YARN ###
背景：Hadoop演进：AdHoc集群时代、Hadoop on demand、共享计算集群的黎明、YARN的出现（可扩展性、可维护性、多租户、位置感知、高集群使用率、安全和可审计、可靠性与可用性、编程模型多样性支持、灵活的资源模型、向后兼容）
- YARN架构
	- RM资源管理器
	- NM节点管理器
	- Application Master（AM）
	- 容器
	- 客户端 
- 开发YARN程序
- YARN监控
- YARN作业调度
- YARN命令行  
### 7）Storm ###
- 批处理与流式处理的对比
- Storm简介
	- 架构
	- 计算模型
	- 用例
	- 开发
- 安装（基于YARN）
- 应用：过滤、聚合、数据库读写、计算

### 8）云Hadoop ###

- 云计算
- 云Hadoop
- Elastic Hadoop

### 9）HDFS替代物 ###

- HDFS的缺点
- AWS S3
- Hadoop中实现文件系统
- Hadoop中实现S3

### 10）HDFS联合 ###

- 旧版HDFS架构限制
- HDFS联合架构
	- HDFS联合好处
	- 部署联合NameNode
- HDFS高可用性
	- 从NameNode、检查节点和备份节点
	- 高可用-----共享edits
	- HDFS实用工具
	- 三层与四层网络拓扑
- HDFS块放置策略

### 11）Hadoop安全 ###

- 安全核心
- Hadoop中的认证
	- Kerberos认证
	- Kerberos架构与工作流
	- Kerberos认证和Hadoop
	- HTTP接口认证
- Hadoop中的授权
	- HDFS的授权
	- 限制HDFS的使用量
	- Hadoop中的服务级授权
- Hadoop中的数据保密性
- Hadoop中的日志审计

### 12）Hadoop数据分析 ###
- 数据分析工作流
- 机器学习
- Mahout
- 文档分析
	- 词频
	- 文频
	- 词频-逆向文频
	- 余弦相思的夜
	- K-means
	- 使用Mahout的Mahout聚类
- RHadoop

