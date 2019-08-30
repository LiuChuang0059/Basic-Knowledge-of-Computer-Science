
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/axt54.JPG)

---

搬运自 https://stackoverflow.com/questions/14379753/what-does-mean-in-python-function-definitions

-----

## 1. 函数的注释

类似于 Python2 中的 docstrings，python3 中用来描述函数的参数和 return values

例如：
1. 用来声明参数的 type
2. Another is to allow parameter-specific documentation instead of encoding it into the docstring.
3. python 解释器不会对注解添加任何的语意，运行和没有添加注释时候一样


## 2. Example

1. 如下面的函数，是一个简单的动能定理公式，
输入的有 m 质量； 速度 v
返回的是能量 e 单位是焦耳 Joules

```python
def kinetic_energy(m:'in KG', v:'in M/S')->'Joules':
...    return 1/2*m*v**2


```

2. 我们可以使用 .__annotations__  调用 注释信息


```python

kinetic_energy.__annotations__
Out[2]: {'m': 'in KG', 'v': 'in M/S', 'return': 'Joules'}

```

3. .__annotations__  返回的类型是 Dictionary ,使用 key 可以调用结果

```python

kinetic_energy.__annotations__['return']
Out[3]: 'Joules'

kinetic_energy.__annotations__['v']
Out[4]: 'in M/S'
```

4. 我们可以在添加注释的时候，就直接添加一个字典结构的注释

```python

rd={'type':float,'units':'Joules','docstring':'Given mass and velocity returns kinetic energy in Joules'}

def f()->rd:
    pass
f.__annotations__['return']['docstring']
Out[8]: 'Given mass and velocity returns kinetic energy in Joules'

f.__annotations__['return']['units']    ## 双重字典嵌套
Out[9]: 'Joules'

```


## 3. Future ：

> The fastest conceivable scheme would introduce silent deprecation of non-type-hint annotations in 3.6, full deprecation in 3.7, and declare type hints as the only allowed use of annotations in Python 3.8.

在未来的版本中   -> 可能只能用来 说明返回的 类型 Type

```python
def f(x) -> int:
    return x
```


而不能是

```python

def f(x) -> 123:
    return x

```

-----










