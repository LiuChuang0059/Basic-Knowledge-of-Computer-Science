本文为 极客时间 景霄老师 的《 python 核心技术与实战》课程 学习笔记

----

**本文 代码基于 python 3.7 较低版本的python运行会出现一些问题**

## 1. python 协程示例

1. 协程是一种并发编程的一种方式， 基于 asyncio 和 async/await 等方法

2. 爬虫实例：

```python
import time

def crawl_page(url):
    print('crawling {}'.format(url))
    sleep_time = int(url.split('_')[-1])
    time.sleep(sleep_time)
    print('OK {}'.format(url))

def main(urls):
    for url in urls:
        crawl_page(url)

%time main(['url_1', 'url_2', 'url_3', 'url_4'])
# jupyter 的语法糖， 需要在jupyternotebook 中执行


########## 输出 ##########

crawling url_1
OK url_1
crawling url_2
OK url_2
crawling url_3
OK url_3
crawling url_4
OK url_4
Wall time: 10 s
```

执行 main() 函数，调取 crawl_page() 函数进行网络通信爬取结果



3. 依次爬取，效率低下， 可以进行 并发化
（需要 python 3.7 ）

```python

import asyncio
import nest_asyncio

nest_asyncio.apply()

async def crawl_page(url):
	print("crawling {} ".format(url))
	sleep_time = int(url.split('_')[-1])
	time.sleep(sleep_time)
	print('OK {}'.format(url))

async def main(urls):
	for url in urls:
		crawl_page(url)
%time asyncio.run(main(["url_1', 'url_2', 'url_3', 'url_4"]))
# jupyter 的语法糖， 需要在jupyternotebook 中执行

###################3

crawling url_1
OK url_1
crawling url_2
OK url_2
crawling url_3
OK url_3
crawling url_4
OK url_4
CPU times: user 8.16 ms, sys: 2.85 ms, total: 11 ms
Wall time: 10 s

```


* asyncio 库包含大部分需要的工具

* async 修饰词声明异步函数，调用异步函数，就会得到一个协程对象，

```python

print(crawl_page(""))

##########结果-- 一个协程对象，并不会真正的执行
<coroutine object crawl_page at 0x111209f48>

```


* await 调用，程序会阻塞在调用的地方， 进入调用的协程函数， 执行完毕返回之后再继续

* asyncio.run() 触发执行

* 因为 await 是同步调用， crawl_page() 在当前调用结束之前，不会触发下一次的调用， 所以运行时间还是 10s


4. **引入 Task 概念**

```python

import asyncio
import nest_asyncio

nest_asyncio.apply()

async def crawl_page(url):
    print('crawling {}'.format(url))
    sleep_time = int(url.split('_')[-1])
    await asyncio.sleep(sleep_time)
    print('OK {}'.format(url))

async def main(urls):
    tasks = [asyncio.create_task(crawl_page(url)) for url in urls]
    for task in tasks:
        await task

%time asyncio.run(main(['url_1', 'url_2', 'url_3', 'url_4']))

########## 输出 ##########
crawling url_1
crawling url_2
crawling url_3
crawling url_4
OK url_1
OK url_2
OK url_3
OK url_4
CPU times: user 8.54 ms, sys: 3.04 ms, total: 11.6 ms
Wall time: 4.01 s
```

* 通过 asyncio.create_task 创建任务， 任务创建之后就会被调度执行，代码不会阻塞在任务里， 之后只需要等待所有的任务结束就好。

* 运行的总时长等于运行时间最长的爬虫

----

## 2. 协程运行

```python

import asyncio
import nest_asyncio

nest_asyncio.apply()

async def worker_1():
    print('worker_1 start')
    await asyncio.sleep(1)
    print('worker_1 done')

async def worker_2():
    print('worker_2 start')
    await asyncio.sleep(2)
    print('worker_2 done')

async def main():
    task1 = asyncio.create_task(worker_1())
    task2 = asyncio.create_task(worker_2())
    print('before await')
    await task1
    print('awaited worker_1')
    await task2
    print('awaited worker_2')

%time asyncio.run(main())

########运行结果

before await
worker_1 start
worker_2 start
worker_1 done
awaited worker_1
worker_2 done
awaited worker_2
CPU times: user 5.11 ms, sys: 3.17 ms, total: 8.28 ms
Wall time: 2.01 s

```

* asyncio.run(main()) ， 使得程序进入 main() 函数， 事件循环开始

* 先后创建 task1 和 task2 ，进入事件循环等待运行。

* 运行 print 函数，输出 before await

* 执行 await task1，从当前的主任务之中切出， 事件调度器开始调度 worker_1

* work_1 开始运行，运行 print ， 之后运行到 await asyncio, 从当前的任务中切出，事件调度器开始调度 work_2

* work_2 开始运行， 运行 print， 之后运行 await asyncio.sleep， 从当前任务之中切出

* 等到 work_1 的 sleep 任务完成， 事件调度器控制权重新回到 task_1， 输入 done 的语句， task1 完成任务，从事件循环之中退出

* await task1 完成，事件调度器控制权重新回归主任务 main之中， 输出， awaited worker1，

* 之后在 await task2 继续等待， 等待 work2 完成，事件调度器的控制权 回到 task_2 输出 work-2 done

* 任务完成之后， 从事件循环之中退出， 控制权回归主任务

* 主任务 输出 await worker2 ， 协程任务结束

----

## 3. 协程任务限定运行时间


```python
import asyncio
import nest_asyncio

nest_asyncio.apply()


async def worker_1():
	await asyncio.sleep(1)
	return 1


async def worker_2():
	await asyncio.sleep(2)
	return 2/0


async def worker_3():
	await asyncio.sleep(3)
	return 3

async def main():
	task_1 = asyncio.create_task(worker_1())
	task_2 = asyncio.create_task(worker_2())
	task_3 = asyncio.create_task(worker_3())

	await asyncio.sleep(2)
	task_3.cancel()

	res = await asyncio.gather(task_1, task_2,task_3, return_exceptions = True)
	print(res)
%time asyncio.run(main())

################

[1, ZeroDivisionError('division by zero'), CancelledError()]
CPU times: user 2.21 ms, sys: 1.67 ms, total: 3.88 ms
Wall time: 2 s

```
return_exceptions = True , 遇到错误也会继续执行， 不会导致后面的任务全部取消掉

-----

## 4.  协程 应用

### 生产者消费者模型

```python

import asyncio
import nest_asyncio

nest_asyncio.apply()

async def consumer(queue,id):
	while True:
		val = await queue.get()
		print("{} get a val: {}".format(id,val))
		await asyncio.sleep(1)

async def producer(queue ,id):
	for i in range(5):
		val = random.randint(1,10)
		await queue.put(val)
		print("{} put a val: {}".format(id,val))
		await asyncio.sleep(1)

async def main():
	queue = asyncio.Queue()

	consumer_1 = asyncio.create_task(consumer(queue, "consumer_1"))
	consumer_2 = asyncio.create_task(consumer(queue, "consumer_2"))

	producer_1 = asyncio.create_task(producer(queue, "producer_1"))
	producer_2 = asyncio.create_task(producer(queue, "producer_2"))

	await asyncio.sleep(5)

	consumer_1.cancel()
	consumer_2.cancel()

	await asyncio.gather(consumer_1,consumer_2,producer_1,producer_2,return_exceptions = True)

%time asyncio.run(main())

###########

producer_1 put a val: 5
producer_2 put a val: 10
consumer_1 get a val: 5
consumer_2 get a val: 10
producer_1 put a val: 3
producer_2 put a val: 6
consumer_1 get a val: 3
consumer_2 get a val: 6
producer_1 put a val: 10
producer_2 put a val: 1
consumer_1 get a val: 10
consumer_2 get a val: 1
producer_1 put a val: 6
producer_2 put a val: 9
consumer_1 get a val: 6
consumer_2 get a val: 9
producer_1 put a val: 6
producer_2 put a val: 7
consumer_1 get a val: 6
consumer_2 get a val: 7
CPU times: user 12 ms, sys: 3.92 ms, total: 15.9 ms
Wall time: 10 s

```

1. class asyncio.Queue(maxsize=0, loop=None)

* maxsize 小于等于零，则队列尺寸是无限的

* get() : 从队列中删除并返回一个元素。如果队列为空，则等待，直到队列中有元素

* put(): 添加一个元素进队列。如果队列满了，在添加元素之前，会一直等待空闲插槽可用。


### 2. 豆瓣

爬取即将上映的电影

```python
import requests
from bs4 import BeautifulSoup

def main():
    url = "https://movie.douban.com/cinema/later/wuhan/"
    init_page = requests.get(url).content
    init_soup = BeautifulSoup(init_page, 'html.parser')

    all_movies = init_soup.find('div', id="showing-soon")
    for each_movie in all_movies.find_all('div', class_="item"):
        all_a_tag = each_movie.find_all('a')
        all_li_tag = each_movie.find_all('li')

        movie_name = all_a_tag[1].text
        url_to_fetch = all_a_tag[1]['href']
        movie_date = all_li_tag[0].text

        response_item = requests.get(url_to_fetch).content
        soup_item = BeautifulSoup(response_item, 'html.parser')
        img_tag = soup_item.find('img')

        print('{} {} {}'.format(movie_name, movie_date, img_tag['src']))

%time main()

```

结果：

```

嘿，蠢贼 07月16日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2560832388.jpg
银河补习班 07月18日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2561542089.jpg
匠心 07月18日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2553935771.jpg
乾坤镯 07月18日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2555945003.jpg
猪八戒·传说 07月19日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561910053.jpg
未来机器城 07月19日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561782665.jpg
游戏人生 零 07月19日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561782374.jpg
旺扎的雨靴 07月19日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561371801.jpg
怨灵3：看不见的小孩 07月19日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561451573.jpg
天池水怪 07月19日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561900220.jpg
傻儿夫妻小神郎 07月19日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2339621885.jpg
精灵小王子 07月20日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561172763.jpg
心之山 07月20日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561899653.jpg
桂香街 07月22日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2562011337.jpg
灰猴 07月23日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2561541477.jpg
隧道尽头 07月25日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2562169238.jpg
小Q 07月25日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561273340.jpg
我的心里住着一只猫 07月25日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2562169907.jpg
巴比龙 07月26日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561899354.jpg
哪吒之魔童降世 07月26日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2562183868.jpg
回到过去拥抱你 07月26日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561543822.jpg
跳舞吧！大象 07月26日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2561786689.jpg
武圣关公 07月26日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2555849137.jpg
极度危机 07月26日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2560754247.jpg
魔音谷 07月26日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2560796049.jpg
CPU times: user 1.59 s, sys: 53.9 ms, total: 1.64 s
Wall time: 18.5 s

```


协程：

```python
import asyncio
import aiohttp

from bs4 import BeautifulSoup

async def fetch_content(url):
    async with aiohttp.ClientSession(
     connector=aiohttp.TCPConnector(ssl=False)
    ) as session:
        async with session.get(url) as response:
            return await response.text()

async def main():
    url = "https://movie.douban.com/cinema/later/beijing/"
    init_page = await fetch_content(url)
    init_soup = BeautifulSoup(init_page, 'html.parser')

    movie_names, urls_to_fetch, movie_dates = [], [], []

    all_movies = init_soup.find('div', id="showing-soon")
    for each_movie in all_movies.find_all('div', class_="item"):
        all_a_tag = each_movie.find_all('a')
        all_li_tag = each_movie.find_all('li')

        movie_names.append(all_a_tag[1].text)
        urls_to_fetch.append(all_a_tag[1]['href'])
        movie_dates.append(all_li_tag[0].text)

    tasks = [fetch_content(url) for url in urls_to_fetch]
    pages = await asyncio.gather(*tasks)

    for movie_name, movie_date, page in zip(movie_names, movie_dates, pages):
        soup_item = BeautifulSoup(page, 'html.parser')
        img_tag = soup_item.find('img')

        print('{} {} {}'.format(movie_name, movie_date, img_tag['src']))

%time asyncio.run(main())

```

结果

```
嘿，蠢贼 07月16日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2560832388.jpg
银河补习班 07月18日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2561542089.jpg
匠心 07月18日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2553935771.jpg
乾坤镯 07月18日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2555945003.jpg
猪八戒·传说 07月19日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561910053.jpg
未来机器城 07月19日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561782665.jpg
游戏人生 零 07月19日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561782374.jpg
旺扎的雨靴 07月19日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561371801.jpg
怨灵3：看不见的小孩 07月19日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561451573.jpg
天池水怪 07月19日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561900220.jpg
傻儿夫妻小神郎 07月19日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2339621885.jpg
精灵小王子 07月20日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561172763.jpg
心之山 07月20日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561899653.jpg
桂香街 07月22日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2562011337.jpg
灰猴 07月23日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2561541477.jpg
隧道尽头 07月25日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2562169238.jpg
小Q 07月25日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561273340.jpg
我的心里住着一只猫 07月25日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2562169907.jpg
巴比龙 07月26日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561899354.jpg
哪吒之魔童降世 07月26日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2562183868.jpg
回到过去拥抱你 07月26日 https://img3.doubanio.com/view/photo/s_ratio_poster/public/p2561543822.jpg
跳舞吧！大象 07月26日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2561786689.jpg
武圣关公 07月26日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2555849137.jpg
极度危机 07月26日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2560754247.jpg
魔音谷 07月26日 https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2560796049.jpg
CPU times: user 1.29 s, sys: 138 ms, total: 1.43 s
Wall time: 4 s
```



* 只需要 4s， 速度提升了很多

-----

## 5.  为什么使用 Asynscio

并发编程的一种是 多线程 thread ， 但是存在一些缺点

* 多线程运行的过程容易被打断，出现 race condition

> 线程或进程之间访问数据的先后顺序决定了最后程序运行的结果


* 线程的切换本身存在一定的损耗


正是 为了解决这些问题， 出现 Asyncio

------

## 6. Async 异步的概念


* Sync 同步指的是 ： 操作一个一个的执行， 一个完成之后才会执行下一个

* Async 是 不同操作之间可以互相交替执行， 一个操作被 block ， 程序会找出其他可执行的操作继续执行


----


## 7. Asyncio 的工作原理


1. Asyncio 实际上是单线程的， 只有一个主线程， 但是可以进行多个不同的任务， 多任务类似于多线程。

2. 不同的任务由 event loop 控制

3. 假设不同任务只存在两个状态， 预备状态：任务等待运行 ； 等待状态： 任务已经运行， 等待外部的操作完成。

4.  event loop 控制两个 任务列表， 先从 预备状态中选取任务，使其运行，直到这个任务把 控制权交还给 event loop

5. event loop 收到控制权， 根据任务完成与否：

* 如果任务完成了， 任务放到预备状态 列表之中
* 如果没有完成， 放在等待状态的列表

6. 之后把遍历 等待状态列表的任务，查看是否完成


7. 当所有的任务放在合适的列表之后， 继续新的 循环， 继续从预备状态列表中选取一个任务执行，直到所有的任务完成


**任务在运行的时候， 不会被外部因素打断， 操作存在 race condition 情况**


----

## 8. async + await 语句

```python

import asyncio
import aiohttp
import time
import nest_asyncio

nest_asyncio.apply()


async def download_one(url):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as resp:
            print('Read {} from {}'.format(resp.content_length, url))

async def download_all(sites):
    tasks = [asyncio.create_task(download_one(site)) for site in sites]
    await asyncio.gather(*tasks)

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
    asyncio.run(download_all(sites))
    end_time = time.perf_counter()
    print('Download {} sites in {} seconds'.format(len(sites), end_time - start_time))

if __name__ == '__main__':
    main()

```


结果

```

Read 129780 from https://www.sina.com.cn/
Read 4373 from https://pypi.org/
Download 12 sites in 3.4443055499978072 seconds

```


* 任务执行的过程中需要等待， 将其放入到等待状态， 继续从预备状态中执行任务

* asyncio.run(download_all(sites)) 是 root call， 开始 event loop， 运行 内部的函数，直到结束，再关闭 event loop

* tasks = [asyncio.create_task(download_one(site)) for site in sites] ： 对输入的协程 创建一个任务， 并返回任务对象

---

## 9. 多进程

计算列表中每个元素，从零到这个元素的 平方和


1. Basic code

```python
import time
def cpu_bound(number):
    print(sum(i * i for i in range(number)))

def calculate_sums(numbers):
    for number in numbers:
        cpu_bound(number)

def main():
    start_time = time.perf_counter()
    numbers = [10000000 + x for x in range(10)]
    calculate_sums(numbers)
    end_time = time.perf_counter()
    print('Calculation takes {} seconds'.format(end_time - start_time))

if __name__ == '__main__':
    main()

```

运行结果：

```
333333283333335000000
333333383333335000000
333333483333355000001
333333583333395000005
333333683333455000014
333333783333535000030
333333883333635000055
333333983333755000091
333334083333895000140
333334183334055000204
Calculation takes 9.35461481900029 seconds


```


2. 多进程  code

```python
import multiprocessing
import time

def cpu_bound(number):
    print(sum(i*i for i in range(number)))
    #return sum(i*i for i in range(number))

def find_sums(numbers):
    with multiprocessing.Pool() as pool:
        pool.map(cpu_bound,numbers)

if __name__ == "__main__":
    numbers = [10000 + x  for x in range(10)]

    start_time = time.time()
    find_sums(numbers)
    duration = time.time() - start_time
    print("duration : {} seconds".format(duration))

```

实验结果：

```
333283335000
333583395005
333383335000
333483355001
333783535030
333683455014
333883635055
333983755091
334083895140
334184055204
duration : 0.1432640552520752 seconds

```



---

## 10. 多线程 和 Asyncio 选择

* I/O bound  I/O操作很慢， 需要很多的任务线程实现，--- Asyncio

* I/O bound I/O操作 很快， 有限的线程和数量 --- 多线程

* CPU bound ， 多进程并行



# 参考

1. https://blog.csdn.net/xingjiarong/article/details/47603813










