本文为 极客时间 景霄老师 的《python 核心技术与实战》课程 学习笔记

----


**将功能模块化，文件化，将不同的功能在大型工程中搭建起来**

## 1. 简单模块化

将不同的功能放在不同的 py 文件中， 不同的文件放在不同的文件夹中


创建一些文件夹 ：

```python
# utils/utils.py

def get_sum(a, b):
    return a + b

```

```python

# utils/class_utils.py

class Encoder(object):
    def encode(self, s):
        return s[::-1]

class Decoder(object):
    def decode(self, s):
        return ''.join(reversed(list(s)))

```


```python

# src/sub_main.py

import sys
sys.path.append("..")

from utils.class_utils import *

encoder = Encoder()
decoder = Decoder()

print(encoder.encode('abcde'))
print(decoder.decode('edcba'))

########## 输出 ##########

edcba
abcde
```

文件结构

```python

.
├── utils
│   ├── utils.py
│   └── class_utils.py
├── src
    └── sub_main.py
```


* 调用子目录的模块，只需要 使用 .  即可

* 调用上层目录 ， sys.path.append("..") ， 即可调用上层的 utils 模块

* import 同一个模块 只会被之心一次， 防止重复导入的问题

* 在 python3 中，模块所在的文件夹中不需要 __init__.py 文件

-----


## 2. 项目模块化

在大型工程中， 要尽可能的使用绝对路径， 最好从项目的根目录开始追溯， （相对的绝对路径）,所有的模块的调用， 都要通过根目录一层层向下索引的方式 import。



```python

.
├── proto
│   ├── mat.py
├── utils
│   └── mat_mul.py
└── src
    └── main.py

```


```python

# proto/mat.py

class Matrix(object):
    def __init__(self, data):
        self.data = data
        self.n = len(data)
        self.m = len(data[0])
```

```python

# utils/mat_mul.py

from proto.mat import Matrix

def mat_mul(matrix_1: Matrix, matrix_2: Matrix):
    assert matrix_1.m == matrix_2.n
    n, m, s = matrix_1.n, matrix_1.m, matrix_2.m
    result = [[0 for _ in range(n)] for _ in range(s)]
    for i in range(n):
        for j in range(s):
            for k in range(m):
                result[i][k] += matrix_1.data[i][j] * matrix_2.data[j][k]

    return Matrix(result)
```

可以直接 from proto.mat import Matrix， 不用退回上级目录


python 解释器在遇到 import 的时候，会在一个特定的列表中寻找模块，列表的获取方式：

```python

import sys

print(sys.path)


['', '/Users/liuchuang/anaconda3/lib/python36.zip', '/Users/liuchuang/anaconda3/lib/python3.6', '/Users/liuchuang/anaconda3/lib/python3.6/lib-dynload', '/Users/liuchuang/.local/lib/python3.6/site-packages', '/Users/liuchuang/anaconda3/lib/python3.6/site-packages', '/Users/liuchuang/anaconda3/lib/python3.6/site-packages/aeosa', '/Users/liuchuang/anaconda3/lib/python3.6/site-packages/python_graph_core-1.8.2-py3.6.egg', '/Users/liuchuang/anaconda3/lib/python3.6/site-packages/python_graph_dot-1.8.2-py3.6.egg', '/Users/liuchuang/anaconda3/lib/python3.6/site-packages/pydot-1.2.4-py3.6.egg']

```


1. 第一项为空， Pycharm 中， 将第一项设置为项目根目录的绝对地址。 import 执行的时候都会去项目根目录中寻找相应的包。



2. 如果想在普通的 Python 运行环境中也可以

2-1  强行修改位置

```python

import sys

sys.path[0] = '/home/ubuntu/workspace/your_projects'

```

2-2 修改 PYTHONHOME 。

对于每一个项目，最好有一个独立运行环境保持 包盒模块的纯净

在 Virtual Environment 里面 ，找到 activate 文件， 文件末尾加入

```python
import sys

sys.path[0] = '/home/ubuntu/workspace/your_projects'

```

---

## 3. if __name__ == '__main__'

python 是脚本语言， 不需要显式的提供 main 函数的入口 : main(){}

```python

.
├── utils.py
├── utils_with_main.py
├── main.py
└── main_2.py

```


```python

# utils.py

def get_sum(a, b):
    return a + b

print('testing')
print('{} + {} = {}'.format(1, 2, get_sum(1, 2)))

```

```python

# utils_with_main.py

def get_sum(a, b):
    return a + b

if __name__ == '__main__':
    print('testing')
    print('{} + {} = {}'.format(1, 2, get_sum(1, 2)))

```

```python

# main.py

from utils import get_sum

print('get_sum: ', get_sum(1, 2))

########## 输出 ##########

testing
1 + 2 = 3
get_sum: 3
```


```python

# main_2.py

from utils_with_main import get_sum

print('get_sum: ', get_sum(1, 2))

########## 输出 ##########

get_sum_2: 3

```

假如， 我们在导入文件的时候， 只想使用部分功能， 但是 python 会自动的把暴露在外面的代码全部都执行一遍。

放在 if __name__ == '__main__': 下面， 被调用的时候， 就不会执行。

__name__ 作为 python 的魔术内置函数参数， 实际上是模块对象的 一个属性。 使用 import 语句的时候， __name__ 会被复制成为 改模块的名字，就不会等于 __main__,

----


## 4.

```python

from module_name import *

import module_name


```


* import * 是把module 中所有的函数和类都会导入， 和其他的函数名类的名字可能会出现冲突

* import module ， 也会导入所有的函数和类， 但是调用的时候使用 model_name.func ，等于加了一层 layer，避免冲突












