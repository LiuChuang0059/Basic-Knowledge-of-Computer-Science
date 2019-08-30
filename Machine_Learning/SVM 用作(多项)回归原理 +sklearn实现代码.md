---


# 0. SVM 原理分析参见上一篇[笔记](https://liuchuang0059.github.io/2019/04/22/SVM%20%E5%8E%9F%E7%90%86%E8%AF%A6%E8%BF%B0/)


----


# 1. SVM用作回归问题 — SV regression

### 1. 回归问题

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/nnbg4.jpg)

- **对于使用** **支持向量机做** **回归预测，主要是拟合一个函数f(x)，使得所有的预测值的误差都小于** **ε**（小于就可以）
- 阴影部分叫做 margin，就是回归的范围
- **不同于一般的回归**
- **同时要使得** 拟合函数尽可能Flatness，就是 **w** **尽可能小**

### 2. 优化

类似于 SVM非分析，抽象成一个优化问题

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/hacm2.jpg)

对于软间隔 soft-margin，允许偏离部分

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/86mmz.jpg)

### 3. 求解

拉格朗日求解

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/k90o7.jpg)



### 4. Kernel 进行 mapping

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/8151s.jpg)

### 5. 完整的回归步骤

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/pbeph.jpg)

----


# 2. SVR 回归 Code — Sklearn





```python
from sklearn.svm import SVR
X_train, y_train, X_test, y_test = load_dataset(filename)

svr_rbf = SVR(kernel='rbf', C=100, gamma=0.01, epsilon=.1)  # 高斯核
svr_lin = SVR(kernel='linear', C=100, gamma='auto') # 线性核
svr_poly = SVR(kernel='poly', C=100, gamma='auto', degree=3, epsilon=.1,
               coef0=1) # 多项式核
y_rbf = svr_rbf.fit(X_train, y_train).predict(X_test)
y_lin = svr_lin.fit(X_train, y_train).predict(X_test)
y_poly = svr_poly.fit(X_train, y_train).predict(X_test)


from sklearn.metrics import mean_squared_error
mse = mean_squared_error(y_test, y_poly) # 计算 MSE
from sklearn.metrics import mean_squared_error,mean_absolute_error
mae = mean_absolute_error(y_test, y_rbf) # 计算 MAE
```

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/72csl.jpg)

-----


# 3. SVR 用于多项回归

### 1. 多项回归实现原理—— sklearn

> **This strategy consists of fitting one regressor per target. Since each target is represented by exactly one regressor it is possible to gain knowledge about the target by inspecting its corresponding regressor.**

* 每一项回归使用一个回归器，
* 没有用到数据之间的关联

### 2. Multi SVR — code

```python
from sklearn.multioutput import MultiOutputRegressor
from sklearn.svm import SVR

X_train, y_train, X_test, y_test = load_dataset2(filename)
svr_rbf_multi = MultiOutputRegressor(SVR(kernel='rbf', C=100, gamma=0.1, epsilon=.1))
y_rbf_multi = svr_rbf_multi.fit(X_train, y_train).predict(X_test)

def plot_result2(i,y_test,y_pred):

    # 绘制完整预测曲线  i 为测试集

    #plt.figure(figsize=(42, 20))
    plt.plot(y_pred[i,:], color='blue', lw=3, label='Predicted')
    plt.plot(y_test[i,:], color='red', lw=3, label='Raw-dose')
    plt.xlabel('Depth(mm)', fontsize='40')
    plt.ylabel('Nomalized Intensity', fontsize='40')
    plt.xticks(fontsize=30)
    plt.yticks(fontsize=30)
    #plt.text(250, 0.8, 'SNR = 3 ', fontsize='40')
    plt.legend(fontsize=30)
    #plt.show()

plt.figure(figsize=(42, 10))
for i in range(3):
    plt.subplot(1,3,i+1)
    plot_result2(i,y_test,y_rbf_multi)
```

rbf 核预测结果

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/1kmzd.jpg)



-----





```python
from sklearn.svm import SVR
from sklearn.multioutput import MultiOutputRegressor

svr_rbf = MultiOutputRegressor(SVR(kernel='rbf', C=100, gamma=0.1, epsilon=.1))
svr_lin = MultiOutputRegressor(SVR(kernel='linear', C=100, gamma='auto'))
svr_poly = MultiOutputRegressor(
    SVR(kernel='poly', C=100, gamma='auto', degree=3, epsilon=.1, coef0=1))

lw = 3

svrs = [svr_rbf, svr_lin, svr_poly]
kernel_label = ['RBF', 'Linear', 'Polynomial']
model_color = ['m', 'c', 'g']
model_color2 = ['c', 'g', 'm']

fig, axes = plt.subplots(nrows=1, ncols=3, figsize=(30, 10), sharey=True)
for ix, svr in enumerate(svrs):
    axes[ix].plot((svr.fit(X_train, y_train).predict(X_test))[1,:], color='r', lw=lw,
                  label='{} Real'.format(kernel_label[ix]))
    axes[ix].plot(y_test[1,:], color='b', lw=lw,label='{} Predicted'.format(kernel_label[ix]))
    axes[ix].legend(loc='upper center', bbox_to_anchor=(0.5, 1.1),
                    ncol=1, fancybox=True, shadow=True,fontsize= 20)

fig.text(0.5, 0.04, 'data', ha='center', va='center')
fig.text(0.06, 0.5, 'target', ha='center', va='center', rotation='vertical')
fig.suptitle("Support Vector Regression", fontsize=20)
plt.show()

```

不同核函数预测结果

![image-20190422162121266](https://github.com/LiuChuang0059/large_file/blob/master/pic/0x17l.jpg)



----

rbf 参数调节

```python
C_2d_range = [1e-2,1e-1, 1e1, 1e2,1e3]
gamma_2d_range = [1e-2,1e-1, 1, 1e1]
regressions = []
for C in C_2d_range:
    for gamma in gamma_2d_range:
        multi_reg = MultiOutputRegressor(
            SVR(kernel='rbf', C=C, gamma=gamma, epsilon=.1))
        multi_reg.fit(X_train, y_train)
        regressions.append((C, gamma, multi_reg))

plt.figure(figsize=(30, 20))
for (k, (C, gamma, multi_reg)) in enumerate(regressions):

    y_rbf_multi = multi_reg.predict(X_test)

    plt.subplot(len(C_2d_range), len(gamma_2d_range), k + 1)
    plt.title("gamma=10^%d, C=10^%d" % (np.log10(gamma), np.log10(C)),
              size='large')
    plot_result3(2,y_test,y_rbf_multi)
    plt.xticks(())
    plt.yticks(())
    plt.axis('tight')
```

调参数结果图

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/ebjuo.jpg)

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


