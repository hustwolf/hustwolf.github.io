---
title: linux-process
date: 2020-07-22 20:36:43
tags: linux
---
http://arthurchiao.art/blog/how-does-ltrace-work-zh/


ltrace 用来跟踪进程调用库函数
strace 用来跟踪系统调用 or 信号产生


他们都是通过ptrace系统调用实现的

ptrace 系统调用非常强大，可以跟踪系统调用、重写运行中程序的内存、读取运行中程 序的寄存器等等。

strace 和 ltrace 都使用 PTRACE_SYSCALL 跟踪系统调用。两者的大致工作过程类 似：为被跟踪程序触发 SIGTRAP 信号，暂停执行，通知跟踪程序（strace 或 ltrace），然后跟踪程序被“唤醒”，分析被暂停的程序。

ltrace 还会通过 PTRACE_POKETEXT 重写程序内存，以便通过特殊指令中断程序的执行。


- VDSO



- usage

* 编写动态分析工具，如gdb,strace
* 反追踪，一个进程只能被一个进程追踪(注：一个进程能同时追踪多个进程)，若此进程已被追踪，其他基于ptrace的追踪器将无法再追踪此进程，更进一步可以实现子母进程双线执行动态解密代码等更高级的反分析技术
* 代码注入，往其他进程里注入代码。
* 本篇关注点–做沙箱，它也能监控系统调用，因此可限制tracee的系统调用。
