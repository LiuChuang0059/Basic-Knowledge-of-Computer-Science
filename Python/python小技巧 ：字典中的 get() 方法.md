Python 3.6.5

----

```Python


name_id = {
        1:"liu",
        2:"zou",
        3:"li",
}


def hello(id):
	return "Hello, {}".format(name_id.get(id,"Everybody"))

>>> hello(1)
'Hello, liu'
>>> hello(111)
'Hello, Everybody'
>>>


```


* get() 函数被调用的时候， 先回检查提供的 key 是否存在

1. 如果存在，： 返回 key 对应的 value

2. 如果不存在： 返回  default 值


# 参考

real python --https://realpython.com/

