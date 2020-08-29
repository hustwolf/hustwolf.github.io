---
title: hadoop历史
date: 2020-07-25 16:20:13
tags:
---


07年，虽然我读大学时hadoop才开始开发，
但一直到13年 我上班 hadoop2.0发布，才算真正稳定下来

18年发布了hadoop3.0


hadoop2的改动主要包括 以前的job tracker到 yarn
hdfs支持ha



hadoop3的改动呢？

- 最低支持java8
- hdfs支持erasure code
- 对于编程来说，不要引入runtime dependency； shaded client jars
- 


而spark history呢？
14年才成为顶级项目，1.6是16年出来的；
2.3是18年出来的

20年才有3.0


== 

大数据技术很多都是来自于传统技术，比如 语法解析AST、基于规则的优化CRO、基于代价的优化CBO、列存，都来自传统数据库；
shuffle broadcast来自大数据



spark3中一大亮点
1、adaptive sql execute 
痛点a、spark.sql.shuffle.partition， 默认为200，对sql里，这个参数设置了就不变，导致后面都是这个值，不合理
b、spark sql的最佳执行计划，比如join的选择
c、数据倾斜

如何解决这个问题？

a、自适应执行架构
以前是一开始就制定好所有stage的执行过程，现在一个stage一个stage来
上一个stage完成时，已经支持结果集的partition大小了，可以用这些信息指导下游reduce partition数量、join算法、数据倾斜问题

 b、