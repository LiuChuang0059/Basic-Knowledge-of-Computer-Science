
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/870l5.jpg)

----

**原文link --- http://karpathy.github.io/2015/05/21/rnn-effectiveness/**

---

### 1. 字符语言模型

> 给 RNN 模型一系列文本，要求 RNN模型学习 给定一个序列后，序列中下一个字符出现的概率。

假设给定一个训练的单词 “hello“ ： 给定文本 “h”后很可能出现“e“； 给定 ”he" 或者“hel" 后很可能出现 “l”;给定文本"hell" 后 很可能出现 “o"

具体来说，使用 one-hot 给字符编码，一次输入 RNN 一个字符，得到 4 个输出

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/mssz8.jpg)

第一步输入 h 到 RNN ， RNN 输出 4个 字符的可能性， 因为下一个字符是 “e”, 我们需要尽可能的提升 e 的数值（绿色的），降低其他数字的数值。 利用 **反向传播算法**实现参数更新--- 趋于我们想要的方向。再次输入会发现 e 的对应的数值会提升，其他的会降低。不断的重复这个过程，直到网络收敛，预测结果准确。
> 每个输出向量上面使用 交叉熵损失， 使用 ADAM 更新优化

**注意到**： 第一次输入 “l”  的目标 是 “l” 而第二次输入 “l” 的目标是“o”. 所以 RNN 模型不能仅仅依靠输入，还要使用 循环关系来追踪文本的上下文来实现这个任务


---


### 2. 模型有效的原因
> code 暂缺，抽空补上

#### 1. 多次训练过程中 模型的进化

训练一个 列夫托尔斯泰的 《战争与和平》，每循环100 次生成一次样本

100 次 --- 模型知道单词之间需要空格隔开
```
tyntd-iafhatawiaoihrdemot  lytdws  e ,tfti, astai f ogoh eoase rrranbyne 'nhthnee e
plia tklrgd t o idoe ns,smtt   h ne etie h,hregtrs nigtike,aoaenns lng
```

300 次。--- 模型开始学会 引用和段落
```
"Tmont thithey" fomesscerliund
Keushey. Thom here
sheulke, anmerenith ol sivh I lalterthend Bleipile shuwy fil on aseterlome
coaniogennc Phe lism thond hon at. MeiDimorotion in ther thize."
```

500 次 --- 模型学会拼写简单的单词 we his
```
we counter. He stutn co des. His stanted out one ofler that concossions and was
to gearang reay Jotrets and with fre colt otf paitt thin wall. Which das stimn
```

1200 次 --- 使用长单词 和标点符号

```
"Kite vouch!" he repeated by her
door. "But I would be done and quarts, feeling, then, son is people...."

```

2000 次 --- 正确拼写的旦夕 引用 名字，标点等

```
"Why do what that day," replied Natasha, and wishing to himself the fact the
princess, Princess Mary was easier, fed in had oftened him.
Pierre aking his soul came to the packs and drove up his father-in-law women.

```


----

#### 2. 预测结果可视化

每个输入字符（蓝色或者绿色）下面，都有 5个字符（模型预测出来的下一个最可能字符的 top5）颜色深浅对应可能性的大小。在一些字符下面，模型对预测非常的自信（例如： http:www.）

输入字符的颜色（蓝或者绿） 是基于 RNN 模型中隐藏状态中的 随机的一个神经元的激活性 上色的。
绿色= 非常活跃， 蓝色 = 很不活跃（对应于 LSTM cell state）。

例如下图，神经元好像对 URL 很兴奋，URL 之外的不是很兴奋，那么 LSTM 可能利用这个神经元来记忆是否在 URL 里面

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/jaqf7.jpg)


下面的图 神经元好像对 [[]] 里面的很兴奋，外面的不是很兴奋。而且**只有在看到第二个 [ 才会开始兴奋激活**

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/kxxyc.jpg)


下面的图，神经元突然在 第一个 w 之后关闭（不兴奋），RNN 可能利用这个神经元计算距离，知道是否应该给出下一个 w 或者给出 URL
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/t46pt.jpg)


----

#### 3. 只输出最可能的预测来可视化文本

1. 学习到了文本的额位置
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/gnphl.jpg)

2. 在引用里面很 excited  turn on


![](https://github.com/LiuChuang0059/large_file/blob/master/pic/z8ym3.jpg)

3. 在 if 态里面激活

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/qan65.jpg)

4. 对深度敏感

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/rrkls.jpg)

等等， 最重要的是， 我们不需要为这些功能编写代码（例如，记忆是否在引用里面这个功能）。我们只需要训练 LSTM。
而他的 cell 在训练过程中逐步调整成为相应的功能细胞（例如 引用检测），因为这样能帮助他更好的实现最终的任务























