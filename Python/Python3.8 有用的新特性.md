原文： Awesome New Python 3.8 Features

link ： https://medium.com/fintechexplained/awesome-new-python-3-8-features-ed027416f2a5

-----


## 1.  赋值表达式 **:=**

* 将值赋值给未初始化的变量

```python
if (my_variable := get_input()) is not None :
    print(my_variable) #exists now
```

my_variable 没有提前初始化， **:=** 用来声明和初始化 my_variable。
get_input()函数的返回值用来给变量赋值。

**This feature is available in list comprehensions and statement forms.**

* 确保变量的赋值在圆括号内
```python
my_variable := get_input() #Invalid
```

* = 和 := 之间 逗号的优先级不同

```python
my_variable = a, b, c #  my_variable = (a, b, c)
(my_variable := a, b, c)  #  my_variable = a
```


----

## 2. 限制必须按照位置传递参数

* 使用 "/"

```python
def add(a, b, c, d=None, /):
    x = a+b+c
    if d is not None:
        x = x+d
    return x
```

* add(a=1,b=2,c=3) or add(1,2,3,d=4) 都是无效的调用

* add(1,2,3) and add(1,2,3,4) 是有效的

这个功能看起来没什么用。
使用位置函数，API编写器可以重命名函数的参数(如果需要)，而不破坏API调用者的代码。因此，它鼓励API向后兼容。

----

## 3. f-strings  支持 =

* f'{expr = }'

= 实际上是对表达式求值然后输出结果

```python
input = 100
output = 50
print(f'{input-output=}')
```

输出结果是： **input-output=50**

----

















----

# 参考

1. [PEP 572 -- Assignment Expressions](https://www.python.org/dev/peps/pep-0572/)