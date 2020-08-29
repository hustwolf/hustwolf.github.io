---
title: hdfs namenode分析
date: 2020-07-24 17:01:38
tags:
---


# normal hdfs


## 启动流程
- loading fsimage
- loading edits
- saving heckpoint
- safe mode : 各个datanodes report各自的block信息，形成block map，然后退出安全模式

![hdfs-startup](../hdfs-namenode/hdfs-namenode-startup.png)

Namenode 存储 Namespace info\block info
注意在namenode的fsimage文件中并不存储block对应的datanode信息，这个是在启动过程中各个datanode report过来的


## Second Namenode 
 -- so called *checkpoint node*


名字很有疑惑性，其实它根本不是name node替代，它唯一的作用就是帮namenode 合并editlog；然后把合并好的checkpoint给回送回去

因为namenode主要是启动的时候才read checkpoint, merge editlog; checkpoint image;
随着时间的增长，系统里的editlog会非常的大，会极大的拖慢系统的启动过程


### 主要就2个参数

|param|default|desc|
|-|-|-|
|dfs.namenode.checkpoint.txns|1000000|事务数 — 超过该数后，NameNode 或 SecondaryNameNode 将为 Namespace 创建一个检查点，无论检查点周期是否已到期。|
|dfs.namenode.checkpoint.period|1 hour|两个周期文件系统检查点之间的秒数。|

参考 http://blog.madhukaraphatak.com/secondary-namenode---what-it-really-do/


# HA下的hdfs

https://hexiaoqiao.github.io/blog/2018/03/30/the-analysis-of-basic-principle-of-hdfs-ha-using-qjm/


包括

![ha-arch](../hdfs-namenode/ha架构图.png)

ANN active namenode 对外提供读写服务
SNN 

journal node



ZKFailOverController  作为watch dog程序监控当前机器上的namenode状态


其实在官方文档里，failover是分2种，一种是 只有 ann、snn与 journal node的 手动failover
一种是 增加了zookeeper与zkcontroler的automate failover

详细的流程
https://developer.ibm.com/zh/articles/os-cn-hadoop-name-node/

zkfc内部还有2个compment



|role|desc|num|
|-|-|-|
|namenode|-|2|


Fencing 隔离
为了防止脑裂

以这个为例，因为靠zkfc跟zookeeper实现选举，如果namenodeA是active，A的zkfc fullGC 暂停时间太长；
那么zookeeper会认为这个client session挂掉了，会notify standy by，如果standby直接切换自己为active，这会产生脑裂，所以步骤永远都是standby需要确认确实把active给干掉了；

a、看zookeeper里数据结构
ls /hadoop-ha/nameservice1
[ActiveBreadCrumb, ActiveStandbyElectorLock]

这其中 ActiveStandbyElectorLock 是ephemeral节点，用来做选主的；
而 ActiveBreadCrumb 保存有当前主的address；standyby在切换时，会调用api切换他的状态为standy；如果成功，才切换自己；

如果不成功或者卡了，会调用已经配置好的fence逻辑比如把这个程序杀掉之类。。。




