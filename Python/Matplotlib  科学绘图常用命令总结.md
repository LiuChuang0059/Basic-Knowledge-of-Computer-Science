
## 0. 万能钥匙 --- [Gallery](https://matplotlib.org/3.1.1/gallery/index.html)

使用 MAtplotlib 绘制图形的 示例， 特别丰富，包含源码

可以直接先去找一圈大体符合自己标准的


----


## 1. 基础绘图

```python
import matplotlib.pyplot as plt

plt.figure(figsize=(12, 9), facecolor= "lightgray") # 图片大小和底色
X_test3 = [i for i in range(10)]
Y_test3 = [i*i for i in range(5)]
plt.plot(X_test3, color='green',lw=4, linestyle = '--', label='y = x')
plt.plot(Y_test3, color='red', lw=4, marker='*',ms=20, label='y = x*x')
plt.title(" A summury of plot", color = "b", fontsize = 40) # s设置标题
plt.xlabel('Depth(mm)', fontsize='50', color = "k")   # 设置坐标名称
plt.ylabel('Nomalized Intensity', fontsize='50')
plt.xticks(fontsize=30)  # 设置坐标轴大小
plt.yticks(fontsize=30,color = "b")
plt.xlim((0,10))
plt.ylim((0,20))   # 手动设定坐标轴的范围
plt.legend(fontsize = 30)  # 显示图例 ，以及图例字体
plt.text(5,2, "Test to insert text.",fontsize = 30, color = "y")    # 图片中添加文字
#plt.savefig("Dose_2.png", dpi = 100, bbox_inches='tight')   # 保存图片
```

![](https://i.loli.net/2019/07/29/5d3ef692b9a1429721.png)



1. plt.figure() :
Matplotlib 的图像都位于 Figure 对象之中，

> The Figure instance returned will also be passed to new_figure_manager in the backends, which allows to hook custom Figure classes into the pylab interface. Additional kwargs will be passed to the figure init function。

```python
figure(num=None, figsize=None, dpi=None, facecolor=None, edgecolor=None, frameon=True, FigureClass=Figure, clear=False, **kwargs)

```


* num : Matlab 式编号架构

如果编号已经存在，则激活相应 figure 对象， 如果不存在， 创建一个新的

使用 plt.gcf() 获得当前 fig 的引用

![](https://i.loli.net/2019/07/29/5d3ebf0a76fbf30512.png)

* figsize : width, height in inches


* dpi： 图形分辨率， 默认 72

![](https://i.loli.net/2019/07/29/5d3ec02261f3f51109.png)

* facecolor 背景颜色，


* edgecolor 边框颜色

* frameon 是否绘制边框




2. plt.plot() ：

* color 线条颜色
* lw： 曲线宽度
* linestyle : 曲线的类型
* marker: 曲线上点的标记
* ms： 标记的大小
* label ： 曲线的图里名称


3. plt.xlabel() ： 设置坐标名称

4. plt.xticks(fontsize=30) ： 设置坐标轴大小

5. plt.xlim((0,10)) ： 手动设定坐标轴的范围

6. plt.legend(fontsize = 30) ： 显示图例 ，以及图例字体

7. plt.text(5,2, "Test to insert text.",fontsize = 30, color = "y") ： 图片中添加文字

> plt.text(x, y, s, fontdict=None, withdash=False, ** kwargs)



6. plt.savefig("Dose_2.png", dpi = 100, bbox_inches='tight')



* 保存图片 ： 绝对路径

* dpi ： 保存的饿图片的分辨率

* bbox_inches='tight' 去除图片周边的白色


---------

## 2. 更好更快的选择绘图颜色


使用 seaborn 库

```python
import seaborn as sns
c=sns.color_palette("hls", 15)
sns.palplot(c)
```

![](https://i.loli.net/2019/07/29/5d3f10f060ad914608.png)

调用的时候，使用

```python

color = c[0] # 对应的就是第一个颜色

```


-----


## 3. 调整绘制图形的边框是否可见

```python

plt.figure(figsize=(12, 9))
X_test3 = [i for i in range(10)]
Y_test3 = [i*i for i in range(5)]
plt.plot(X_test3, color='green',lw=4, linestyle = '--', label='y = x')
plt.plot(Y_test3, color='red', lw=4, marker='*',ms=20, label='y = x*x')
plt.title(" A summury of plot", color = "b", fontsize = 40)
plt.xlabel('Depth(mm)', fontsize='50', color = "k")
plt.ylabel('Nomalized Intensity', fontsize='50')
plt.xticks(fontsize=30)
plt.yticks(fontsize=30,color = "b")
plt.xlim((0,10))
plt.ylim((0,20))
plt.legend(fontsize = 30)
plt.text(5,2, "Test to insert text.",fontsize = 30, color = "y")




frame = plt.gca()
#frame.axes.get_yaxis().set_visible(False) # y 轴不可见
#frame.axes.get_xaxis().set_visible(False) # x 轴不可见
frame.axes.spines['top'].set_visible(False)
frame.axes.spines['right'].set_visible(False)

```


![](https://i.loli.net/2019/07/29/5d3f14fdafbe959524.png)


------

## 4. 直方图纵坐标转化为百分比


```python




plt.subplot(132)
plt.hist(
    per50_list,
    bins=15,
    density=0,
    facecolor=c[8],
    edgecolor="black",
    alpha=0.8)

plt.xlabel("REL50 (%)", fontsize='60') # 显示纵轴标签
plt.ylabel("Percentage", fontsize='60')
plt.xticks(fontsize=45)
plt.yticks(fontsize=45)

frame = plt.gca()
#frame.axes.get_yaxis().set_visible(False)# y 轴不可见
#frame.axes.get_xaxis().set_visible(False) # x 轴不可见
frame.axes.spines['top'].set_visible(False)
frame.axes.spines['right'].set_visible(False)


def to_percent(y, position):
    return str(y / 200 * 100) + '%'

from matplotlib.ticker import FuncFormatter
formatter = FuncFormatter(to_percent)
plt.gca().yaxis.set_major_formatter(formatter)




```

![](https://i.loli.net/2019/07/29/5d3f1633b9b2062890.png)


-----

## 5. 条形图对应显示数据


```python


plt.subplot(131)
left_1 = range(len(data))
rects1 = plt.bar(x=left_1, height=data, width=0.6, alpha=0.8, color=c[13]) # 参数依次： x轴， 高度数据， 条形宽度， 条形透明度， 条形颜色
plt.ylabel("Percentage", fontsize='60')
plt.xlabel("Absolute Shift(mm)", fontsize='60')
plt.xticks([index for index in left_1], left_1)
plt.xticks(fontsize=45)
plt.yticks(fontsize=45)
for a, b in zip(left_1, data):
    plt.text(
        a,
        b + 0.05,
        '%.1f' % (b / 200 * 100) + '%',
        ha='center',
        va='bottom',
        fontsize=50)  #  加入数字注释

```

![](https://i.loli.net/2019/07/29/5d3f1633b9b2062890.png)



-----

## 6.  设置字体字典


调整图中文字的字体，大小，颜色，是否加粗等


```python

font = {
    'family': 'serif',   # 字体
    'style': 'italic',   # 斜体
    'weight': 'bold',   # 是否加粗
    'color': 'red',
    'size': 30
}


font2 = {
    'family': 'serif',
    'style': 'normal',
    'weight': 'bold',
    'size': 30
}

font3 = {
    'family': 'Times New Roman',
    'style': 'normal',
    'weight': 'bold',
    'size': 50
}

```


使用

```python
plt.ylabel('Nomalized Intensity', **font)


```


示例：

```python

plt.figure(figsize=(12, 9))
X_test3 = [i for i in range(10)]
Y_test3 = [i*i for i in range(5)]
plt.plot(X_test3, color='green',lw=4, linestyle = '--', label='y = x')
plt.plot(Y_test3, color='red', lw=4, marker='*',ms=20, label='y = x*x')
plt.title(" A summury of plot", **font3)
plt.xlabel('Depth(mm)', **font)
plt.ylabel('Nomalized Intensity', **font)
plt.xticks(fontsize=30)
plt.yticks(fontsize=30,color = "b")
plt.xlim((0,10))
plt.ylim((0,20))
plt.legend(prop =font3)    # 不能使用   **
plt.text(5,2, "Test to insert text.",**font)

```

![](https://i.loli.net/2019/07/30/5d3f1b91c9dbc18006.png)


------




## 7. 绘制非均匀分布多子图

```python

rect_d = [0.1, 0.1, 0.85, 0.15]   # [left, bottom, width, height]
rect_m = [0.1, 0.25, 0.85, 0.6]
rect_u = [0.1, 0.85, 0.85, 0.15]

# start with a rectangular Figure
plt.figure(1, figsize=(30, 15))

ax_u = plt.axes(rect_u)
ax_u.plot(Hu, color='black', lw=6)
ax_u.set_xlim((0, 250))
plt.yticks(fontsize=40)
ax_u.set_ylabel('HU Value', font3)
plt.locator_params('y', nbins=4)
#ax_u.set_yticklabels([0.0,-1000,500])
plt.setp(ax_u.get_xticklabels(), visible=False)

####  底部图

ax_d = plt.axes(rect_d)
per_list = []
per_array = np.array(per_list) * 100
x = range(len(per_list))
markerline, stemlines, baseline = ax_d.stem(x, per_array, '--')
plt.setp(
    markerline,
    marker='D',
    markersize=4,
    markeredgecolor="blue",
    markeredgewidth=1)
plt.setp(stemlines, linestyle="-", color="blue", linewidth=3)
plt.setp(baseline, visible=False)
plt.grid(True, axis='y', linestyle='--')
ax_d.set_xlim((0, 250))
#ax_d.set_ylim((0,10))
ax_d.set_ylabel("REL (%)", font3)
ax_d.set_xlabel('Depth(mm)', font3)
ax_d.set_yticklabels([0.0, 0.0, 20.0])
#ax_d.set_yticks([0,5,10])
plt.locator_params('y', nbins=2)
plt.xticks(fontsize="50")
plt.yticks(fontsize="50")

##### 中间绘图

ax_m = plt.axes(rect_m)
ax_m.plot(X_test3, color='green', lw=6, label='Activity')
ax_m.plot(Y_test3, color='red', lw=6, label='Raw_dose ')
ax_m.plot(predicted_test3, color='blue', lw=6, label='Predicted_dose ')
ax_m.set_ylabel('Nomalized Intensity', font3)
ax_m.set_xlim((0, 250))
plt.yticks(fontsize=50)
plt.legend(prop=font)

plt.setp(ax_m.get_xticklabels(), visible=False)
#plt.savefig("Test2_angle_3.png", bbox_inches='tight', dpi=300)
plt.show()

```






### 1. 设置多子图之间的坐标轴不可见

```python
plt.setp(ax_u.get_xticklabels(), visible=False)

```



![](https://i.loli.net/2019/07/30/5d3f1d80413e231200.png)

----


## 8. 坐标轴显示刻度数


```python

plt.locator_params('y', nbins=2)

```


纵坐标，只显示两个刻度， 见上图


-----



## 9. 绘制中间有值空缺的 图



空缺值 使用 np.NAN 代替。

```python

ax_u = plt.axes(rect_u)
X_test3 = X_train_0[k]
Hu =list(X_test3[:,1])
for i in range(len(Hu)):
    if Hu[i] < -200 or Hu[i] > 200:
        Hu[i] = np.NAN
ax_u.plot(Hu, color='black', lw=6)
ax_u.set_xlim((0, 250))
plt.yticks(fontsize=40)
ax_u.set_ylabel('HU Value', font3)
plt.locator_params('y', nbins=4)
#ax_u.set_yticklabels([0.0,-1000,500])
plt.setp(ax_u.get_xticklabels(), visible=False)


```
![](https://i.loli.net/2019/07/30/5d3f1e4198d8096469.png)


----


## 10. pcolor 绘制 二维图


```python

fig, ax = plt.subplots(figsize=(40, 20))
b = np.swapaxes(h_state[114], 0, 1)  # 交还矩阵

c = ax.pcolor(b, cmap='RdBu', vmin=-1, vmax=1)  # cmap 选择颜色图
ax.set_xticklabels([0, 50, 100, 150, 200, 250, 300], fontsize=40) # 坐标轴的刻度
ax.set_yticklabels([0, 5, 10, 15, 20, 25, 30], fontsize=40)
ax.set_xlabel('Depth', fontsize=50)
ax.set_ylabel('Units', fontsize=50)
#ax0.set_title('default: no edges')
#fig.subplots_adjust(right=0.8)
#cbar_ax = fig.add_axes([0.85, 0.15, 0.05, 0.7])
e = fig.colorbar(c, ax=ax) # 绘制 Colorbar
e.ax.tick_params(labelsize='large')  # 调整 coloebar 字体的， 调整不明显， 使用matlab 绘图会好些

# 带网格线的
# c = ax[1].pcolor(b, edgecolors='k', linewidths=1)
# ax1.set_title('thick edges')

#fig.tight_layout()
plt.savefig("LSTM_colorbar",bbox_inches='tight')
plt.show()


```

![](https://i.loli.net/2019/07/30/5d3f21f428af418704.png)





---


## 11. 自定义 Colorbar

```python

import matplotlib
import matplotlib.colors as col
import matplotlib.cm as cm
import matplotlib.pyplot as plt
import numpy as np


startcolor = '#15b01a'   # 可以自行修改
midcolor = '#ffffff'     # 可以自行修改
#midcolor = '#56fca2'
endcolor = '#1805db'    # 可以自行修改
cmap2 = col.LinearSegmentedColormap.from_list('own2',[startcolor,midcolor,endcolor])
# extra arguments are N=256, gamma=1.0
cm.register_cmap(cmap=cmap2)
# we can skip name here as it was already defined
#cm.get_cmap('own2')

### test 结果
im = np.outer(np.ones(10), np.linspace(0, 255, 256))
fig = plt.figure(figsize=(9, 2))
ax = fig.add_subplot('111')
ax.set_xticks(np.linspace(0, 255, 3))
ax.set_xticklabels([0, 0.5, 1])
ax.imshow(im, interpolation='nearest', cmap='RdBu')
```

![](https://i.loli.net/2019/07/30/5d3f20a12fc5885474.png)



-------


## 12. Subplot 用法：

### 1. 创建多子图

```python
fig = plt.figure()
ax1= fig.add_subplot(2,2,1)
ax2= fig.add_subplot(2,2,2)
ax3= fig.add_subplot(2,2,3)
```


![](https://i.loli.net/2019/07/30/5d3fa5d2997e622675.png)



### 2.  多子图内绘制

```python

import numpy as np
fig = plt.figure()
ax1= fig.add_subplot(2,2,1)
ax2= fig.add_subplot(2,2,2)
ax3= fig.add_subplot(2,2,3)
ax1.hist(np.random.randn(200),bins=20,color= "k")
ax2.scatter(np.arange(30), np.arange(30)+ 3 * np.random.randn(30))


```

![](https://i.loli.net/2019/07/30/5d3fecb2ea84792969.png)


### 3. plt.subplots 绘制多子图

* 创建一个 figure 和一组 subplots

plt.subplots(nrows=1, ncols=1, sharex=False, sharey=False, squeeze=True, subplot_kw=None, gridspec_kw=None, ** fig_kw)

1. nrows ncols ： 行数，列数

2. sharex, sharey 使用相同刻度 : bool or {'none', 'all', 'row', 'col'}, default: False

* True : = all , False = None

* 'row', 'col' 每一行的子图或者每一列的子图共享坐标轴



3. subplot_kw=None， 创建各个 subplot 的关键字字典。

```python
x = np.linspace(0, 2*np.pi, 400)
y = np.sin(x**2)

fig, axes = plt.subplots(2, 2, subplot_kw=dict(polar=True))
axes[0, 0].plot(x, y)
axes[1, 1].scatter(x, y)

```


![](https://i.loli.net/2019/07/30/5d3ff17435d0b74822.png)


4. gridspec_kw : 创建 grid 的参数

```python

x = np.linspace(0, 2*np.pi, 400)
y = np.sin(x**2)

fig, axes = plt.subplots(2, 2, gridspec_kw=dict(wspace = 2, hspace = 2))
axes[0, 0].plot(x, y)
axes[1, 1].scatter(x, y)


```




![](https://i.loli.net/2019/07/30/5d3ff47957b5681922.png)

5.  ** fig_kw ：接受其他的关键字

```pytho
plt.subplots(2,2 ,figsize = (8,6))

```



### 4. 调整subplot 间距

subplots_adjust(left=None, bottom=None, right=None, top=None,
                  wspace=None, hspace=None)

The parameter meanings (and suggested defaults) are::

```python

  left  = 0.125  # the left side of the subplots of the figure
  right = 0.9    # the right side of the subplots of the figure
  bottom = 0.1   # the bottom of the subplots of the figure
  top = 0.9      # the top of the subplots of the figure
  wspace = 0.2   # the amount of width reserved for space between subplots,
                 # expressed as a fraction of the average axis width
  hspace = 0.2   # the amount of height reserved for space between subplots,
                 # expressed as a fraction of the average axis height
```


----

## 13. 添加注解和 指向箭头

### 1. 基本使用

```python
fig, ax = plt.subplots()

t = np.arange(0.0, 5.0, 0.01)
s = np.cos(2*np.pi*t)
line, = ax.plot(t, s, lw=2)

ax.annotate('local max', xy=(2, 1), xytext=(3, 1.5),
            arrowprops=dict(facecolor='black', shrink=0.05),
            )
ax.set_ylim(-2, 2)
plt.show()
```

![](https://i.loli.net/2019/07/30/5d3ff81e900f126694.png)

* xy 指向点的坐标

* xytext ： 文本注释的坐标

* arrowprops 箭头样式


### 2. 使用不同的坐标系 定位 字符

```python
fig, ax = plt.subplots()

t = np.arange(0.0, 5.0, 0.01)
s = np.cos(2*np.pi*t)
line, = ax.plot(t, s, lw=2)
ax.annotate('local max', xy=(3, 1),  xycoords='data',
            xytext=(0.8, 0.95), textcoords='axes fraction',
            arrowprops=dict(facecolor='black', shrink=0.05),
            horizontalalignment='right', verticalalignment='top',
            )
ax.set_ylim(-2, 2)

```


* 上面 textcoords='axes fraction'  ： text 文本的坐标系基于 坐标轴，而不是数据

* 0,0 is lower left of axes and 1,1 is upper right

![](https://i.loli.net/2019/07/30/5d3fffdabf82886780.png)


### 3. 调整  arrowprops 样式
```
Key          Description
    ==========   ======================================================
    width        the width of the arrow in points
    headwidth    the width of the base of the arrow head in points
    headlength   the length of the arrow head in points
    shrink       fraction of total length to 'shrink' from both ends
    **kwargs     any key to :class:`matplotlib.patches.FancyArrowPatch`
    ==========   ======================================================

```

```python
fig = plt.figure()
ax = fig.add_subplot(111, polar=True)
r = np.arange(0,1,0.001)
theta = 2 * 2*np.pi * r
line, = ax.plot(theta, r, color='#ee8d18', lw=3)

ind = 800
thisr, thistheta = r[ind], theta[ind]
ax.plot([thistheta], [thisr], 'o')
ax.annotate('a polar annotation',
            xy=(thistheta, thisr),  # theta, radius
            xytext=(0.05, 0.05),    # fraction, fraction
            textcoords='figure fraction',
            arrowprops=dict(facecolor='black', shrink=0.05),
            horizontalalignment='left',
            verticalalignment='bottom',
            )
plt.show()

```
arrowprops=dict(facecolor='black', shrink=0.05),
            horizontalalignment='left',
            verticalalignment='bottom',)

* horizontalalignment ， verticalalignment 箭头位置

![](https://i.loli.net/2019/07/30/5d40070f0c87061050.png)



**详细参考 [Annotations](https://matplotlib.org/3.1.1/tutorials/text/annotations.html)**


-----

## 14. 修改 Matplotlib 默认绘图配置


Matplotlib 自带默认的配置信息，如果有必要，可以调整默认配置


```python

plt.rc(figure,figsize = (10,10))

font = {'family' : 'monospace',
        'weight' : 'bold',
        'size'   : 'larger'}

rc('font', **font)  # pass in the font dict as kwargs

rcdefaults()  # restore the defaults
```

* figure : 希望自定义的对象 例如： axes, xtick , legend, grid deng


* 也可以直接将配置信息写成字典之后导入



**全部的自定义选项在 matplotlibrc 文件里**

![](https://i.loli.net/2019/07/30/5d400cda01b7793403.png)


下面是一些常用的命令的默认配置

```
#### LINES
#lines.linewidth   : 1.5     ## line width in points
#lines.linestyle   : -       ## solid line
#lines.color       : C0      ## has no affect on plot(); see axes.prop_cycle
#lines.marker      : None    ## the default marker
#lines.markeredgewidth  : 1.0     ## the line width around the marker symbol
#lines.markersize  : 6            ## markersize, in points
#lines.dash_joinstyle : round        ## miter|round|bevel
#lines.dash_capstyle : butt          ## butt|round|projecting
#lines.solid_joinstyle : round       ## miter|round|bevel
#lines.solid_capstyle : projecting   ## butt|round|projecting
#lines.antialiased : True         ## render lines in antialiased (no jaggies)

#### FONT
#font.family         : sans-serif
#font.style          : normal
#font.variant        : normal
#font.weight         : normal
#font.stretch        : normal

#### AXES
## default face and edge color, default tick sizes,
## default fontsizes for ticklabels, and so on.  See
## http://matplotlib.org/api/axes_api.html#module-matplotlib.axes
#axes.facecolor      : w       ## axes background color
#axes.edgecolor      : k       ## axes edge color
#axes.linewidth      : 0.8     ## edge linewidth
#axes.grid           : False   ## display grid or not
#axes.grid.axis      : both    ## which axis the grid should apply to
#axes.grid.which     : major   ## gridlines at major, minor or both ticks
#axes.titlesize      : large   ## fontsize of the axes title
#axes.titleweight    : normal  ## font weight of title
#axes.titlepad       : 6.0     ## pad between axes and title in points
#axes.labelsize      : medium  ## fontsize of the x any y labels
#axes.labelpad       : 4.0     ## space between label and axis
#axes.labelweight    : normal  ## weight of the x and y labels
#axes.labelcolor     : k


#### TICKS
## see http://matplotlib.org/api/axis_api.html#matplotlib.axis.Tick
#xtick.top            : False  ## draw ticks on the top side
#xtick.bottom         : True   ## draw ticks on the bottom side
#xtick.labeltop       : False  ## draw label on the top
#xtick.labelbottom    : True   ## draw label on the bottom
#xtick.major.size     : 3.5    ## major tick size in points
#xtick.minor.size     : 2      ## minor tick size in points
#xtick.major.width    : 0.8    ## major tick width in points
#xtick.minor.width    : 0.6    ## minor tick width in points
#xtick.major.pad      : 3.5    ## distance to major tick label in points
#xtick.minor.pad      : 3.4    ## distance to the minor tick label in points
#xtick.color          : k      ## color of the tick labels
#xtick.labelsize      : medium ## fontsize of the tick labels
#xtick.direction      : out    ## direction: in, out, or inout
#xtick.minor.visible  : False  ## visibility of minor ticks on x-axis
#xtick.major.top      : True   ## draw x axis top major ticks
#xtick.major.bottom   : True   ## draw x axis bottom major ticks
#xtick.minor.top      : True   ## draw x axis top minor ticks
#xtick.minor.bottom   : True   ## draw x axis bottom minor ticks
#xtick.alignment      : center ## alignment of xticks

#ytick.left           : True   ## draw ticks on the left side
#ytick.right          : False  ## draw ticks on the right side
#ytick.labelleft      : True   ## draw tick labels on the left side
#ytick.labelright     : False  ## draw tick labels on the right side
#ytick.major.size     : 3.5    ## major tick size in points
#ytick.minor.size     : 2      ## minor tick size in points
#ytick.major.width    : 0.8    ## major tick width in points
#ytick.minor.width    : 0.6    ## minor tick width in points
#ytick.major.pad      : 3.5    ## distance to major tick label in points
#ytick.minor.pad      : 3.4    ## distance to the minor tick label in points
#ytick.color          : k      ## color of the tick labels
#ytick.labelsize      : medium ## fontsize of the tick labels
#ytick.direction      : out    ## direction: in, out, or inout
#ytick.minor.visible  : False  ## visibility of minor ticks on y-axis
#ytick.major.left     : True   ## draw y axis left major ticks
#ytick.major.right    : True   ## draw y axis right major ticks
#ytick.minor.left     : True   ## draw y axis left minor ticks
#ytick.minor.right    : True   ## draw y axis right minor ticks
#ytick.alignment      : center_baseline ## alignment of yticks



#### Legend
#legend.loc           : best
#legend.frameon       : True     ## if True, draw the legend on a background patch
#legend.framealpha    : 0.8      ## legend patch transparency
#legend.facecolor     : inherit  ## inherit from axes.facecolor; or color spec
#legend.edgecolor     : 0.8      ## background patch boundary color
#legend.fancybox      : True     ## if True, use a rounded box for the
                                 ## legend background, else a rectangle
#legend.shadow        : False    ## if True, give background a shadow effect
#legend.numpoints     : 1        ## the number of marker points in the legend line
#legend.scatterpoints : 1        ## number of scatter points
#legend.markerscale   : 1.0      ## the relative size of legend markers vs. original
#legend.fontsize      : medium

#### FIGURE
#figure.titlesize : large      ## size of the figure title (Figure.suptitle())
#figure.titleweight : normal   ## weight of the figure title
#figure.figsize   : 6.4, 4.8   ## figure size in inches
#figure.dpi       : 100        ## figure dots per inch
#figure.facecolor : w      ## figure facecolor; 0.75 is scalar gray
#figure.edgecolor : w      ## figure edgecolor
#figure.frameon : True          ## enable figure frame
#figure.max_open_warning : 20  ## The maxi

```

-----

# 参考：

1. 利用 python 进行数据分析 -- Wes McKinney

2. [matplotlib figure函数学习笔记](https://blog.csdn.net/zjyklwg/article/details/79477261)

3. [How to change fonts in matplotlib (python)?](https://stackoverflow.com/questions/21321670/how-to-change-fonts-in-matplotlib-python)

4. [matplotlib.pyplot.subplots](https://matplotlib.org/3.1.1/api/_as_gen/matplotlib.pyplot.subplots.html)

5. [Annotations](https://matplotlib.org/3.1.1/tutorials/text/annotations.html)












