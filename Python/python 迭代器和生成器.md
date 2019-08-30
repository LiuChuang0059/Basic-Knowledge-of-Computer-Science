本文为 极客时间 景霄老师 的《 python 核心技术与实战》课程 学习笔记

----

## 1. 可迭代对象 和 迭代器

1. 容器 ： python 中一切都是对象，对象的抽象是类， 对象的集合就是容器

例如，列表，元组， 字典， 集合等都是容器。


2. 所有的容器都是可以迭代的(iterable).

3. 迭代器提供了一个 next 的方法， 调用这个方法之后， 要么得到下一个对象，要么得到一个 stopiteration; next
方法可以不遗漏的一个个拿到所有的元素。

4. 对于可以迭代对象， iter() 函数返回一个迭代器， 通过 next() 函数实现遍历

5. 判断一个对象是否是可迭代的

```python

def is_iterable(param):
	try:
		iter(param)
		return true
	except TypeError:
		return False
```


----

## 2. 生成器
生成器是简化的 迭代器

1. 声明一个迭代器 [ i for i in range(10000)] 生成元素列表， 每个元素生成后都会保存在内存中，占用大量的内存

2. 有时候不需要保存全部的元素， 用完就可以扔掉了

3. 生成器 ： 只有调用 next() 函数的时候，才会生成下一个变量。 (i for i in range(10000))



```python

def generator(k):
	i = 1
	while True :
		yield i**k
		i = i+1

print(generator(1))
<generator object generator at 0x10afcbca8>

gen_1 = generator(1)
gen_3 = generator(3)
def get_sum(n):
	sum_1 , sum_3 = 0,0
	for i in range(n):
		next_1 = next(gen_1)
		next_3 = next(gen_3)
		print("next 1 = {} ,next 3 = {}".format(next_1,next_3))
		sum_1 += next_1
		sum_3 += next_3
	print(sum_1*sum_1, sum_3)


#################
next 1 = 1 ,next 3 = 1
next 1 = 2 ,next 3 = 8
next 1 = 3 ,next 3 = 27
36 36

```

yeild 函数 会使程序跳出， 跳到 next() 函数

如果一个函数定义中包含yield关键字，那么这个函数就不再是一个普通函数，而是一个generator。 在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行。

----

## 3. 生成器 应用

1. 查询指定数字在 list 中的位置

```python
def index_generator(L, target):
	for i , num in enumerate(L):
		if num == target:
			yield i



print(list(index_generator([1,2,3,4,1,2,3,4,1,2,3,4], 1)))
[0, 4, 8]

```

2. 给定两个序列， 判断是否是子序列

```python

def is_subsequence(a,b):
	b = iter(b) #  将列表转换成 迭代器
	print(b)

	gen = (i for i in a)  # 列表转换成一个生成器

	for i in gen:
		print(i)

	gen = ((i in b ) for i in a )
	print(gen)


	for i in gen:
		print(i)

	return all(((i in b ) for i in a ))

print(is_subsequence([1,2,3],[1,2,3,4,5]))
<list_iterator object at 0x10b01ecf8>
1
2
3
<generator object is_subsequence.<locals>.<genexpr> at 0x10b551b48>
True
True
True
False

```

gen = ((i in b ) for i in a )

等价于

```python

while True :
	val = next(b)
	if val == i :
		yield True
```
这样保存了 当前的指针， 可以继续判断


all 判断是否全部为 true ， 如果是，返回 true， 否则返回 false

最后 返回 false 是因为

for i in gen 已经把 迭代器 消耗完了， b 已经是一个空的迭代器， 再执行 all() 就是 false
(需要复位生成器才能重新进行遍历)



----

简化代码：

```python

def is_subsequence(a,b):
	b = iter(b)
	return all(i in b for i in a)


print(is_subsequence([1, 3, 5], [1, 2, 3, 4, 5]))
True

print(is_subsequence([1,5, 3], [1, 2, 3, 4, 5]))
False

```












