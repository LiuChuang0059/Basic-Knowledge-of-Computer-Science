本文为 极客时间 景霄老师 的《python 核心技术与实战》课程 学习笔记

----


## 1. 函数的基础

1. 函数基本形式：

```python

def name(param1, param2, ..., paramN):
    statements
    return/yield value # optional

```

2. 和其他的需要编译的语言不一样， python 中的 **def 是可执行语句**，函数知道被调用前都是不存在的的。当程序调用函数的时候， def 语句才会创建一个新的函数对象，赋予名字


3. Python 是 dynamically typed 的， 可以接受任何数据类型（整型，浮点型，字符串等），函数的参数传递可以人很数据类型。

```python
def sum_test(a,b):
	return a+b
sum_test(1,2)

>>> sum_test(1,2)
3

>>> sum_test([1,2],[3,4])
[1, 2, 3, 4]
>>> sum_test("liu","chuang")
'liuchuang'

```

不考虑输入的数据类型，交给具体的代码去判断，这种行为称为 **多态**



## 2. 函数的嵌套

```python

def f1():
	print("hello")
	def f2():
		print("world")
	f2()

>>> f1()
hello
world

```
函数嵌套的作用：

* 保证内部数据的隐私

```python

def connect_DB():
    def get_DB_configuration():
        ...
        return host, username, password
    conn = connector.connect(get_DB_configuration())
    return conn
```

上面获取用户的 姓名密码等 函数封装在连接函数内，是内部函数，无法外部调用。


* 提高程序的运行效率


```python

def factorial(n):
	"""
	计算一个数字的阶乘
	先检查输入的合法性

	"""
	if not isinstance(n , int):
		raise Exception('Input must be integer')
	if n < 0:
		raise Exception(" Input must > 0")

	def calculate_fac(n):
		if n <= 1:
			return 1
		else:
			return n * calculate_fac(n-1)
	return calculate_fac(n)

```

计算之前需要检查输入是否合法， 只是定义一个函数， 每次递归调用都需要检查一次，降低程序的运行效率，耗费资源。
写成嵌套函数， 只需要检查一次。



---

## 3. 函数的变量作用域

变量是在函数内部定义的，称为局部变量，只在函数的内部有效，函数执行完毕，局部变量回收，无法访问。

全局变量： 定义在整个文件层次

1. **不能在函数内部随意改变全局变量的值**


```python

Min = 1

def value_square(value):
	Min = Min * Min

>>> value_square(2)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 2, in value_square
UnboundLocalError: local variable 'Min' referenced before assignment

```

Python 解释器会默认函数内部的变量为局部变量， 局部变量 Min 并没有声明。

2. 一定要改变全局变量的值， 必须加上 global。
global 告诉 Python 解释器，函数内部变量 Min , 就是之前定义的全局变量。



```python

Min = 2

def value_square(value):
	global Min
	Min = Min * Min
```

3. 全局变量和 局部变量命名冲突

在函数内部， 局部变量会覆盖全局变量



4. 嵌套函数

内部函数可以访问外部函数定义的变量，但是不能修改。 想要修改，需要加入 nonlocal 关键字

```python

def outer():
	x = 1
	def inner():
		nonlocal x
		x = 3
		print("inner :", x)
	inner()
	print("outer", x)
outer()

>>> outer()
inner : 3
outer 3

```
加入 关键字之后，成功修改外层函数定义的数值



---

## 4. 闭包 closure

类似于嵌套函数， 只不过外部函数返回的是一个函数，而不是具体的值

例如 ： 外部函数 返回函数 exponent_of()

```python
def nth_power(exponent):
	def exponent_of(base):
		return base ** exponent
	return exponent_of

square = nth_power(2)
cube = nth_power(3)

>>> square = nth_power(2)
>>> cube = nth_power(3)
>>> square
<function nth_power.<locals>.exponent_of at 0x105fc20d0>
>>> square(2)
4

```

闭包的优势：

* 程序简单易读


* 函数开头需要一些额外的工作， 多次调用函数的时候，就可以将函数放在外部函数，减少多次调用导致的不必要的开销


存在一些 参数性 而且不常改变的量的时候，使用闭包函数， 只需要关注核心的输入变量，减少全局变量的使用





























