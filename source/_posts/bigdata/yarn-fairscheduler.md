---
title: yarn公平调度器
date: 2020-07-24 16:42:58
tags:
---
# resource manager
分为2部分
- 调度器 scheduler
- 应用程序管理器 application manager, ASM




## fair scheduler
目标 所有的job获得同样份额的资源

1、系统默认按照内存来公平调度
2、系统按照queue来组织apps，在queue上公平共享

资源在每个子队列里共享 
a (b(c, d), e(f, g, h))

这里有5个queue， a.b.c  a.b.d  a.e.f a.e.g a.e.h
他们share是 25 25 16  16 16

在每一个queue里，可以指定单独的调度策略，比如 fifo、fair、capacity

各个queue可以设置最高与最低的限额 
也可以设置不同的优先级

当一个资源池有资源多余时，可以将多余资源共享给其他pool

* 根据每个资源池的最小资源保障，将系统中的部分资源分配给各个资源池；
* 根据资源池的指定优先级将剩余资源按照比例分配给各个资源池；
* 在各个资源池中，按照作业的优先级或者根据公平策略将资源分配给各个作业；

- 参数


|参数|default|说明|
|-|-|-|
|yarn.scheduler.fair.user-as-default-queue| true  |使用提交用户为队名|


## capacity scheduler

多租户调度器，强度资源在租户之间而不是job之间的公平性


按照queue来组织group，强制划分各自的地盘






## 全局调度器
global scheduler



