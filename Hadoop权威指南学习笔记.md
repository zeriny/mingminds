## Hadoop权威指南学习笔记



### 一、Hadoop分布式文件系统

#### 1 HDFS

- 以流式数据访问模式来存储超大文件：一次写入，多次读取
- 数据块默认64MB
- HDFS集群有两类节点
  - 一个namenode(管理者)：管理文件系统的命名空间，维护文件系统书及整棵树内所有的文件和目录，也记录每个文件中各个块所在的数据节点信息。**没有namenode，文件系统将无法使用**。
  - 多个datanode(工作者)：根据需要存储并检索数据块（受客户端或namenode的调度），定期向namenode 发送所存储的块的列表。

#### 2 文件系统的基本操作

获取每个命令的详细帮助文件

```shell
$ hadoop fs -help
```

从本地文件系统(input/docs/a.txt)复制文件到HDFS(hdfs://localhost/usr/tom/b.txt)

```shell
$ hadoop fs -copyFromLocal input/docs/a.txt /usr/tom/b.txt
```

新建目录/查看目录

```
$ hadoop fs -mkdir books
$ hadoop fs -ls .
```

### 二、MapReduce

- Hadoop MapReduce是一个使用简易的软件框架，基于它写出来的应用程序能够运行在由上千个商用机器组成的大型集群上，并以一种可靠容错的方式并行处理上T级别的数据集。
- 一个Map/Reduce作业（job）会把输入的数据集切成若干独立的数据块，由map task完全并行地处理它们。map的输出先经过排序，在把结果输入给reduce task。job的输入输出通常会被存储在文件系统中。
- Map/Reduce框架由一个单独的master JobTracker和每个集群节点的slave TaskTracker共同组成。master负责调度和监控所有任务，这些任务被分布在不同slave上。
- 应用程序需指明输入输出路径，并通过实现合适的借口或抽象类来提供map和reduce函数。
- map和reduce函数遵循如下常规格式

```
mapper: (k1, v1) --> list(k2, v2)
combiner: (k2, list(v2))->list(k2, v2) （在排序后执行，数据更紧凑）
partitioner: (k2,v2) -> integer（指定分区索引）
reduce: (k2, list(v2)) --> list(k3, v3)
```

reduce函数的输入类型必须与map函数的输出类型相同

#### 1 Map任务处理

- 读取HDFS文件，每行解析成一个<k, v>对。每个键值对调用一次map函数，例如<0, hello you> <10, hello me>
- 覆盖map()，接受上一步产生的键值对，进行处理转换成新的<k,v>输出。例如，<hello,1> <you,1> <hello, 1><me, 1>
- 对前两步输出的<k,v>对进行分区，默认分为一个区
- 对不同分区的数据根据k进行排序、分组（相同key的value放在同一集合中）
  - 排序：<hello,1><hello,1><me, 1> <you,1>
  - 分组：<hello, {1,1}> <me, {1}> <you,{1}>
- 对分组后的数据进行规约（可选）

#### 2 Reduce任务处理

- 多个map任务的输出，按照不同的分区，通过网络复制到不同的reduce节点上（shuffle）
- 对多个map的输出进行合并、排序
- override reduce函数，接受的是分组后的数据，实现自己的业务逻辑，产生新的<k,v>输出
- 将reduce输出的<k,v>写到HDFS中



