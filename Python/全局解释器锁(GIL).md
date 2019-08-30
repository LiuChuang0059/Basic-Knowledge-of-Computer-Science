本文为 极客时间 景霄老师 的《 python 核心技术与实战》课程 学习笔记

----


## 1. 问题

Countdown 一个 数字


1. Basic 版本

```python

def count_down(n):
    while n > 0 :
        n -= 1

n = 100000000
count_down(n)

#########结果 ######


executed in 4.80s,

```


2. 双线程版本

```python
from threading import Thread

t1 = Thread(target= count_down, args= [n//2])
t2 = Thread(target= count_down, args= [n//2])
t1.start()
t2.start()
t1.join()
t2.join()

#########结果 ######

executed in 4.87s,

```

3. 4 线程

```python

from threading import Thread

t1 = Thread(target= count_down, args= [n//4])
t2 = Thread(target= count_down, args= [n//4])
t3 = Thread(target= count_down, args= [n//4])
t4 = Thread(target= count_down, args= [n//4])
t1.start()
t2.start()
t3.start()
t4.start()
t1.join()
t2.join()
t3.join()
t4.join()

#########结果 ######
executed in 4.93s

```

* 多线程没有提升运行速度


------

## 2. 为什么引入 GIL

1. 每一个 Python 线程， 在 CPython解释器中执行的时候，都会锁住自己的线程，阻止别的线程执行。


2. Cpython 使用计数来管理内存， Python 中创建的实例 的都会有一个引用计数，来记录由多少个指针指向实例，当引用计数为 0 的时候，自动释放内存。

```python
>>> import sys
>>> a = []
>>> b = a
>>> sys.getrefcount(a)
3
>>>
```

> In the above example, the reference count for the empty list object [] was 3. The list object was referenced by a, b and the argument passed to sys.getrefcount().
 空列表 [] 的引用计数是3 ，被  a, b 和 作为参数 传递给函数。 如果有两个 Python 线程同时引用了 a， 会造成引用计数的 race condition，引用计数最终可能只增加 1， 当第一个线程结束的时候， 引用计数减少1， 可能导致内存释放的条件， 第二个线程访问 a 的时候， 找不到有效的饿内存

3. Cpython 引入 GIL

* 避免 race condition

* CPython 使用很多 C语言库， 但是大部分 C语言库 不是原生线程安全的




---


## 3. GIL 工作原理


![](https://i.loli.net/2019/07/18/5d301edebbdfe41692.jpg)


1. 每一个线程开始的时候，都会锁住 GIL阻止别的线程执行， 每一个线程执行完，都会释放 GIL，允许别的线程开始使用资源

> 从release GIL到acquire GIL之间几乎是没有间隙的。所以当其他在其他核心上的线程被唤醒时，大部分情况下主线程已经又再一次获取到GIL了。这个时候被唤醒执行的线程只能白白的浪费CPU时间，看着另一个线程拿着GIL欢快的执行着。然后达到切换时间后进入待调度状态，再被唤醒，再等待，以此往复恶性循环



![](https://i.loli.net/2019/07/18/5d302362a11c712910.png)




上图 是 CPU 密集型运算，
绿色表示线程正在执行， 红色表示线程被调度唤醒，但是由于没有 GIL 无法执行， 白色表示 IO线程处于等待
GIL的存在导致多线程无法很好的立即多核CPU的并发处理能力。


2. 使用 check_ interval 机制释放 GIL： 每隔一段时间 Python 解释器会强制当前的线程去释放 GIL。

3. GIL 在遇到 IO 的时候 自动释放， IO Bound场景下得多线程会得到较好的性能
----

## 4. Python 的线程安全

* 虽然 GIL仅允许一个线程运行，但是还有 check_interval 强行抢占线程机制。

* GIL的设计，主要是为了 Cpython 解释器层面的编写者，而不是 Python 应用者

* 还是需要 lock 等工具 保证线程安全


---

## 5. 如何避免 GIL

1. 绕过 Cpython 解释器，（使用其他的。javapython） 目前已经有很多的 Python 库是 C 实现的， 不受 GIL 影响

2. 关键性能代码， 使用 C++ 实现

3. 使用 Multi-processing

```python

from multiprocessing import Pool
import time

if __name__ == '__main__':
    pool = Pool(processes=2)
    r1 = pool.apply_async(count_down, [n//2])
    r2 = pool.apply_async(count_down, [n//2])
    pool.close()
    pool.join()

###### 结果 ######

executed in 2.63s,

```

运行时间 减少了 近乎一半，因为调用多进程也需要大量的消耗

# 参考

1. http://cenalulu.github.io/python/gil-in-python/

2. https://juejin.im/post/5ce1707df265da1ba431be19

3. What is the Python Global Interpreter Lock (GIL)? --https://realpython.com/python-gil/

4. https://www.youtube.com/watch?v=Obt-vMVdM8s&feature=youtu.be














