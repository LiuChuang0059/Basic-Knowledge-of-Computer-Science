原文：  Your Guide to the Python Print Function  by Bartosz Zaczyński

原文链接 ： https://realpython.com/python-print/


-----

# 1. print 函数用法概述

print(* objects, sep=' ', end='\n', file=sys.stdout, flush=False)

下面每个小节会详细介绍每个参数的意义和用途


## 1. 调用 print

1. 最简单的例子是直接调用，不需要传入任何参数。
但是后面需要加上 **()**, 告诉 python 执行的是函数，而不是只是指向 print

```python
>>> print()

>>>
```

不输入任何参数， 但是会有默认的看不见的换行符（文本编辑中的 enter ） ， 会打印一个空白行。

> * 换行符是一种用来表明行末的特殊控制符号， 通常在很多的文本编辑器里面都是不可见的， 有一些文本编辑器会显示这个小的符号。
> * 换行通常不止一个字符长。例如，Windows操作系统和HTTP协议用一对字符表示换行。有时候，您需要考虑这些差异来设计真正可移植的程序。
> * 可以使用 python 内置的 os 模块， 查看当前系统的换行表示
> * 注意到这些字符使用 一个反斜杠 (\\) 来表示转义字符的开始


```python
>>> import os
>>> os.linesep
'\n'
>>>
```


> * 在字符前面加入一个反斜杠之后， 他的意思完全变了， 我们可以比较一下两者的 ASCII 值： 使用内置的 ord() 函数

```python
>>> ord('n')
110
>>> ord('\n')
10
>>>
```

调用 print() 函数，不输入任何参数，会打印一个 blank line，仅仅由一个换行符构成
blank line 和 empty line ，empty line 不包含任何字符，甚至不包含换行符。


2. 使用rstrip() 方法一处字符串中的 换行符。: 移除字符串右边之后任何尾部的空白。

```python
>>> 'A line of text.\n'.rstrip()
'A line of text.'

```
> * python 中的字符串既可以使用 单引号，也可以使用双引号，这两者之间没有差别. 根据 PEP 8 编码规范， 保持使用一个， 除非在一个引用里面包含另外一个引用。
> * 例如字符串之中包含需要引用的部分, 就需要使用两种不同的字符串。
```python
'My favorite book is "Python Tricks"'
"My favorite book is 'Python Tricks'"
```
> * 同样的， 可以使用转意字符 反斜杠， 让 python 将内部的双引号当作字符串的一部分。

```python
"My favorite book is \"Python Tricks\""
```

> * 但是当我们真的要使用 反斜杠的时候， 例如在 windows 中表示文件路径的时候, 我们就需要另一个反斜杠作为转意字符来转意表示路径的反斜杠。
```python
'C:\\Users\\liu'
```
> * 上面的情况在正则表达式中表现的更加明显， 因为表达式中要使用大量的特殊字符
```python
'^\\w:\\\\(?:(?:(?:[^\\\\]+)?|(?:[^\\\\]+)\\\\[^\\\\]+)*)$'
```
> * 如果按照上面的写法来会很累很繁琐， 但是我们可以使用 原始字符串 这种表达方式， 在字符串引号前面加入 r,字符串中的字符表现的就是他原本的意思。
```python
r'C:\Users\jdoe'
r'^\w:\\(?:(?:(?:[^\\]+)?|(?:[^\\]+)\\[^\\]+)*)$'
```
> * 当我们需要定义多行的字符串的时候， 例如**docstrings**
```python
"""
This is an example
of a multi-line string
in Python.
```

> * 上面的写法会初始就有一个空行， 所以为了避免空行， 我们将文本放在引号之后
```python
"""This is an example
of a multi-line string
in Python.
"""
```
```python
>>> print("""
... This is an example
... of a multi-line string
... in Python.
... """)

This is an example
of a multi-line string
in Python.

>>> print("""This is an example
... of a multi-line string
... in Python.
... """)
This is an example
of a multi-line string
in Python.
```
> * 有时候需要移除字符串中的缩紧， 我们可以使用 内置模块 ： textwrap

```python
>>> import textwrap
>>> paragraph = '''
...     This is an example
...     of a multi-line string
...     in Python.
...     '''
>>> print(paragraph)

    This is an example
    of a multi-line string
    in Python.

>>> print(textwrap.dedent(paragraph).strip())
This is an example
of a multi-line string
in Python.
```


3.  可以将字符串等信息 赋值到一个变量， 加强代码的可读性和重复使用性

```python
>>> message = 'Please wait while the program is loading...'
>>> print(message)
```

4. 可以向 print 传入一个表达式，例如 字符串拼接 ； print 函数会在输出结果之前，先进行计算。

```python
>>> import os
>>> print('Hello, ' + os.getlogin() + '! How are you?')
Hello, liuchuang! How are you?
```

5. 在 python 中格式化信息， python36 之后，建议使用 f-strings, 有着最简洁的语法。
```python
>>> print(f'Hello, {os.getlogin()}! How are you?')
Hello, liuchuang! How are you?
```
> f-strings 的使用可以参考另一篇笔记

6. 和其他的函数不同， print() 函数可以接受任意类型的的东西

```python
>>> print(42)                            # <class 'int'>
42
>>> print(3.14)                          # <class 'float'>
3.14
>>> print(1 + 2j)                        # <class 'complex'>
(1+2j)
>>> print(True)                          # <class 'bool'>
True
>>> print([1, 2, 3])                     # <class 'list'>
[1, 2, 3]
>>> print((1, 2, 3))                     # <class 'tuple'>
(1, 2, 3)
>>> print({'red', 'green', 'blue'})      # <class 'set'>
{'red', 'green', 'blue'}
>>> print({'name': 'Alice', 'age': 42})  # <class 'dict'>
{'name': 'Alice', 'age': 42}
>>> print('hello')                       # <class 'str'>
hello
>>> print(None)
None
```
> * 最后print None， None 代表值的缺失，但是print 的结果不是空的字符，而是 'None'

print 函数是怎么知道如何处理这些不同类型的的输入？ 实际上 print() 不知道， 他只是隐式的的调用 str() ， 将任何传入的对象转换为 string 类型，之后以一个统一的方式处理 字符串。

**到现在，已经可以简单调用 print() 函数， 并传入一个单一的内容(固定内容或者有一定格式的)**


-----

## 2. 分割多个输入内容


1. print()  函数实际可以接受任意数量的位置参数（positional arguments）, 因为 print 里面有 * args 参数
> * Arguments 能够以多种方式被传入函数。
> * 一种方式是显式的传入赋值
```python
>>> def div(a, b):
...     return a / b
...
>>> div(a=3, b=4)
0.75
```
> * 因为传入的内容能够根据变量名准确识别， 他们的传入的顺序就不影响，交换传入的顺序也会得到永阳的结果
```python
>>> div(b=4, a=3)
0.75
```
> * 相反的， 传入的内容没有明确的赋值到相应的变量名上， 将会根据他们传入的位置进行识别，就是我们上面说的 **positional arguments**
```python
>>> div(3, 4)
0.75
>>> div(4, 3)
1.3333333333333333
```
> * 除了可以接受大量的位置参数， print() 还有关键字参数，在调用的时候是可选的，因为有 default 值

2. print() 会对传入的多个内容之间插入一个空格，例如下面👇
```python
>>> import os
>>> print('my name is ' , os.getlogin(), 'and I am' , 18 )
my name is  liuchuang and I am 18
```
> 类似的还有 一个 list 或者 tuple 中有混合的不同类型的内容， 需要使用 (* ) 操作符 对序列进行解压缩。
```python
>>> print(*['liu' ,'is' , 18 ])
liu is 18
>>> print(['liu' ,'is' , 18 ])
['liu', 'is', 18]
```


3. 默认传入一个 ' '， 我们也可以修改这个默认值。

```python
>>> print('hello', 'world', sep=None)
hello world
>>> print('hello', 'world', sep=' ')
hello world
>>> print('hello', 'world')
hello world
```
发现上面三种形式的等价， 我们做一些不一样的修改：
```python
>>> print('Hello', 'world', sep ='')
Helloworld
```
传入一个换位符
```python
>>> print('Hello', 'world', sep ='\n')
Hello
world
```

4. 另外一个很有用的例子是传入一个斜杠或者反斜杠进行分割，在 print 文件路径的时候经常用到。

```python
>>> print('Users', 'liuchuang','liuchuang0059.github.io',sep ='/')
Users/liuchuang/liuchuang0059.github.io
```
注意到Users 前面实际上也应该有 ‘/’ , 我们可以直接在前面插入一个符号，也可以插入一个空的字符串
```python
>>> print('/Users', 'liuchuang','liuchuang0059.github.io',sep ='/')
/Users/liuchuang/liuchuang0059.github.io

>>> print('','Users', 'liuchuang','liuchuang0059.github.io',sep ='/')
/Users/liuchuang/liuchuang0059.github.io
```

5. 另外一个很有趣的例子是： 将数据导入到 CSV (comma separated values) 逗号分隔值
```python
>>> print('liu', 'male',18, sep = ',')
liu,male,18
```

6. 最后， 分隔符不仅仅限于单个的字符， 还可以是任意长度的 字符串
```python
>>> print('node', 'child', 'child', sep=' -> ')
node -> child -> child
```

-----

## 3. 防止换行符的出现

1. 有些时候，我们不希望以换行符结束消息，之后再次调用 print() 的希望能够在同一行上继续。一个经典的例子是提示用户输入，我们希望用户输入的答案在同一行

```text
Are you sure you want to do this? [y/n] y
```

2. 为了实现上面的功能， 我们需要使用另外一个关键字 **end** ,类似于上面介绍的
* 首先必须是 string 或者 None
* 可以是任意长度的
* 默认值是'\n' 换行
* 如果输入 None 等价于默认值
* 如果输入 '' ， 就会防止换到新的一行

```python
print('Checking file integrity...', end='')
# (...)
print('ok')


Checking file integrity...ok
```
两次调用 print() 函数，即使中间执行很长一部分的其他代码， 再次调用 print， 还是会出现在同一行。

3. 类似于 sep， 可以自定义一些 end 符， 将不同的文本连接到一个大的文本之中
```python
print('The first sentence', end='. ')
print('The second sentence', end='. ')
print('The last sentence.')


The first sentence. The second sentence. The last sentence.
```

4. sep 和 end 组合使用, 将所有的文本汇集到一行，并且所有item 之间的分隔符都是 逗号

```python
print('Mercury', 'Venus', 'Earth', sep=', ', end=', ')
print('Mars', 'Jupiter', 'Saturn', sep=', ', end=', ')
print('Uranus', 'Neptune', 'Pluto', sep=', ')


Mercury, Venus, Earth, Mars, Jupiter, Saturn, Uranus, Neptune, Pluto
```

5. 想在文本每行的开头加入一些， 例如 markdown 中，有些行开头加入 * ，
```python
print('Printing in a Nutshell', end='\n * ')
print('Calling Print', end='\n * ')
print('Separating Multiple Arguments', end='\n * ')
print('Preventing Line Breaks')


Printing in a Nutshell
 * Calling Print
 * Separating Multiple Arguments
 * Preventing Line Breaks
```

6. 在读取文本文件的时候，循环读取每行的文本，会包含文本自身的换行符， 再使用print() 函数输出的时候，还会结合 print 中的换行符， 导致多一个空行, 如👇
```python
>>> with open('file.txt') as file_object:
...     for line in file_object:
...         print(line)
...
Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod

tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,

quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo

```

我们需要删除一个换行符：
* 去掉文本自身的

```python
print(line.rstrip())
```
* 去掉print() 中的
```python
print(line, end = '')
```
```text
Lorem ipsum dolor sit amet, consectetur adipisicing elit, sed do eiusmod
tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam,
quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo
```

----

## 4. print() 输出到文件

实际上 print() 函数并不知道如何将信息转换为呈现在屏幕上的文本，当然他也不需要知道， 因为这是更加底层的代码的工作。

print() 抽象在这些层上面，提供一个更加方便的接口。将实际的打印工作嫁给 stream, 或者 file-like object。
> 流可以是磁盘上任意文件， network socket, 或者 an in-memory buffer.

1. 操作系统提供三种标准的流：

*  stdin ： 标准输入
*  stdout ：标准输出
*  stderr ：标准错误

> 标准流 介绍：
> * 标准输出： 运行命令程序或者脚本文件之后看到的结果

```bash
$ touch test.py
$ cat test.py
$ vi test.py
$ cat test.py
print("hello world")
$ python test.py
hello world
```
> * 如果没有其他的指令， print() 会默认将内容写到标准输出。 然而你可以告诉你的操作系统暂时的将标准输出换成一个文件流， 这样输出会在那个文件结束，而不是显示在屏幕上。这个叫做** stream redirection**
```bash
$ touch file.txt
$ python test.py > file.txt
$ cat file.txt
hello world
```
> * 标准错误类似于标准输出，因为它也显示在屏幕上。尽管如此，它是一个单独的流，其目的是为诊断记录错误消息。通过重定向其中一个或两者，可以保持整洁。
>> 为了能够重定向 stderr， 我们需要了解一下，  file descriptors, also known as file handles.（文件描述符， 文件句柄）。 他们是与标准流相关的数字。

__Stream__ | __File Descriptor__
---- | ---
stdin| 0
stdout |  1
stderr | 2

>> 知道这些标准流之后， 我们每次重定向一个或者几个标准流


__Command__ | __Description__
---- | ---
program > out.txt| Redirect stdout
program 2> err.txt|  Redirect stderr
program > out.txt 2> err.txt | Redirect stdout and stderr to separate files
program &> out_err.txt | Redirect stdout and stderr to the same file

> * stdout 和 stderr 是 write-only 的， stdin 是 read-only，可以想象标准输入是你的键盘打字输入， 淡然你也可以重定向到一个文件从那里读取数据。


2. 在 Python 中， 可以通过内置的 sys 模块获取所有的标准流, 如 👇 所示，这些标准流有 mode， 和编码属性，类似于 文件对象。


```python
>>> import sys
>>> sys.stdin
<_io.TextIOWrapper name='<stdin>' mode='r' encoding='UTF-8'>
>>> sys.stdin.fileno()
0
>>> sys.stdout
<_io.TextIOWrapper name='<stdout>' mode='w' encoding='UTF-8'>
>>> sys.stdout.fileno()
1
>>> sys.stderr
<_io.TextIOWrapper name='<stderr>' mode='w' encoding='UTF-8'>
>>> sys.stderr.fileno()
2
```

3. 默认的 print() 是基于 sys.stdout ，通过 file 参数实现。 我们可以通过调整 file 参数改变 print()  的输出。

```python
>>> with open('file.txt' , mode = 'w') as file_object:
...     print('hi siri',file = file_object)
```

```sh
$ cat file.txt
hi siri
```
> * 注意不要使用 print() 去写入二进制数据， print() 只适合写入 text
> * 对于二进制数据， 直接调用二进制数据的 write() 方法
```python
with open('file.dat', 'wb') as file_object:
    file_object.write(bytes(4))
    file_object.write(b'\xff')
```

> * 不能对 标准输出写 raw bytes， 因为标准输出是一个 字符流
```python
>>> sys.stdout.write(bytes(4))
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: write() argument must be str, not bytes
```
> * 我们需要再深入一步， 了解更加底层的 byte 流
```python
>>> import sys
>>> num_bytes_written = sys.stdout.buffer.write(b'\x41\x0a')
A
```

4. 注意到 print() 函数不能控制 character 编码。这是 stream 的任务，将接收到的饿 Unicode strings 编码成 字符， 默认的编码格式就是 UTF-8.， 如果必须要用到的话， 可以对 encoding，传入相应的内容

```python
with open('file.txt', mode='w', encoding='iso-8859-1') as file_object:
    print('über naïve café', file=file_object)
```

5. 除了真实存在的文件， 我们也可以提供一个假的，仅存在计算机的内存中的， 这对于单元测试很有用。
```python
>>> import io
>>> fake_file = io.StringIO()
>>> print('hi siri',file = fake_file)
>>> fake_file.getvalue()
'hi siri\n'
>>>
```

--------

## 5. 缓冲打印

1. 例如 倒计时，每隔一秒打印一个数字

```python
import time

def count_down(num_seconds):
	for count_down in reversed(range(num_seconds + 1)):
		if count_down > 0 :
			print(count_down,end = '...')
			time.sleep(1)
		else:
			print('Go !')

```

```text

count_down(3)
3...2...1...Go !
```
上面的写法是没有问题的， 3，2，1 每隔一秒出现，但是在终端换一种写法, 会等待 3秒之后一起

```python
import time

num_seconds = 3
for countdown in reversed(range(num_seconds + 1)):
    if countdown > 0:
        print(countdown, end='...')
        time.sleep(1)
    else:
        print('Go!')
```

```sh
$ vi count_down.py
$ python count_down.py
3..2..1..Go!
```

这是因为在这种情况下， 操作系统会缓冲 后续写入到 标准输出的内容。

2. 关于缓冲 有三种流 ：

* 不缓冲 ： 所有的写操作都会立刻有效
* 行缓冲 ： 行缓冲流在触发任何I/O调用之前会等待，直到缓冲区中出现换行符
* 区块缓冲 ： 块缓冲流只允许缓冲区填充到一定的大小，而不管它的内容如何。

标准输出同时具有行缓冲和块缓冲，这取决于哪个事件先出现。

3. 缓冲能够帮助减少 I/O调用的损耗。 例如我们通过一个高延迟的网络发送信息，当我们连接到一个远端的服务器，通过 SSH协议执行命令，每一次敲击键盘可能会产生一个独立的数据包，大小是它实际负载的指数倍。这意味着我们输入一些字符之后一起发送会减少很大的开销。这就是缓冲的意义。


4. 有时候缓冲也会带来一些我们不想要的结果。 例如上面的在终端运行 count_down.py 的结果。 为了修复上面的问题， 我们告诉 print() 函数强制 flush 缓冲，而不是缓冲等待一个换行符。

```python
print(countdown, end='...', flush=True)
```

------

## 6.  print 自定义数据类型
上面介绍的都是处理内置的数据类型， 我们有时候也需要print 自己定义的数据类型。

1. 对于只是简单存储数据，不包含逻辑的对象， 我们可以使用标准库中的 namedtuple ，

```python
from collections import namedtuple
>>> Person = namedtuple('Person','name age')
>>> liu = Person('liu',18)
>>> print(liu)
Person(name='liu', age=18)
```

2. 为了给 Person 数据类型添加更多的特性， 我们需要定义一个 class

```python
class Person:
    def __init__(self,name,age):
        self.name ,self.age = name , age


liu = Person('liu', 18)

print(liu)
<__main__.Person object at 0x1173f5940>
```

print liu 的结果就是对象的默认表示，包括内存中的地址、对应的类名和定义它们的模块。

我们可以通过继承的方法改进上面的 class，
```python
from collections import namedtuple

class Person(namedtuple('Person', 'name age')):
    pass



liu = Person('liu',18)

print(liu)
Person(name='liu', age=18)
```
Person class 现在是 namedtuple 的特殊类， 继承了其相关特性。这样我们不但可以添加自定义的方法和特性，还能很方便的进行 print。
但是，由于我们导入的是tuple 类型的数据，没办法进行修改。 在许多情况下，我们还是希望能够修改。
> 在 python 3.7 中，引入了 data classes 是一种 可变的 tuple 类型数据
```python
>>> from dataclasses import dataclass
>>> @dataclass
... class Person:
...     name: str
...     age: int
...     def add_age(self):
...         self.age+= 1
...
>>> liu = Person('liu',18)
>>> liu.add_age()
>>> print(liu)
Person(name='liu', age=19)

```

3. 根据之前的学习我们知道，print() 函数实际上隐式的调用内置的 str() 函数将 position arguments 转换成为 strings， 世界上手动调用 str()  和 print() 结果是一样的。

为了获得较好的 print 结果， 我们通常需要定义两个 magic methods .

```python
def __str__(self)

def __repr__(self)
```

* 第一个通常返回一个较短的，可读的文本，包含相关属性的信息， 规避一些敏感信息

* 第二个应该提供关于对象的完整信息，以便从字符串恢复对象的状态。

* 使用 print 的时候，搜索的顺序就是上面的顺序

```python

class User:
    def __init__(self, login, password):
        self.login = login
        self.password = password

    def __str__(self):
        return self.login

    def __repr__(self):
        return f"User('{self.login}', '{self.password}')"
```

尽管 print() 函数本身使用 str()  进行 类型转换， 对于一些复合数据类型，，需要调用 rper() ， 例如 list 和 tuple。

print calss 中一个单一的实例的时候，只会 print return的 login， 因为 print(user)会调用 str(user)， 最终会调用 user.__str__


```python
user = User('liu', 1234)

print(user)
liu
```

然而如果我们将 user 变量放入到 list 中， 就可以看到 登陆密码。 因为序列(list, tuple)
**That’s because sequences, such as lists and tuples, implement their .__str__() method so that all of their elements are first converted with repr().**
```python
print([user])
[User('liu', '1234')]

```

----
----

# 2. 深入理解  python Print

## 1. Print 在 Python3 中是一个 Function

1. print() 实际是一个内置的函数， 不需要 import 就可以在任何地方使用。 可以直接调用，也可以通过标准库 builtins 导入。 这样可以避免和自己定义的函数产生名字冲突。

```python
>>> print
<built-in function print>
>>> import builtins
>>> builtins.print
<built-in function print>
```

2. 假设我们想要 重新定义内置的 print() ，添加一个特性， 不再默认添加一个换行符,末尾加上一个 *


```python
import builtins
println = builtins.print
def print(*args, **kwargs):
    builtins.print(*args, **kwargs, end='*')


println('hello')
hello

print('hello')
hello*
```
现在我们有两个 独立的print 函数，  println 是原始的默认的， print 是我们自定义过的。


3. 另一方面，  print() 不是数学意义上的函数，它不返回任何有意义的值，除了一个隐式的 None

```python
>>> value = print('hello world')
hello world
>>> value
>>> print(value)
None
```

4. 函数是很容易扩展。向函数添加新特性与添加另一个关键字参数一样简单，但是更改语言以支持该新特性要麻烦得多。例如，考虑流重定向或缓冲区刷新。

5. print() 作为函数的另一个好处是可组合性，函数被叫做第一类对象，就是说他们值就像字符串和数字一样， 可以将函数复制给一个变量，传递到另外一个函数中，甚至用一个函数返回一个函数。

```python
def download(url, log=print):
    log(f'Downloading {url}')
    # ...

def custom_print(*args):
    pass  # Do not print anything

download('/js/app.js', log=custom_print)

```
可组合性就是允许将几个函数组合成一个相同类型的新函数。我们自定义一个error()函数来看看它的作用，该函数将输出到标准错误流，并为所有具有给定日志级别的消息加上前缀。

```python
from functools import partial
import sys
redirect = lambda function, stream: partial(function, file=stream)
prefix = lambda function, prefix: partial(function, prefix)
error = prefix(redirect(print, sys.stderr), '[ERROR]')
error('Something went wrong')


[ERROR] Something went wrong
```
这个自定义函数使用 partial functions，它是从函数式编程范式中借用的一个高级概念。


6. 函数是 values ， 这意味着我们可以将他们混合到表达式中， 特别的是 lambda 表达式

```python
def download(url, log=print):
    log(f'Downloading {url}')
    # ...

def custom_print(*args):
    pass  # Do not print anything



download('/js/app.js', lambda msg: print('[INFO]', msg))
[INFO] Downloading /js/app.js
```
这里传入 log 的 print 函数不需要仔单独定义，而是放在一个 lambda 函数中，方便但是不能在其他地方重复调用。


7. 另外一种表达式是 ternary conditional expression:
```python
>>> user = 'liu'
>>> print('hi') if user is None else print('hi', user)
hi liu

```

----

# 3. 更有个性的 Print

* 格式化复杂的数据结构 添加颜色以及其他修饰， 创建接口， 使用动画，甚至使用文本播放声音


## 1. 漂亮的 print 嵌套的数据结构

1. 使用标准库中的 pprint 模块。 因为他的 print 结果对人类友好，许多工具 Ipytohn jupyterLab 都是默认使用 pprint 代替常规的 print
> 在 Ipython 中切换 pprint ， 使用下面的命令
```python
%pprint
Pretty printing has been turned OFF

%pprint
Pretty printing has been turned ON
```
我们可以导入 pprint ， 这样同时有两个 print 函数
```python
from pprint import pprint as pp

>>> print('hello')
hello
>>> pp('hello')
'hello'
```
观察上面的print 结果，我们可以发现两者不一样， 这是因为 pprint **调用的是 repr()** 来做类型转换 而不是 str()


2. 当我们传入更加复杂的数据的时候，二者的区别会更加明显
```python
pp(data)
{'powers': [0,
            1,
            1024,
            59049,
            1048576,
            9765625,
            60466176,
            282475249,
            1073741824,
            3486784401]}

print(data)
{'powers': [0, 1, 1024, 59049, 1048576, 9765625, 60466176, 282475249, 1073741824, 3486784401]}
```

3. 我们可以调整 pprint 的参数获得更好的自定义结果
```python
cities = {'USA': {'Texas': {'Dallas': ['Irving']}}}

pp(cities, depth= 2)
{'USA': {'Texas': {...}}}
```

4. 对于字典结构的数据， 我们可以转换为 JSON 数据， 使用 json 模块

```python
import json

data = {'username': 'liu', 'password': '1234'}

ugly = json.dumps(data)
pretty = json.dumps(data,indent=4, sort_keys=True)

print(ugly)
{"username": "liu", "password": "1234"}

print(pretty)
{
    "password": "1234",
    "username": "liu"
}

pp(ugly)
'{"username": "liu", "password": "1234"}'
```

5. 要注意的是， pprint 的结构和 print() 完全不一样， 甚至不能传递多余一个的位置参数，可见它只是专注于更好的呈现数据结构。

-----

## 2. 构建控制台用户界面

有一些库提供对终端的高层控制，其中最常用的是 **curses**。 首先，它允许从独立的图形小部件而不是文本块的角度进行思考。此外，你可以自由地表达你内心的想法，因为这就像在一张空白的画布上作画。库隐藏了处理不同终端的复杂性。除此之外，它还支持键盘事件，这可能对编写视频游戏很有用。

**我们利用这个库完成一个贪吃蛇的动画界面**


-----

1. 首先我们导入 curses 模块， 可以修改正在运行的终端的状态

```python
import time, curses

def main(screen):
    time.sleep(1)

if __name__ == '__main__':
    curses.wrapper(main)

```

注意到函数需要接收一个 screen 对象，也可以叫做 stdscr

现在在终端运行上面的程序， 终端会完全变空白一秒， 但是光标还在， 为了隐藏光标，我们调用模块中的配置函数。
```python
curses.curs_set(0)  # Hide the cursor
```


2. 我们创建 snake： 相当于屏幕上一列点

* 注意下x,y 轴和我们常规的是不一样的， 坐标原点在左上角的右手系
```python
snake = [(0,i) for i in reversed(range(20))] # 初始时是一个水平的的，在左下角
```

3. 画出这个 snake

```python
screen.addstr(*snake[0], '@')
for segment in snake[1:]:
    screen.addstr(*segment, '*')
```

4. 画出之后，我们必须显示的刷新屏幕， 屏幕才会显示

```python
screen.refresh()
```
综合代码

```python

import time
import curses

def main(screen):
    curses.curs_set(0)

    snake = [(0,i) for i in reversed(range(20))]

    screen.addstr(*snake[0], '@')
    for seg in snake[1:]:
        screen.addstr(*seg,'*')

    screen.refresh()
    time.sleep(4)


if __name__ == '__main__':
    curses.wrapper(main)

```

![](https://i.loli.net/2019/08/19/hLZuyIYrnm1i4DN.png)


5. 我们希望snake 移动，四个方向，上下左右，最好是方向会随着方向键的敲击而改变，所以你可以把它与库的键码连接起来:

```python
directions = {
    curses.KEY_UP: (-1, 0),
    curses.KEY_DOWN: (1, 0),
    curses.KEY_LEFT: (0, -1),
    curses.KEY_RIGHT: (0, 1),
}
direction = directions[curses.KEY_RIGHT] # 默认方向
```

6.  snake 移动实际上是 头部移动到一个新的位置，其他的部分朝向它移动， 假设要是 snake 没有增长，实际上一个移动就是删掉尾部，在头部加一个新的点

```python
snake.pop()
snake.insert(0, tuple(sum, zip(snake[0], direction))) # 原始坐标加 移动方向的坐标，
# tuple 直接相加会得到一个更大的 tuple， 而不是对应相加

```

7. 我们希望方向随着键盘的敲击改变，所以我们需要调用 getch 获取按下键的代码
```python
direction = directions.get(screen.getch(), direction)
```

8. 然而上面的 getch 是一种阻塞调用， 每次按键 snake 才会移动， 我们需要添加一些配置调用非阻塞

```python
screen.nodelay(True)  # Don't block I/O calls
```

9. 还要注意，为了 snake 看起来是移动而不是增长， 我们每次迭代之前需要显式的擦除屏幕。
```python
screen.erase()

```

完整代码

```python
import time
import curses

def main(screen):
    curses.curs_set(0)
    screen.nodelay(True)

    snake = [(0,i) for i in reversed(range(20))]


    directions = {
        curses.KEY_UP: (-1, 0),
        curses.KEY_DOWN: (1, 0),
        curses.KEY_LEFT: (0, -1),
        curses.KEY_RIGHT: (0, 1),
    }
    direction = directions[curses.KEY_RIGHT]
    while True:
        screen.erase()
        screen.addstr(*snake[0],'@')
        for seg in snake[1:]:
            screen.addstr(*seg, '*')

        snake.pop()
        snake.insert(0, tuple(map(sum,zip(snake[0],direction))))

        direction = directions.get(screen.getch(),direction)

        screen.refresh()
        time.sleep(0.1)


if __name__ == '__main__':
    curses.wrapper(main)
```

![](https://i.loli.net/2019/08/19/iHeMbf7BJwASuPc.gif)


-----

## 3. 创建很酷的动画

要使终端的文本具有动画效果，必须要能够自由的移动光标。
假如动作是限制在单行中的，有两个转义字符序列比较有用
* Carriage return: \r ； 将光标移动到行的最前面
* Backspace: \b ： 光标向左移动一个字符


1. 我们经常使用旋转的指针表示工作正在运行，但是不确切知道还有多长时间完成， 类似于动画片， 我们循环 print 一系列分解的 指针，从而实现动态的效果。

```python
from itertools import cycle
from time import sleep

for frame in cycle(r'-\|/-\|/'):
    print('\r', frame, sep='', end='', flush=True)
    sleep(0.2)
```
整个循环，获取在一个需要 print 的字符，然后将光标移动到行的开始，然后覆盖掉之前存在的字符


![](https://i.loli.net/2019/08/19/cGlftP3ZpYb2jwy.gif)


2. 还有的知道还剩余多少进度，显示进度条
```python
from time import sleep

def progress(percent=0, width=30):
    left = width * percent // 100
    right = width - left
    print('\r[', '#' * left, ' ' * right, ']',
          f' {percent:.0f}%',
          sep='', end='', flush=True)

for i in range(101):
    progress(i)
    sleep(0.1)
```
* 首先计算进度 n% 的时候，展示多少 #，和多少空格，

* 之后使用回车字符， '\r' 擦去显示的内容

> 可以使用 [progressbar](https://pypi.org/project/progressbar2/) 库，更方便跨界创建进度条。

![](https://i.loli.net/2019/08/19/4kQPTt3owijVxYn.gif)

----
----

# 4.  Print Debugging

## 1. Tracing
最基本的 debugging 形式，主要思想是遵循程序执行的路径，直到它突然停止，或给出不正确的结果，以确定有问题的确切指令。可以在相应的关键点加入 print 函数输出一些重要的状态以此来判断问题。

```python
def average(numbers):
    print('debug1:', numbers)
    if len(numbers) > 0:
        print('debug2:', sum(numbers))
        return sum(numbers) / len(numbers)

0.1 == average(3*[0.1])

debug1: [0.1, 0.1, 0.1]
debug2: 0.30000000000000004
False

```
跟踪是一个费力的手工过程，它可能会让更多的错误溜走。构建和部署周期需要时间。之后，需要记住小心翼翼地删除调用 print() 的语句。

其次它需要我们对代码做出修改，如果 debug 一个远端网络的服务，有时候很难获得标准输出。


---

## 2. Logging

当我们进行打印调试时，可以考虑将其转换为永久日志消息。当需要在问题发生后分析问题时，在无法访问的环境中，这可能会有所帮助。

可以将日志看作文本文件。每一行都传递关于系统中某个事件的详细信息。

```text

[2019-06-14 15:18:34,517][DEBUG][root][MainThread] Customer(id=123) logged out

```

日志是有标准格式的， 除了描述性消息之外，还有几个可定制的字段，它们提供了事件的上下文。上面的日志中有确切的日期和时间、日志级别、日志程序名称和线程名称。

日志级别允许您快速过滤消息以减少噪音。例如，如果您正在寻找错误，则不希望看到所有警告或调试消息。

通过日志，可以将 debug 信息和标准输出分离。 所有的日志输出默认输出到 standard error stream。 当然，可以将日志消息重定向到单独的文件。

通常，错误配置的日志会导致服务器磁盘上的空间耗尽。为了防止这种情况，您可以设置log rotation，它将日志文件保存指定的时间，比如一周，或者当它们达到一定的大小时。尽管如此，归档旧日志始终是一个很好的做法。

在 Python 中使用日志相对较为容易， 可以调用标准库中的 logging 模块，

```python
import logging
logging.basicConfig(level = logging.DEBUG)
```
较为常见的做法是为每个文件创建一个日志。

```python
logger = logging.getLogger(__name__)
logger.debug('hello')   # Logger's method

```

从print()函数切换到日志记录的最后一个原因是线程安全。 print 不能很好地处理多线程。


----

## 3. Debugging

真正的 debug 需要一个 debug tool ：
* 交互式地逐步执行代码。
* 设置断点，包括条件断点。
* 查找内存中的变量
* 在运行时计算自定义表达式

pdb 是python 中常用的一个 调试器， 然而 pdb 没有图形界面。 如果你不能编辑代码，你必须运行它作为一个模块，并传递你的脚本的位置:

```bash
python -m pdb my_script.py
```
可以直接在代码中设置断点，它将暂停脚本的执行然后进入调试器


```python
>>> import pdb
>>> pdb.set_trace()
--Return--
> <stdin>(1)<module>()->None
(Pdb)

```

python 3.7 之后，可以调用内置的 breakpoint() 函数，做的是同样的事情，但是有更多的 **bells and whistles**:

```python

>>> def average(numbers):
...     if len(numbers) > 0:
...         breakpoint()  # Python 3.7+
...         return sum(numbers) / len(numbers)
...
>>> average([1,2,3,4])
> <stdin>(4)average()
(Pdb) numbers
[1, 2, 3, 4]
(Pdb)

```

----

# 5. 与 Input 输入 相关的命令

## 1. 内置的 input()

input() 从标准输入流接受数据， 通常是键盘

```python

>>> name = input('Enter your name: ')
Enter your name: liu
>>> print(name)
liu
```

input() 可以不输入任何参数，但是不会显示任何东西，直接输入

```python
>>> x = input()
hello world
>>> print(x)
hello world
```

----

## 2. 标准库中的 getpass，

对于密码等输入，避免显示， 可以使用 getpass()

```python
>>> from getpass import getpass
>>> password = getpass()
Password:
>>> password
'1234'

```

-----

## 3. 第三方库

* bullet

* cooked-input

* prompt_toolkit

* questionnaire

-----







