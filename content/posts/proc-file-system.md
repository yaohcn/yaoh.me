---
title: "The proc file system"
date: 2020-01-05T19:47:52+08:00
draft: false
---
linux下/proc目录是一个位于内存中的虚拟文件系统，不占用存储空间。   
用户和应用程序可以通过读取proc目录下的文件获取一些“运行时”信息，   
如系统内存、磁盘io、设备挂载信息和硬件配置信息等。  
关于proc目录下各个目录和文件的详细含义可参考[/proc](http://www.tldp.org/LDP/Linux-Filesystem-Hierarchy/html/proc.html)。  

### cpu 使用率
通过/proc/stat文件计算cpu使用率
> $ cat /proc/stat  
  cpu  1857314 3211 356507 31551216 80248 134314 52630 0 0 0  
  cpu0 196329 383 42897 3977884 9308 14784 12381 0 0 0  
  cpu1 256375 505 45263 3920256  9719 15274 7695 0 0 0  
  cpu2 260143 376 44832 3917212 10058 15987 6058 0 0 0  
  cpu3 231825 371 42633 3950562 10070 14658 5111 0 0 0  
  .....


详细计算方法见[Linux平台Cpu使用率的计算](http://www.blogjava.net/fjzag/articles/317773.html)。golang实现参考[gopsutil](https://github.com/shirou/gopsutil)

### 内存使用
通过读取/proc/meminfo计算内存使用，[参考](https://stackoverflow.com/questions/41224738/how-to-calculate-system-memory-usage-from-proc-meminfo-like-htop)。
> $ cat /proc/meminfo  
MemTotal:        8030092 kB  
MemFree:          537684 kB  
MemAvailable:    1954400 kB  
Buffers:          119264 kB  
Cached:          2162372 kB  

### 运行时间
> $ cat /proc/uptime  
43996.65 327179.11  

第一个值为系统启动到现在的时间（单位为秒）。  

### 网路收发速率  
由于网路使用率需要知道网络的实际带宽。使用网络收发速率表示网络使用更准确。  
间隔一定的时间两次读取/proc/net/dev文件即可计算收发速率。  
> $ cat /proc/net/dev  
Inter-|   Receive                                                |  Transmit  
 face |bytes    packets errs drop fifo frame compressed multicast|bytes    packets errs drop fifo colls carrier compressed  
wlp0s20f3: 2103829674 2125459    0    0    0     0          0         0 779170057 1223079    0    0    0     0       0          0  
    lo: 867056551  500712    0    0    0     0          0         0 867056551  500712    0    0    0     0       0          0   


注意：文件格式会随着时间的推移而不断演进，在不同的内核版本中增加新字段（极少情况下也会删除字段）。  
当这些文件由多个条目组成时，对其解析应当查找包含特殊字符串的匹配行记录，而非按照行号来处理文件。  

