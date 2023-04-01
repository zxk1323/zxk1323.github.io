---
title: 'Hadoop-HDFS'
date: 2021-06-01
permalink: /posts/2021/06/Hadoop-HDFS/
tags:
  - Hadoop
  - HDFS
---
### HDFS

### HDFS架构

![img](../img/Blog/Hadoop/Hadoop-1.png)



![img](/images/Blog/Hadoop/Hadoop-1.png)

- Client：客户端

- - 切分文件。文件上传HDFS的时候，client将文件切分为一个一个的Block，然后进行存储。
  - 与NameNode交互，获取文件的位置信息
  - 与DataNode交互，读取或者写入数据
  - client提供一些命令来管理HDFS，比如启动关闭HDFS、访问HDFS目录及内容。

- NameNode：名称结点，也称主节点，存储数据的元数据信息，不存储具体的数据

- - 管理HDFS的名称空间
  - 管理数据块Block映射信息
  - 配置副本策略
  - 处理客户端的读写请求

- DataNode: 数据节点，也称从节点。NameNode下达命令，DataNode执行实际操作。

- - 存储实际的数据块
  - 执行数据块的读/写操作

- Secondary NameNode：并非NameNode的热备。当NameNode挂掉的时候，它并不能马上替换NameNode并提供服务。

- - 辅助NameNode，分担其工作量
  - 定期合并Fsimage和Edits，并推送给NameNode
  - 在紧急情况下，可辅助回复NameNode

### HDFS文件块大小的设置

HDFS中的文件在物理上分块存储(Block),块的大小可以通过配置dfs.blocksize设置。

默认大小Hadoop2.x/3.x版本中为128M，1.x版本为64M。

![img](/images/Blog/Hadoop/Hadoop-2.png)

### HDFS写数据流程
![img](/images/Blog/Hadoop/Hadoop-3.png)

（1）客户端通过Distributed FileSystem模块向NameNode请求上传文件，NameNode检查目标文件是否已存在，父目录是否存在。

（2）NameNode返回是否可以上传。

（3）客户端请求第一个 Block上传到哪几个DataNode服务器上。

（4）NameNode返回3个DataNode节点，分别为dn1、dn2、dn3。

（5）客户端通过FSDataOutputStream模块请求dn1上传数据，dn1收到请求会继续调用dn2，然后dn2调用dn3，将这个通信管道建立完成。

（6）dn1、dn2、dn3逐级应答客户端。

（7）客户端开始往dn1上传第一个Block（先从磁盘读取数据放到一个本地内存缓存），以Packet为单位，dn1收到一个Packet就会传给dn2，dn2传给dn3；dn1每传一个packet会放入一个应答队列等待应答。

（8）当一个Block传输完成之后，客户端再次请求NameNode上传第二个Block的服务器。（重复执行3-7步）。

### HDFS读数据流程

![img](/images/Blog/Hadoop/Hadoop-4.png)

（1）客户端通过DistributedFileSystem向NameNode请求下载文件，NameNode通过查询元数据，找到文件块所在的DataNode地址。

（2）挑选一台DataNode（就近原则，然后随机）服务器，请求读取数据。

（3）DataNode开始传输数据给客户端（从磁盘里面读取数据输入流，以Packet为单位来做校验）。

（4）客户端以Packet为单位接收，先在本地缓存，然后写入目标文件。