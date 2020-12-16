# HADOOP学习

## 0. Hadoop原理



## １．启动YARN并运行MapReduce程序

### 1.1 分析

* 配置集群在YARN上运行MR
* 启动，测试集群增，删，查
  * 测试包含功能测试，性能测试(压力测试)
* 在YARN上执行wordcount案例

### 1.2 配置集群

#### a. 配置yarn-env.sh

配置JAVA_HOME

#### b. 配置yarn-site.xml

```xml
<configuration>
    <!--Reducer获取数据的方式-->
    <property>
        <name>yarn.nodemanager.aux-services</name>
        <value>mapreduce_shuffle</value>
    </property>
    <!--制定YARN的ResourceManager地址-->
    <property>
        <name>yarn.resourcemanager.hostname</name>
        <value>hadoop1</value>
    </property>    
</configuration>
```

#### c. 修改mapred-env.sh

配置JAVA_HOME

#### d. 修改mapred-site.xml

```xml
<configuration>
    <!--制定MR运行在YARN上-->
    <property>
        <name>mapreduce.framework.name</name>
        <value>yarn</value>
    </property>   
</configuration>
```

#### e. 启动resourcemanager,nodemanager

```shell
./sbin/yarn-daemon.sh start resourcemanager
./sbin/yarn-daemon.sh start nodemanager
```

启动后可以通过:http://192.169.0.101:8088/ 查看map reduce任务

## 2. 配置历史服务器

修改mapred-site.xml

```xml
<configuration>
    <!--历史服务器地址-->
    <property>
        <name>mapreduce.jobhistory.address</name>
        <value>hadoop1:10020</value>
    </pr	operty>
        <!--历史服务器web端地址-->
    <property>
        <name>mapreduce.jobhistory.webapp.address</name>
        <value>hadoop1:19888</value>
    </property> 
</configuration>
```

修改日志聚集

开启日志狙击，可以方便查看程序运行的详情，方便开发调试。

开启日志聚集功能，需要重启NodeManager,ResourceManager和HistoryManager

修改yarn-site.xml

```xml
 <!--使用日志聚集功能-->    
	<property>
        <name>yarn.log-aggregation-enable</name>
        <value>true</value>
    </property>
 <!--日志保存时间-->  
    <property>
        <name>yarn.log-aggregation.retain-seconds</name>
        <value>604800</value>
    </property>
```

## 3. 完全分布式运行模式

## 4. HADOOP编译

TODO

## 5. Hadoop写入数据流程

* 写入流程

![image-20201203233333423](HADOOP学习.assets/image-20201203233333423.png)

## 6. NameNode和SecondNameNode工作流程：

* 若NameNode的数据每次通过磁盘读取，则IO开销大，同时效率低，所以NameNode信息需要存储内存，但若存储内存故障掉电会出现数据丢失，所以需要有磁盘备份。
* 若每次写入内存后都再写入一份数据fsimage则整体耗时和直接写入差不多。
  * 创建一个edits文件只以最佳的方式记录操作
  * 定期将edits文件和fsimages文件合并
* edits和fsimages合并的条件
  * SecondNameNode触发
    * 设置定时
    * edits操作数操作阈值
* SecodNameNode用处：
  * 主要是用于做数据备份
  * 定时请求checkpoint
  * 本地完成edits和fsimage合并，减轻NameNode负担
* NameNode中并没有保存数据块的位置，位置信息以块列表的形式保存再DataNode中。
  * DataNode定期像NameNode同步，可以方便NameNode管理，知道是否数据节点挂掉。

![image-20201204173252725](HADOOP%E5%AD%A6%E4%B9%A0.assets/image-20201204173252725.png)

## 7. 集群的安全模式

![image-20201207102414332](HADOOP%E5%AD%A6%E4%B9%A0.assets/image-20201207102414332.png)

## 8.DataNode

### 8.1 DataNode工作机制

![image-20201207104751869](HADOOP%E5%AD%A6%E4%B9%A0.assets/image-20201207104751869.png)

### 8.2 DataNode超时时间

![image-20201208114807286](HADOOP%E5%AD%A6%E4%B9%A0.assets/image-20201208114807286.png)

### 8.3 添加DataNode节点方式

* DataNode白名单设置
* DataNode

### **8.4 DataNode多目录**

**DataNode多目录只是分多目录，并没有做数据备份**

## 9 HDFS 2.X新特新

### 9.1 集群拷贝

采用distcp实现Hadoop集群之间的数据拷贝

### 9.2 小文间存档

小文件多了占据NameNode空间很多

#### 9.2.1 解决小文件存储办法一

HDFS存档文件HAR文件，==具体来说HDFS存档文件对内还是一个个独立文件，但对NameNode而言确实一个整体，减少了NameNode的内存==

## 10. MapReduce

MapReduce核心功能将用户编写的逻辑代码和自带的默认组件整合成为完成的分布式运算程序，并发运行在Hadoop集群上。

### 10.1 MapReduce优缺点

优点：

* PB级别数据处理
* 机器陈本低，扩展容易

缺点：

* 不擅长实时计算，流式计算
* 不擅长DAG计算，能做但是磁盘操作量较大

### 10.2 编程核心

Map阶段和Reduce阶段

### 10.3 MapReduce进程

完整的MapReduce程序在分布式与性能又三个实例进程

1. MrAppMaster:负责整个程序的过程调度以及状态调度
   1. Driver，获取Job对象
   2. 设置jar存储目录
   3. 关联Map和Reduce
   4. 设置Mapper输出数据的key和value
   5. 设置Reducer输出的key和value
   6. 设置输入路径和输出路径
   7. 提交job
2. MapTask: 负责Map阶段的整个数据处理流程
3. ReudceTask

## 11 MapReduce框架原理

### 11.1 MapTask并行度决定机制

数据块：Block是HDFS物理上分块。

数据切片：数据切片是逻辑上对输入进行分片。

