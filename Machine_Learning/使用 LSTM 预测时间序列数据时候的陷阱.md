
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/k361e.jpg)

---

**原文link** --- https://towardsdatascience.com/how-not-to-use-machine-learning-for-time-series-forecasting-avoiding-the-pitfalls-19f9d7adf424

----


### 1. 时间序列预测的机器学习模型

LSTM ： 一种特殊的神经网络，通过之前的数据来进行预测
然而， 通常好像更简单的模型例如：随机森林，gradient boosting regressor ， 时间延迟神经网络等等

### 2. 例子： 时间序列数据预测
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/dlmrk.jpg)

观察上图，预测结果和真实的结果很接近，具有较好的准确性。

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/6p5q5.jpg)

为了更准确一点， 我们可以通过plot 预测的 和实际的 数值来评估模型的精确度。



----

### 3. 可能这个模型是错的


在本例中，为了便于说明，显式地选择了数据来表示实际上无法预测的数据。更具体地说，我称之为“股票指数”的数据实际上是用随机游走过程建模的
顾名思义，随机游走是一个完全随机的过程。因此，使用历史数据作为训练集来学习行为和预测未来结果的想法是不可能的。既然如此，那么这个模型怎么可能给出如此准确的预测呢?随着我将更详细地讨论，这一切都归结于对精度度量的(错误)选择。


### 4. 时间延迟预测 和自相关

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/ynx5n.jpg)


时间序列数据具有时间相关性，且具有显著的自相关性。在这种情况下，这意味着时间t+1的值很可能接近时间t的值。正如上图所示，模型实际所做的是，在预测“t+1”时刻的值时，它只是使用“t”时刻的值作为预测(通常称为持久性模型)。通过绘制预测值与实际值之间的相互关系(下图)，我们可以看到在1天的滞后时间内出现了一个清晰的峰值，这表明模型只是使用之前的值作为对未来的预测

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/8vwzn.jpg)


### 5. 时间序列数据的平稳性

平稳时间序列是指其统计特性，如均值、方差、自相关等，在一段时间内都是恒定的。大多数统计预测方法都是基于时间序列可以近似平稳的假设(即(“固定的”)通过数学变换的使用。一个这样的基本转换，对数据进行时间差分，如下图所示。

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/bfln1.jpg)



### 6. 时间差分数据 的预测模型

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/1svai.jpg)

预测时间差分数据，而不是直接预测数据，模型的预测能力更强

图片显示，模型不能够基于历史事件来预测未来的变化，这是我们想要看到的结果，因为我们使用的数据是来自统计的随机行走过程，这个过程未来是几乎不可预测的。




### 7. 时间序列是否是一个随机游走过程


* 时间序列具有很强的时间相关性(自相关)，线性衰减或以类似的模式衰减。

* 时间序列是非平稳的，使其平稳在数据中没有明显的可学习结构。

* 持久性模型(将上一个时间步骤的观察结果作为下一个时间步骤的观察结果)提供了可靠预测的最佳来源

使用持久性模型的基线预测可以快速表明您是否可以做得更好。如果你不能，你可能正在处理一个随机游走(或接近它)

































