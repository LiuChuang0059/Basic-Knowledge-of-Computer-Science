
* 本文主要内容为**刘超**老师在极客时间上的《趣谈网络协议》课程学习笔记

---

* 多台交换机连接会形成一个拓扑结构


###  两台交换机 -- 广播
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/hkhru.jpg)

* 机器1想要知道 机器4的 MAC 地址
> 	机器 1 进行广播，交换机 A也收到广播，但是不知道任何的拓扑信息，所以除了广播包来的方向外，转发给其他的所有的网口，机器3接收到广播消息，交换机B接收到广播信息，将包转发的局域网3， 机器4和机器5 收到广播信息，机器4主动响应，提供 MAC 地址。

> 这个过程 交换机 A 和 交换机B 记住机器1 的网口。当接下来机器3 访问机器1 的时候，广播 ARP的请求， A收到请求后知道机器1 的位置，将消息广播到局域网一，交换机B接收到广播消息后，知道不在，所以不会广播。

----


### 交换机 -- 环路问题
> 两台交换机将两个局域网同时连接起来

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/4abqv.jpg)

* 对于机器1访问机器2 的过程
> 两个交换机都能接收到广播消息，交换机A不知道机器2在哪个局域网，将广播消息放到局域网2 ，交换机B 从局域网2 得到消息传到局域网1 ，从左边传入A，形成循环。

* 环形结构 学习不到 拓扑结构。


----


### STP 协议基本概念 --- Spanning Tree Protocol

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/w64le.jpg)

* Root Bridge ： 根交换机

* Designated Bridges ： 指定交换机；树的树枝，其他的交换机通过他到达根交换机

* Bridge Protocol Data Units （BPDU） 网桥协议数据单元，只有 根交换机可以发，其他的交换机发自己上层的交换机的协议

* Priority Vector，优先级向量； [Root Bridge ID, Root Path Cost,Bridge ID ,Port ID]
> 先比较 跟交换机 ID， 如果 ID 一样，比较 和根交换机的距离。比较自身的 ID。


-----

### STP 工作过程

#### 1. 初始

* 初始的时候，所有的交换机都是 RootBridge， 每个交换机有不同的分配的优先级

* 每个 网桥也有不同的ID 不同的优先级

* 都是 RootBridge，互相会发送BPDU ，赢得是 RB 输的是 DB。 输了的 DB 智能 转发 RB 的BPDU

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/qjndz.jpg)

> 形成 5-6， 1-7， 2-8 ，3-4 这样的的一个个小部分


#### 2. 合并

* 当 5 碰到了 1 ， 都是RB， 1的优先级高， 1567合并

*  1 和 6 相遇， 1567 已经合并， 原本就是 一支，但是 6 距离1 是2  ，而 从5的那一路走是 4+ 1 =5 所以5，6 不在分上下

* 当 5，6 再相遇， 5 发现从6 走的距离是3 ，所以6 称为5 的上支

* 2 和7 相遇， 2 比7 强，但是 7 的上层1 比1 强 ，所以 2 成为 1的分支

* 5 和 4 相遇， 5 的上层是1 ， 4 的上层是3。，4 要加入5

* 最终生成一棵树



#### 3. 广播问题和安全问题

* 在同一个广播域里面，很多包都会在一个局域网里面传播， 很容易泄露



#### 4. 虚拟隔离 --- VLAN。虚拟局域网

* 一个交换机会连接属于多个局域网的机器， 通过 Tag 的 VLAN ID 区分 哪个机器属于哪个局域网

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/8m9b2.jpg)


* 只有 相同 VLAN的包 才会互相转发，不同 VLAN的包互相看不到

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/xxw8d.jpg)

> 例如程序员的口 VLAN10 ， 财务 VLAN 30

> 交换机之间的 连接使用 TRUNK 口， 转发属于任何 VLAN的口






















































