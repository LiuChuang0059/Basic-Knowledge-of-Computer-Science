原文 ： Python 3's f-Strings: An Improved String Formatting Syntax (Guide)


link 🔗： https://realpython.com/python-f-strings/


---

在Python 3.6中，f-string是格式化字符串的一种很好的新方法。它们不仅比其他格式更具可读性、更简洁、更不易出错，而且速度更快!


----

# 1.  Old school 字符串格式化方法

## 1. 使用 %-formatting

%-formatting是不推荐使用的

> “The formatting operations described here exhibit a variety of quirks that lead to a number of common errors (such as failing to display tuples and dictionaries correctly).

> Using the newer formatted string literals or the str.format() interface helps avoid these errors. These alternatives also provide more powerful, flexible and extensible approaches to formatting text.”

1. 假设需要使用 ：

```python
>>> name = 'liu'
>>> 'hello, %s' %name
'hello, liu'
```

2. 当插入不止一个变量的时候，必须使用一个 tuple 表示这些变量

```python
>>> 'hello %s , you are %s' %(name,age)
'hello liu , you are 18'
```

3. 当我们插入更多更长的变量的时候， 代码的可读性会变的很差.

```python

>>> first_name = "Chuang"
>>> last_name = "Liu"
>>> age = 18
>>> profession = "comedian"
>>> affiliation = "Monty Python"
>>> "Hello, %s %s. You are %s. You are a %s. You were a member of %s." % (first_name, last_name, age, profession, affiliation)
'Hello, Chuang Liu. You are 18. You are a comedian. You were a member of Monty Python.'
```

4. **而且这种方法不能正确的显示 tuple 和 dictionary**


-------

## 2. str.format()

它使用普通的函数调用语法, 通过 __format__() 方法将对象转换成为 string

1. str.format() 被替换的部分使用 {} 占位

```python
>>> 'hello {}, you are {}'.format(name, age)
'hello liu, you are 18'
```


2. 可以插入索引选择变量的顺序
```python

>>> 'hello {1}, you are {0}'.format(age, name)
'hello liu, you are 18'
```

3. 可以插入变量名
```python
>>> person = {'name': 'liu', 'age': 18}
>>> "Hello, {name}. You are {age}.".format(name=person['name'], age=person['age'])
'Hello, liu. You are 18.'
```

> 上面的操作可以使用 ** 对字典解包

```python
>>> "Hello, {name}. You are {age}.".format(**person)
'Hello, liu. You are 18.'
>>>
```

**相比第一代，已经好很多了，但是对于多参数，长字符处理起来还是很冗杂**

```python
>>> first_name = "Chuang"
>>> last_name = "Liu"
>>> age = 18
>>> profession = "comedian"
>>> affiliation = "Monty Python"
>>> print(("Hello, {first_name} {last_name}. You are {age}. " +
>>>        "You are a {profession}. You were a member of {affiliation}.") \
>>>        .format(first_name=first_name, last_name=last_name, age=age, \
>>>                profession=profession, affiliation=affiliation))
'Hello, Chuang Liu. You are 18. You are a comedian. You were a member of Monty Python.'
```
> 上面的写成字典形式会好很多

```python
>>> print(("Hello, {first_name} {last_name}. You are {age}. " +
...        "You are a {profession}. You were a member of {affiliation}.") \
...        .format(**person))
Hello, Chuang Liu. You are 18. You are a comedian. You were a member of Monty Python.

```


----

# 2.  f-strings ; 更好的 字符串格式化方式

f-strings ： formatted string literals,

在开头有个 f, 里面有大括号{} 包含要被替换的值。

表达式在运行时计算，然后使用_format__协议进行格式化



## 1. 简单的语法

类似于 str.format, 但是更加简洁

```python
>>> name = 'liu'
>>> age  = 18
>>> f" hello, {name}, you are {age} ."
' hello, liu, you are 18 .'
```



## 2.任意的表达式

因为 f-stirngs是在运行的时候计算， 可以在括号里放置任何有效的 python 表达式


1. 可以直接写入括号
```python
>>> f'{2*2+3}'
'7'
>>>
```

2. 可以调用函数

```python
>>> def add_number(a,b):
...     return a+b

>>> a , b = 1, 2
>>> f'{a} + {b} = {add_number(a,b)}'
'1 + 2 = 3'
```

3. 可以直接调用 内置的 method
```python
>>> name= 'LIU'
>>> f"{name.lower()} is funny."
'liu is funny.'

```

4. 甚至可以是 class 里面的对象

```python
class Person:
    def __init__(self, first_name, last_name, age):
        self.first_name = first_name
        self.last_name = last_name
        self.age = age

    def __str__(self):
        return f"{self.first_name} {self.last_name} is {self.age}."

    def __repr__(self):
        return f"{self.first_name} {self.last_name} is {self.age}. repr is calling!"


liu = Person('Chuang','liu',18)

f'{liu}'
Out[3]: 'Chuang liu is 18.'

```

__str__() 和 __repr__() 方法将对象展示为字符，在 class 定义中， 确保至少包括其中一个方法。 如果只能选一个，建议使用 __repr__() ，因为它可以代替 __str__() .

__str__() 的返回值是对象的一个非正式的字符表示，应该具有可读性
__repr__() 的返回值是一个官方正式的表达。

默认的 f-strings 使用 __str__() , 可以使用转换标志  !r  强制使用 __repr__()

```python

f'{liu}'
Out[3]: 'Chuang liu is 18.'

f'{liu!r}'
Out[4]: 'Chuang liu is 18. repr is calling!'

```

## 3. 多行的 f-strings

每一行开头都需要放置一个 f

```python

name = "liu"
profession = "comedian"
affiliation = "Monty Python"
message = (
    f"Hi {name}. "
    f"You are a {profession}. "
    f"You were in {affiliation}."
)



message
Out[6]: 'Hi liu. You are a comedian. You were in Monty Python.'

```

如果不放 f ， {} 就会是代表原始的意义

```python
name = "liu"

profession = "comedian"
affiliation = "Monty Python"
message = (
    f"Hi {name}. "
    "You are a {profession}. "
    "You were in {affiliation}."
)



message
Out[8]: 'Hi liu. You are a {profession}. You were in {affiliation}.'
```

使用 \ 作多行分割

```python
>>> message = f"Hi {name}. " \
...           f"You are a {profession}. " \
...           f"You were in {affiliation}."
>>> message
'Hi liu. You are a comedian. You were in Monty Python.'
>>> print(message)
Hi liu. You are a comedian. You were in Monty Python.
```


```python
>>> message = f"""
...     Hi {name}.
...     You are a {profession}.
...     You were in {affiliation}.
... """
>>> message
'\n    Hi liu. \n    You are a comedian. \n    You were in Monty Python.\n'
>>> print(message)

    Hi liu.
    You are a comedian.
    You were in Monty Python.

>>>
```


## 4. 运行速度

f-string是在运行时计算的表达式，而不是常量值。

在运行时，大括号内的表达式在其自己的范围内求值，然后与f-string的字符串文字部分放在一起。然后返回结果字符串。

> “F-strings provide a way to embed expressions inside string literals, using a minimal syntax. It should be noted that an f-string is really an expression evaluated at run time, not a constant value. In Python source code, an f-string is a literal string, prefixed with f, which contains expressions inside braces. The expressions are replaced with their values.”

例如下面👇：

```python
>>> def greet_f(name):
...     return f'hello {name} !'
...
>>> greet_f('liu')
'hello liu !'

```

当拆开函数并检查后台发生了什么，会看到函数中的 f-string 被转换成类似于下面的代码:

```python
>>> def greet(name):
...     return 'hello ' + name + ' !'
...
>>> greet('liu')
'hello liu !'
```
实际的实现稍微快一些，因为它使用BUILD_STRING操作码作为优化。但功能上是一样的:


使用 dis 模块对代码进行简单分析
```python
>>> import dis
>>> dis.dis(greet)
  2           0 LOAD_CONST               1 ('hello ')
              2 LOAD_FAST                0 (name)
              4 BINARY_ADD
              6 LOAD_CONST               2 (' !')
              8 BINARY_ADD
             10 RETURN_VALUE

>>> dis.dis(greet_f)
  2           0 LOAD_CONST               1 ('hello ')
              2 LOAD_FAST                0 (name)
              4 FORMAT_VALUE             0
              6 LOAD_CONST               2 (' !')
              8 BUILD_STRING             3
             10 RETURN_VALUE
```

看到greet_f() 中多了 BUILD_STRING  操作码

> Python 代码先被编译为字节码后，再由Python虚拟机来执行字节码，python语句会对应若干的字节码指令，虚拟机一条一条执行,完成程序执行。

> python dis 模块对python代码进行反汇编 生成字节码指令

> * 第一列数字代表源代码的行数
> * 第二列数字代表 字节码的索引， 指令LOAD_CONST在0位置， 后面是指令所对应的人类可读的名字
> * 第三列数字表示指令的参数， 后面是实际的参数








运行速度比较 ： Macos

```python
>>> import timeit
>>> timeit.timeit("""name = "liu"
... age = 18
... '%s is %s.' % (name, age)""", number = 10000)
0.002527003000068362
```


```python
>>> timeit.timeit("""name = "liu"
... age = 18
... '{} is {}.'.format(name, age)""", number = 10000)
0.0042402640010550385
```

```python
>>> timeit.timeit("""name = "liu"
... age = 18
... f'{name} is {age}.'""", number = 10000)
0.0022931600014999276
```


可以看到 f-string 会稍快一些


----

# 3.  一些关于 f-strings 的细节


## 1. 引用符号


可以使用各种各样的符号，只要不和外面的符号重复

```python
>>> f"{'liu'}"
'liu'

>>> f'{"liu"}'
'liu'

>>> f"""liu"""
'liu'

>>> f'''liu'''
'liu'

>>> f'"liu"'
'"liu"'

>>> f"'liu'"
"'liu'"

```

如果需要使用相同的， 可以转义


```python
>>> f"The \"old man \" is  actually {age}"
'The "old man " is  actually 18'
```

## 2. 处理字典

```python
person = {'name': 'liu', 'age': 18}
>>> f"The person {person['name']} is {person['age']} years old"
'The person liu is 18 years old'
```
注意 字典的引号 要和 f-Strings 的引号不一样， 不然会把 字典的引号当作 f-Strings 的结束，导致错误

```python
>>> f'The person {person['name']} is {person['age']} years old'
  File "<stdin>", line 1
    f'The person {person['name']} is {person['age']} years old'
                             ^
SyntaxError: invalid syntax
```


## 3. 处理括号

如果想在字符中展示 {} ，必须使用双层

```python
>>> f"{{'liu'}}"
"{'liu'}"

>>> f"{{"liu"}}"
  File "<stdin>", line 1
    f"{{"liu"}}"
           ^
SyntaxError: invalid syntax
>>>
```

但是 使用 三层的 {} 也只会展示 一层, 测试结果应该是层数除2 向下取整。

```python

>>> f"{{{'liu'}}}"
'{liu}'

>>> f"{{{{'liu'}}}}"
"{{'liu'}}"

>>> f"{{{{{'liu'}}}}}"
'{{liu}}'

>>> f"{{{{{{'liu'}}}}}}"
"{{{'liu'}}}"

```


## 4. 行内注释

表达式不允许行内注释

```python
>>> f"liu is {18  #age!}."
  File "<stdin>", line 1
SyntaxError: f-string expression part cannot include '#'
```


----

# 参考

1. [Python String Formatting Best Practices](https://realpython.com/python)

2. [Python dis 模块初步使用](https://blog.csdn.net/qs9816/article/details/51661659)

3. [https://learnku.com/docs/pymotw/dis-python-bytecode-disassembler/3489](https://learnku.com/docs/pymotw/dis-python-bytecode-disassembler/3489)





















