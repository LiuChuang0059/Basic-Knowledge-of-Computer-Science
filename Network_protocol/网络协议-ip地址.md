* 本文主要内容为**刘超**老师在极客时间上的《趣谈网络协议》课程学习笔记

---


### 1. 查询 IP 地址

* IP 地址是一个网卡在网络世界的通讯地址，相当于门牌号码，每个人应该都不一样。
> 有时候电脑弹出网络地址冲突，上不去网，多半是 IP地址冲突

```python
$ ifconfig # linux
$ ip addr # linux

$ ipconfig # windows

```

### 2.  运行 ifconfig,输出
```
lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> mtu 16384
	options=1203<RXCSUM,TXCSUM,TXSTATUS,SW_TIMESTAMP>
	inet 127.0.0.1 netmask 0xff000000
	inet6 ::1 prefixlen 128
	inet6 fe80::1%lo0 prefixlen 64 scopeid 0x1
	nd6 options=201<PERFORMNUD,DAD>
gif0: flags=8010<POINTOPOINT,MULTICAST> mtu 1280
stf0: flags=0<> mtu 1280
XHC20: flags=0<> mtu 0
XHC0: flags=0<> mtu 0


en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
	ether 88:e9:fe:4f:1a:da
	inet6 fe80::1847:2eb3:3b44:bc47%en0 prefixlen 64 secured scopeid 0x6
	inet 172.20.10.4 netmask 0xfffffff0 broadcast 172.20.10.15
	inet6 2409:8915:1c12:334a:8dc:f8ec:6219:1cdc prefixlen 64 autoconf secured
	inet6 2409:8915:1c12:334a:e0fb:3a4:2a22:57e0 prefixlen 64 autoconf temporary
	nd6 options=201<PERFORMNUD,DAD>
	media: autoselect
	status: active

```

* lo 全称是loopback，又称环回接口，往往会被分配到127.0.0.1 这个地址。这个地址用于本机通信，经过内核处理后直接返回，不会在任何网络中出现。


* 第一行：  en0 表示第一块网卡） UP (代表网络开启),BROADCAST （网卡有广播地址，可以发送广播包）,SMART,RUNNING（网卡的网线被接上）,SIMPLEX,MULTICAST（支持组播）mtu 1500（最大传输单元： 1500字节）

* 第二行：  ether（网络连接类型是 以太网连接） 88:e9:fe:4f:1a:da （mac地址）

* 第三行 ：ipv6 的网络地址 ： fe80::1847:2eb3:3b44:bc47 有128位

* 第四行： **IP 地址** 172.20.10.4 地址有四个部分，每部分8个 bit，总共 32 位 ； netmask（掩码地址）； broadcast（广播地址）

### 3.  ip 地址的分类


![](https://github.com/LiuChuang0059/large_file/blob/master/pic/smmb4.jpg)

* 主要分为两部分 ： 网络号 + 主机号


![](https://github.com/LiuChuang0059/large_file/blob/master/pic/i4r6j.jpg)


* 对于 A 类： IP 地址范围 ： 地址的第一个部分为网络号 1位固定为 0 ；7位可选， 2的7次方 共 128个即 0--127，剩余三部分，每部分8位 256个，0:255

* 对于 B 类： 网络号，有 6+ 8 位 即： 64 ，256 对应前面两部分 128-191 和 0 -- 255 ；主机号8 + 8 位

* 对于 C 类： 网络号 5+ 8+ 8。即： 32 + 256 + 256 ；主机号 8 位 对应 256 个主机

> C 类地址包含主机数目太少， B类地址包含主机数目太多， 需 6 万多台机器，会有很多闲置地址


### 4. 无类型域间选路（CIDR）

* 将32 位的 IP 地址 一分为2 前面是网络号，后面是主机号
> 例如 ：10.100.122.2/24。斜杠后面的 24 表示 前24 位 是网络号，后8位是主机号

* 伴随 CIDR 存在的有 广播地址 172.20.10.15，发送这个地址，所有172.20.10 网络里面的机器都可以收到

* 子网掩码地址 ：0xfffffff0 对应于 255.255.255.240

> /24    255.255.255.0      0xffffff00    11111111 11111111 11111111 00000000
> /28    255.255.255.240    0xfffffff0    11111111 11111111 11111111 11110000

* 网络号 ； 子网掩码和 IP地址安位计算 AND  ： 得到 172.20.10.0 就是网络号


**16.158.165.91/22 这个 CIDR  22不是8 的整数倍，，需要转换为 二进制求解**


### 5. 公有 IP 和 私有 IP


![](https://github.com/LiuChuang0059/large_file/blob/master/pic/i4r6j.jpg)


* 家庭 学校，等 一般都是私有 IP  ，私有 IP 允许 重复
> 小区有自己的楼编号和门牌号，你们小区可以叫 6 栋，我们小区也可以叫6栋

* 局域使用，例如学校，私有 IP 即可； 公有 ip 全世界访问，需要购买

* 192.168.0.x 是最常有的 私有 IP 地址
> 192.168.0 是网络号，后面是主机号。而整个网络里面的的第一个地址 192.168.0.1，往往就是私有网络的出口地址
> Wi-Fi 路由器的地址就是。192.168.0.1 ，而 192.168.0.255 就是广播地址，放松这个地址，网络里面所有的机器都能收到


### 6. MAC 地址

>  ether 88:e9:fe:4f:1a:da

* 网卡的物理地址， 16进制  6 个 byte

* MAC 地址 全局唯一，不会有两个网卡有相同的 MAC。网卡自生产出来就带着这个地址

* 但是 不能 仅使用 MAC 地址进行通信。 需要 IP  地址的 远程定位功能

> MAC 地址更像 身份证，IP 地址 像 居住或者所在的位置信息

* MAc 地址的通信范围 局限在一个 子网里面












# 参考


1. 每天一个linux命令（52）：ifconfig命令----https://www.cnblogs.com/peida/archive/2013/02/27/2934525.html

2. 极客时间: https://time.geekbang.org/column/article/7772

