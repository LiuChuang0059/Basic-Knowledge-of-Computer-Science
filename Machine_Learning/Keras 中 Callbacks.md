主要介绍 ： EarlyStopping， ModelCheckpoint 等

---


## 1. 什么是 callbacks

在训练模型的时候使用的一组函数。 通过 callbacks 可以知道那个模型的内部状态和相应的统计数据。

> A callback is a set of functions to be applied at given stages of the training procedure. You can use callbacks to get a view on internal states and statistics of the model during training.


当我们需要在每一个 epoch 或者 training 之后自动化执行一些任务来帮助我们控制训练过程， 我们可以使用 callbacks。 例如：
* 当模型达到某一个精度或者准确率的时候，停止训练

* 在每一次 epoch 之后， 保存模型作为一个 checkpoint。

* 随着时间调整学习率


----

## 2. EarlyStopping

避免过拟合的一个方法就是提前终止程序。 EarlyStopping 函数有不同的规格和参数可以调整，用来设定什么时候模型应该停止。

* monitor: 被持续关注的量 ， 例如 val_loss

* min_delta：  监视值的最小变化。例如，min_delta=1表示如果监控值的绝对变化小于1，则训练过程将停止

* patience ： 容忍多少个 epoch 模型没有提升

* restore_best_weights ： 保存最好的参数

```python
from keras.callbacks import EarlyStopping
earlystop = EarlyStopping(monitor = 'val_loss',
                          min_delta = 0,
                          patience = 3,
                          verbose = 1,
                          restore_best_weights = True)
```

关注于 val_loss, 如果连续三个 epoch 模型没有提升， 停止模型， 保存模型的最好 参数。

----


## 3. ModelCheckpoint

每个 epoch 之后保存模型。

Checkpoint是为长时间运行进程准备的容错技术，避免运行意外停止，没有保存模型。

这是一种在系统故障的情况下拍摄系统状态快照的方法。一旦出现问题不会让进度全部丢失。Checkpoint可以直接使用，也可以作为从它停止的地方重新运行的起点。

他们可以用来作预测，或作持续训练的基础。


* filepath : 保存模型的路径

* monitor: 被持续关注的量 ， 例如 val_loss

* save_best_only: if `save_best_only=True`,最新最好的模型的将不会被覆盖

* save_weights_only: 只保存模型

* mode: auto, min, or max ： 如果if `save_best_only=True` ，是否覆盖之前的模型判断取决于这个

* period: 多少个 epoch 一个 checkpoint


```python
from keras.callbacks import ModelCheckpoint


filepath="weights.best.h5"
checkpoint = ModelCheckpoint(filepath,
                             monitor='val_loss',
                             mode='min',
                             save_best_only=True,
                             verbose=1)
callbacks_list = [checkpoint]
model.fit(X, Y, validation_split=0.33, epochs=150, batch_size=10, callbacks=callbacks_list, verbose=1)
```

只有当验证数据集上的模型的分类精度提高到到目前为止最好的时候，才会将模型权重写入文件“weights.best.hdf5”。


```python
filepath="weights-improvement-{epoch:02d}-{val_acc:.2f}.h5"

checkpoint = ModelCheckpoint(filepath,
                             monitor='val_loss',
                             mode='min',
                             save_best_only=True,
                             verbose=1)
```

这样就会保存一系列的模型。



**导入保存的模型**


```python
model = load_model( “your.model.h5”,
custom_objects={‘my_metric’: my_metric }) # 如果自定义 metric 去 moniter
```

-----

## 4. LearningRateScheduler

调整学习率: 根据随机梯度下降优化过程调整学习率可以提高性能并减少训练时间。

schedule 是一个自定义的函数，接受当前 的epoch index 作为输入， 给出相应的学习率作为输出。
默认的的 schedule 是一个常数

```python

from keras.callbacks import LearningRateScheduler
scheduler = LearningRateScheduler(schedule, verbose=0) # schedule is a function

```

最简单和最常用的自适应学习率的方法是随着时间不断的减小学习率。
> 在训练过程的开始阶段, 使用较大的学习率值 可以有较大的更改，更快的下降。 在训练过程的后期降低学习率，对权重进行较小的训练更新。

这样的好处是： 先快速的学习一个较好的权重，之后再优化调节。



* 根据 epoch(time) 来逐渐减小学习率

* 根据 drop 来减小学习率


### 1. Time-Based Learning Rate Schedule


keras 有内置的 基于时间的 改变学习率函数

SGD 中有一个参数是： decay, 这个参数被用来更新学习率， 默认是 0 ，不随时间变化
```python

LearningRate = LearningRate * 1/(1 + decay * epoch)

```

如果设置 decay 是 0.001

```python
Epoch	Learning Rate
1	0.1
2	0.0999000999
3	0.0997006985
4	0.09940249103
5	0.09900646517

```

可以创建一个较为合适的 decay
```python
Decay = LearningRate / Epochs
Decay = 0.1 / 100
Decay = 0.001

```

使用 例子：

```python
from keras.optimizers import SGD


learning_rate = 0.1
decay_rate = learning_rate / epochs
momentum = 0.8
sgd = SGD(lr=learning_rate, momentum=momentum, decay=decay_rate, nesterov=False)
model.compile(loss='binary_crossentropy', optimizer=sgd, metrics=['accuracy'])
# Fit the model
model.fit(X, Y, validation_split=0.33, epochs=epochs, batch_size=28, verbose=2)
```


### 2. Drop-Based Learning Rate Schedule

这种方法通常是： 经过固定的 epochs 之后，下降相应的学习率.
例如： 初始的学习率是0.1 每 10个 epochs 下降 一半（* 0.5）


![](https://i.loli.net/2019/08/28/4mhBGEkotFraz2R.png)

```python
def step_decay(epoch):
	initial_lrate = 0.1
	drop = 0.5
	epochs_drop = 10.0
	lrate = initial_lrate * math.pow(drop, math.floor((1+epoch)/epochs_drop))
	return lrate
```


完整应用

```python
from keras.optimizers import SGD
from keras.callbacks import LearningRateScheduler

def step_decay(epoch):
	initial_lrate = 0.1
	drop = 0.5
	epochs_drop = 10.0
	lrate = initial_lrate * math.pow(drop, math.floor((1+epoch)/epochs_drop))
	return lrate

sgd = SGD(lr=0.0, momentum=0.9, decay=0.0, nesterov=False)
model.compile(loss='binary_crossentropy', optimizer=sgd, metrics=['accuracy'])
# learning schedule callback
lrate = LearningRateScheduler(step_decay)
callbacks_list = [lrate]
# Fit the model
model.fit(X, Y, validation_split=0.33, epochs=50, batch_size=28, callbacks=callbacks_list, verbose=2)
```


### 3. Metric-based Learning Rate Schedule

使用 内置的 callback ReduceLROnPlateau ， 当 metric 不再提高的时候， 降低学习率 .

一旦学习停滞不前，模型通常会从将学习率降低2-10倍中获益。这个callback 会监视一个数量，超过 设定的 patience 还没有提高，学习率就会降低。

```python
keras.callbacks.ReduceLROnPlateau(monitor='val_loss',
                                  factor=0.1,
                                  patience=10,
                                  verbose=0,
                                  mode='auto',
                                  min_delta=0.0001,
                                  cooldown=0,
                                  min_lr=0)

```


* monitor: 监测的量

* factor: 学习率降低因子. new_lr = lr * factor


* patience: 多少个 epoch 不提升就下降学习率


* mode: one of {auto, min, max}.


* min_lr : 学习率的最低值





-----

## 5.  TensorBoard

使用 TensorBoard 进行可视化

```python
keras.callbacks.TensorBoard(log_dir='./logs',
                            histogram_freq=0,
                            batch_size=32,
                            write_graph=True,
                            write_grads=False,
                            write_images=False,
                            embeddings_freq=0,
                            embeddings_layer_names=None,
                            embeddings_metadata=None,
                            embeddings_data=None,
                            update_freq='epoch')

```


* log_dir : log 文件储存路径

* histogram_freq: ： 计算每层激活值和权重的 频率

* write_graph: 是否绘制图， 如果绘制图， 文件将会变得十分大

* write_grads: 可视化梯度， histogram_freq 不能为 0

* update_freq ： 'batch' or 'epoch' or integer
> 'batch': 每个 batch 将 loss 和 metrics 写入 tensorboard ， 'epoch'每个 epoch， 数字 例如1000， 就是 每 1000个 样本， 如果写入过于频繁，会降低训练速度


在命令行运行下面的命令启动 Tensorboard
```sh
tensorboard --logdir=/full_path_to_your_logs
```


使用实例：

每层每次训练 epoch 保存一下梯度

```python
# prepare callback
tb = TensorBoard(histogram_freq=1, write_grads=True)
# fit model
model.fit(trainX, trainy, validation_data=(testX, testy), epochs=500, verbose=0, callbacks=[tb])
```






----

## 6 .  其他的 callbacks

* CSVLogger ： 将每个 epoch的结果导出到 csv

* LambdaCallback ： 允许自定义 callback



---

## 7. 创建 一个 callback

扩展基本的keras.callbacks.Callback 类

代码来自 官网

```python
class LossHistory(keras.callbacks.Callback):
    def on_train_begin(self, logs={}):
        self.losses = []

    def on_batch_end(self, batch, logs={}):
        self.losses.append(logs.get('loss'))

model = Sequential()
model.add(Dense(10, input_dim=784, kernel_initializer='uniform'))
model.add(Activation('softmax'))
model.compile(loss='categorical_crossentropy', optimizer='rmsprop')

history = LossHistory()
model.fit(x_train, y_train, batch_size=128, epochs=20, verbose=0, callbacks=[history])

print(history.losses)
# outputs
'''
[0.66047596406559383, 0.3547245744908703, ..., 0.25953155204159617, 0.25901699725311789]
'''
```



# 参考 ：

1. [Keras Callbacks Explained In Three Minutes](https://medium.com/towards-artificial-intelligence/keras-callbacks-explained-in-three-minutes-846a43b44a16)

2. [How to Check-Point Deep Learning Models in Keras](https://machinelearningmastery.com/check-point-deep-learning-models-keras/)

3. [Using Learning Rate Schedules for Deep Learning Models in Python with Keras](https://machinelearningmastery.com/using-learning-rate-schedules-deep-learning-models-python-keras/)

4. [keras :Usage of callbacks](https://keras.io/callbacks/)


5. [How to Fix the Vanishing Gradients Problem Using the ReLU](https://machinelearningmastery.com/how-to-fix-vanishing-gradients-using-the-rectified-linear-activation-function/)


6. [ReduceLROnPlateau(Callback)](https://github.com/keras-team/keras/blob/a39f10acf3eac5e6ef69d084616982a4d4ebd8bb/keras/callbacks.py#L1287)

















