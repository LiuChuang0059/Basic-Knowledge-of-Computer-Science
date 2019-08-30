---


### 1. 同时迭代两个列表

```python
list_1 = [1,3,4]
list_2 = ['first','second','third']
for i ,i_en in zip(list_1,list_2):
	print(str(i)+i_en)

```
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/rmf57.jpg)




----

### 2. 带索引的列表迭代

```python
list_2 = ['first','second','third']
for index ,i_en in enumerate(list_2):
	print(index,i_en)
```
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/4ij4i.jpg)

----

### 3. 列表推导(list comprehensions)

```python
numbers = [1,2,3,4,5,6]
even = [number for number in numbers if number%2 == 0]
```
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/3v6vv.jpg)

----
### 4. 字典推导(Dictionary comprehension

```python
list_2 = ['first','second','third']
dic = {key: value for value ,key in enumerate(list_2)}
print(dic)
```
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/ng6g0.jpg)

----

### 5. 迭代工具 --- itertools

查找所有的组合方式


```python
from itertools import combinations
list_2 = ['first','second','third']
for comb in combinations(list_2,2):
	print(comb)
```

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/emxey.jpg)

----

# 参考

1. https://mp.weixin.qq.com/s/ThktUK0m8tIxk2mTwkR-Tw




