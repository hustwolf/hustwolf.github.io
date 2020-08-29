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





## join order
对于 equi-join 来说，顺序是
- Broadcast Hash join
    把小表broadcast大表的每一个分区，然后分别join，
    适合 有一个表比较小的场景

- Shuffle Hash join： 
    首先把数据分区，然后对每一个分区，进行hash join，简而言之，即使把大表叫做 prob table，小表叫build table；对build table做hash table(主要是为了可以存到内存里)，然后开始扫描prob table
- Sort Merge join:
    首先也是对数据分区，然后对每一个分区的2个表，都进行sort，然后2个开始一起扫描，碰到一致的就生成输出
    其实这个操作也不必shuffle hash join简单，但是因为spark里现在默认都是事先的sort shuffle；所以理论上可以认为分区后的数据是已经排好序的 

    