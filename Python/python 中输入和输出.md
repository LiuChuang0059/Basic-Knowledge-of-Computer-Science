本文为 极客时间 景霄老师 的《python 核心技术与实战》课程 学习笔记

----

## 1. 输入输出基础

1. input() 函数


input 函数暂停程序的运行，等待键盘的输入，

输入的类型永远是字符串型(str)

需要输入数字需要 int ，float 等强制转换

-----



## 2. 文件的输入输出

计算机文件访问的基础知识：

1. open() 函数拿到文件的指针， 第一个参数是文件的绝对位置或者相对位置；第二个参数 表示文件读取。

```python
with open('test.txt','r') as fin

```

PS： 使用 open() 函数，完成读取任务之后需要立即关掉它， 使用了 with 语句，就不需要显示的调用 close().

2. 拿到文件的指针之后， 通过 read() 函数，读取文件的全部内容

```python

text = fin.read()
```
* 这样读取如果文件过大， 一次性读取可能造成内存崩溃

read() 函数加入 size 参数
readline() 函数每次读取一行

----


## 3. JSON 序列化


JSON ： 轻量级数据交换格式，所有的事情(字符串，整数型，浮点数型，布尔型变量)都使用 字符串表示。

* 输入 所有类型数据， 输出一个字符串
* 输入一个字符串， 输出所有的信息


```python

>>> import json
>>> liu = {"name":"liu","age" : 23, "height": 80, "gender":"male"}
>>> liu_str = json.dumps(liu)
>>> print("type of liu_str is {}, liu_str is {}.".format(type(liu_str),liu_str))

type of liu_str is <class 'str'>, liu_str is {"name": "liu", "age": 23, "height": 80, "gender": "male"}.

>>> original_liu = json.loads(liu_str)

>>> print("type of original_liu is {}, liu is {}.".format(type(original_liu),original_liu))
type of original_liu is <class 'dict'>, liu is {'name': 'liu', 'age': 23, 'height': 80, 'gender': 'male'}.
```

json.dumps() : 接收python 的基本数据类型， 序列化为 string

json.loads() : 接收字符串， 反序列化为 python的 基本数据类型
































