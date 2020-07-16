---
title: jvm-gc
date: 2020-07-09 13:58:31
tags: jvm
---

safepoint:


cms
```
2020-07-08T12:36:57.963+0800: 6.552: [GC (Allocation Failure) 2020-07-08T12:36:57.963+0800: 6.552: [ParNew: 78654K->8704K(78656K), 0.0856753 secs] 83665K->83798K(253440K), 0.0857600 secs] [Times: user=0.47 sys=0.00, real=0.08 secs]

step 1
2020-07-08T12:36:58.049+0800: 6.638: [GC (CMS Initial Mark) [1 CMS-initial-mark: 75094K(174784K)] 85197K(253440K), 0.0008928 secs] [Times: user=0.00 sys=0.00, real=0.00 secs]

step 2
2020-07-08T12:36:58.050+0800: 6.639: [CMS-concurrent-mark-start]
2020-07-08T12:36:58.051+0800: 6.640: [CMS-concurrent-mark: 0.001/0.001 secs] [Times: user=0.01 sys=0.00, real=0.01 secs]

step 3
2020-07-08T12:36:58.051+0800: 6.640: [CMS-concurrent-preclean-start]
2020-07-08T12:36:58.051+0800: 6.640: [CMS-concurrent-preclean: 0.000/0.000 secs] [Times: user=0.00 sys=0.00, real=0.00 secs]

step 4
2020-07-08T12:36:58.052+0800: 6.640: [GC (CMS Final Remark) [YG occupancy: 10102 K (78656 K)]
2020-07-08T12:36:58.052+0800: 6.640: [Rescan (parallel) , 0.0012227 secs]2020-07-08T12:36:58.053+0800: 6.642: [weak refs processing, 0.0000137 secs]2020-07-08T12:36:58.053+0800: 6.642: [class unloading, 0.0003208 secs]2020-07-08T12:36:58.053+0800: 6.642: [scrub symbol table, 0.0004448 secs]2020-07-08T12:36:58.054+0800: 6.643: [scrub string table, 0.0775358 secs][1 CMS-remark: 75094K(174784K)] 85197K(253440K), 0.0796394 secs] [Times: user=0.08 sys=0.00, real=0.08 secs]

step 5 
2020-07-08T12:36:58.131+0800: 6.720: [CMS-concurrent-sweep-start]
2020-07-08T12:36:58.174+0800: 6.763: [CMS-concurrent-sweep: 0.043/0.043 secs] [Times: user=0.09 sys=0.00, real=0.04 secs]

step 6
2020-07-08T12:36:58.174+0800: 6.763: [CMS-concurrent-reset-start]
2020-07-08T12:36:58.175+0800: 6.764: [CMS-concurrent-reset: 0.000/0.000 secs] [Times: user=0.00 sys=0.00, real=0.00 secs]
```





- Write Barrier
https://blog.csdn.net/nazeniwaresakini/article/details/105947623

就是对一个对象引用进行写操作 之前或之后 附加执行的逻辑 ； 相当于是一个钩子代码

