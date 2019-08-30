Python 3.6.5


----


1. 默认 按照 key 值排序
```python
>>> xs = {'a': 4, 'c': 3, 'b': 2, 'd': 1}
>>> sorted(xs.items())
[('a', 4), ('b', 2), ('c', 3), ('d', 1)]
```


2. 指定 按照 value 排序
```python

>>> xs = {'a': 4, 'b': 3, 'c': 2, 'd': 1}
>>> sorted(xs.items(), key = lambda x: x[1])
[('d', 1), ('c', 2), ('b', 3), ('a', 4)]

```


```python
>>> xs = {'a': 4, 'b': 3, 'c': 2, 'd': 1}
>>> import operator
>>> sorted(xs.items(), key=operator.itemgetter(1))
[('d', 1), ('c', 2), ('b', 3), ('a', 4)]

```



----

# 参考

real python --https://realpython.com/