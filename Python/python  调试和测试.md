本文为 极客时间 景霄老师 的《 python 核心技术与实战》课程 学习笔记

----

## 0. 代码调试场景

* 代码存在问题

* 代码效率存在问题

* 开发新的功能



----

## 1. assert 语句

1. assert 语句用来测试条件是否满足，

* 如果测试条件满足，相当于 pass 语句；
* 如果测试条件不满足，跑出异常 AssertionError， 返回错误信息


2. 使用 assert 语句，如果程序出现问题， 根据错误信息， 定位错误位置。




3. 具体语法：

```python
def ffunc(n):
	assert n !=0 , 'n is zero !'
	return 10/n

def main():
	func(0)


### 运行结果 #####

AssertionError: n is zero !


```


4. 启动 python解释器的时候， 使用 -O 关闭 assert

5. assert 语句实际应用例子

对输入做简单的判断：

```python

def func(input):
	assert isinstance(input,list), "input must be type of list."

	if len(input) == 1:
		pass
```


-----

## 2. pdb 调试

```python
a = 1
b= 2
import pdb
pdb.set_trace()
c= 3
print(a+b+c)
```

```
--Return--
> <ipython-input-1-b800449844b4>(4)<module>()->None
-> pdb.set_trace()
(Pdb) p a
1

(Pdb) p c
*** NameError: name 'c' is not defined

(Pdb) n
> /Users/liuchuang/anaconda3/lib/python3.6/site-packages/IPython/core/interactiveshell.py(2966)run_code()
-> sys.excepthook = old_excepthook

(Pdb) q


```

* 程序运行到 pdb.set_trace() 进入暂停， 等待用户输入

* 执行一部分操作， p <expression > ： 打印

* 因为 变量 c 还没有定义（没有运行到）， 所以抛出异常

* 常见的操作 ‘n’  表示继续执行代码到下一行； 但是抛出异常


```python

import pdb
def test_pdb_example():
    a = "aaa"
    pdb.set_trace() # use 'n' for navigate to next line
    b = "bbb"
    c = "ccc"
    final = a + b + c
    return final

test_pdb_example()

```

* 写成函数的形式， 没有异常

```

> <ipython-input-2-dfd952554eb5>(5)test_pdb_example()
-> b = "bbb"
(Pdb) n
> <ipython-input-2-dfd952554eb5>(6)test_pdb_example()
-> c = "ccc"
(Pdb) n
> <ipython-input-2-dfd952554eb5>(7)test_pdb_example()
-> final = a + b + c
(Pdb) s
> <ipython-input-2-dfd952554eb5>(8)test_pdb_example()
-> return final
(Pdb) s
--Return--
> <ipython-input-2-dfd952554eb5>(8)test_pdb_example()->'aaabbbccc'
-> return final


```


----



## 3. 单元测试

编写测试来验证模块的功能正确性

**Python unittest 库**

```python

import unittest

def sort(list_test):
    l = len(list_test)
    for i in range(0,l):
        for j in range(i+1, l):
            if list_test[i] >= list_test[j]:
                temp = list_test[i]
                list_test[i] = list_test[j]
                list_test[j] = temp


class TestSort(unittest.TestCase):
    def test_sort(self):
        list_1 = [5,4,2,6]
        sort(list_1)
        self.assertEqual(list_1,[2,4,5,6])

if __name__ == '__main__':
    unittest.main(argv=['first-arg-is-ignored'], exit= False)


```

测试结果

```
----------------------------------------------------------------------
Ran 1 test in 0.005s

OK

```


* 编写 子类继承 unittest.TestCase

* 在类中定义相应的测试函数 test_sort(), **测试函数要以 test开头**

* 最后使用 assert 语句
> assertEqual(), assertTrue(), assertRaise() 等语句



----

## 4. 单元测试 技巧

### 1. mock

```python
import unittest
from unittest.mock import MagicMock

class TestMock(unittest.TestCase):
    def f1(self):
        val =self.f2()
        self.f3(val)

    def f2(self):
        pass

    def f3(self,val):
        pass

    def test_f1(self):
        a = TestMock()
        a.f2 = MagicMock(return_value = "custom_val")
        a.f3 = MagicMock()
        a.f1()
        self.assertTrue(a.f2.called)
        a.f3.assert_called_with("custom_val")

if __name__ == '__main__':
    unittest.main(argv=['first-arg-is-ignored'], exit= False)

```


运行结果

```
..
----------------------------------------------------------------------
Ran 2 tests in 0.004s

OK
```




* 对方法1 f1() 做单元测试， 但是 f1() 中调用了 f2(), f3()

* 把 f2() 替换为一个返回具体数值 “custom_value"

* 把 f3() 替换为一个空函数 MagicMock()


* 这样的测试只是在测试逻辑功能是否正确


----

### 2. Mock side effect

是一个 mock 函数， 根据不同的输入， 返回不同的数值， 不仅是一个 return_value.

```python

def side_effect(arg):
    if arg < 0 :
        return 1
    else:
        return 2

mock = MagicMock()
mock.side_effect = side_effect

#### 运行结果 ####

mock(-2)
1
mock(2)
2

```


---

## 5. cProfile 进行性能分析

* profile ： 对代码的每个部分进行动态的分析。 例如计算每个模块消耗的时间， 知道程序的瓶颈， 从而进行修正。

* 使用 cprofile 进行上述功能的实现

```python

def fib(n):
    if n == 0:
        return 0
    elif n == 1:
        return 1
    else:
        return fib(n-1) + fib(n-2)

def fib_seq(n):
    res = []
    if n > 0:
        res.extend(fib_seq(n-1))
    res.append(fib(n))
    return res

import cProfile
cProfile.run('fib_seq(30)')

```

计算斐波那契数列数列， 后面执行的时候加入 cProfile， 获得结果

```

 7049218 function calls (96 primitive calls) in 1.601 seconds

   Ordered by: standard name

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
7049123/31    1.601    0.000    1.601    0.052 <ipython-input-2-ed66ab8d0cdc>:1(fib)
     31/1    0.000    0.000    1.601    1.601 <ipython-input-2-ed66ab8d0cdc>:9(fib_seq)
        1    0.000    0.000    1.601    1.601 <string>:1(<module>)
        1    0.000    0.000    1.601    1.601 {built-in method builtins.exec}
       31    0.000    0.000    0.000    0.000 {method 'append' of 'list' objects}
        1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}
       30    0.000    0.000    0.000    0.000 {method 'extend' of 'list' objects}

executed in 1.61s
```

* ncalls : 相应代码或者函数调用的次数

* tottime ： 相应代码或者函数总共执行所需要的时间（不包括调用其他代码的时间）

* tottime percall = tottime / ncalls

* cumtime : 代码总共的执行时间，宝库奥调用其他代码的时间

* cumtime percall = cumtime/ ncalls



观察上述结果， 函数 fib 是程序的执行效率瓶颈


----

观察上述计算，发现有太多向前递归的重复计算， 我们如果可以记忆那些递归计算结果， 就可以减少计算次数


```python

def memorize(func):
    memo = {}
    def helper(n):
        if n not in memo:
            memo[n] = func(n)
        else:
            return memo[n]
    return helper

@memorize
def fib(n):
    if n == 0:
        return 0
    elif n == 1:
        return 1
    else:
        return fib(n-1) + fib(n-2)


def fib_seq(n):
    res = []
    if n > 0:
        res.extend(fib_seq(n-1))
    res.append(fib(n))
    return res

import cProfile
cProfile.run('fib_seq(30)')

```

* 使用装饰器， 创建字典记录已经计算的数据

运行结果

```
215 function calls (127 primitive calls) in 0.000 seconds

   Ordered by: standard name

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
       31    0.000    0.000    0.000    0.000 <ipython-input-3-c37119fa560e>:10(fib)
     31/1    0.000    0.000    0.000    0.000 <ipython-input-3-c37119fa560e>:20(fib_seq)
    89/31    0.000    0.000    0.000    0.000 <ipython-input-3-c37119fa560e>:3(helper)
        1    0.000    0.000    0.000    0.000 <string>:1(<module>)
        1    0.000    0.000    0.000    0.000 {built-in method builtins.exec}
       31    0.000    0.000    0.000    0.000 {method 'append' of 'list' objects}
        1    0.000    0.000    0.000    0.000 {method 'disable' of '_lsprof.Profiler' objects}
       30    0.000    0.000    0.000    0.000 {method 'extend' of 'list' objects}


executed in 19ms
```

* 可以看到，调用 fib 函数的次数减少到 31


-----


## 6. 文档测试


* 文档中 有很多示例代码， 交互环境中输入，和文档中显示的代码一致

* 明确的告诉调用者期望的输入和输出

```python
def abs(n):
    '''
    function to get absolute value

    Example:


    >>>abs(-1)
    1
    >>>abs(3)
    3
    >>>abs(0)
    0
    '''

    return n if n>= 0 else (-n)


if __name__ == '__main__':
    import doctest
    doctest.testmod()

```

* 正常测试， 什么输出都没有，说明编写都是正确的


```python
def abs(n):
    '''
    function to get absolute value

    Example:
    >>> abs(-1)
    1
    >>> abs(3)
    3
    >>> abs(0)
    1
    '''

    return n if n>= 0 else (-n)

if __name__ == '__main__':
    import doctest
    doctest.testmod()
    print("end")


######## 输出结果#######

**********************************************************************
File "__main__", line 10, in __main__.abs
Failed example:
    abs(0)
Expected:
    1
Got:
    0
**********************************************************************
1 items had failures:
   1 of   3 in __main__.abs
***Test Failed*** 1 failures.
end


```


* 测试报错， 但是还是会执行后面的命令

* 注意 >>> 后面有空格







































