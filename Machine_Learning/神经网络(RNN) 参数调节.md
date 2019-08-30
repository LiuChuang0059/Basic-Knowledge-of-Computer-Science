
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/rvxla.JPG)

----


原文 ：https://karpathy.github.io/2019/04/25/recipe/

同时推荐作者的另一篇极好的博客： The Unreasonable Effectiveness of Recurrent Neural Networks
https://karpathy.github.io/2015/05/21/rnn-effectiveness/

----
----

## 1.神经网络经常无声无息的失败

1. 只有在运气好的时候，错误配置的神经网络才会抛出异常，大多数情况下，它会继续训练，但默默地使运行变糟。

例如：
* 正则化强度
* 学习率
* 衰减率
* 模型大小
等设置

**Solve：** 对所有可能的事情进行可视化


## 2. 如何训练一个神经网络

从简单到复杂构建，每一步都要对将要发生的事情进行具体的假设，然后通过试验进行验证


### 1. 整理数据

1. 浏览数据，了解数据的分布：
数据中的重复，错误数据，数据的不平衡 等等

由于神经网络实际上是数据集的压缩/编译版本，因此你能够查看你的网络预测并了解它们可能来自何处。如果你的网络给你的预测与你在数据中看到的不一致，那就是有什么地方错了。



### 2. 建立 端到端的训练

我们希望训练这个网络，可视化损失，任何其他指标(例如准确性)，模型预测，并在此过程中执行一系列带有明确假设的消融实验。


**Tips**


* 固定 random seed  ： 有利于得到相同的结果

* 简化数据，先不要想着数据增强

* 正确的初始化：
正确的初始化最后一层权重。 最后的回归值均值是50 ，我们在初始化的时候，设定 最后的 bias = 50
如果数据集不平衡，例如 positive： negative = 1:10 ，在 logits 上设定 偏置，使得网络预测的初始概率是 0.1. 并且消除 “hocky stick” 损失曲线（初始的几次迭代，网络只是在学习 bias）

> 几种初始化
uniform均匀分布初始化：
w = np.random.uniform(low=-scale, high=scale, size=[n_in,n_out])
Xavier初始法，适用于普通激活函数(tanh,sigmoid)：scale = np.sqrt(3/n)He初始化，适用于ReLU：scale = np.sqrt(6/n)normal高斯分布初始化：
w = np.random.randn(n_in,n_out) * stdev # stdev为高斯分布的标准差，均值设为0
Xavier初始法，适用于普通激活函数 (tanh,sigmoid)：stdev = np.sqrt(n)He初始化，适用于ReLU：stdev = np.sqrt(2/n)svd初始化：对RNN有比较好的效果。



* 人工 baseline ：

* input-indepent baseline。训练一个独立于输入的基线(例如，最简单的方法就是将所有输入设置为零)。这应该比实际插入数据而不将其归零的情况更糟。也就是说，可以知道你的模型是否学会从输入中提取任何信息?

* 在单个批数据上过拟合。只过拟合包含少量例子(例如只有两个)的一个batch。为此，我们需要增加模型的大小(例如添加层或过滤器)，并验证我们可以达到的最低损失值(例如0)。同一个图中可视化 label 和预测值，并确保一旦达到最小损失，它们最终会完美地对齐。如果没有完美对齐，那么在某个地方就有一个bug，我们无法继续到下一个阶段。

* 验证训练损失的下降。在这个阶段，对于数据集建模很可能欠拟合，因为该阶段的模型是一个玩具模型。试着增加一点它的容量，在看看训练损失是否下降了。

* 在模型预测之前进行可视化：可视化输入数据，labels，输出数据等

* 可视化预测的动态结果： 在一个测试集上面 动态的可视化预测结果。 如果可视化的结果摆动太多次，可能是网络很难去拟合数据



### 3. 过拟合

在这个阶段，我们已经对数据集有一个很好的理解，同时我们有一个完整的训练-评估pipeline

找到一个好的模型可以分为两个阶段：
1. 首先 让模型尽可能的大，即使可能会过拟合，在这个过程我们只关心 training loss
2. 之后损失一些 training loss 提高 validation loss

使用这种方法的原因： 如果我们使用任何模型都没办法很小的 loss error， 说明我们的模型存在一定的问题。


**Tips**

* 选择合适的模型 ： Don not be a hero: 不要急躁。 先去寻找最相关的文献，使用文献中的模型结构进行测试。然后再去自定义创造 layer 等

* adam is safe： 早起设定baseline 的时候，通常选取 adam with 学习率 3E-04。
如果是RNN或者相关的序列模型， 最常用的就是 Adam

* 从简单到复杂，如果有很的输入信号，每次只输入一个，或者先输入一个小的图片，再输入大的

* 尽量减少使用默认的学习率下降： 如果是从其他领域哪来的代码直接应用，就要小心学习率下降，
可以使用 恒定的学习率 tune this all the way at the very end.

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/4x00z.png)

学习率太高，损失函数在某点开始震荡，不会收敛
学习率太低，模型将花费太长的时间来收敛。

**寻找学习率的范围**

在每个 iteration 之后线性的增加 学习率，例如（1e-7 到 1e-1） 在每个 iteration之后，评估 loss。使用对数坐标绘制 loss

The idea is to start with small learning rate (like 1e-4, 1e-3) and increase the learning rate after each mini-batch till loss starts exploding.

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/sgl0s.png)

梯度最陡的区域 即为最合适的

> 训练集有1000个样本，batchsize=10，那么：训练完整个样本集需要：100次iteration，1次epoch。


* 梯度剪裁： 限制最大的梯度






### 4. 调整
牺牲一些 training loss 提高 validation的精度

**Tips**

* 获取更多的真实数据

* 数据增强：获得 half-fake data

* 创造性数据增强。如果半假数据不能做到这一点，假数据也许可以。人们正在寻找扩展数据集的创新方法;例如，域随机化，模拟的使用，巧妙的混合，比如将（可能模拟的）数据引入场景，甚至是GAN中。

* 进行预训练

* 尽可能小的输入数据纬度 ： 移除 spurious signal. 避免过拟合

* 使用专业领域知识，限制网络的 size

* 减小批大小。由于归一化是基于批大小的，更小的batch size会具有更明显的正则化效果。这是因为批量经验均值/标准是完整均值/标准的更近似版本，因此标度和偏移量“摆动”您的批次更多

* dropout ： 谨慎使用，一般设置为 0.5
对于RNN,建议放到输入->RNN与RNN->输出的位置.

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/qhvmb.png)

* 使用 early stopping

* batch-size： 可以设置 等于 1



### 5. 调参数

* 随机网格搜索。为了同时调整多个超参数，使用网格搜索确保覆盖所有设置，这听起来很诱人，但请记住，最好使用随机搜索。直观地说，这是因为神经网络通常对某些参数比其他参数更敏感。在极限情况下，如果一个参数很重要，但是改变另一个参数没有效果，那还不如对第一个参数进行彻底采样。

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/7z6fx.png)

* 超参数优化，目前很多基于贝叶斯的超参数优化工具箱

贝叶斯方法试图建立一个函数（更准确地说，是关于可能函数的概率分布），用于估计模型对于某个超参数选择的好坏程度。通过使用这种近似函数（在文献中称为代理函数），您不必在设置、训练、评估的循环上花费太多时间，因为你可以优化代理函数的超参数。



### 6. 优化提高

* 集成 ：

同样的参数,不同的初始化方式
不同的参数,通过cross-validation,选取最好的几组
同样的参数,模型训练的不同阶段，即不同迭代次数的模型。
不同的模型,进行线性融合. 例如RNN和传统模型.


* 保持训练： 不要 validation loss 平稳就停止训练， 如果时间允许，一直训练可能会达到 state of the art

* 如果你的模型包含全连接层（MLP），并且输入和输出大小一样，可以考虑将MLP替换成Highway Network,我尝试对结果有一点提升，建议作为最后提升模型的手段，原理很简单，就是给输出加了一个gate来控制信息的流动，


-----


# 参考

1.https://karpathy.github.io/2015/05/21/rnn-effectiveness/
2. https://blog.nanonets.com/hyperparameter-optimization/
3. [Finding Good Learning Rate and The One Cycle Policy.](https://towardsdatascience.com/finding-good-learning-rate-and-the-one-cycle-policy-7159fe1db5d6)
4. https://www.zhihu.com/question/41631631

