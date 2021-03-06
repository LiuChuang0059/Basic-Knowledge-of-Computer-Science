本文为 极客时间 景霄老师 的《 python 核心技术与实战》课程 学习笔记

----


## 1. Python 变量及其赋值

1. 不可变的变量

```python

a = 1
b = a
a = a + 1

```

* a= 1 , 变量 a 指向 1 这个对象

*  b= a , 变量 b 同时也指向 1 这个对象 （对象可以被多个变量指向和引用）

* a = a+1 , python 中数据类型 int string 是不可变的， 所以， 重新创建一个 对象 让 a 指向他， b 仍指向 1


2. 可变的变量


```python

l1 = [1, 2, 3]
l2 = l1
l1.append(4)
l1
[1, 2, 3, 4]
l2
[1, 2, 3, 4]

```

l1 和 l2 同时指向了 [1,2,3] 这个对象

列表是可变的， 不会创建新的列表， 所以 l1 和 l2 的值 同时改变。


3. 变量可以删除， 对象无法删除

```python
l = [1, 2, 3]
del l

```

删除了 l ，但是对象 [1,2,3] 还在。 python 程序云心烦的时候， 自带的垃圾回收系统会跟踪每个对象的引用，

如果对象 [1,2,3] 还在其他的地方被引用，就不会回收， 否则会被回收。


---


## 2. python 函数的参数传递

Python 的参数传递都是赋值传递(pass by assignment), 对象的引用传递，python 中参数传递的时候 只是让新变量和原始变量指向相同的对象。


### 1. 不可变对象

```python

def my_func1(b):
	b = 2

a = 1
my_func1(a)
a
1

```


1. my_func() 进行参数传递， 使得变量 a 和变量 b 同时指向了一个对象。
2. 当执行下面的 b= 2 的时候， 由于 不可变 系统会创建一个值为 2 的新的对象，b 指向 2
3. a 仍然指向 1 这个对象



```python
def my_func2(b):
	b = 2
	return b

a = 1
a = my_func2(a)
a
2

```

1. 返回的新变量赋值给了 a， 因此 a 的值也变成了 2


### 2. 可变对象


```python
def my_func3(l2):
	l2.append(4)

l1 = [1, 2, 3]
my_func3(l1)
l1
[1, 2, 3, 4]

```

1. l1 和 l2 同时指向 [1,2,3] 列表。
2. 由于列表可变， 执行 append() 函数，变量 l1 和 l2 值 也改变


```python
def my_func4(l2):
	l2 = l2 + [4]

l1 = [1, 2, 3]
my_func4(l1)
l1
[1, 2, 3]

```
1. 这里的 l2  + 表示创建了 一个新的列表， 并让 l2 指向 新的对象。

2. 过程与 l1 无关， l1 的值不会改变


* 如果要改变  l1 的值，  就得让上述的函数返回值， 赋予给 l1



```python

def my_func5(l2):
	l2 = l2 + [4]
	return l2

l1 = [1, 2, 3]
l1 = my_func5(l1)
l1
[1, 2, 3, 4]

```


----


## 3. 思考题


```python

l1 = [1, 2, 3]
l2 = [1, 2, 3]
l3 = l2

```

1,2,3 是否指向同一个对象？

2,3 指向同一个对象， 1，2 指向不同的内存空间


```python

def func(d):
    d['a'] = 10
	d['b'] = 20

d = {'a': 1, 'b': 2}
func(d)
print(d)


```
最后的输出



```python
{'a': 10, 'b': 20}

```

因为 字典是可变， 传入函数之后， 函数的内部和外部 d 都是指向同一个对象


---

## 4. 补充

python 里面的一切都是对象：


例如   a = 1 : 先是在内存中申请一份空间， 存下值= 1 ， 然后再给这块空间贴上标签，叫做变量 a .

通过储存的值可以推断变量的类型， 由解释器完成。









