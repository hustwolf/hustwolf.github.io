---
title: vscode
date: 2020-07-09 14:59:34
tags:
---


一些主要的概念

workspace  vs folder



folder vscode在打开每个folder时，会建立一个文件 .vscode，这个里面从来存储配置文件

主要有
- launch.json
- 


workspace 

可以把很多个root folder都给拉进来，这样可以更新一些公共的配置；
整体保存在一个 *.code-workspace这样命名的一个json文件中


launch.json
定义在run或者debug时启动的命令

task.json

定义各种task的地方



## 对于c/cpp project来说
c_cpp_properties.json
