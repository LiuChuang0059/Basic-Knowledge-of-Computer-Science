
tuple1 = (1, "liu")
```


* 列表是 动态的（mutable）， 长度大小不固定，
* 元组的大小是固定的 ，

如果想对元组进行改变，只能开辟一块新的内存，创建新的元组。

```python
>>> tup = (1,2,3)
>>> new_tup = tup + (5)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can only concatenate tuple (not "int") to tuple
>>> new_tup = tup + (5,)
>>> new_tup
(1, 2, 3, 5)
```


## 2. 基本操作

1. 支持负数索引
```python
list1 = [1,2 "liu"]
list1[-1].

tuple1 = (1, "liu")
tuple1[-1]
```
- 1 表示最后一个元素。


2. 支持切片操作

```python
list1 = [1,2 "liu"]
list1[1:3].

tuple1 = (1, "liu")
tuple1[1:3]
```


3. 支持嵌套


```python
list1 = [1,2 "liu",[1,2,3]]
list1[1:3].

tuple1 = (1, "liu",(1,"liu"))
tuple1[1:3]
```


4. 相互转换

```python
list1 = [1,2 "liu"]
tuple(list1)

tuple1 = (1, "liu")
list(tuple1)
```

5. 常用内置函数

* count ： item 出现的次数
* index： 第一次出现的索引
* list.reverse, list.sort() : 倒转和排序
* reversed(),sorted(): 返回 列表或者元组


## 3. 列表元组储存方式的差异

```python
>>> l = [1,2,3]
>>> l.__sizeof__()
64
>>> tup = (1,2,3)
>>> tup.__sizeof__()
48
>>>
```


相同的元素，元组的储存空间比列表要少 16 字节

* 列表是动态的， 需要储存指针， 8 字节

* 列表可变， 额外储存已经分配长度的大小 ， 8 字节

```python
>>> l = []
>>> l.__sizeof__()
40   # 空列表储存空间 40
>>> l.append(1)
>>> l.__sizeof__()
72  # 列表分配了 4个 元素的空间， 每个元素的空间 8
>>> l.append(2)
>>> l.__sizeof__()
72
>>> l.append(2)
>>> l.__sizeof__()
72
>>> l.append(2)
>>> l.__sizeof__()
72
>>> l.append(2)
>>> l.__sizeof__()
104  # 空间不足， 额外分配 4 个 元素的空间

```


为了减小 删除和添加的时候空间分配的开销， 每次分配空间都会多分配一些， 这个机制保证了操作的高效性， 删除添加的时间复杂度都是 O(1)


## 4. 列表和元组的性能

* 元组比列表要轻量， 性能速度要略优于列表


* python 中垃圾回收机制， 如果一些变量不被使用， python回收占用的内存， 但是杜预一些静态变量 -- 元组， 不被使用， python 回暂时缓存这部分内存， 在创建同样大小的元组的时候， 不必寻找内存， 直接分配之前缓存的内存， 加快运行速度。


```python
>>> timeit.timeit('a= []',number= 10000)
0.00041297206189483404
>>> timeit.timeit('a= ()',number= 10000)
0.00041262293234467506


timeit.timeit('a= (1,2, 3,4 ,5)',number= 10000)
0.0001837840536609292
>>> timeit.timeit('a= [1,2, 3,4 ,5]',number= 10000)
0.000875954981893301


>>> timeit.timeit('a= (1,2, 3,4 ,5,6,7,7,8,8,9,9,5,5,5,5,5,5,5,5)',number= 10000)
0.0002209079684689641
>>> timeit.timeit('a= [1,2, 3,4 ,5,6,7,7,8,8,9,9,5,5,5,5,5,5,5,5]',number= 10000)
0.0016364289913326502

```

tuple 初始化，速度优于 list，  数组越大，优势越明显。



## 5. 列表和元组的使用场景

1. 储存的数据和数量不变 --- 元组

2. 输出或者数量可变  --- 列表



##  6. 创建列表方式

```python
list2 - []

list3 = list()

```

list() 是一个 python调用函数， 需要进行一系列参数检查的操作

[]  是内置的 C 函数， 直接调用



























