本文为 极客时间 景霄老师 的《 python 核心技术与实战》课程 学习笔记

----


## 1. 并发 concurrency 和 并行 parallesim

1. 并发 concurrency

同一个时刻只允许有一个操作存在， 但是 tread /task  之间会互相切换，直到 完成。

* 切换 thread 对应 python 并发形式 treading

> 操作系统知道每一个线程，自动在适当的时候进行线程切换



* 切换 task 对应 ： asyncio

> 主程序想要切换任务， 必须有命令切换

**通常用于 I/O 操作频繁的任务**


2. 并行

同一时刻， 允许多个操作同时进行 multi-processing
> 电脑是 6核处理器，运行程序的饿时候， 强制python开6个进程，同时进行。


**应用于 CPU heavy的任务 ： MapReduce，使用多个机器多个处理器**



----

## 2. 单线程和多线程


1. 下载网站的内容， 单线程实现


```python
import requests
import time

def download_one(url):
	resp = requests.get(url)
	print("Read {} from {}".format(resp,url))

def download_all(sites):
	for site in sites :
		download_one(site)

def main():
	sites = [
            'https://www.baidu.com/',
            'https://pypi.org/',
            'https://www.sina.com.cn/',
            'https://www.163.com/',
            'https://news.qq.com/',
            'http://www.ifeng.com/',
            'http://www.ce.cn/',
            'https://news.baidu.com/',
            'http://www.people.com.cn/',
            'http://www.ce.cn/',
            'https://news.163.com/',
            'http://news.sohu.com/'
            ]

    start_time = time.perf_counter()
    download_all(sites)
    end_time = time.perf_counter()
    print('Download {} sites in {} seconds'.format(len(sites), end_time - start_time))

if __name__ = '__main__':
	main()

```


爬取结果

```
Read 2443 from https://www.baidu.com/
Read 18818 from https://pypi.org/
Read 572160 from https://www.sina.com.cn/
Read 695359 from https://www.163.com/
Read 6216 from https://news.qq.com/
Read 251170 from http://www.ifeng.com/
Read 107852 from http://www.ce.cn/
Read 73977 from https://news.baidu.com/
Read 152458 from http://www.people.com.cn/
Read 107852 from http://www.ce.cn/
Read 208375 from https://news.163.com/
Read 175232 from http://news.sohu.com/
Download 12 sites in 2.802098234999903 seconds

```


2. 多线程实现

Futures 会将处于等待的操作，放到队列中， 结果即使是异常也能在操作完成后被获取


```python

import concurrent.futures
import requests
import threading
import time

def download_one(url):
    resp = requests.get(url)
    print('Read {} from {}'.format(len(resp.content), url))


def download_all(sites):
    with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
        executor.map(download_one, sites)

def main():
	sites = [
            'https://www.baidu.com/',
            'https://pypi.org/',
            'https://www.sina.com.cn/',
            'https://www.163.com/',
            'https://news.qq.com/',
            'http://www.ifeng.com/',
            'http://www.ce.cn/',
            'https://news.baidu.com/',
            'http://www.people.com.cn/',
            'http://www.ce.cn/',
            'https://news.163.com/',
            'http://news.sohu.com/'
            ]

    start_time = time.perf_counter()
    download_all(sites)
    end_time = time.perf_counter()
    print('Download {} sites in {} seconds'.format(len(sites), end_time - start_time))

if __name__ == '__main__':
    main()
```

爬取结果

```
Read 107901 from http://www.ce.cn/
Read 107901 from http://www.ce.cn/
Read 251208 from http://www.ifeng.com/
Read 2443 from https://www.baidu.com/
Read 175297 from http://news.sohu.com/
Read 152458 from http://www.people.com.cn/
Read 149 from https://news.baidu.com/
Read 572152 from https://www.sina.com.cn/
Read 6216 from https://news.qq.com/
Read 208376 from https://news.163.com/
Read 695344 from https://www.163.com/
Read 18898 from https://pypi.org/
Download 12 sites in 1.0387027110000417 seconds

```


```python

with concurrent.futures.ThreadPoolExecutor(max_workers=5) as executor:
        executor.map(download_one, sites)

```

创建一个线程池， 总共有 5个线程可以调用

 executor.map 将 sites 中的每一个元素 并发的调用函数 download_one()


* 线程不是越多越好， 线程的创建 维护 和删除 都有一定的开销


3. 并行方式

```python

import concurrent.futures
import requests
import threading
import time

def download_one(url):
    resp = requests.get(url)
    print('Read {} from {}'.format(len(resp.content), url))


def download_all(sites):
    with concurrent.futures.ProcessPoolExecutor() as executor:
        executor.map(download_one, sites)

def main():
    sites = [
            'https://www.baidu.com/',
            'https://pypi.org/',
            'https://www.sina.com.cn/',
            'https://www.163.com/',
            'https://news.qq.com/',
            'http://www.ifeng.com/',
            'http://www.ce.cn/',
            'https://news.baidu.com/',
            'http://www.people.com.cn/',
            'http://www.ce.cn/',
            'https://news.163.com/',
            'http://news.sohu.com/'
            ]

    start_time = time.perf_counter()
    download_all(sites)
    end_time = time.perf_counter()
    print('Download {} sites in {} seconds'.format(len(sites), end_time - start_time))

if __name__ == '__main__':
    main()

```

运行结果


```
Read 2443 from https://www.baidu.com/
Read 6216 from https://news.qq.com/
Read 18920 from https://pypi.org/
Read 251226 from http://www.ifeng.com/
Read 572237 from https://www.sina.com.cn/
Read 695100 from https://www.163.com/
Read 107959 from http://www.ce.cn/
Read 107959 from http://www.ce.cn/
Read 152458 from http://www.people.com.cn/
Read 73977 from https://news.baidu.com/
Read 175322 from http://news.sohu.com/
Read 208376 from https://news.163.com/
Download 12 sites in 1.6489576409999245 seconds

```

with concurrent.futures.ProcessPoolExecutor() as executor:

括号中缺省值是 cpu 数量 作为可以调用的进程数

----


## 3. Futures 模块实现

```python

import concurrent.futures
import requests
import threading
import time




def download_one(url):
    resp = requests.get(url)
    print('Read {} from {}'.format(len(resp.content), url))


def download_all(sites):
	with concurrent.futures.ThreadPoolExecutor(max_workers = 3) as executor:
		to_do = []
		for site in sites:
			future = executor.submit(download_one,site)
			to_do.append(future)

		for future in concurrent.future.as_completed(to_do):
			future.result()
def main():
    sites = [
            'https://www.baidu.com/',
            'https://pypi.org/',
            'https://www.sina.com.cn/',
            'https://www.163.com/',
            'https://news.qq.com/',
            'http://www.ifeng.com/',
            'http://www.ce.cn/',
            'https://news.baidu.com/',
            'http://www.people.com.cn/',
            'http://www.ce.cn/',
            'https://news.163.com/',
            'http://news.sohu.com/'
            ]

    start_time = time.perf_counter()
    download_all(sites)
    end_time = time.perf_counter()
    print('Download {} sites in {} seconds'.format(len(sites), end_time - start_time))

if __name__ == '__main__':
    main()

```

结果

```
Read 2443 from https://www.baidu.com/
Read 572221 from https://www.sina.com.cn/
Read 6216 from https://news.qq.com/
Read 251256 from http://www.ifeng.com/
Read 107958 from http://www.ce.cn/
Read 695009 from https://www.163.com/
Read 73970 from https://news.baidu.com/
Read 152458 from http://www.people.com.cn/
Read 107958 from http://www.ce.cn/
Read 18774 from https://pypi.org/
Read 175844 from http://news.sohu.com/
Read 208376 from https://news.163.com/
Download 12 sites in 1.367539946999841 seconds

```

1. 首先 调用 executor.submit()， 将下载任务 放进 future 队列之中 等待执行

2. 使用 as_completed()  函数将 future 对象包装成 生成器

> Any futures given by fs that are duplicated will be returned once.Any futures that completed before as_completed() is called will be yielded first.


使用 as_completed: 首先存在一个去重复的操作， 其次，不按照顺序输出，先执行完的先输出
不使用 as_completed 的结果


```

Read 2443 from https://www.baidu.com/
Read 572150 from https://www.sina.com.cn/
Read 695122 from https://www.163.com/
Read 251198 from http://www.ifeng.com/
Read 107922 from http://www.ce.cn/
Read 18766 from https://pypi.org/
Read 6216 from https://news.qq.com/
Read 107922 from http://www.ce.cn/
Read 74016 from https://news.baidu.com/
Read 152458 from http://www.people.com.cn/
Read 208371 from https://news.163.com/
Read 175634 from http://news.sohu.com/
Download 12 sites in 1.6681905510004071 seconds
```



3. result(). 返回相应的结果


---

# 参考

1. http://youguanxinqing.xyz/index.php/archives/56/




























