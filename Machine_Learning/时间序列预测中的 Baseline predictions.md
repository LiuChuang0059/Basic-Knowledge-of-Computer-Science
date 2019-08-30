![](https://github.com/LiuChuang0059/large_file/blob/master/pic/65pl7.jpg)

---


**原文 link** --- https://machinelearningmastery.com/persistence-time-series-forecasting-with-python/

----



### 1. 预测效果的 Baseline

这个 baseline 是一个参考点，如果其他的模型表现效果不如或者和 baseline 差不多，那么模型需要被修正或者丢弃。

在进行建立基线模型前，需要准备好 数据集，采样方法，效果评估方法（MSE）。之后选择一个简单的原始的方法进行建模预测，并且计算baseline 的表现，便于自己理解数据集，更好的开发新的模型。

适合 baseline 预测方法的 三个特性：

*  简单

* 快速

* 可重复性： 给定同样的输入，得到相同的输出

**用于建立 baseline的算法 称为 persistence algorithm**

---



### 2. Persistence Algorithm

对于监督学习最常用的 baseline 方法就是 Zero Rule 算法 --简单的说就是根据问题自定义合适的函数
> 参考 https://machinelearningmastery.com/implement-baseline-machine-learning-algorithms-scratch-python/


1. 数据准备

```
     t-1    t+1
0    NaN  266.0
1  266.0  145.9
2  145.9  183.1
3  183.1  119.3
4  119.3  180.3

```

2. 定义 persistence 模型 为一个函数 ： 返回输入值的函数

```python
# persistence model
def model_persistence(x):
	return x

```

3. 之后不需要模型训练，直接进行预测 计算 MSE（ Mean Squared Error）

```python
# walk-forward validation
predictions = list()
for x in test_X:
	yhat = model_persistence(x)
	predictions.append(yhat)
test_score = mean_squared_error(test_y, predictions)
print('Test MSE: %.3f' % test_score)
```

4. plot  persistence 模型预测的结果（就是我们定义的返回输入值（t-1时刻值）的函数）；可以看到就是我们设计的 推后一天

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/dgw05.jpg)


-----



### 3. why

persistence 模型是 naive的 ，通常也被叫做 naive forecast

他没有利用到 他处理的时间数据的细节，所以很容易快速的实现。

**This is useful because these ideas can become input features in a feature engineering effort or simple models that may be combined in an ensembling effort later.**

































































