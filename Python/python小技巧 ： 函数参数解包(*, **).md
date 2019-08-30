Python 3.6.5


----


## 1. tuple and list

```python
def print_vector(x,y,z):
	print(" ( {},{},{} )".format(x,y,z))

```

1. 直接打印 tuple 会报错,因为值传递了一个值 tuple

```python
>>> tuple_vec = (1,2,3)
>>> print_vector(tuple_vec)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: print_vector() missing 2 required positional arguments: 'y' and 'z'

```

2. 手动逐个给定

```python
print_vector(tuple_vec[0],tuple_vec[1],tuple_vec[2])
 ( 1,2,3 )


```


3. 使用 * , 达到同样的效果

```python

print_vector(*tuple_vec)
 ( 1,2,3 )

```



4. 同理 list

```python
>>> list_vec = [1,2,3]
>>> print_vector(*list_vec)
 ( 1,2,3 )

```



## 2. 字典



3.6+ 版本，字典是有顺序的

```python
>>> dict_vec = {'x': 1, 'y': 2, 'z': 3}
>>> print_vector(*dict_vec)
 ( x,y,z )
>>> print_vector(**dict_vec)
 ( 1,2,3 )
```




**向一个函数中 传递不同的数据类型**

----

# 参考

real python --https://realpython.com/