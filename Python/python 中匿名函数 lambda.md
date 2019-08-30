本文为 极客时间 景霄老师 的《python 核心技术与实战》课程 学习笔记

----

## 1. 匿名函数的基础

匿名函数的格式

```python
lambda argument1, argument2,... argumentN : expression

```

```python

square = lambda x : x**2

>>> square(3)
9

```

* 匿名函数和常规函数一样，返回的都是一个函数对象

* lambda 可以用在列表内部 -- 常规函数不能

```python

[(lambda x : x*x)(x) for x in range(10)]


[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

```

* lambda 可以用作某些函数的参数


```python

l = [(1, 20), (3, 0), (9, 10), (2, -1)]
l.sort(key=lambda x: x[1]) # 按列表中元祖的第二个元素排序

>>> l
[(2, -1), (3, 0), (9, 10), (1, 20)]

```



* lambda 适用于简短 不重复调用的


```python

squared = map(lambda x : x*x , [1,2,3,4,5])

```
对列表中所有的元素平方





----


## 2. Python 函数式编程


代码中的每一块都是不可变的， 都是由纯函数的形式组成

纯函数： 本身相互独立， 相同的输入，有相同的输出

优点：

* 程序更加健壮

* 易于调试和测试


## 3. 函数式编程接口


1. map() 函数， 之前介绍过

函数直接由 c语言写成吗内部做了诸多优化， 运行速度快

2. filter(function, iterable)

filter 对于后面迭代的元素，都使用函数进行判断， 返回 True 或者 False
返回 True的元素组成一个新的集合

```python

l = [1,2,3,4,5,6,7,8]

new_list = filter(lambda x: x%2 == 0, l)


>>> new_list
<filter object at 0x105fc5470>

>>> print(new_list)
<filter object at 0x105fc5470>
>>> print(list(new_list))      ## 注意 加入 list()
[2, 4, 6, 8]

```


3. reduce(function, iterable)

累积的操作 : 对于 iterable 的每个元素以及上一次的结果 使用 function (两个参数)

```python

from functools import reduce

l = [1, 2, 3, 4, 5]

result = reduce(lambda x, y:  x *y , l)
```




---

## 4. 例子

```python
d = {'mike': 10, 'lucy': 2, 'ben': 30}

```
根据值 由高到低排


```python
sorted(d.items(), key = lambda x : x[1], reverse = True)


[('ben', 30), ('mike', 10), ('lucy', 2)]
```





























