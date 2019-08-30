---

#  1.SVM 基础了解


### What is SVM ？

* 支持向量机是一种监督的机器学习，可用于分类和回归问题，主要用于分类问题

* 在 SVM 中，我们把每个数据项看作 高维空间的一个坐标点（空间维度对应与数据的特征数）

* 支持向量机的学习策略就是 间隔最大化，形式化为求解一个凸二次规划，学习算法就是凸二次规划的最优化算法

----

### SVM 模型

- 线性可分支持向量机 ： 训练数据线性可分， 通过硬间隔最大化(harf margin maximization) 学习一个线性分类器
- 线性支持向量机 ：训练数据线性**近似**可分， 通过软间隔最大化(soft margin maximization) 学习一个线性分类器
- 非线性支持向量机 ： 训练数据线性不可分，使用核技巧(kernel kick) 进行分类

------

### How is the data classified

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/okaqt.jpg)

如图所示， 有两类数据，⭕️ 和 三角，由于某些原因，⭕️**表示的数据标签** **y = +1,** 三角表示数据标签 **y= -1**

* 通过寻找一个能够分开两类数据的超平面 从而实现分类

* 要寻找一个最优化的 超平面

* 超平面到每一类的数据的距离都是最大的 即为最优化

超平面函数：

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/b3hon.jpg)

数据点到超平面的距离可以简单表示为：

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/xzkdn.jpg)

其中 Xp是 X 在超平面上面的正交投影

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/bsxt8.jpg)

通过上面式子计算得到 r，为了使得 距离非负数，我们乘上标签值

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/j19ly.jpg)

---------------

# 2.SVM 详述

### 1. 线性可分支持向量机

> 数据集的线性可分性 （2分数据）：存在某个超平面能将两类实例点完全正确（所有实例点）的划分到超平面的两侧，称数据集线性可分，否则线性不可分

* 支持向量机的学习都是在特征空间完成的，将输入从输入空间**线性或者非线性**映射到特征空间，生成特征向量。

* 支持向量机通过间隔最大化求解最优的超平面，所以最终解是唯一的

#### 1. 间隔（Margin）

> 一个点距离超平面的远近可以表示分类预测的确信程度

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/cu4a8.jpg)



1. **函数间隔** ： 在数据集和超平面确定的情况下，使用$y*(w*x+ b)$ 表示分类的正确性和可信度，所以$y*(w*x+ b)$ 就是函数间隔

2. 选择分离超平面的时候不能只看函数间隔，因为等比例的改变 w,b 超平面没有变函数间隔却改变了，需要规范化参数，使得间隔是确定的，即为**几何间隔**。

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/5vvuu.jpg)

3. 观察上面的公式，**分子分母上下等比例的变化**，所以我们固定上面的式子

   ![](https://github.com/LiuChuang0059/large_file/blob/master/pic/qs1sg.jpg)

   这个称为 函数间隔，一般便于计算，取为 1





   　

#### 2. 支持向量机的学习算法 --- 最大间隔法

**SVM的模型是让所有点到超平面的距离大于一定的距离，也就是所有的分类点要在各自类别的支持向量两边，同时距离尽可能的远** ，即以充分大的确信度对数据进行分类。这样的超平面对**未知的数据有较好的预测能力**

* 问题转化为一个约束最优化问题 ： 最大化间隔 r ，满足所有的点都间隔都大于 r

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/iearn.jpg)

* 如上面的分析，函数间隔 r 的取值不影响最优化问题的解，取 r = 1； 同时最大化 $\frac{1}{||w||}$ 等价于 最小化 $1/2 ||w||^{2}$

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/51rbc.jpg)

**目标函数** **min** **是** **凸函数，** **同时约束条件不等式是仿射函数的，根据凸优化理论，我们可以通过拉格朗日函数将我们的优化目标转化为无约束的优化函数**

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/ghzh7.jpg)

根据 下面两个式子，消掉 L(w,b,a)中的 w和 b，转化为

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/93zu4.jpg)



根据**SMO** 算法，求解出 a向量，再 带回 关系式 求解 w，b

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/890f3.jpg)

对于所有的支持向量 xs，ys ，根据下面的两个公式求解 b，之后取平均值

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/ktgnb.jpg)

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/o61wl.jpg)

**PS： 观察 W，b 公式，我们不难发现，参数都是由支持向量决定的，这个可以很直观的理解，外围的对于超平面的划分不起作用。**

**决定分离超平面的时候，只有支持向量起作用，其他的实例点并不起作用；所以分类模型称为支持向量机**

#### 3. 支持向量

> 线性可分的样本中，样本点距离超平面最近的样本点的实例称为 **支持向量**
> H1 和 H2 上面的点就是支持向量

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/ecei6.jpg)

根据 KKT  互补条件

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/eqfod.jpg)

所以， **a>0** ,括号内的函数就要等于零，上面的点就都是 支持向量。

#### 4. 新数据预测

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/5ganp.jpg)

**对于新点** x的预测，只需要计算它与训练数据点的内积即可，由于**非支持向量的点的** **a** 是 **0** **，** 新的内即运算只是计算和支持向量之间



----

### 2. 线性支持向量机 --- 软间隔最大化

1. 训练数据往往都是线性不可分的，如噪声，异常点等等
   数据线性不可分，那么约束的不等式就不成立了， 就需要修改为软间隔最大分类，如下图所示

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/l12xf.jpg)

2. 某些即使线性可分，但是分割影响模型的泛化

   ![](https://github.com/LiuChuang0059/large_file/blob/master/pic/mooxl.jpg)




#### 1. 松弛变量

* 线性不可分意味着，对于某些点，不满足约束条件 $$y*(w*x + b) > 1$$

* 对于每一个样本点，引入一个松弛变量 l >= 0 ; $$y*(w*x + b) + l > 1$$

* 对于每一个松弛变量，在目标函数中对应一个代价，所以目标函数变为：

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/9i5yx.jpg)



* C> 0  称为惩罚参数， C 值大，对于误分类的惩罚大。

* **若** **C ——> 0** ,误差分量实质消失，目标最大化间隔

  **若** **C ——> ∞** ,间隔消失，目标最小化误差损失

* 最小化目标函数： 间隔尽量大 + 误分类的点尽量少

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/oj8ly.jpg)

**k** **决定了误差损失的形式，**

**K = 1** ,称为 **hinge loss**    **通常选择，也是本文后面内容推导选择**

**K = 2 ,** **称为** **quadratic loss**



#### 2. 求解参数

同样的 拉格朗日方法

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/4zez6.jpg)

消除 W, b 转化为

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/itgf0.jpg)

相比于上文的硬间隔分类，只是多了一个 a 的范围限制条件

还是通过 **SMO**  算法求解 参数



#### 3. 软间隔的支持向量 ------稍微复杂些

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/z1dz9.jpg)



软间隔最大化时 KKT 条件中的对偶互补条件

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/nbtah.jpg)



1.  对于  a = 0

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/1i50u.jpg)

所以 样本点被正确的分类



2.    0< a <. C. ——— 向量为支持向量

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/i6rwu.jpg)

所以 推导得到

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/i3ue7.jpg)

样本点在 间隔上面，属于支持向量



3.   a = C   ——— 向量为支持向量

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/76crc.jpg)

如图所示，$\xi$  表示 点和其所属类支持向量平面的之间的距离

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/b9y8m.jpg)

或者可以用下面的条件 进行分析，来判断样本点的位置

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/3dw0c.jpg)

**无论样本点在哪，这些点都是软间隔的支持向量，**$\xi$  表示偏差。



-----

### 3. 非线性支持向量机和 核函数



#### 1. 存在线性不可分的数据

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/j0902.jpg)



观察上面的图，可以发现，对于线性不可分的数据，映射到高维空间中，就可以继续应用线性可分的 SVM思想来解决问题

**但是这样存在一个问题，初始数据维度高，再向高维映射，维度会有爆炸性的增长**** 。**而且特征空间的维度一般都很高，甚至是无穷维的，例如后文的高斯核**


#### 1. 核技巧(kernel trick)

通过非线性变换，将非线性问题变换为线性问题

核技巧应用到 SVM 中的核心思想 ： 通过一个非线性变换将输入空间转 对应到一个特征空间，使得输入空间的超曲面模型转换为 特征空间的超平面模型

避免显式地将输入空间的每一个点映射到特征空间中，输入对象用他们之间的$n·n$ 成对相似度来表示。其中相似度函数称为kernel

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/hpiv3.jpg)

> 核方法不再将数据看作输入空间或者特征空间的向量，而是仅仅考虑点对之间的核值。
>
> 可以看作 **n** **个输入点的完全图带权邻接矩阵**


#### 2， 核函数定义

* X 为输入空间， H 为特征空间存在一个 X 到 H 的映射； $\phi (x)  X--> H$

* 对于 $ x,z \in X  , K(x,z) = \phi (x) * \phi(z)$

* K(x,z)  就是核函数， 使用的时候，不显示的定义 映射函数，而是直接 **依据领域知识选取核函数**

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/zqgj9.jpg)

这样可以 **在特征空间中直接计算内积 〈φ(xi · φ(x)〉，**而不是，先将特征映射到高维空间，之后再在高维空间做线性分类。

> **学习是隐式的在特征空间进行，不需要显示的定义特征空间和映射函数**


#### 3. 常用核函数

* 多项式核函数 ： p次多项式分类器
* 高斯核函数 ： 高斯径向基函数分类器、
* 字符串核函数

-----



### 4. SMO 优化算法 -- 序列最小最优化算法


> 暂不详

-----



# 参考

1. 统计学习方法 --- 李航
2. https://github.com/Avik-Jain/100-Days-Of-ML-Code/blob/master/Code/Day%2013%20SVM.md
3. 机器学习实战
4. **刘建平** **个人博客：[http://www.cnblogs.com/pinard/p/6100722.html](http://www.cnblogs.com/pinard/p/6100722.html)
5.  [**Please explain Support Vector Machines (SVM) like I am a 5 year old**](https://link.zhihu.com/?target=https%3A//www.reddit.com/r/MachineLearning/comments/15zrpp/please_explain_support_vector_machines_svm_like_i/)
6.  [https://blog.csdn.net/qq_26898461/article/details/50481803](https://blog.csdn.net/qq_26898461/article/details/50481803)
7. [https://medium.com/coinmonks/support-vector-regression-or-svr-8eb3acf6d0ff](https://medium.com/coinmonks/support-vector-regression-or-svr-8eb3acf6d0ff)
8.  [http://kernelsvm.tripod.com/](http://kernelsvm.tripod.com/)
9. 数据挖掘与分析， 概念与算法
10. Sklearn 官方网站


