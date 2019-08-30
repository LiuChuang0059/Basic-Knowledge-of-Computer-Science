原文 ： 10 Simple hacks to speed up your Data Analysis in Python

link ： https://towardsdatascience.com/10-simple-hacks-to-speed-up-your-data-analysis-in-python-ec18c6396e6b

----


编程时候一个小技巧 很可能大幅度提高 生产力



----

## 1. 分析 pandas 中的 dataframe


* 使用 pandas profilings package 可以快速分析 pandas 的 Dataframe


* df.describe() 和 df.info()  两个 函数是 pandas 中通常进行简单数据分析的方法， 但是， 只能给出基本的 overview ， 对于大的数据集， 没有太大的帮助

* Profilings  ： 使用 df.profile_report() 进行快速详细的数据分析



### 1. 安装

```python

pip install pandas-profiling

```

### 2. 使用

```python
import pandas as pd
from pandas import DataFrame
import pandas_profiling

df = pd.read_excel('train_3.xlsx')
df.profile_report()


```

数据报告 导出到 html

```python

profile = df.profile_report(title='Pandas Profiling Report')
profile.to_file(outputfile="Titanic data profiling.html")

```


1. 基本的 profile

![](https://i.loli.net/2019/07/25/5d397269ec36a71961.png)

2. 使用 profiling package 的 profile


![](https://i.loli.net/2019/07/25/5d39732e30ac417420.gif)

3. 输出 html

![](https://i.loli.net/2019/07/25/5d397438d3d1966345.png)



* 信息更加丰富

* 详细 document --- https://pandas-profiling.github.io/pandas-profiling/docs/?source=post_page---------------------------#other-integrations


--------

## 2. pandas  交互性 plot

pandas 有内置的 plot() 函数，方便简单

```python
pd.DataFrame.plot()

```

然而plot 可视化结果不具有交互性

* 使用 Cufflinks 库 来绘制交互性结果

*  [Cufflinks](https://github.com/santosjorge/cufflinks/blob/master/Cufflinks%20Tutorial%20-%20Pandas%20Like.ipynb) 结合了 ploty 的强大 和 pandas 的灵活性



### 1. 安装

```python

pip install plotly
pip install cufflinks

```



-----


## 3. 魔法命令


Magic command 是jupyter notebook 中一些 便捷的函数

* 查看所有的 command

```Python
%lsmagic
```

结果

```
Available line magics:
%alias  %alias_magic  %autocall  %automagic  %autosave  %bookmark  %cat  %cd  %clear  %colors  %config  %connect_info  %cp  %debug  %dhist  %dirs  %doctest_mode  %ed  %edit  %env  %gui  %hist  %history  %killbgscripts  %ldir  %less  %lf  %lk  %ll  %load  %load_ext  %loadpy  %logoff  %logon  %logstart  %logstate  %logstop  %ls  %lsmagic  %lx  %macro  %magic  %man  %matplotlib  %mkdir  %more  %mv  %notebook  %page  %pastebin  %pdb  %pdef  %pdoc  %pfile  %pinfo  %pinfo2  %popd  %pprint  %precision  %profile  %prun  %psearch  %psource  %pushd  %pwd  %pycat  %pylab  %qtconsole  %quickref  %recall  %rehashx  %reload_ext  %rep  %rerun  %reset  %reset_selective  %rm  %rmdir  %run  %save  %sc  %set_env  %store  %sx  %system  %tb  %time  %timeit  %unalias  %unload_ext  %who  %who_ls  %whos  %xdel  %xmode

Available cell magics:
%%!  %%HTML  %%SVG  %%bash  %%capture  %%debug  %%file  %%html  %%javascript  %%js  %%latex  %%markdown  %%perl  %%prun  %%pypy  %%python  %%python2  %%python3  %%ruby  %%script  %%sh  %%svg  %%sx  %%system  %%time  %%timeit  %%writefile

Automagic is ON, % prefix IS NOT needed for line magics.

```

magic command 有两种类型：

* line magics  单 %， 只在单行执行

* cell magics  双 %% ， 在多行执行


### 1. %pastebin

将代码上传到 Pastebin 网站， 返回相应的 URL ，以便将代码分享给别人， 类似于 github gist

```python

%pastebin KNN.py

#### 运行结果

'http://dpaste.com/192FB27'

```
![](https://i.loli.net/2019/07/26/5d39dba667a7e18250.png)


### 2. %matplotlib inline

渲染静态 matplotlib 图

```pytohn
%matplotlib inline

import matplotlib.pyplot as plt
plt.plot([[0,0],[1,1]])
plt.show()


```

![](https://i.loli.net/2019/07/26/5d39dcfc21a9666965.png)

%matplotlib notebook 绘制可以交互处理的 图片


```python
%matplotlib notebook
import matplotlib.pyplot as plt
plt.plot([[0,0],[1,1]])
plt.show()

```

![](https://i.loli.net/2019/07/26/5d39dd29560ce93769.png)



### 3. % run

在 jupyter notbook 中运行 脚本文件

```python
%run time_time.py


##########

1564073384.0277078
costtime:2.5795509815216064
456569
```

### 4. %%writefile

将一个 cell 中的内容可入文件中， 保存到当前文件夹

```python
%%writefile foo.py
def foo():
    pass

####### 运行结果########

Writing foo.py
```


### 5.  %%latex

在 cell 中 书写 latex

```python
%%latex
\begin{equation}
-\frac{\hbar^{2}}{2 m} \nabla^{2} \Psi(\mathbf{r}, t)+V(\mathbf{r}) \Psi(\mathbf{r}, t)=i \hbar \frac{\partial}{\partial t} \Psi(\mathbf{r}, t)
\end{equation}

```


![](https://i.loli.net/2019/07/26/5d39dfc95a7e750020.png)


-----------


## 4. 发现和消除 错误 %debug

交互性的 debug， 使用之后会直接定位到出现异常的地方，进行 value 查询


![](https://i.loli.net/2019/07/26/5d39e21cad85369824.png)



----



## 5. 高亮笔记

高亮的颜色取决于 提醒事件的类型

**注意下面代码是 markdown 语句**


1. 蓝色的提醒框 ：信息



```markdown

<div class="alert alert-block alert-info">
<b>Tip:</b> Use blue boxes (alert-info) for tips and notes.
If it’s a note, you don’t have to include the word “Note”.
</div>


```


2.  黄色的提醒框 ：警告

```markdown
<div class="alert alert-block alert-warning">
<b>Example:</b> Yellow Boxes are generally used to include additional examples or mathematical formulas.
</div>
```

3. 绿色的提醒框 ： 成功

```markdown

<div class="alert alert-block alert-success">
Use green box only when necessary like to display links to related content.
</div>


```


4. 红色的提醒框 ： danger

```
<div class="alert alert-block alert-danger">
It is good to avoid red boxes but can be used to alert users to not delete some important part of code etc.
</div>

```

![](https://i.loli.net/2019/07/26/5d3a5c7f851fa39365.png)


------


## 6.  一次性打印出一个 cell 中的所有的输出


1. 正常一个cell 默认只会打印最后一行的输出， 要打印其他的中间输出 ，需要加 print

```python
In  [1]: 10+5
         11+6
Out [1]: 17

```

2. 添加下面的代码：

```python
from IPython.core.interactiveshell import InteractiveShell
InteractiveShell.ast_node_interactivity = "all"


```


3. 会打印出所有中间的输出


```python
In  [1]: 10+5
         11+6
         12+7
Out [1]: 15
Out [1]: 17
Out [1]: 19


```


4. 不希望打印所有的输出， 恢复默认设置 ：

```python
InteractiveShell.ast_node_interactivity = "last_expr"

```



-----

















