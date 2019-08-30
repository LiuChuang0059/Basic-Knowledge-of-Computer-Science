原文 ： Structural Time Series modeling in TensorFlow Probability  by Dave Moore, Jacob Burnim, and the TFP Team

link : https://medium.com/tensorflow/structural-time-series-modeling-in-tensorflow-probability-344edac24083


----

主要介绍 tfp.sts 这个 library ， 进行时间序列预测


## 1. Structural Time Series (STS) models

结构化时间序列模型是一组应用到时间序列的概率模型， 包括很多标准时间序列模型思想。

* 自回归过程

* 移动平均

* local linear trends

* 季节性

* regression and variable selection on external covariates (other time series potentially related to the series of interest).


一个 STS 模型可以看作是上面各种特性成分的加和。

结构化时间序列通常可以从相对较少的数据(例如，只有一个包含数十个点的输入序列)中产生合理的预测。模型的假设是可解释的，我们可以通过可视化将过去的数据和未来的预测分解为结构组件来解释预测。此外，结构时间序列模型使用概率公式，可以自然地处理丢失的数据，并提供有原则的不确定性量化。

----

## 2. Structural Time Series in TensorFlow Probability

TensorFlow Probability (TFP)  现在支持 ： 利用变分推理(VI)和哈密顿蒙特卡罗(HMC)对模型参数进行贝叶斯推理，计算点预测和预测不确定性。

因为这些内置在 tensorflow 中， 这些方法可以并行化处理 ， 也可以和深度神经网络进行集成。

----

## 3. 实例 ： Forecasting CO2 Concentration

![](https://i.loli.net/2019/08/29/OXLsuZity13NfaS.png)

进过观察可以发现上面的时序数据既有一个长期的趋势又有一定的周期性。 我们可以直接将这两个特性编码到 结构化时间序列模型, 使用 TFP

```python
import tensorflow_probability as tfp
trend = tfp.sts.LocalLinearTrend(observed_time_series=co2_by_month)
seasonal = tfp.sts.Seasonal(
    num_seasons=12, observed_time_series=co2_by_month)
model = tfp.sts.Sum([trend, seasonal], observed_time_series=co2_by_month)

```

这里我们使用了一个局部线性趋势模型，它假设趋势是线性的，随着时间的推移，斜率随随机游走缓慢地演化。将模型与数据进行拟合，得到基于我们建模假设的概率预测:

![](https://i.loli.net/2019/08/29/Pyf9TZcgsezFLpj.png)

可以看出，随着线性趋势模型对斜率外推的信心降低，预测的不确定性随着时间的推移而增加。平均预测结合了季节变化和现有趋势的线性外推，真实值在95%的预测区间内。



**[完整代码](https://github.com/tensorflow/probability/blob/master/tensorflow_probability/examples/jupyter_notebooks/Structural_Time_Series_Modeling_Case_Studies_Atmospheric_CO2_and_Electricity_Demand.ipynb)**





















