本文为 极客时间 景霄老师 的《python 核心技术与实战》课程 学习笔记

----


## 1. 错误和异常

* 错误： 代码不符合语法规范 --

```python
invalid syntax

```

* 异常 ： 语法正确，执行过程中遇到了错误，抛出了异常

```python

TypeError, NameError, ValueError

```

---

## 2. 处理异常 --- try except


![](https://i.loli.net/2019/07/24/5d380635ceefc67157.png)

> 图片来源 https://realpython.com/python-exceptions/

1.

```python

try:
    s = input('please enter two numbers separated by comma: ')
    num1 = int(s.split(',')[0].strip())
    num2 = int(s.split(',')[1].strip())
    ...
except ValueError as err:
    print('Value Error: {}'.format(err))

print('continue')
...

```

如果输入的不是 int ，而是字母， 程序会抛出异常 ，跳出 try，
由于 valueError 和 except 的异常类型相匹配，所以 执行 except 的block， 继续执行下去


但是如果抛出的错误不是 ValueError， 错误类型不匹配， 程序会终止并退出。



2. 解决： except block 中加入多种异常的类型

```python
try:
    s = input('please enter two numbers separated by comma: ')
    num1 = int(s.split(',')[0].strip())
    num2 = int(s.split(',')[1].strip())
    ...
except ValueError as err:
    print('Value Error: {}'.format(err))
except IndexError as err:
    print('Index Error: {}'.format(err))

print('continue')
...

```

或者

```python
try:
    s = input('please enter two numbers separated by comma: ')
    num1 = int(s.split(',')[0].strip())
    num2 = int(s.split(',')[1].strip())
    ...
except (ValueError, IndexError) as err:
    print('Error: {}'.format(err))

print('continue')
...

```

3. **更加通常的做法** ： 在最后一个 except block 声明处理的异常类型是 Exception。
Exception 是其他所有非系统异常的基类，能够匹配任意非系统的异常

```python

try:
    s = input('please enter two numbers separated by comma: ')
    num1 = int(s.split(',')[0].strip())
    num2 = int(s.split(',')[1].strip())
    ...
except ValueError as err:
    print('Value Error: {}'.format(err))
except IndexError as err:
    print('Index Error: {}'.format(err))
except Exception as err:
    print('Other error: {}'.format(err))

print('continue')
...

```

4. 在 except 后面省略异常类型， 表示与任意异常相匹配（包括系统异常）

系统异常： Keyboardinterrupt

```python
try:
    s = input('please enter two numbers separated by comma: ')
    num1 = int(s.split(',')[0].strip())
    num2 = int(s.split(',')[1].strip())
    ...
except ValueError as err:
    print('Value Error: {}'.format(err))
except IndexError as err:
    print('Index Error: {}'.format(err))
except:
    print('Other error')

print('continue')
...

```


**多个 except block， 组多只有一个 except block 会被执行，很多个匹配的时候，只有最前面的会匹配**



5. 使用 finally

* 无论发生什么情况， finally block 中的语句都会被执行，

放置一些无论如何都要执行的语句。

```python
try:
    print('try')
    n = 10 / 0
    print('result ：', n )

except ValueError as err:
    print('Value Error: {}'.format(err))

except ZeroDivisionError as err:
    print('ZeroDivisionError:' err)

else:
    print('no error')

finally:
    print('finally')

print('end')

```

运行结果 ：

```
File "<ipython-input-4-dd5e2bbf166b>", line 10
    print('ZeroDivisionError:' err)
                                 ^
SyntaxError: invalid syntax


```


----

## 3. python 的错误是 class

* 所有的错误类型都继承自 BaseException

* 捕获错误的时候， 还会捕获子类的错误

```
BaseException
 +-- SystemExit
 +-- KeyboardInterrupt
 +-- GeneratorExit
 +-- Exception
      +-- StopIteration
      +-- StopAsyncIteration
      +-- ArithmeticError
      |    +-- FloatingPointError
      |    +-- OverflowError
      |    +-- ZeroDivisionError
      +-- AssertionError
      +-- AttributeError
      +-- BufferError
      +-- EOFError
      +-- ImportError
      |    +-- ModuleNotFoundError
      +-- LookupError
      |    +-- IndexError
      |    +-- KeyError
      +-- MemoryError
      +-- NameError
      |    +-- UnboundLocalError
      +-- OSError
      |    +-- BlockingIOError
      |    +-- ChildProcessError
      |    +-- ConnectionError
      |    |    +-- BrokenPipeError
      |    |    +-- ConnectionAbortedError
      |    |    +-- ConnectionRefusedError
      |    |    +-- ConnectionResetError
      |    +-- FileExistsError
      |    +-- FileNotFoundError
      |    +-- InterruptedError
      |    +-- IsADirectoryError
      |    +-- NotADirectoryError
      |    +-- PermissionError
      |    +-- ProcessLookupError
      |    +-- TimeoutError
      +-- ReferenceError
      +-- RuntimeError
      |    +-- NotImplementedError
      |    +-- RecursionError
      +-- SyntaxError
      |    +-- IndentationError
      |         +-- TabError
      +-- SystemError
      +-- TypeError
      +-- ValueError
      |    +-- UnicodeError
      |         +-- UnicodeDecodeError
      |         +-- UnicodeEncodeError
      |         +-- UnicodeTranslateError
      +-- Warning
           +-- DeprecationWarning
           +-- PendingDeprecationWarning
           +-- RuntimeWarning
           +-- SyntaxWarning
           +-- UserWarning
           +-- FutureWarning
           +-- ImportWarning
           +-- UnicodeWarning
           +-- BytesWarning
           +-- ResourceWarning

```

错误类型 详见参考 2



----


## 4. 跨层捕获错误

```python

def func(s):
    return 10 / int(s)

def bar(s):
    return func(s)

def main():
    try:
        bar("0")
    except Exception as err:
        print('Error : {}'.format(err) )

    finally:
        print('finally')

```

运行结果：

```
Error : division by zero
finally

```



## 5. logging 模块记录错误信息

```python
import logging


def func(s):
    return 10 / int(s)

def bar(s):
    return func(s)

def main():
    try:
        bar("0")
    except Exception as e:
        logging.exception(e)

    finally:
        print('finally')
main()
print("end")
```

使用 logging ， 程序打印完错误信息后回继续执行， 并且正常退出

```
ERROR:root:division by zero
Traceback (most recent call last):
  File "<ipython-input-10-496ce55aad8c>", line 12, in main
    bar("0")
  File "<ipython-input-10-496ce55aad8c>", line 8, in bar
    return func(s)
  File "<ipython-input-10-496ce55aad8c>", line 5, in func
    return 10 / int(s)
ZeroDivisionError: division by zero

finally
end

```


----

## 6. 抛出异常 ， raise an Exception  🌟

```python
x = 10

if x  > 5 :
    raise Exception(" x should not exceed 5 , The value of x is {}".format(x))



####### 结果########


Exception                                 Traceback (most recent call last)
<ipython-input-19-7cc74a347544> in <module>()
      2
      3 if x  > 5 :
----> 4     raise Exception(" x should not exceed 5 , The value of x is {}".format(x))

Exception:  x should not exceed 5 , The value of x is 10




```



```python
def func(s):
    n = int(s)
    if n == 0 :
        raise ValueError("Invaild value {}".format(s))
    return 10 /n

def bar():
    try:
        func("0")
    except ValueError as e:
        print("ValueError")
        raise
bar()

##########运行结果

ValueError
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-12-cc000407188d> in <module>()
     11         print("ValueError")
     12         raise
---> 13 bar()

<ipython-input-12-cc000407188d> in bar()
      7 def bar():
      8     try:
----> 9         func("0")
     10     except ValueError as e:
     11         print("ValueError")

<ipython-input-12-cc000407188d> in func(s)
      2     n = int(s)
      3     if n == 0 :
----> 4         raise ValueError("Invaild value {}".format(s))
      5     return 10 /n
      6

ValueError: Invaild value 0



```


* 捕获错误之后， 向上抛出错误

* raise 不带参数， 将当前的错误原样的抛出
















## 6. 用户自定义异常


为了异常更加详细可读，增加一些异常类型的功能



---

## 4. 异常使用场景

不确定某段代码能否执行成功，使用异常处理

正常的 flow-control 不需要异常处理


---

## 5. 附

```python

except E as N:
	foo


```

等价于

```python

except E  as N :
	try :
		foo
	finally:
		del N
```

最后 N 会被 clear



-----


# 参考

1. 廖雪峰官方网站 ：https://www.liaoxuefeng.com/wiki/1016959663602400/1017598873256736

2. https://docs.python.org/3/library/exceptions.html#exception-hierarchy

3. https://realpython.com/python-exceptions/


























