# netty-definitive-guide

> Netty权威指南 第2版
>李林峰/著

## 查看Linux句柄数
```sh
> cat /proc/sys/fs/file- max
```
## 2.I/O效率不会随着FD数目的增加而线性下降。

传统的select/poll另一个致命弱点就是当你拥有一个很大的socket集合，由于网络延时或者链路空闲，任一时刻只有少部分的socket是“活跃”的，但是select/poll每次调用都会线性扫描全部的集合，导致效率呈现线性下降。epoll不存在这个问题，它只会对“活跃”的socket进行操作-这是因为在内核实现中epoll是根据每个fd上面的callback函数实现的，那么，只有“活跃”的socket才会主动的去调用callback函数，其他idle状态socket则不会。在这点上，epoll实现了一个伪AIO。针对epoll和select性能对比的benchmark测试表明：如果所有的socket都处于活跃态-例如一个高速LAN环境，epoll并不比select/poll效率高太多；相反，如果过多使用epoll_ctl，效率相比还有稍微的下降。但是一旦使用idle connections模拟WAN环境，epoll的效率就远在select/poll之上了。
