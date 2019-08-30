本文为 极客时间 景霄老师 的《python 核心技术与实战》课程 学习笔记

----

## 1. 条件语句

```python

if condition_1:
    statement_1
elif condition_2:
    statement_2
...
elif condition_i:
    statement_i
else:
    statement_n


```

基本的 条件语句， 顺序执行， 遇到一个条件满足，执行完 statement 之后，退出整个条件语句。

![](http://ww3.sinaimg.cn/large/006tNc79ly1g438mw670bj31b60i5tce.jpg)

-----


## 2. 循环语句

1. for 循环

数据结构是可以迭代的，都可以遍历 (iterable)

字典 ： 本身只有 key 可以迭代，

```python

>>> for k in liu:
...   print(k)

name
age
height
gender

```

如果遍历 value 或者 key-value 对，需要调用内置函数。

```python

>>> for v in liu.values():
...   print(v)
...
liu
23
80
male

```

```python

>>> for k, v in liu.items():
...   print("key:{},value:{}".format(k,v))
...
key:name,value:liu
key:age,value:23
key:height,value:80
key:gender,value:male

```

2. while 循环

```python

l = [1, 2, 3, 4]
index = 0
while index < len(l):
    print(l[index])
    index += 1

```

3. continue 和 break 语句

continue ： 跳出当前这层循环， 继续执行下面的循环。
break ： 完全跳出循环体


4. while 和 for 的 比较

for ： 遍历一个已知的集合
while： 没有特定的集合需要去遍历


while和 for 的效率问题：

```python
i = 0
while i < 1000000:
    i += 1
```

```python

for i in range(0, 1000000):
    pass
```

range 是基于 c 语言的， 调用的速度很快

while 中的 i+= 1，设计到了对象的创建和删除，
> i 是 immutable，  i  = new int(i + 1)

------


## 3. 条件和循环的复合使用



```python

expression1 if condition else expression2 for item in iterable

```

等价于

```python
for item in iterable:
	if condition:
		expression1
	else:
		expression2

```

如果没有 else 语句

```python

expression for item in iterable if condition

```


同时两个循环 ：

返回两个列表元素组成的元组

```python

[(xx,yy) for xx in x for yy in y if x != y]

```

等价于

```python
l = []
for xx in x :
	for yy in y :
		if xx != yy
		l.append((xx,yy))

```

----

## 4. 例题 ：

```python

attributes = ['name', 'dob', 'gender']
values = [['jason', '2000-01-01', 'male'],
['mike', '1999-01-01', 'male'],
['nancy', '2001-02-01', 'female'],
['liu','1997-04-15','male']
]

# expected outout:
[{'name': 'jason', 'dob': '2000-01-01', 'gender': 'male'},
{'name': 'mike', 'dob': '1999-01-01', 'gender': 'male'},
{'name': 'nancy', 'dob': '2001-02-01', 'gender': 'female'}]

```



```python

outout =[]

for i in range(len(values)):
	dict1 = {}
	for j in range(len(values[0])):
		dict1[attributes[j]] = values[i][j]
	outout.append(dict1)
```


```python
[ dict(zip(attributes,value))for value in values]

```

zip()是Python的一个内建函数，它接受一系列可迭代的对象作为参数，将对象中对应的元素打包成一个个tuple（元组），然后返回由这些tuples组成的list（列表）。若传入参数的长度不等，则返回list的长度和参数中长度最短的对象相同。利用 * 号操作符，可以将list unzip（解压）

```python

>>> a = [1,2,3]
>>> b = [4,5,6]
>>> c = [4,5,6,7,8]
>>> zipped = zip(a,b)
[(1, 4), (2, 5), (3, 6)]
>>> zip(a,c)
[(1, 4), (2, 5), (3, 6)]
>>> zip(*zipped)
[(1, 2, 3), (4, 5, 6)]

```

# 参考

1. zip 函数 ---- https://www.jb51.net/article/53051.htm





























