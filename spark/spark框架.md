# 1. Spark介绍

## 1.1 spark和Hadoop

* hadoop做一次性数据计算。会从存储设备中读取数据，逻辑操作，得到的结果重新存储到介质中。不太满足数据迭代计算，效率很低。
* spark和Hadoop的根本差异是多个作业之间的数据通信问题，Spark多个作业之间数据通信基于内存，而Hadoop基于磁盘。
* spark是基于内存的，实际环境中会由于内存的限制导致Job执行失败。所以Spark或hadoop的选用要看实际的环境。

## 1.2 Spark核心模块

![image-20201214104651697](spark%E6%A1%86%E6%9E%B6.assets/image-20201214104651697.png)

* Spark Core提供Spark最基础核心的功能，Spark的其他功能如：Spark Sql,Spark streaming ,GraphX MLib都是在Core基础上扩展的。

# 2. spark上手

本地环境TODO

# 3. Spark运行环境

![image-20201214114649764](spark%E6%A1%86%E6%9E%B6.assets/image-20201214114649764.png)

## 3.1 本地模式

解压spark运行./bin/spark-shell即可

## 3.2 standlone模式

1. 集群启动

```shell
#设置slaves
mv slaves.template slaves
vim slaves
#添加slaves的地址
mv spark-env.sh.template spark-env.sh
#添加JAVA_HOME
#SPARK_MASTER_HOST=hadoop1
#SPARK_MASTER_PORT=7077
./sbin/start-all.sh
```

2. 配置历史服务

人物一旦运行hadoop1:4040看不到历史情况，所以需要配置历史服务器记录运行情况

TODO

3. 高可用

## 3.3 Yarn模式

* standlone模式由spark自身提供计算资源，无需其他框架提供资源。
* spark主要是计算框架，不擅长资源调度。

所以可以利用Yarn来完成资源调度。



## 3.4 部署模式对比

| 模式       | spark安装机器 | 需启动的进程   | 所属   | 场景     |
| ---------- | ------------- | -------------- | ------ | -------- |
| local      | 1             | 无             | spark  | 测试     |
| standalone | 3             | master和worker | spark  | 单独部署 |
| Yarn       | 1             | Yarn和HDFS     | hadoop | 混合部署 |



# 4. Spark运行框架

主从结构

![image-20201215213104955](spark%E6%A1%86%E6%9E%B6.assets/image-20201215213104955.png)

Dirver:

驱动器节点，执行程序的main方法

* 将用户程序转化为作业(job)
* 在Executor之间调度任务(task)
* 跟踪Executor执行情况
* 通过UI展示查询运行情况

> 实际上，在整个编程过程中么没有Driver字眼。简单理解就是Driver驱使整个应用程序运行起来，叫做Driver类。

Executor:

* 负责运行spark应用的任务
* 他们通过自身的块管理，也会做RDD缓存，加速计算。