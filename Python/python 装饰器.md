本文为 极客时间 景霄老师 的《 python 核心技术与实战》课程 学习笔记

----

## 1. 从函数到装饰器

1. 函数也是对象， 可以把函数赋予变量

```python

def func(txt):
	print("hello world! {}".format(txt))

func2 = func
func2("liu")

# 输出
hello world! liu
```

* 将 func 函数 赋予给 func2 ,调用 func2 就相当于调用函数 func()

2. 函数可以当作参数，传入到另一个函数中

```python

def get_message(message):
	return "Get a message :" + message

def root_func(func, message):
	print(func(message))

root_func(get_message(), "hello")

```

3. 函数的嵌套，前面的笔记里提到过

```python

def func(message):
	def get_message(message):
		print("Got a message : {}".format(message))
	return get_message(message)

func("hello world")

```

4. 返回值可以是函数对象(闭包)

```python

def func_closer():
	def get_message(message):
		print("Got a message : {}".format(message))
	return get_message

send_message = func_closer
send_message("hello word")

```

* 返回函数 get_message() 赋值给 send_message

* 之后调用 get_message() 函数

-----

## 2. 简单的装饰器

1.

```python

def my_decorator(func):
    def wrapper():
        print('wrapper of decorator')
        func()
    return wrapper

def greet():
    print('hello world')


>>> greet = my_decorator(greet)
>>> greet()
wrapper of decorator
hello world

# 输出
wrapper of decorator
hello world
```

* 变量 test 指向内部函数 wrapper(),

* 内部函数 wapper() 又回调用函数 great()

* 这里的 my_decorator 函数就是一个 **装饰器**， 把真正需要执行的函数 great() 包裹在其中


2.  简化代码

```python

def my_decorator(func):
    def wrapper():
        print('wrapper of decorator')
        func()
    return wrapper

@my_decorator
def greet():
    print('hello world')

greet()

```



1. @my_decorator 等价于  great = my_decorator(greet)

2. @ 是 语法糖， 提高函数的重复利用和程序的可读性

----

## 3. 带有参数的装饰器

1. 在相应的装饰器函数  wrapper() 上面加入相应的参数

```python

def my_decorator(func):
    def wrapper(message):
        print('wrapper of decorator')
        func(message)
    return wrapper


@my_decorator
def greet(message):
    print(message)


greet('hello world')

# 输出
wrapper of decorator
hello world
```

* 但是这样的装饰器不适用新的函数

2. 使用 * args 和 ** kwargs  作为装饰器内部函数的参数。 表示接受任意数量和类型的参数

```python

def my_decorator(func):
	def wrapper(*args,**kwargs):
		print("")
		func(*args,**kwargs)
	return wrapper

```

---

## 4. 带有自定义参数的装饰器

定义一个参数，表示装饰器内部函数执行的次数

```python
def repeat(num):
    def my_decorator(func):
        def wrapper(*args, **kwargs):
            for i in range(num):
                print('wrapper of decorator')
                func(*args, **kwargs)
        return wrapper
    return my_decorator


@repeat(4)
def greet(message):
    print(message)

greet('hello world')

# 输出：
wrapper of decorator
hello world
wrapper of decorator
hello world
wrapper of decorator
hello world
wrapper of decorator
hello world
```

---

## 5. 原函数是否改变



```python

>>> greet.__name__
'wrapper'
>>> help(greet)
Help on function wrapper in module __main__:

wrapper()

```

* great() 函数装饰之后，元信息发生了改变

* 使用内置的装饰器  @functools.wraps(func) 解决这个问题 ---- 将原函数的信息拷贝到装饰器函数里面

```python
import functools

def my_decorator(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        print('wrapper of decorator')
        func(*args, **kwargs)
    return wrapper

@my_decorator
def greet(message):
    print(message)

greet.__name__

# 输出
'greet'
```

---

## 6. 类装饰器


类也可以作为装饰器，主要依靠于 __call__()， 每当调用一个类的实例， __call__ 就会被执行一次


```python
class Count(object):
	"""docstring for Count"""
	def __init__(self, func):
		self.func = func
		self.num_calls = 0

	def __call__(self, *args, **kwargs):
		self.num_calls += 1
		print("Num calls. is {}".format(self.num_calls))
		return self.func(*args, **kwargs)

@Count
def example():
	print("hello world")


```

调用结果

```python

example()
Num calls. is 1
hello world

example()
Num calls. is 2
hello world

example()
Num calls. is 3
hello world

```

----

## 7. 装饰器的嵌套

```python
@decorator1
@decorator2
@decorator3
def func():
    ...
```

执行顺序

```python

import functools

def my_decorator1(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        print('execute decorator1')
        func(*args, **kwargs)
    return wrapper


def my_decorator2(func):
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        print('execute decorator2')
        func(*args, **kwargs)
    return wrapper


@my_decorator1
@my_decorator2
def greet(message):
    print(message)


greet('hello world')

# 输出
execute decorator1
execute decorator2
hello world

```

---

## 8. 装饰器的用法


### 1. 身份认证

* 登陆需要输入用户名和密码， 服务器端进行查询验证

```python

def authenticate(func):
	@functools.wraps(func)
	def wrappper(*args, **kwargs):
		request = args[0]:
		if chech_user_logged_in(request):
			return func(*args , **kwargs)
		else:
			raise Exception("authenticate failed")
	return wrapper
@authenticate
def post_commments(request):
	pass

```

* 每当调用 post 函数发表评论， 都会由装饰器检查是否处于登陆状态，


---

### 2. 日志记录

* 线上测试函数的执行时间

```python
import time
import functools

def log_execution_time(func):
	@functools.wrap(func)
	def wrapper(*args, **kwargs):
		start_time= time.perf_counter()
		res = func(*args, **kwargs)
		end_time = time.perf_counter()
		print("{} took {} ms ".format(func.__name__, (end-start)* 1000))
	return wrapper

@log_execution_time
def example():
	pass
```

* 想计算任何函数的执行时间， 直接在函数上面加上 @log_execution_time


---

### 3. 缓存

python 内置的 LRU cache -- @lru_cache

@lru_cache 会缓存进程中的函数参数和结果， 缓存满了， 删除 least recently used。

大型公司的服务端代码之中存在着关于设备的检查， 通常使用缓存装饰器，包裹这些检查函数，避免被反复调用，进而提高程序的额运行效率

``` python

@lru_cache

def check():
	...

```















































