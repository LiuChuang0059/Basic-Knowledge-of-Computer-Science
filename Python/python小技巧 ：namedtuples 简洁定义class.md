Python 3.6.5

----

使用 nametuples 来定义一个 class

```python
>>> from collections import namedtuple
>>> Student = namedtuple('Student','age score')
>>> liu = Student(18,90)
>>> liu.age
18
>>> liu.score
90
>>> liu
Student(age=18, score=90)


```



namedtuple 创建的 类和 tuples 相同， 不可进行更改

```python

>>> liu.score = 89
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
AttributeError: can't set attribute

```