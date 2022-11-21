---
title: 重识c++
date: 2020-08-29 14:55:54
tags:
---

自从大二(2007年）开始学习c，进入编程的世界，到大三学习cpp,
研究生阶段写了不少vc的代码；到2013年开始上班，用vc++写了一些工程，算是高强度的接触c有一年多了把；
从2014年10月开始到现在，已经有6年没有摸过c、c++代码了；
这些年写java写了好多，还接触了很多jvm的变种语言，比如clojure、scala；
clojure这么语言在接触的时候，我还是java小白，拿他改了一个项目，实在是改得糊涂，学得也糊涂；

在没有接触大数据之前，其实也学习过scala，直到2017年开始做大数据之后，才正儿八经的把scala学了下，scala的语言特性太多了；

而python则是一直在学习的东西

2018年接触到了docker、k8s等


然而为何现在又要找回来呢？

主要现在看东西想看得更加的深入一点，java程序，毕竟还是比较上层；
写多了java程序，现在看c++，也觉得生疏多了；

- object creation

* 直接定义对象
eg： A a；
这个时候会自动 构造函数，对象分配在栈上； 当函数调用结束，栈需要被回收，也会自动调用析构函数； 这是编译器的工作吧。。

* new创建

需要手动delete；对象在堆上

new对象的指针可以作为函数返回值以及函数参数

- operator overloading


|缩写|含义|
|---|---|
|TR1 | c++库扩展的一般名字，包括正则表达式|



- 返回引用
- 返回对象