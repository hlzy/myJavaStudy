# MLlib

ML目的让机器学习**可扩展** 和**简便**，提供以下工具包:

* ML算法包：分类，回归，聚类，协同过滤
* 特征工程：特征提取，变换，降维和选择
* 管道(PineLines):构造器，评估器，调整器
* 持久工具：保存和加载算法，模型和管道
* 使用工具：线性计算，统计，数据处理等

## 声明：数据处理API是最优先API

> RDD-based API是维护状态，spark2.0后RDD-base(spark.mllib)进入维护状态，最优先的机器学习SPARK API是 DataFrame-based API (spark.ml)

***影响：***

* spark.mllib中的RDD-base API会通过bug修复支持
* RDD-base API不增加新功能
* Spark 2.X版本主要是增加DataFrames-basedAPI达到和RDD-based API均等

***为何切换到DataFrame-based API***

* DataFrames相比RDDS提供更多友好的API.包括但不限于以下：
  * Spark Datasources
  * SQL/DataFrame 查询
  * Tungsten and Catalyst optimizations (**这是啥**)
  * 各语言统一的API
* DataFrame-based API
* DataFrame有助于实际的ML管道，特别是转换功能。有关详细信息，请参见[管道](https://spark.apache.org/docs/latest/ml-pipeline.html)指南。

## 依赖

线性函数包：[Breeze](http://www.scalanlp.org/) 

数值处理:[netlib-java](https://github.com/fommil/netlib-java) 

> 由于OSS licenses，netlib-java不能与spark一起部署。查看 [MLlib Linear Algebra Acceleration Guide](https://spark.apache.org/docs/latest/ml-linalg-guide.html)如何使用线性处理。如果不能使用本地库，将会使用JVM实现替代，并收到告警信息
>
> ```
> WARN BLAS: Failed to load implementation from:com.github.fommil.netlib.NativeSystemBLAS
> WARN BLAS: Failed to load implementation from:com.github.fommil.netlib.NativeRefBLAS
> ```

如果使用Python,需要Numpy >=1.4

## 3.0中的两点



