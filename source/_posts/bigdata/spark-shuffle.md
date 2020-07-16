---
title: spark-shuffle
date: 2020-06-30 21:36:06
tags: spark
---
shuffle是spark这种类map-reduce计算引擎的核心概念；

历史上，也经历了多轮实现。

spark shuffle是一个昂贵的操作，因为涉及到disk IO、数据序列化、网络IO
map task来组织data，reduce task来聚合

分为2个阶段

## shuffle write

## shuffle read





## join in spark sql

### 

[BHJ](!https://sujithjay.com/spark/broadcast-joins)

```
spark.sql.autoBroadcastJoinThreshold=10M
```
当join的某一个表小于以上阈值时，会选择使用 Broadcast Hash Joins 算法




 
