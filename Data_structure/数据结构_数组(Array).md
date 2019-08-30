* 本文为极客时间 王争老师的《数据结构与算法之美》专栏课程笔记。整理内容，改用 python 实现，一点扩展内容。

[原文link](https://time.geekbang.org/column/intro/126)

---


## 1. 数组(Array)— Define:

数组是一种**线性表**数据结构。它利用一组**连续的内存空间**，储存一组具有**相同类型的数据**。

1. 线性表

   数组， 队列，链表，栈 等

   数据排列成一条线，只能向前和向后。

   ![](https://github.com/LiuChuang0059/large_file/blob/master/pic/f8m3h.png)

2. 非线性表：

   二叉树，图等，数据之间不是简单的前后关系。

3. 连续的内存空间

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/tsklt.png)

计算机会给每个内存单元分配一个地址，计算机通过地址来访问内存中的数据。
随机访问数组中的元素的时候，首先激素那 元素的内存地址

```python
a[i]_address = base_address + i * data_type_size

# 对于二位数组 m*n

a[i][j]_address = base_address + ( i * n + j) * type_size


```
如上图所示 base_address = 1000， data_type_size 是数组中每个元素的大小，数组储存 int , 4个字节

> 数组根据下标随机访问的时间复杂度是 O(1), 正常查找的时间复杂度是 O(logn)


## 2. 插入和删除

### 插入操作


1. 时间复杂度  --- 数组有序
对于有序数组，插入一个数据在 k 位置， k 之后的数据都要向后移动一个位置
* 如果是在末尾， 时间复杂度 O(1)
* 如果是在开头、 时间复杂度是 O(n)
* 计算平均时间复杂度  (1+2+3...+n)/n = O(n)

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/grn7y.png)

2. 时间复杂度 --- 数组无序
对于无序数组，直接将 k 位置的数据搬移到数组元素的最后，把新的元素插入第 k 个位置
* 时间复杂度 是 O(1)

### 删除操作

1. 常规删除
类似于插入操作，进行数据删除的时候，为了保持内训的连续性，需要进行数据搬移
* 如果是在末尾， 时间复杂度 O(1)
* 如果是在开头、 时间复杂度是 O(n)
* 计算平均时间复杂度  (1+2+3...+n)/n = O(n)

2. 多次删除操作一起执行

每次的删除操作不是真的去搬移数据，而是记录数据已经被删除了，当数组没有更多空间储存数据的时候，再触发执行一次删除操作。

## 3. 为什么 数据编号从 0 开始

根据内存公式来说，下标的定义应该是偏移 a[0] 就是偏移为0 的位置，就是首地址

如果数组从1 开始计数

```python
a[k]_address = base_address + (k-1)*type_size

```

每次访问数组，都多了一次减法运算，多了一个减法指令，
为了效率的最优化，从 0 开始编号

------
##  4. 代码实现数组

1. 数组可以随意访问元素
2. 数组支持 插入删除操作

```python

class Array_define(object):
	"""docstring for Array_define"""
	def __init__(self, capcity: int):
		"""
		初始化一个 大小固定的空数组

		"""
		self._data = []
		self._capcity = capcity

	def __getitem__(self,positon: int):
		"""
		使用 订制类实现访问数据中任意一项
		"""
		if not isinstance(positon,int):
			raise ValueError('input must be an integer !')
		return self._data[positon]

	def __setitem__(self,index : int,value):
		"""
		使用 订制类实现外部 修改属性
		"""
		if not isinstance(index,int):
			raise ValueError('index must be an integer !')
		self._data[index] = value

	def __len__(self):
		"""
		使用 订制类 获取列表的长度
		"""
		return len(self._data)

	def __iter__(self):
		"""
		使用 订制类 数组可以进行 for 循环操作
		"""
		for item in self._data:
			yield item

	def find(self,index: int):
		return self._data[index]

	def delete(self,index:int):
		try:
			self._data.pop(index)
			return True
		except IndexError:
			return False

	def insert(self, index: int,value):
		if len(self) >= self._capcity:
			return False
		else:
			self._data.insert(index,value)

	def print_all(self):
		for item in self:
			print(item)

	def test():
		arr = Array_define(5)
		arr.insert(0, 3)
		arr.insert(0, 4)
		arr.insert(1, 5)
		arr.insert(3, 9)
		arr.insert(3, 10)
		assert arr.insert(0, 100) is False # 超过数组的大小
		assert len(arr) == 5
		assert arr.find(1) == 5
		assert arr.delete(4) is True
		arr.print_all()
if __name__ == "main":
	test()
```

测试结果：


```python
test()
4
5
3
10

```










































