![](https://github.com/LiuChuang0059/large_file/blob/master/pic/gbc10.jpg)


----

### 1. Seq2Seq 简介

Seq2Seq 包含两部分 Encoder 和 Decoder，

1. Encoder-Decoder结构先将输入数据编码成一个上下文向量c：

* 最简单的方法就是把Encoder的最后一个隐状态赋值给c

* 还可以对最后的隐状态做一个变换得到c，

* 也可以对所有的隐状态做变换

2. 拿到c之后，就用另一个RNN网络对其进行解码

* c当做之前的初始状态h0输入到Decoder

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/2kf3o.jpg)

* c当做每一步的输入 ：

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/nl4sg.jpg)


观察 下图对比发现， Decoder 的公式和 RNN 几乎一样，只是 Decoder的公式中 多了一个 C

C 指的是 **context vector** 包含所有输入信息的向量，在 Encoder中的 **hidden state**

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/717nd.jpg)


Encoder将输入句压缩成固定长度的context vector，context vector即可完整表达输入句，再透过Decoder将context vector内的信息产生输出句。

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/ufygd.jpg)

### 2. seq2seq 远离展示 -- 动图

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/ai2o6.gif)















----

### 3. 注意力模型

如果句子很长，使用固定长度的 context vector 效果不好，引入 注意力模型
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/rhl8o.jpg)


**对输入句的每一个文字都创建一个 context vector**，而不是值从最终的 hidden state 获得一个 context vector。

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/2d7ik.jpg)


观察上图，可以知道，**关键就是如何计算 $c_{i}$**

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/s9lrk.jpg)

其中 $\alpha_{i,j}$ 是**attention score**衡量输入句中每个文字对目标句每个文字所带来的重要性的程度

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/hzsqn.jpg)

attention score由score $e_{ij}$ 求得

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/hm9sq.jpg)

其中 a 是根据输入字位置 j 和输出字位置 i 这两者的关联程度（decoder中的hidden state s_{i-1}和输入句中的第j个文字hidden state h_{j}的关系所计算出的权重 ），常见形式有：

*  点乘 $\vec{e_t}=\vec{s_{t-1}}^T\vec{h}$

* 加权点乘

* 加和

----
1. 得到$e_{ij}$后  进行softmax操作将其 normalize 得到 attention score 的分布

2. 利用 attention score 可以计算 context vector

3. 计算 decoder的 下一个 hidden state  $s_t = f(s_{t-1},y_{t-1},c_t)$ 和 该位置的输出 $p(y_t|y_1,...,y_{t-1}, \vec{x}) = g(y_{i-1}, s_i, c_i)$

---

其中 context vector 又称为 attention vector

可以将 attention score 列为矩阵，透过此矩阵可看到输入端文字和输出端文字间的对应关系

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/q9sbr.jpg)


----

### 4. 注意力机制原理动图

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/0p5oj.gif)

注意力机制区别于传统的 Seq2Seq

* encoder 传递所有的 hidden state 到 decoder

* decoder 歪了 focus on 最和自己相关的部分，需要额外做一步

	* 给 每一个 隐藏态一个 score
	* 将每个隐藏状态乘以 softmaxed 之后的分数，从而放大分数高的隐藏状态，淹没分数低的隐藏状态 ，最终获得 context


![](https://github.com/LiuChuang0059/large_file/blob/master/pic/nyse1.gif)



* 经过attention 获得 新的 h 之后，将 h 和 c 拼接成一个向量 输入到前馈神经网络   ？？

* 前馈神经网络的输出表示该时间步长的输出单词

* 重复该过程。





![](https://github.com/LiuChuang0059/large_file/blob/master/pic/167gk.gif)


----












# 参考

1. 从Seq2seq到Attention模型到Self Attention（一）--- https://mp.weixin.qq.com/s/7Kxnmaoh10hrM6VpQj3ZQA

2. 完全图解RNN、RNN变体、Seq2Seq、Attention机制--https://zhuanlan.zhihu.com/p/28196873


3. 深度学习中的注意力机制(2017版)---https://blog.csdn.net/malefactor/article/details/78767781

4. Seq2seq pay Attention to Self Attention: Part 1 --- https://medium.com/@bgg/seq2seq-pay-attention-to-self-attention-part-1-d332e85e9aad

5. Visualizing A Neural Machine Translation Model (Mechanics of Seq2seq Models With Attention) ---https://jalammar.github.io/visualizing-neural-machine-translation-mechanics-of-seq2seq-models-with-attention/
