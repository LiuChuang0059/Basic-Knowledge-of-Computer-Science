---

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/3xsf0.jpg)

---

### 1. 获取模型结构
> [可以参看这篇笔记(https://liuchuang0059.github.io/2019/02/23/%E6%8F%90%E5%8F%96%20keras%20%E4%B8%AD%E7%9A%84%E6%A8%A1%E5%9E%8B%20weights/)

```python
model.summary()
```

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/y11ru.jpg)

* test_layer 是我们自定义的网络名

```python
model.add(
    #Bidirectional(
    LSTM(input_shape=(302, 1),
        units=UNITS,
        return_sequences=True,
        name="test_layer"))  # 自定义的网络名
```


----


### 2. 查看 keras LSTM 源码

观察上面的网络，可知道我们需要的 参数在第一层

```python
model.layers[0].trainable_weights
```
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/qhgps.jpg)

得到，kernel0 recurrent_kernel bias， 所以

```python

W = model.layers[0].get_weights()[0] ###  x和 units 之间的权重 --- kernel
U = model.layers[0].get_weights()[1] ###  h 和 Units 之间的权重 --- recurrent kernel
b = model.layers[0].get_weights()[2]
```



但是没有细分每个 门 的权重，只能去找[源码](https://github.com/keras-team/keras/blob/master/keras/layers/recurrent.py) ，4个门按顺序拼接


```python

self.kernel_i = self.kernel[:, :self.units]
self.kernel_f = self.kernel[:, self.units: self.units * 2]
self.kernel_c = self.kernel[:, self.units * 2: self.units * 3]
self.kernel_o = self.kernel[:, self.units * 3:]

self.recurrent_kernel_i = self.recurrent_kernel[:, :self.units]
self.recurrent_kernel_f = (self.recurrent_kernel[:, self.units: self.units * 2])
self.recurrent_kernel_c = (self.recurrent_kernel[:, self.units * 2: self.units * 3])
self.recurrent_kernel_o = self.recurrent_kernel[:, self.units * 3:]

if self.use_bias:
    self.bias_i = self.bias[:self.units]
    self.bias_f = self.bias[self.units: self.units * 2]
    self.bias_c = self.bias[self.units * 2: self.units * 3]
    self.bias_o = self.bias[self.units * 3:]
```


观察上面的源码，可以知道  i f c o 四个门参数矩阵放在一起，每个的维度 Units 划分的

所以：

```python
###  x和 units 之间的权重 --- kernel
W_i = W[:, :units]   #
W_f = W[:, units:units * 2]
W_c = W[:, units * 2:units * 3]
W_o = W[:, units * 3:]

###  h 和 Units 之间的权重 --- recurrent kernel
U_i = U[:, :units]  #
U_f = U[:, units:units * 2]
U_c = U[:, units * 2:units * 3]
U_o = U[:, units * 3:]

b_i = b[:units]
b_f = b[units:units * 2]
b_c = b[units * 2:units * 3]
b_o = b[units * 3:]
```


得到每个门之间的权重


------

### 3. 使用 numpy 计算每个门的激活值

可以根据 LSTM 原理，使用 numpy 计算每个门的激活中

**参照 keras 源码写就好**

```python

h_tm_i, h_tm_o, h_tm_c, h_tm_f, c_tm = None, None, None, None, None


def hard_sigmoid(x):
    x = 0.2 * x + 0.5
    x[x < -2.5] = 0
    x[x > 2.5] = 1
    return x


def lstm_keras_verify(inputs):
    global h_tm_c, h_tm_f, h_tm_i, h_tm_o, c_tm
    # kernel初始化 W U b

    # step 1 計算W * x
    x_i = inputs * W_i
    x_f = inputs * W_f
    x_c = inputs * W_c
    x_o = inputs * W_o

    # step 2 加上bias
    x_i += b_i
    x_f += b_f
    x_c += b_c
    x_o += b_o

    # step 3 計算
    if not isinstance(h_tm_i, np.ndarray):
        h_tm_i = np.zeros((1, 30))
        h_tm_o = np.zeros((1, 30))
        h_tm_f = np.zeros((1, 30))
        h_tm_c = np.zeros((1, 30))
        c_tm = np.zeros((1, 30))
    i = hard_sigmoid(x_i + np.dot(h_tm_i, U_i))
    f = hard_sigmoid(x_f + np.dot(h_tm_f, U_f))
    c = f * c_tm + i * np.tanh(x_c + np.dot(h_tm_c, U_c))
    o = hard_sigmoid(x_o + np.dot(h_tm_o, U_o))

    i_list.append(i)
    f_list.append(f)
    o_list.append(o)
    c_list.append(c)

    h = o * np.tanh(c)

    h_list.append(h)

    h_tm_c = h_tm_f = h_tm_o = h_tm_i = h
    c_tm = c

    print("当前hidden state", h)
    #print("当前cell state", c)
    return h, c

```

```python

if __name__ == "__main__":
    i_list = []
    f_list = []
    c_list = []
    o_list = []
    h_list = []
    for i in range(302):
        print("输入内容:", X_train_0[114,i])
        # lstm_keras_verify(inputs[:, i])
        lstm_keras_verify(X_train_0[114,i])
```

----

### 4. 误差比较

1. 直接提取 keras 模型的一步输出 -- hidden state

```python
dense1_layer_model = Model(
    inputs=model.input,
    outputs=model.get_layer('test_layer').output)  # layer 的 名称
#以这个model的预测值作为输出
h_state = dense1_layer_model.predict(
    X_train_0.reshape(X_train_0.shape[0], X_train_0.shape[1], 1))
```

2. 通过 numpy 计算的 hidden state
```python
h_array = np.array(h_list)
```

3. 两者比较

```python
h_state[114]- h_array.reshape(1,302,30)

```


![](https://github.com/LiuChuang0059/large_file/blob/master/pic/07967.jpg)

可能由于计算精度问题，有少许的误差


----

### 5. 可视化

绘制一个 遗忘门的结果

```python
f_array = np.array(f_list)
f_s = np.swapaxes(f_array.reshape(302,30), 0, 1)

fig, ax = plt.subplots(figsize=(40, 20))
#b = np.swapaxes(h_state[114], 0, 1)

c = ax.pcolor(f_s, cmap='RdBu', )
             #vmin = -1,vmax =1 )
              #vmin=-f_s.max(), vmax=f_s.max())
ax.set_xticklabels([0, 50, 100, 150, 200, 250, 300], fontsize=30)  # 坐标轴的刻度
ax.set_yticklabels([0, 5, 10, 15, 20, 25, 30], fontsize=30)
ax.set_xlabel('Depth', fontsize=40)
ax.set_ylabel('Units', fontsize=40)

e = fig.colorbar(c, ax=ax)
e.ax.tick_params(labelsize='large')  # 调整 coloebar 字体的

### 带网格线的
# c = ax[1].pcolor(b, edgecolors='k', linewidths=1)
# ax1.set_title('thick edges')

#fig.tight_layout()
#plt.savefig("LSTM_cellstate",bbox_inches='tight')
plt.show()
```

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/5mzk7.jpg)

-----


# 参考




1. 获取权重 ：

https://stackoverflow.com/questions/41711190/keras-how-to-get-the-output-of-each-layer

https://bigquant.com/community/t/topic/1403

https://github.com/keras-team/keras/issues/3088

https://fairyonice.github.io/Extract-weights-from-Keras's-LSTM-and-calcualte-hidden-and-cell-states.html


2. keras GRU LSTM Cell 源码： https://github.com/keras-team/keras/blob/master/keras/layers/recurrent.py

3. keras 和 tf 实现 LSTM 内部计算 ： https://www.twblogs.net/a/5c1f1dd7bd9eee16b4a6e5f4


4. pcolor + colorbar 绘图 参考

https://matplotlib.org/gallery/images_contours_and_fields/pcolor_demo.html#sphx-glr-gallery-images-contours-and-fields-pcolor-demo-py

https://matplotlib.org/gallery/lines_bars_and_markers/scatter_hist.html#sphx-glr-gallery-lines-bars-and-markers-scatter-hist-py

https://matplotlib.org/gallery/color/colormap_reference.html#sphx-glr-gallery-color-colormap-reference-py

https://stackoverflow.com/questions/13784201/matplotlib-2-subplots-1-colorbar/38940369



