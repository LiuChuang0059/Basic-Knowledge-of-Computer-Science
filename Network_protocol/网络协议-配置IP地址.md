
* 本文主要内容为**刘超**老师在极客时间上的《趣谈网络协议》课程学习笔记

---

### 配置 IP 地址

* Net - tools
```
$ sudo ifconfig eth1 10.0.0.1/24
$ sudo ifconfig eth1 up

```


### 动态主机配置协议 --- DHCP

> 数据中心的服务器， IP 一旦配置号，基本不会改变，相当于买房自己装修； DHCP 相当于租房， 暂时使用，用完退租。


####  DHCP Discover

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/lewx6.jpg)

* 一台机器加入一个新的网络，只有自己的 MAC 地址， 使用 IP 0.0.0.0 发送一个广播包 到目的 IP ： 255.255.255.255

* 广播包 封装了 UDP， UDP封装了 BOOTP （DHCP 是BOOTP 的增强版）

* 信任度 会。Boot request ，---- 我的 MAC是 。。。 我还没有 IP ， 租给我个 IP


#### DHCP Server

* 网络里面配置了 DHCP Server， 相当于 IP 的管理者， 能够知道有 “新人” 进来 --- MAC 的唯一性


#### DHCP Offer

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/pmcmu.jpg)

* 租借给新人一个 IP 地址

* 同时，为 新人保留这个 IP 地址， 从而不会提供给别人

* DHCP Server 使用 广播地址作为目的地址，因为 新人还没有自己的 IP

* 服务器 同时发送， 子网掩码， 网关， IP 地址租用期限 等信息



#### DHCP Request
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/6anl4.jpg)

* 如果有 多个 DHCP Server ，新人会收到 多个 IP 地址， 会选择一个，一般是最先到达的那个

* 之后向网络发送一个 DHCP Request 的广播数据包， 包括： MAC 地址， 接受的 IP 地址， 提供 IP 的 DHCP 服务器地址， 并告诉其他的 DHCP 服务器，撤销 提供的 IP 地址，以便提供给下一个

* 由于还没有 DHCP Server  的确认， IP地址 仍然不变

#### DCHO ACK

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/c5orq.jpg)

*  DHCP Server 接收到 request 后， 广播返回一个 ACK 的消息包， 包扩： IP 的配置信息等



#### IP 的 收回和续租

* 客户机在 租期 50% 的时候发送 DHCP Request ， 服务器将返回ACK 消息包， 包含新的租期以及其他的 TCP/IP 参数













