---
title: java-enums
date: 2020-07-09 11:25:25
tags:
---
本质：
一个只有有限个常量值的类型

enum 其实比较像class

- extends java.lang.Enum
- can't be extended；但是可以implement interface

 Yes, Enum can implement any interface in Java. Since enum is a type, similar to any other class and interface, it can implement any interface in java. This gives lot of flexibility in some cases to use Enum to implement some other behavior.




## 枚举可以有自己的field、method、constructor
其实枚举中的常量值就是一个个的instance，所以某种程度来说，枚举是singlton的泛化

其实在effective java中，也提到了合理使用enum的建议



## EnumSet、EnumMap
key是enum；直接使用



https://blog.scottlogic.com/2016/07/28/java-enums-how-to-use-them-smarter.html