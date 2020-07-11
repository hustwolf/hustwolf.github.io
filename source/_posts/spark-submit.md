---
title: spark submit
date: 2020-07-01 16:34:04
tags:
---
这里以在yarn上跑为例解析

如果以yarn-client mode提交的话，那么提交的进程就是spark的driver与yarn的application master
如果以yarn-cluster mode提交的话，那么yarn会启动一个spark进程，这个进程在yarn里叫application master，负责向yarn resource manager要资源以及分发程序；同时它也是spark中的driver，负责解析任务，调度作业。


这个与flink不一样，