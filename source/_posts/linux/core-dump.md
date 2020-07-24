---
title: linux-core-dump
date: 2020-07-22 20:36:43
tags: linux
---

关于 计算机的内存说明 pmap


pmap -x pid
输出

Address           Kbytes     RSS   Dirty Mode  Mapping
0000000000400000       4       4       0 r-x-- java
0000000000600000       4       4       4 r---- java
0000000000601000       4       4       4 rw--- java
00000000009c0000 1400692 1400496 1400496 rw---   [ anon ]
00007f2f84000000   38316   38316   38316 rw---   [ anon ]
00007f2f8656b000   27220       0       0 -----   [ anon ]
00007f2f94000000   58752   58752   58752 rw---   [ anon ]
00007f2f97960000    6784       0       0 -----   [ anon ]
00007f2fb0000000   27388   27388   27388 rw---   [ anon ]
00007f2fb1abf000   38148       0       0 -----   [ anon ]
00007f2fd0000000   58752   58752   58752 rw---   [ anon ]
00007f2fd3960000    6784       0       0 -----   [ anon ]
00007f2fe4000000   37800   37800   37800 rw---   [ anon ]
00007f2fe64ea000   27736       0       0 -----   [ anon ]
00007f2ff0000000   54556   54556   54556 rw---   [ anon ]
00007f2ff3547000   10980       0       0 -----   [ anon ]
00007f300c000000   50688   50688   50688 rw---   [ anon ]
00007f300f180000   14848       0       0 -----   [ anon ]
00007f3010000000   50424   50424   50424 rw---   [ anon ]
00007f301313e000   15112       0       0 -----   [ anon ]
00007f3028000000   62912   62912   62912 rw---   [ anon ]
00007f302bd70000    2624       0       0 -----   [ anon ]
00007f3034000000   61508   61508   61508 rw---   [ anon ]
00007f3037c11000    4028       0       0 -----   [ anon ]
00007f3038000000   62924   62924   62924 rw---   [ anon ]
00007f303bd73000    2612       0       0 -----   [ anon ]



含义 https://techtalk.intersec.com/2013/07/memory-part-2-understanding-process-memory/

dirty - . Dirty pages are pages that have been written to, but have not been synced back to the underlying file yet. As a consequence, the amount of dirty pages is only meaningful for mappings with write-back, that is shared file-backed mappings 





- perf

基于事件采样原理，可以对cpu与os的性能进行分析

第一次使用 
首先开启内核标记

sysctl -w kernel.kptr_restrict=1