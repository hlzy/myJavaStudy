# hadoop 单节点

## １．需求工具

* ssh
* pdsh

并行管理工具。

> 当我们管理数以十计或者更多的集群系统时，往往需要在每台机器上执行同样的命令，或者拷贝同样的文件，这时，我们就可以考虑使用三个小工具，分别是pdsh、ClusterSSH和mussh。

测试，启动了hadoop1~hadoop3,编辑/etc/hosts

```shell
192.169.0.101 hadoop1
192.169.0.102 hadoop2
192.169.0.103 hadoop3
```

```shell
pdsh -w ssh:root@hadoop[1-3] hostname
#得到
hadoop2: hadoop2
hadoop3: hadoop3
hadoop1: hadoop1
```

## 2.  独立运行模型

```shell
 $ mkdir input
  $ cp etc/hadoop/*.xml input
  $ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.2.1.jar grep input output 'dfs[a-z.]+'
  $ cat output/*
```

