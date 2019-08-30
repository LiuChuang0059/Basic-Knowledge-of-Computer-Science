本文为 极客时间 景霄老师 的《python 核心技术与实战》课程 学习笔记

----


## 1. 字符串基础

python 中 三种字符串独立等价

(''), (""),(""" """)

字符串内有转义字符


![](http://ww3.sinaimg.cn/large/006tNc79ly1g41x9m2iedj30us0duwfr.jpg)

```python

>>> s = 'a\nb\tc'
>>> print(s)
a
b	c
>>> len(s)
5

```

转义字符也算一个字符

## 2. 字符串常用操作

1. 字符串相当于丹个字符组成的数组， 支持 **索引，切片和遍历**


2. 字符串是不可变的（immutable)

```python
>>> s = 'hello'
>>> s[0] = 'H'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'str' object does not support item assignment

```

```python

s = 'H' + s[1:].  # 新建字符， 拼接

s = s.replace('h','H')  # 扫描原来的字符串， 替换字符，

```

上面的方法改变字符串， 需要 O(n) 的时间复杂度。


**Python 中性能优化方法** --- ‘+=’

```python

str2 += str1
```

上面的字符串拼接操作，Python 会首先检测 str1 是否存在其他的引用，如果没有，Python 会尝试扩充字符串 buffer 的大小，而不是重新分配内存创建新的字符串并拷贝， 时间复杂度从 O(n) -- > O(1).


3. 字符串拼接

```python
s = ''
for n in range(0, 100000):
    s += str(n)

```

等价于

```python

l = []
for n in range(0, 100000):
    l.append(str(n))
l = ' '.join(l)

```
 join 函数，是 字符串内置的 函数，用来吧每个元素按照指定的格式连接起来。


等价于

```python

s = " ".join(map(str,range(1000)))
```

map 函数

map函数的原型是map(function, iterable, …)，它的返回结果是一个列表。

function 是函数名，可以是自定义的，也可以是内置的函数。
iterable传的是一个可以迭代的对象，例如列表，元组，字符串

function应用于iterable的每一个元素，结果以列表的形式返回


4. 字符串的分割

```python

string.split(sepratator)

```

字符串分割成多个子字符串，返回一个子字符串组合的列表。

```python

>>> path = 'hive://ads/training_table'
>>> namespace = path.split('//')[1].split('/')[0]
>>> namespace
'ads'

```

5. 去除字符串

* 去除首尾的： string.strip()
* 去除首部的： string.lstrip()
* 去除尾部的： string.rstrip()

例如开头和结尾存在 空字符

```python
 s=  '   Hello. world '
 s.strip()
```


## 字符串格式化

Python 最新规范 ： format 函数

```python

print("no data available for person with id: {} , ; name: {}".format(id, name))
```





# 参考

1. https://blog.csdn.net/csdn15698845876/article/details/73321593







































