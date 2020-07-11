---
title: spark-partition
date: 2020-07-01 15:24:36
tags: spark
---

spark是一个分布式的计算引擎，分布式处理的对象，就是一个个partition
data stored in partitions AND oerated in parallel


# 那些因素决定了Partition？

在spark中，有各种地方都跟partition有关，比如
- source rdd
- 

## source RDD
- HDFS
one partition per HDFS block

## one to one Tran

Partitions in = Partitions out

## shuffle



## 总结
```
If shuffle required
  If spark.default.parallelism set
      New hash partitioning with `spark.default.parallelism`
  else if spark.default.parallelism not set
      Number of partitions in parent RDD with most partitions
else
  Partitions in = Partitions out
```


## datasets/DataFrame/SparkSQL
参数 spark.sql.shuffle.partitions 控制


## appendix
当然我们自己随时都可以自己定义partition的数量


## spark

> spark.sql.shuffle.partitions  默认值200，控制在join 或者aggregations时partitions的数量; 貌似这个参数只在spark sql中起作用；用于dataframe

> spark.default.parallelism 在RDD的join、reduceBykey等算子中起作用; 如果是这些shuffle算子，那么等于父rdd的最大值，如果没有parent rdd；那就是 Local mode: number of cores on the local machine
Mesos fine grained mode: 8
Others: total number of cores on all executor nodes or 2, whichever is larger


- sparkSession created DataFrame 


### partition policy
- Hash Partition

这是默认的行为
> Partition id = key.hashCode() % numPartitions

- range Partition

不同的算子，使用不同的partition policy
比如 GroupByKey、ReduceByKey  默认使用Hash Partitioning

而sortByKey使用range partitioning


