

训练好的模型使用 save weights 保存权重：

因为自定义损失函数之后，使用 save model 会出错，修改起来比较麻烦（搞了很久没有解决）


```python
model.save_weights('test%d.h5'%(i+1))
```

 再次导入 使用

```python
 model.load_weights('test10.h5',by_name= True)

```

**但是直接导入会报错**

----


**Solve：**

1. 首先构建一个 完全相同的模型 之后compile ，这时候导入 就可能出现：

```python
expects 0 weight(s), but the saved weights have 6 element(s)
```

2. 我的 较笨但是在我这里有效的办法： compile 模型之后， 训练模型 1个 epoch

```python
model.fit()
```

3. 之后再导入 权重，就没有报错，结果和训练好的模型相同
```python
 model.load_weights('test10.h5',by_name= True)
```

