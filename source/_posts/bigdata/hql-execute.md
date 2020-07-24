---
title: hql-execute
date: 2020-07-16 14:24:42
tags: hive
---

- compiler DO： HQL to AST
使用antlr进行解析
- Semantic Analyzer DO: AST to Query Block Tree
语义分析，比如检查table是否存在，类型、表达式是否合法
- Logical Plan Generator DO: QB tree to logical plan ， 即operator Tree
- Optimizer 优化operator Tree
包括以下算法，比如 列裁剪、partition裁剪、谓词下推
- physical plan gen DO: 生成task tree
- physical optimizer



# hive 的一些优化
常规的不说了；说一下join
