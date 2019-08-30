本文为 极客时间 景霄老师 的《 python 核心技术与实战》课程 学习笔记

----

## 1. 图灵机架构

1. 图灵机架构的本质是无限长的纸袋， 相当于 存储器， 之后HU年间完善出现 寄存器， 易失性存储器（内存） 和 永久性储存器（硬盘） ，

2. Python 程序运行的时候， 需要 内存开辟空间，用于运行时候产生的临时变量， 计算完成， 再将结果储存到磁盘。

3. 数据量过大， 内存可能不足， 内存管理不好，导致内存泄漏

* 内存泄漏指的是 程序未能释放已经不再使用的内存

* 分配了某段内存之后，因为设计失误，失去了对内存的控制，造成内存的浪费


---

## 2. 计数引用

Python 中创建的实例 的都会有一个引用计数，来记录由多少个指针指向实例，当引用计数为 0 的时候，自动释放内存。


1. 垃圾回收

```python

import os
import psutil


def show_memory(hint):
    pid = os.getpid()
    p = psutil.Process(pid)

    info = p.memory_full_info()
    memory = info.uss/1024./1024
    print("{} memory used : {} MB ".format(hint,memory))


def func():
    show_memory("initial")
    a = [i for i in range(10000000)]
    show_memory("after processing")

func()
show_memory("finished")

############

initial memory used : 78.33984375 MB
after processing memory used : 465.3359375 MB
finished memory used : 82.0703125 MB

```

* 函数内部声明的变量是 局部变量， 函数返回之后， 局部变量的引用会被注销掉， 声明的列表就会被执行垃圾回收， 收回占用的内存


2.


```python

import os
import psutil


def show_memory(hint):
    pid = os.getpid()
    p = psutil.Process(pid)

    info = p.memory_full_info()
    memory = info.uss/1024./1024
    print("{} memory used : {} MB ".format(hint,memory))



def func():
    show_memory("initial")
    global a
    a = [i for i in range(10000000)]
    show_memory("after processing")


func()
show_memory("finished")


#########

initial memory used : 81.48828125 MB
after processing memory used : 453.5078125 MB
finished memory used : 453.51171875 MB


```

* 因为 a 声明的是全局变量， 函数返回之后， 列表的引用依然存在，对象不会被垃圾回收，依然占据大量的内存





3.

```python

def func():
    show_memory("initial")
    a = [i for i in range(10000000)]
    show_memory("after processing")
    return a

a = func()
show_memory("finished")

#############

initial memory used : 454.28125 MB
after processing memory used : 768.890625 MB
finished memory used : 768.890625 MB

```


4. 引用计数机制


* 函数调用的时候， 会产生两次引用， 一次来自函数栈 ，里一个来自函数参数

* 函数统计的是引用的次数， 而不是指针


---

## 3. 手动释放内存


接着上面的 内存占用 768

```python
del a

import gc
gc.collect()

show_memory("finished")

########

gc.collect() 返回 清除掉的垃圾变量的个数。

finished memory used : 78.22265625 MB

```

---

## 4. 循环引用

1. 如果两个对象互相引用， 没有别的对象引用， 是否应该被垃圾回收


```python

def func():
    show_memory("initial")
    a = [i for i in range(10000000)]
    b = [i for i in range(10000000)]
    show_memory("after created")
    a.append(b)
    b.append(a)

func()
show_memory("finished")

###########

initial memory used : 77.90234375 MB
after created memory used : 850.109375 MB
finished memory used : 850.109375 MB

```

我们可以发现， 因为互相引用， 导致 a,b 引用数都不为 0

重复调用 函数的话，内存占用会越来越大，

```
initial memory used : 760.92578125 MB
after created memory used : 1025.984375 MB
finished memory used : 1025.984375 MB

```


2. 显示调用 gc.collect()

```python

gc.collect()

show_memory("finished")

######## 结果#########
184
finished memory used : 82.5234375 MB

```

**可以看到 垃圾回收机制对于循环引用，也可以很好的回收**



---

## 4. Python 垃圾回收机制

标记清除 和 分代收集

1. 标记清除：

对于一个 有向图，如果从一个节点出发进行遍历， 标记其经历过的节点， 没有被标记的节点，就是不可达节点。
这些节点的存在没有意义， 需要对其进行垃圾回收


2. 分代收集算法

Python 将所有的对象分为三代， 刚刚创立的对象是 0 代， 经过一次垃圾回收之后，依然存在的对象，便会从上一代移动的下一代。

每一代启动自动垃圾回收的阈值 都是可以单独制定的，

新生的对象更有可能被垃圾回收； 存活更久的对象更高的概率存活

---



## 5. 调试 内存泄漏

使用 objgraph --- 可视化引用关系


1. show_refs() : 生成引用关系图

```python
import objgraph

a = [1,2,3]

b = [4,5,6]

a.append(b)
b.append(a)


objgraph.show_refs([a])

```

![](https://i.loli.net/2019/07/18/5d3074f8403a741426.png)

直观的看到互相引用



























































