
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/cfq6c.jpg)

-----

### 0. RNN 简单理解

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/ol2ns.jpg)

为了预测最后的结果，我先用第一个词预测，当然，只用第一个预测的预测结果肯定不精确，我把这个结果作为特征，跟第二词一起，来预测结果；接着，我用这个新的预测结果结合第三词，来作新的预测；然后重复这个过程；直到最后一个词。这样，如果输入有n个词，那么我们事实上对结果作了n次预测，给出了n个预测序列。整个过程中，模型共享一组参数。因此，RNN降低了模型的参数数目，防止了过拟合，同时，它生来就是为处理序列问题而设计的，因此，特别适合处理序列问题。

**转载自苏神苏剑林https://spaces.ac.cn/archives/3924**



----
### 1. RNN 记忆信息

例如给一个影片中的图片打标签（活动）， 如果前一个场景是在海滩， 我们应该在打标签的时候尽可能的倾向海滩活动。如果图片里一个人在水中，那我们我们猜测他的活动应该是**游泳**，而不是洗澡。

所以我们需要让我的模型能够追踪状态的变化

1. 在看完每张照片后，模型不仅要输出一个标签，还要**更新学习到的知识**

2. 在遇到新的照片的时候，模型需要结合他之前获得到的信息来更好的预测

3. 这些关键的中间信息，已经由神经网络的隐藏层编码提取出来了。


![](https://github.com/LiuChuang0059/large_file/blob/master/pic/iq2sn.jpg)


----

### 2. LSTM 的长期记忆

上文之介绍了记忆和更新信息，但是没有考虑如何更新，何时更新；所以信息更新的会很混乱。混乱意味着信息迅速变化和消失。所以我们需要网络保存一个**长期记忆**，而记忆的信息要缓和的改变（gently）

1. 加入**遗忘机制** ： 一个场景结束了，模型就需要忘旧当前场景的相关信息，例如： 位置，时间等。但是**不是所有的额信息都遗忘**。例如，这一个场景里面有人死了，模型就需要记住这个信息。

2. 加入**保存机制** ： 模型看到一个新的照片，需要学习关于图片的一些信息是否值得使用和保存

3. 当一个新的输入进来的时候，模型先忘记一些不需要记忆的信息，然后再嘘唏一些新的输入里面值得使用的信息，把他们保存在长期记忆里面。

4. **将长期记忆转化为工作记忆**： 最终模型需要学习哪部分的长期记忆是立即可用的。
> 例如 李儒的年龄信息是长期记忆里面一个有用的片段，但是如果 李儒不在当前的场景里面，这个信息可能就无关紧要

----

### 3. LSTM 记忆机制 -- 架构
> 详细运算参考 -- https://github.com/LiuChuang0059/ComplexNetwork-DataMining/blob/master/techs/RNN/RNN%E6%A6%82%E8%BF%B0.md#1-lstm

LSTM 会输出两个状态到下一个单元： cell state 和 hidden state。cell 负责各个隐藏状态或者前面时间步的事件，记忆方式一般通过三种门控机制实现 ： 输入门，遗忘门，和输出门



#### 1. 遗忘门

负责从单元状态中移除信息。例如下图，当模型遇到 person 后面的 第一个句号，遗忘门就可能意识到下一个语句的语境发生变化，就需要遗忘前一句的主语 Bob

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/huk6a.jpg)


#### 2. 输入门
 输入门将信息添加到单元状态，例如下图，

!![](https://github.com/LiuChuang0059/large_file/blob/master/pic/cwe7x.jpg)

* 通过 sigmoid 函数调节需要添加到单元状态的值，类似于一个滤波器过滤来自 $h_{t-1}$ 和 $x_{t}$

* 创建一个 包含所有可能值的向量，添加到单元状态中，使用 tanh 输出 -1 到 1

* 调节滤波器的值乘以创建的向量


#### 3. 输出门
输出门从当前状态选择有用的信息并将其显示为输出

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/5mddk.jpg)

* 把 tanh 函数应用到单元状态之后创建一个向量，从而将值缩放在-1 到+1 之间。

* $h_{t-1}$ 和 $x_{t}$ 经过过滤器，调节需要从上述创建向量中输出的值

* 过滤器和 向量乘 作为**输出** 发送，并发送到下一个单元的隐藏态

**过滤器建立在输入和隐藏态上，并应用到单元状态**






----


### 4. LSTM internals --- LSTM cell states，otter memory mechanisms


> 使用 LSTM 训练一个计数器，aaaaaXbbbbb； N 个 a  预测 N个 b

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/6eq1o.jpg)

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/zorpf.jpg)

* 对比发现**工作记忆**看起来像长期记忆的一个 锐化版
> 因为 长期记忆被 tanh 函数压缩，并且输出门 限制一部分输出。

* 观察 cell state
> a 的颜色逐渐加深直到遇到限制符，然后颜色再逐渐变浅


-----

### 5.  LSTM  的记忆状态

序列：
```
AxxxxxxYa
BxxxxxxYb
```

需要模型记忆前面的是 A、还是B

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/dlzso.jpg)

* Neuron 8 是一个 记录 A的神经元， 可以发现 输入门 忽略了其中的字母x； 而隐藏状态会触发所有中间 x



给定下面两个序列，训练模型，给定一个如初，预测下一个值是什么。

```
3, 0, 1, 2, 3
4, 0, 1, 2, 4

```

注意到，两个序列之间信息有冲突， 在序列1里面，给定 2为输入，需要预测的结果为3， 而在序列2 里面，寻药输出 4
这个问题对于**多层感知机或者其他的非循环的神经网络是难以学习的**


-----


### 6. LSTM 返回 状态还是返回序列


**return sequences** ： 如果要返回序列

```python
lstm1 = LSTM(1, return_sequences=True)
```

则网络的每一个隐藏状态(hidden state)值都输出 （维度对应于时间步），


![](https://github.com/LiuChuang0059/large_file/blob/master/pic/6dt8y.jpg)

如果是堆叠（stacked LSTM）LSTM 网络，一定要返回所有状态值，作为下一层网络的输入

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/e3sx0.jpg)

如果不选择返回状态,则只返回最后的一个状态值 --- 常用于异步的 seq2seq

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/ua8wq.jpg)


-----


**return states**

```python
lstm1, state_h, state_c = LSTM(1, return_state=True)
```
lstmq1 : The LSTM hidden state output for the last time step.
state_h : The LSTM hidden state output for the last time step (again).
state_c : The LSTM cell state for the last time step.

不同于上文的隐藏状态 hidden state , 这里的 State 指的是每个 LSTM cell 的 **cell state**
> 详见 本文第三部分 LSTM 记忆机制

通常情况下，我们不需要获取 cell state，但是如果是一个复杂的模型，后续的层需要前面层的最终 cell state 来初始化他们的 cell state。
> 例如 encoder-decoder 模型

-----

同时 **Return States and Sequences**
```python
lstm1, state_h, state_c = LSTM(1, return_sequences=True, return_state=True)
```

**不同于上面的输出**
lstm1 :  returns the hidden state for each input time step
state_h: the hidden state output for the last time step
state_c : and the cell state for the last input time step.


-----

### 7. TimeDistributed layer
> This wrapper allows us to apply a layer to every temporal slice of an input
> TimeDistributedDense applies a same Dense (fully-connected) operation to every timestep of a 3D tensor.

#### 1. One-to-One LSTM for Sequence Prediction
> 输入 sequence [0.0, 0.2, 0.4, 0.6, 0.8] ； 输出 sequence [0.0, 0.2, 0.4, 0.6, 0.8]；一个时间步输出一个值。

定义网络模型： 一个时间步一个输入，隐藏层就有 5个 units ，输出层是一个全连接层对应一个输出
> 全连接层： keras 中的 Dense： output = activation(dot(input, kernel) + bias)

```python

X = seq.reshape(len(seq), 1, 1) # 5 个样本 一个时间步 一个特征
y = seq.reshape(len(seq), 1) # 5个样本 1个特征

length = 5
n_neurons = length
# create LSTM
model = Sequential()
model.add(LSTM(n_neurons, input_shape=(1, 1)))
model.add(Dense(1))
model.compile(loss='mean_squared_error', optimizer='adam')
print(model.summary())

# train LSTM
model.fit(X, y, epochs=n_epoch, batch_size=n_batch, verbose=2)


```

全连接层参数计算：  5个 输入（来自上一层），  一个输出（一个神经元） + bias
```python
n = inputs * outputs + outputs
n = 5 * 1 + 1
n = 6

```

-----

#### 2. Many-to-One LSTM for. Sequence Prediction

模型一次性输出序列 ： 输出一个向量（5个时间步输出一个），而不是一个序列（每个时间步输出一个）

```python
X = seq.reshape(1, 5, 1) # 一个样本，5个时间步， 一个特征
y = seq.reshape(1, 5) #  一个样本 5个特征

# create LSTM
length = 5
model = Sequential()
model.add(LSTM(5, input_shape=(5, 1)))
model.add(Dense(length))  # 5 个神经元的全连接层
model.compile(loss='mean_squared_error', optimizer='adam')
print(model.summary())
```
全连接层参数计算：  5个 输入（来自上一层），  5个输出（5个神经元） + bias


序列作为一个片段被生成，而不是逐步的输入数据； 但是这样没有利用到 LSTM的 序列学习能力。

-----

#### 3. Many-to-Many LSTM for Sequence Prediction (with TimeDistributed)

使用 TimeDistributed 的 要点：

* 输入必须是 3D； 需要配置 TimeDistributed dense 层的前一层 **返回序列**
> you will need to configure your last LSTM layer prior to your TimeDistributed wrapped Dense layer to return sequences eg: return_sequences = True

* 输出可能是 3D ： 如果 TimeDistributed dense 层 是输出层，输出一个序列，可能需要将 y reshape into 3D



每个 LSTM unit 返回一个 包含五个输出值的序列，每个输出对应输入数据的一个时间步
```python
model.add(LSTM(n_neurons, input_shape=(length, 1), return_sequences=True))
```


使用  TimeDistributed  包裹 单个输出的的 全连接 dense 层

```python
model.add(TimeDistributed(Dense(1)))
```

**输出层的单值输出是关键**： 对于输入序列的时间步，我们每次只输出数列中的一步的值，

TimeDistributed 对于每个时间步的 LSTM 输出都应用一次 Dense层的 运算 （5个输入一个输出  + bias 汇成 一个序列）
> The TimeDistributed achieves this trick by applying the same Dense layer (same weights) to the LSTMs outputs for one time step at a time. In this way, the output layer only needs one connection to each LSTM unit (plus one bias).


全连接层参数计算：  5个 输入（来自上一层），  一个输出（一个神经元每个输入有一个权重） + bias


----

### 8. Dense and TimeDistributedDense of Keras 的区别

1. 假设有 N 组数据，魅族数据 700 个特征（700 个值），得到一个 200个值输出。首先需要将data reshape （N，700，1）

2. 700 就是时间步， 数据从 t = 1 到 700，输入到RNN， 对应 700 个输出，就是 h1 到 h700. 所以输出的数据格式 N * 700* 200(channels)
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/g34gb.jpg)

3. 使用 TimeDistributedDens， 相当于对于每一个时间步，都用了一个 dense层，就是 对 h1，h2,h3 分别用 dense 层。 从 h1 到 h700 ，对他们的 channels 分别使用全连接操作 --- 变为 （1，1，200）

4. 为什么这么做？ -- 不希望 Flattern RNN 的输出

5. 为什不 flattern --- 希望时间步值独立

6. 为什么独立？

* 只想在自己的时间步有交互

* 不想不同的时间步和 channels 有交互。


----

### 9. When and How to use TimeDistributedDense

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/i37fp.jpg)

RNN 可以实现不同类型的转换，TimeDistributedDense 允许我们构建 _one-to-many_ and _many-to-many_ 结构的模型。因为对于多个输出的输出函数，在每个时间步上必须相同。

如果不使用这个，只能有一个输出，在最后使用一个 demnse layer


> For each sample, the input is a sequence (a1,a2,a3,a4...aN) and the output is a sequence (b1,b2,b3,b4...bN) with the same length. bi could be viewed as the label of ai.
Push a1 into a recurrent nn to get output b1. Than push a2 and the hidden output of a1 to get b2...

使用 TimeDistributedDense ，计算出来的 损失函数是所有时间步上面的输出，不使用的话，损失韩式只是最后一步的输出，只能等到最后的 bN

----



### 10. 避免 LSTM 模型的 Overfitting and Underfitting


#### 1. good fit

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/qkgft.jpg)

#### 2. Underfit

* 在训练集上表现好，在 test set 上表现的比较差， validation loss 有提高更好的可能
> 加训练的 epochs

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/52ye8.jpg)


* 另一种是 训练集比 validation set 好很多，而且训练已经趋于稳定
> 这种情况需要提高模型的能力， units 个数， 隐藏层的数目

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/a2ngq.jpg)


#### 3. Overfit

* validation set 精度在某个点开始反弹
> 在反弹 地方停止epoch， 或者增加 数据集。

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/03kqz.jpg)



#### 4.  多次运行

**LSTM 是随机的**，每 run 一次得到一个不同的图

重复 5 到 10次 run ，train and validation 的多次 plot 能够给出一个更鲁棒的模型表现情况

```python
for i in range(5):
	# define model
	model = Sequential()
	model.add(LSTM(10, input_shape=(1,1)))
	model.add(Dense(1, activation='linear'))
	# compile model
	model.compile(loss='mse', optimizer='adam')
	X,y = get_train()
	valX, valY = get_val()
	# fit model
	history = model.fit(X, y, epochs=300, validation_data=(valX, valY), shuffle=False)
	# story history
	train[str(i)] = history.history['loss']
	val[str(i)] = history.history['val_loss']

# plot train and validation loss across multiple runs
pyplot.plot(train, color='blue', label='train')
pyplot.plot(val, color='orange', label='validation')
pyplot.title('model train vs validation loss')
pyplot.ylabel('loss')
pyplot.xlabel('epoch')
pyplot.show()


```

-----

### 11.  LSTM 输入数据格式


```python

model = Sequential()
model.add(LSTM(32,input_shape=(50,2)))
model.add(Dense(1))
```

LSTM 输入是三维的

* 样品 ： 一个序列是一个样本。批次由一个或多个样本组成。

* 时间步 ： 一个时间步代表样本中的一个观察点。 （50个时间步长，2个特征）

* 特征 ： 一个特征是在一个时间步长的观察得到的。



```python
data= data.reshape(1,10,1)  # 每个时间步长一个样本 需要10个时间步长和1个特征。
model.add(LSTM(32,input_shape(10,1)))
```
该数据现在可以为input_shape（10，1）的LSTM的输入（X）。



----

**LSTM 有多个输入特征**

例如： 有两个平行序列作为输入

```python
series 1: 0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0
series 2: 1.0, 0.9, 0.8, 0.7, 0.6, 0.5, 0.4, 0.3, 0.2, 0.1

```

数据 ： 一个 样本； 10个 时间步， 2个特征

```python
data = data.reshape(1, 10, 2)

```

---

LSTM 输入 Tips

* LSTM 输入层必须是 3D

* LSTM 输入层是由 第一隐藏层 的 input_shape 定义

* input_shape 输入一个 tuple （时间步，特征数）


-----

### 12.  训练一个 final LSTM 模型
final LSTM 模型 ： 用于预测新的数据的模型

#### 1. 如何最终化一个 LSTM 模型 -- 所有数据

用所有的数据来 fit 模型（没有 test set ，validation）

* 最终模型可以 保存，
* 导入模型用来新数据的预测

#### 2. Train/Test set 的目的

分割数据集为训练集和测试集可以快速的评估模型的表现

我们假设测试集数据是新的数据，比较预测的值和真实的值，能够评估模型的表现情况

**我们根据模型在 test set 上的表现 推断模型在新的数据上的表现**，是一个很大的跳跃，需要很多的约束

* 过程要严格鲁棒

* 模型表现测量的选择要能够包括位置数据的感兴趣的特征

* 数据预处理在新数据上可重复，数据可逆

* 选择的算法对实验的问题是有意义的


实际上，使用test set 判断 unseen data 通常有一定的偏差，除非有大量的数据。重复多次实验，通常得到多个结果。这样可能会导致我们不确定程序在 unseen data 上的实际表现。 通常情况下如果时间允许，我们更倾向于使用 **k-fold cross-validation**


#### 3. 使用 k-fold cross-validation 的目的

cross-validation 能够系统的创建和评估多个子数据集上的多个模型的效果

* 计算多个模型的平均值评估模型的平均表现情况

* 计算测量方法的标准误差， 从而了解实际情况下的偏差

#### 4. 重采样的方法

Both train-test splits and k-fold cross validation 都是重采样方法

**Why？** 我们想要训练的理想模型就是能够在新数据表现的最好，但是我们没有新数据，所以我们需要使用统计技巧预测。


#### 5. 问题

1. 为什么不使用cross-validation 中最好的模型，而是使用 final model
> final model 使用的数据是所有可用数据，，而 其他的只是使用的子数据集


2. 在整个数据集上面训练的模型会有不同吗，有什么不同 ---- 训练在整个数据集上面，怎么知道模型的好坏
> If well designed, the performance measures you calculate using train-test or k-fold cross validation suitably describe how well the finalized model trained on all available historical data will perform in general.

3. 每次训练的模型都得到不同的表现结果？
> 机器学习算法是有随机性的，出现不同的结果很正常。多次重复实验 train/test 会帮助我们掌握变化的范围


-----


### 13.  保存 final model

keras 中 模型可以保存在 HDF5 类型文件中,将保存模型的框架结构和权重，以及模型选取的 损失函数，优化算法。
模型的网络结构保存在 JSON 或者 YAML 文件里面。（详见另一篇笔记 ）

```python
# save model to single file
model.save('lstm_model.h5')

```


模型可以再次**导入**
> 可能是再次打开文件，或者在另一个 script 中打开

```python
from keras.models import load_model
# load model from single file
model = load_model('lstm_model.h5')
# make predictions
yhat = model.predict(X, verbose=0)
```

----

### 14. LSTM 参数计算

参考另一篇笔记 https://github.com/LiuChuang0059/ComplexNetwork-DataMining/blob/master/techs/RNN/LSTM%E5%8F%82%E6%95%B0%E8%AE%A1%E7%AE%97.md

---

### 15. LSTM 中 Units 参数 和 cell

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/c1q1u.jpg)

cell 里面有四个黄色小框，每一个小黄框代表一个前馈网络层，num_units 就是这个层的隐藏神经元个数，其中1、2、4的激活函数是 sigmoid，第三个的激活函数是 tanh。

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/oqgu4.jpg)

Num_units 指的是一层的output size(hidden size)

LSTM cell 会将每一时刻的 x dim 非线性变化为 output hidden size



---

### 16. LSTM 模型预测股票 ---coding

https://github.com/LiuChuang0059/ComplexNetwork-DataMining/blob/master/techs/RNN/LSTM-keras.ipynb




-----


### 17. How do LSTMs Work?

 由于之前的网络中反向传播中的错误要么是指数形式激增要么是指数形式衰减，无法获取长期记忆。
而 LSTM 层包含了一组循环连接的块，称为记忆块。这些记忆块可以被看作数字电脑中的记忆芯片。每个单元包含一个或多个循环连接的记忆单元和三个乘法单元——输入、输出和遗忘门——它们为单元提供连续的写、读和重置操作的模拟。网络只能通过门与细胞相互作用。

> The Long Short Term Memory architecture was motivated by an analysis of error flow in existing RNNs which found that long time lags were inaccessible to existing architectures, because backpropagated error either blows up or decays exponentially.

An LSTM layer consists of a set of recurrently connected blocks, known as memory blocks. These blocks can be thought of as a differentiable version of the memory chips in a digital computer. Each one contains one or more recurrently connected memory cells and three multiplicative units – the input, output and forget gates – that provide continuous analogues of write, read and reset operations for the cells. … The net can only interact with the cells via the gates.  ---Alex Graves, et al., Framewise Phoneme Classification with Bidirectional LSTM and Other Neural Network Architectures, 2005.



---



### 18. What are Bidirectional LSTMs?


双向递归网络的基本思想就是，将每个训练序列向前和向后表示为两个独立的递归网络，它们都连接到同一个输出层。这意味着对于给定序列中的每个点，BRNN都有关于它之前和之后的所有点的完整的顺序信息

对于像语音识别这样的暂时性问题，依赖于对未来的认知乍一看似乎违反了因果关系，我们如何才能将我们对所听到的东西的理解建立在尚未说过的东西之上呢?

然而， 人类听众正是这样做的。听到的声音，单词，阵阵整句话，可能刚开始并没有什么意义，只有听到后来的话 才明白意义
> The basic idea of bidirectional recurrent neural nets is to present each training sequence forwards and backwards to two separate recurrent nets, both of which are connected to the same output layer. … This means that for every point in a given sequence, the BRNN has complete, sequential information about all points before and after it. Also, because the net is free to use as much or as little of this context as necessary, there is no need to find a (task-dependent) time-window or target delay size.

… for temporal problems like speech recognition, relying on knowledge of the future seems at first sight to violate causality … How can we base our understanding of what we’ve heard on something that hasn’t been said yet? However, human listeners do exactly that. Sounds, words, and even whole sentences that at first mean nothing are found to make sense in the light of future context.


![](https://github.com/LiuChuang0059/large_file/blob/master/pic/9k44m.jpg)

举例来说，”我喜欢苹果，因为它很好吃”？和”我喜欢苹果，因为他比安卓稳定”这两个句子当中，如果只看”我喜欢苹果”，你可能不知道苹果指的是水果还是手机，但如果可以根据后面那句得到信息，答案就很显而易见，这就是双向RNN运作的方式。




-----









----



# 参考

1. http://blog.echen.me/2017/05/30/exploring-lstms/

2. http://colah.github.io/posts/2015-08-Understanding-LSTMs/

3. Understand the Difference Between Return Sequences and Return States for LSTMs in Keras---
https://machinelearningmastery.com/return-sequences-and-return-states-for-lstms-in-keras/

4. How to Use the TimeDistributed Layer for Long Short-Term Memory Networks in Python --- https://machinelearningmastery.com/timedistributed-layer-for-long-short-term-memory-networks-in-python/

5. https://datascience.stackexchange.com/questions/10836/the-difference-between-dense-and-timedistributeddense-of-keras


6. When and How to use TimeDistributedDense -- https://github.com/keras-team/keras/issues/1029

7. How to Diagnose Overfitting and Underfitting of LSTM Models --- https://machinelearningmastery.com/category/lstm/page/2/

8. How to Reshape Input Data for Long Short-Term Memory Networks in Keras --https://machinelearningmastery.com/reshape-input-data-long-short-term-memory-networks-keras/

9. 如何为LSTM重新构建输入数据（Keras）--- https://www.jianshu.com/p/246f117af8f0

10. Data and Notebook for the Stock Price Prediction Tutorial-- https://github.com/mwitiderrick/stockprice


11. How to Make Predictions with Long Short-Term Memory Models in --- https://machinelearningmastery.com/make-predictions-long-short-term-memory-models-keras/


12. Alex Graves, et al., Framewise Phoneme Classification with Bidirectional LSTM and Other Neural Network Architectures, 2005.


13.  🌟 A Gentle Introduction to Long Short-Term Memory Networks by the Experts---https://machinelearningmastery.com/gentle-introduction-long-short-term-memory-networks-experts/


14.  🌟 Understanding LSTM Networks ---http://colah.github.io/posts/2015-08-Understanding-LSTMs/


15. 在调用API之前，你需要理解的LSTM工作原理 --- https://cloud.tencent.com/developer/article/1120123




16. 中文分词系列 基于双向LSTM的seq2seq字标注---- https://spaces.ac.cn/archives/3924


17. https://www.zhihu.com/question/64470274
