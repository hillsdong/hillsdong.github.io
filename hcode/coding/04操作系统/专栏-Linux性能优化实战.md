## 作者
倪朋飞 微软Azure工程师 Kubernetes项目维护者

## 如何学习
- 基础/案例/套路/答疑
- 原理/指标/工具/调优

### 性能指标
- 应用负载视角：吞吐/延迟 ，描述如高并发/响应快
- 系统资源视角：CPU/内存...

### 如何找出应用或系统的瓶颈
- 选择指标/设置指标/基准测试/定位瓶颈/优化/监控告警

### 重点
- 建立整体系统性能的全局观，原理/工具/演练
![Linux性能工具](http://cdn1.klib.cn/u/hp8aDu.png)
![专栏导图](http://cdn1.klib.cn/u/AUXvXq.png)

### 技巧
- 不扣细节/边学边实践/多思考


## CPU性能

### CPU平均负载
- 平均负载：单位时间内，系统处于可运行状态和不可中断状态的平均进程数，即平均活跃进程数
- 可运行状态：正在使用 CPU 或正在等待 CPU 的进程，PS 命令处于 R 状态的进程
- 不可中断状态：正处于内核关键流程中，不可打断，比如等待硬件设备 I/O，PS 中 D 状态
- CPU 负载高，使用率不一定高，负载包括等待 CPU 的进程和不可打断状态的进程
- stress 压力测试工具，sysstat 包是常用性能工具，包含 mpstat 和 pidstat 
- mpstat 多核 CPU 性能分析工具，pidstat 进程形成分析工具
- uptime/top 查看平均负载
- stress 模拟 CPU 密集/ I/O密集 /大量进程

### CPU上下文切换
- CPU 上下文：CPU 寄存器和程序计数器
- 上下文切换场景：进程上下文切换/线程上下文切换/中断上下文切换
- 系统调用是特权模式切换，不是上下文切换，由用户态到内核态，再由内核态到用户态
- 进程调度场景：时间片耗尽/系统资源不足/进程主动挂起/优先级更高的进程运行/硬件中断
- 查看上下文切换工具：vmstat/pidstat -wt(sysstat)
- 模拟多线程调度切换工具：sysbench
- watch 监测变化工具

### CPU使用率
- top/ps/pidstat
- perf top/record/report(linux-tools-common)
- ab (apache2-utils)
- pstree 查看进程父子关系 /execsnoop 监控短时进程

### CPU中断
- 中断是一种异步的事件处理机制，可以提高系统的并发处理能力
- 硬中断：硬件触发，CPU即时处理
- 软中断：中断处理的下半部/内核触发，内核线程处理

### 案例：僵尸进程
- 进程状态 Running/DiskSleep/Zombie/Sleep/Idle / T(Traced/Stopped)/X(Dead)
- dstat/strace/perf

### 案例：软中断频繁
- sar
- watch -d cat /proc/softirqs

### 套路
- 性能指标：CPU利用率/平均负载/上下文切换/缓存命中率
- 性能工具：top/vmstat/pidstat...
- 优化思路：应用程序（编译器优化/算法优化/异步处理/多线程代替多进程/善用缓存）
- 优化思路：系统（CPU绑定/CPU独占/优先级调整/设置资源限制/NUMA优化/中断负载均衡）

### 答疑



## 内存性能

### 工作原理
- cat /proc/meminfo / free / top / ps / vmstat
- free -h / free --help / man free / man proc 

### Buffer和Cache
- sysstat 软件包 apt-get install -y sysstat
- sysstat 包含 vmstat
```
$1 vmstat 1
# 清理缓存
$2 echo 3 > /proc/sys/vm/drop_caches
# 运行dd命令读/写文件/磁盘
$2 dd if=/dev/urandom of=/tmp/file bs=1M count=500
$2 dd if=/dev/urandom of=/dev/sdf1 bs=1M count=2048
$2 dd if=/tmp/file of=/dev/null bs=1M count=500
$2 dd if=/dev/sda1 of=/dev/null bs=1M count=1024
```
- /proc/<pid>/smaps
- awk '/Pss:/{ sum += $2 } END { print sum }' /proc/<pid>/smaps

### 案例：优化程序运行效率
- 安装 bcc 软件包，基于 eBPF（extended Berkeley Filters）机制，内核版本需大于 4.1
- bcc 包含 cachestat / cachetop
```
$ sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4052245BD4284CDD
$ echo "deb https://repo.iovisor.org/apt/xenial xenial main" | sudo tee /etc/$ apt/sources.list.d/iovisor.list
$ sudo apt-get update
$ sudo apt-get install -y bcc-tools libbcc-examples linux-headers-$(uname -r)
$ export PATH=$PATH:/usr/share/bcc/tools
```
- pcstat
```
go get github.com/tobert/pcstat/pcstat
```
- 测试
```
$2 cachetop 5
$1 dd if=/dev/urandom of=file bs=1M count=512
$1 pcstat file
$1 echo 3 > /proc/sys/vm/drop_caches
$1 pcstat file
$1 dd if=file of=/dev/null bs=1M
$1 pcstat file
$1 dd if=file of=/dev/null bs=1M
```
- strace
- 直接IO是跳过Buffer，裸IO是跳过文件系统（还是有buffer的）

### 案例：内存泄漏
- /usr/share/bcc/tools/memleak -a -p $(pidof app)
- valgrind

### 案例：Swap变高
- numactl --hardware， 如何安装？
- /proc/zoneinfo 查看内存阈值
- /proc/sys/vm/min_free_kbytes 内存回收阈值
- /proc/sys/vm/zone_reclaim_mode 内存回收模式
- /proc/sys/vm/swappiness swap使用的积极程度

- sar -r -S 1 间隔 1s 输出内存和 Swap 使用情况

```
# 创建Swap文件
$ fallocate -l 8G /mnt/swapfile
# 修改权限只有根用户可以访问
$ chmod 600 /mnt/swapfile
# 配置Swap文件
$ mkswap /mnt/swapfile
# 开启Swap
$ swapon /mnt/swapfile
# 关闭开启Swap
$ swapoff -a && swapon -a
```

- watch grep  /proc/zoneinfo
```
# -d 表示高亮变化的字段# -A 表示仅显示Normal行以及之后的15行输出
$ watch -d grep -A 15 'Normal' /proc/zoneinfo
```

- for awk sort head /proc/*/status
```
# 按VmSwap使用量对进程排序，输出进程名称、进程ID以及SWAP用量
$ for file in /proc/*/status ; do awk '/VmSwap|Name|^Pid/{printf $2 " " $3}END{ print ""}' $file; done | sort -k 3 -n -r | head
```

### 套路
- 全局 free/top/vmstat/pidstat/ps/sar
- 缓存 pcstat/cachetop/cachestat/slabtop
- 分配 memleak/valgrind/pmap/slabtop/ /proc/buddyinfo
- 进程 pidstat/top/pmap/ /proc/pid/state / /proc/pid/smaps

### 答疑


## I/O性能

### 文件系统
- 一切皆文件，如块设备/套接字/管道
- 
- 每个文件拥有两种数据结构：索引节点（index node）和目录项（directory entry）
- 索引节点记录inode编号/文件大小/访问权限/修改日期/数据位置等数据，持久化到磁盘
- 目录项记录文件名字/索引节点指针/与其他目录项关联等，不同于索引节点，目录项是内存缓存
- 一个文件可以有多个目录项，但只有一个索引节点
- 磁盘最小读写单位是扇区，512B 大小，连续8个扇区组成 4k 大小的逻辑块
- 文件系统分为超级块/索引节点区/数据块区三个存储区域，超级块存储整个文件系统的状态
- linux文件系统四大要素：目录项/超级块/索引节点/逻辑块
- 
- VFS（Virtual File System）虚拟文件系统，是用户层和文件系统中间引入的抽象层
- 
- 基于磁盘的文件系统，如 Ex4/XFS/OverlayFS
- 基于内存的文件系统，如 /proc /sys
- 基于网络的文件系统，如 SMB/NFS/iSCSI
- I/O链：标准库-系统调用-文件系统-磁盘
- 缓冲I/O与非缓冲I/O，区别于是否利用标准库缓冲
- 直接I/O与非直接I/O，区别于是否利用操作系统页缓存，O_DIRECT标志
- 裸I/O，直接读写磁盘，跳过文件系统
- 堵塞I/O与非堵塞I/O，区别于I/O操作是否堵塞当前线程执行，O_NONBLOCK标志，默认堵塞
- 同步I/O与异步I/O，区别于是否等待响应结果，O_SYNC/O_DSYNC标志同步I/O
- 
- df -h /dev/sda1 数据块区使用情况
- df -h -i /dev/sda1 索引节点区使用情况
- free/vmstat 观察页缓存大小，free 输出的 cache，是页缓存和 Slab 缓存的和
- 操作系统通过 Slab 机制，管理目录项和索引节点缓存
- slabtop 查看 dentry / inode_cache 缓存占用
- cat /proc/slabinfo | grep -E '^#|dentry|inode'
- 
- find / -name file-name 会导致 slab 缓存升高

### 磁盘I/O
- 机械磁盘 Hard Disk Driver HDD，连续I/O与随机I/O差距大，最小单位是扇区，512B
- 固态磁盘 Solid State Disk SSD，最小单位是页，4KB/8KB
- 
- IDE Integrated Drive Eletronics，hd前缀
- SCSI Small Computer System Interface，sd前缀
- SAS Serial Attached SCSI
- SATA Serail ATA，sd前缀
- FC Fibre Channel
- 
- 逻辑分区
- RAID Redundant Array of Independent Disks 0/1/5/10
- 网络集群存储，通过NFS/SMB/iSCSI协议暴露
- 
- 磁盘作为块设备管理，以块为单位读写，支持随机读写，每个块设备有主次设备号，如 sd a
- 通用块层，管理块设备，向上提供标准接口，向下抽象异构设备，通过重新排序等方式提高效率
- 
- I/O调度算法：NONE/NOOP/CFQ/DeadLine
- NONE，不作处理
- NOOP，先入先出，基本请求合并
- CFQ Completely Fair Scheduler，完全公平算法，默认I/O调度器，平均进程I/O请求
- DeadLine，读写队列分离，提高吞吐量，适合I/O压力重的场景，如数据库
- 
- I/O栈：文件系统层-通用块层-设备层
- 优化性能：页缓存/索引节点缓存/目录项缓存
- 
- 性能指标：使用率/饱和度/IOPS/吞吐率/响应时间
- 性能测试：不同I/O大小（512B-1MB）分别在随机读/顺序读/随机写/顺序写下的性能情况,fio

### 案例：狂打日志
- iostat -d -x 1，来源 /proc/diskstats
- 进程I/O观测 pidstat -d 1/iotop
- strace 跟踪进程
- lsof -p  进程打开的文件列表

### 案例：I/O延迟高
- bcc工具包中的filetop/opensnoop
- strace -f -p xxx 跟踪所有线程

### 案例：慢SQL

### 案例：Redis延迟高
- nsenter

### 套路
- 文件系统I/O性能指标：存储空间使用情况/缓存使用情况/IOPS/响应时间/吞吐量
- 磁盘I/O性能指标：使用率/饱和度/IOPS/吞吐率/响应时间
- 性能工具：df/slabtop/top/iostat/pidstat/strace/lsof/filetop/opensnoop
- 
- 基准测试 fio
- 应用优化/系统优化/磁盘优化

### 答疑
- 应用程序视角：堵塞/非堵塞IO
- 系统视角：异步/同步IO


## 网络性能

### 收发流程

### 性能指标
- 带宽 bit/s
- 吞吐量 bit/s 或 B/s
- 延时 握手延时或RTT
- PPS Pacekt Per Second
- 可用性
- 并发连接数（TCP连接数）
- 丢包率
- 重传率

### 性能工具
- 网络配置 ifconfig(net-tools)/ip(iproute2)
- 套接字信息 netstat/ss
- 网络吞吐和PPS netsate/ 
- 连通性和延时 ping

### C10K
- select/poll 非堵塞I/O和水平触发通知，轮询
- epoll 非堵塞I/O和边缘触发通知，红黑树/事件驱动
- 异步I/O
- 工作模型：主进程+多个worker子进程/监听到相同端口的多进程模型

### 性能测试
- 转发性能 pktgen
- TCP/UDP性能 iperf3/netperf
- HTTP性能 ab/webbench
- 应用负载性能 wrk/TCPCopy/Jmeter/LoadRunner

### 案例
- DNS分析 host/nslookup/dig
- 流量分析 tcpdump/wireshark
- DDOS
- 网络延迟 ping/traceroute/hping3 / tcpdump/wireshark/strace
- NAT原理 iptables SNAT DNAT
- NAT丢包 systemtap/perf/dmesg/journalctl/conntrack

### 套路
- 整体目标：降低网络延迟，如RTT，提高吞吐量，如BPS/PPS
- 应用程序层：主要优化 I/O 模型，工作模型以及应用层的网络协议
- 套接字层：主要优化套接字的缓冲区大小
- 传输层：优化各TCP/UDP系统参数
- 网络层：优化路由/转发/MTU/ICMP等
- 链路层：优化包收发/网络功能卸载及网卡选项

### 答疑
- 缓存：环形缓冲区/sk_buff缓冲区/套接字缓冲区，都在内存
- 网络收发的软中断处理，由ksoftirqd内核线程负责


## 综合实战

## 其他


