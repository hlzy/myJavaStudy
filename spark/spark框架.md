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

## 4.1主从结构

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

### 4.2 分布式模拟

由Driver发送Task到Excutor

```scala
//task.scala
package com.helian.task

class Task extends Serializable {
  val datas = List(1,2)
  def logic(x:Int):Int ={
    x*2
  }
  def compute():List[Int] ={
    datas.map(logic)
  }
}
```

```scala
//driver.scala
package com.helian.task

import java.io.ObjectOutputStream
import java.net.Socket

object Driver {
  def main(args: Array[String]): Unit = {
    val task = new Task()
    var client =  new Socket("localhost",9999)
    var out = client.getOutputStream()
    val objOut =  new ObjectOutputStream(out)
    objOut.writeObject(task)
    objOut.flush()
    objOut.close()
    client.close()
  }
}
```

```scala
//Executor.scala
package com.helian.task

import java.io.ObjectInputStream
import java.net.{ServerSocket, Socket}


object Executor {
  def main(args: Array[String]): Unit = {
    val server = new ServerSocket(9999)
    println("server start,waiting...")
    val client:Socket = server.accept()
    val in = client.getInputStream
    val objIn = new ObjectInputStream(in)
    val task = objIn.readObject().asInstanceOf[Task]
    println(task.compute())
    in.close()
    client.close()
    server.close()
  }
}
```

# 5. Spark编程核心

Spark为**高并发**和**高吞吐**封装了三个数据结构，使用在不同应用场景。

* RDD:弹性分布式数据集
* 累加器:分布式共享**只写**变量
* 广播变量:分布式共享**只读**变量

> 只读和只写变量是怎么工作的？

## 5.1 RDD

### * RDD是什么

RDD就是上面写道的Task，下图中的Task就是呗拆分后的subtask

![image-20201223113307504](spark%E6%A1%86%E6%9E%B6.assets/image-20201223113307504.png)

### * RDD实现原理

IO: Input & Output

**装饰者设计模式**

> 在原有功能基础上扩展更丰富的功能，如IO操作是装饰者设计模式。
>
> 参考:https://blog.csdn.net/csdn15698845876/article/details/81544562

![image-20201223204816481](spark%E6%A1%86%E6%9E%B6.assets/image-20201223204816481.png)

> PariRDDFunctions存在隐式转化
>
> https://www.cnblogs.com/xia520pi/p/8745923.html

### * RDD核心特性

```java
/** 
* Internally, each RDD is characterized by five main properties:
 *
 *  - A list of partitions
 *  - A function for computing each split
 *  - A list of dependencies on other RDDs
 *  - Optionally, a Partitioner for key-value RDDs (e.g. to say that the RDD is hash-partitioned)
 *  - Optionally, a list of preferred locations to compute each split on (e.g. block locations for
 *    an HDFS file)
 **/
```

* 分区列表

  * 主要用于并行计算

  * ```java
      /**
       * Implemented by subclasses to return the set of partitions in this RDD. This method will only
       * be called once, so it is safe to implement a time-consuming computation in it.
       *
       * The partitions in this array must satisfy the following property:
       *   `rdd.partitions.zipWithIndex.forall { case (partition, index) => partition.index == index }`
       */
      protected def getPartitions: Array[Partition]
    ```

* 分区计算函数

  * 不同分区计算方式相同

```java
  /**
   * :: DeveloperApi ::
   * Implemented by subclasses to compute a given partition.
   */
  @DeveloperApi
  def compute(split: Partition, context: TaskContext): Iterator[T]
```

* RDD之间依赖关系

```java
  /**
   * Implemented by subclasses to return how this RDD depends on parent RDDs. This method will only
   * be called once, so it is safe to implement a time-consuming computation in it.
   */
  protected def getDependencies: Seq[Dependency[_]] = deps
```

* 可选：给Key-value结构RDD的分区器

```java
  /** Optionally overridden by subclasses to specify how they are partitioned. */
  @transient val partitioner: Option[Partitioner] = None
```

* 可选：计算分区时首选位置

判断数据发送给哪个位置更优

```java
  /**
   * Optionally overridden by subclasses to specify placement preferences.
   */
  protected def getPreferredLocations(split: Partition): Seq[String] = Nil
```

### * RDD原理

* Yarn启动，启动ResourceManger以及NodeManager
* Spark通过Yarn启动Driver和Executor
* Driver通过RDD更具不同分区划分成不同的Task任务

![image-20201224204511063](spark%E6%A1%86%E6%9E%B6.assets/image-20201224204511063.png)

* 调度节点将任务根据计算节点发送到对应的节点(excutor)计算



* ### RDD源码

TextFile

```Java
// FileInputFormat.java
    
```

## 5.2 RDD各项算子

* makeRDD
* map
* mapPartition
* mapPartitionWithIndex
* flatMap
* glom：将内存中的数据转化为数组，分区不变
* groupBy：groupBy会将数据打乱(打散)，重新组合，这个操作我们称只为shuffle。
  * 一个组的数据放在一个分区中，不一定说一个分区中只有一个组。
* filter:数据筛选，分区不变，但分区数据可能不均衡。
* sample:
  * 泊松分布:计算一段时间内一个事情的发生次数https://blog.csdn.net/ccnt_2012/article/details/81114920
* distinct:
* coalesce:缩减分区，分区合并，不会重新打乱分区（原来一个分区还是在一个分区）
* sortBy
* 双value操作
  * intersection
  * union
  * substract
  * zip(分区和分区类元素的数目要一致)
* key-value类型
  * partionBy
    * -HashPartitioner(分类器有很多)
* reduceByKey

>  相较于groupByKey,reduceBykey支持分区内预聚合功能，减少减少了suffle落盘的数据。

* groupByKey

> 这里有个有意思的地方，spark中的shuffle操作必须要落盘处理，不能再内存中数据等待，这样会导致内存溢出。当然落盘会收到磁盘IO性能影响。

![image-20201230164548459](spark%E6%A1%86%E6%9E%B6.assets/image-20201230164548459.png)

截图是reduceBykey写的有一点问题。

* groupBy
* aggregateByKey
* combineByKey
* Join(SQL中的innerJoin)
* leftOuterJoin
* rightOuterJoin
* cogroup: connect + group

## 5.3 RDD行动算子

* reduce
* collect
* count
* take
* first
* takeOrdered
* aggregate:初始值会参加分区内计算，也会参加分区间计算
* fold:aggregate的分区内和分区间规则一样时候使用
* countByKey&countByValue

## 5.4 保存相关算子

save:

* saveAsTextFile
* SaveAsObjectFile
* Save

## 5.5 算子操作

RDD方法的外部操作都在Driver端执行，而方法内部的逻辑操作都是再Executor操作执行。

### 5.5.1 RDD序列化

* 闭包检查

  RDD方法的外部操作都在Driver端执行，而方法内部的逻辑操作都是再Executor操作执行。再Scala函数式编程中，会导致**算子内**使用**算子外**的数据，形成**闭包**（算子内是一个闭包），闭包在执行前会进行序列化检查，也就是**闭包检测**

```scala
//一个比较巧妙的例子
class Search(Query:String) {
    def isMath(s:String):Boolean ={
        s.contains(query)
	}
    //query本身是Serch的一个属性（构造参数也是属性），需要被进行序列化检测，所以会报错
    def getMatch1(rdd:RDD[String]):RDD[String] ={
        rdd.filter(isMatch)
    }
    
   
    def getMatch2(rdd:RDD[String]):RDD[String] ={
        //和getMattch1一样的错误
        rdd.filter(s.contains(query))
        //s是字符串，这样就通过了序列化filter触发的序列化检测
        val s= query
        rdd.filter(s.contains(s))
    }
}
```

### 5.5.2 Kryo序列化框架

Java序列化可以序列化任何类，但是序列化比较重，序列化后，对象比较大。Spark出与性能考虑，Spark2.0开始支持Kryo序列化。Kryo速度是Serialzable十倍。当RDD在Shuffle数据，简单的数据类型，数组，和字符串类型在Spark内部使用Kryo序列化。

## 5.6 RDD依赖关系

![image-20210104215158982](spark%E6%A1%86%E6%9E%B6.assets/image-20210104215158982.png)

* 每个RDD会保存血缘关系

RDD不会保存数据，RDD为了提供容错性，需要将RDD间的关系保存下来，一旦出现错误，可以根据血缘关系讲数据源重新读取进行计算。

![image-20210104215737586](spark%E6%A1%86%E6%9E%B6.assets/image-20210104215737586.png)

```scala
//RDD.toDebugString打印血缘关系
//主语shuffle后的分段表示
```

### 5.6.1 窄依赖

一个上游的RDD只被最多一个下游的RDD依赖(一个下游有多个上游也可以是窄依赖)。

