---
title: "panic and recover"
date: 2020-01-05T19:47:52+08:00
draft: false
---
### panic and recover

引发panic的情况
* 程序主动调用panic函数
* 程序产生运行时错误，由运行时检测并抛出

发生panic后，程序会从调用panic的函数位置或发生panic的地方立即返回，  
逐层向上执行函数的defer语句，然后逐层打印函数调用堆栈，  
直到被recover捕获或运行到最外层函数而退出。






https://blog.golang.org/defer-panic-and-recover

