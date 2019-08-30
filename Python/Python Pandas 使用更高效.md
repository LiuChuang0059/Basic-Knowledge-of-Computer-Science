原文： 10 Python Pandas tricks that make your work more efficient

link ： https://towardsdatascience.com/10-python-pandas-tricks-that-make-your-work-more-efficient-2e8e483808ba

-----


## 0. read_csv() 读取特别大的数据

**read_csv() 可以将数据分块导入** ： 将数据分成很多块，而不是一次性将所有的数据都导入到内存中

```python
chunks = pd.read_csv('center_aircon_result.csv', chunksize=100000)
data_chunk = pd.concat(chunks)

```

### 1. 单线程使用 chunk

```python
import pandas as pd

LARGE_FILE = "center_aircon_result.csv"
CHUNKSIZE = 1000 # processing 100,000 rows at a time

def process_frame(df):
        # process data frame
        return len(df)

if __name__ == '__main__':
        reader = pd.read_csv(LARGE_FILE, chunksize=CHUNKSIZE)

        result = 0
        for df in reader:
                # process each data frame
                result += process_frame(df)

        print("There are {} rows of data".format(result))
```


### 2. 多线程使用 chunk

```python
import pandas as pd
import multiprocessing as mp

LARGE_FILE = "center_aircon_result.csv"
CHUNKSIZE = 1000 # processing 100,000 rows at a time

def process_frame(df):
        # process data frame
        return len(df)

if __name__ == '__main__':
        reader = pd.read_table(LARGE_FILE, chunksize=CHUNKSIZE)
        pool = mp.Pool(4) # use 4 processes

        funclist = []
        for df in reader:
                # process each data frame
                f = pool.apply_async(process_frame,[df])
                funclist.append(f)
        result = 0
        for f in funclist:
                result += f.get(timeout=1) # timeout in 10 seconds

        print("There are {} rows of data".format(result))
```


* 进程池Pool ： 就是我们将所要运行的东西，放到池子里，Python会自行解决多进程的问题

* apply_async 中只能传递一个值，它只会放入一个核进行运算，但是传入值时要注意是可迭代的，所以在传入值后需要加逗号, 同时需要用get()方法获取返回值

----

## 1. dask.dataframe

[dask](https://github.com/dask/dask) : Dask is a flexible parallel computing library for analytics. See documentation for more information.

dask.dataframe 是根据索引将数据表分割的较小的pandas frame
的集合，可以在一台机器上并行处理，也可以在集群上的多台机器上并行处理。
**可以对超出内存大小的数据进行分析**

安装

```sh
pip install dask[dataframe] --upgrade

# or
pip install dask[complete]
```
> * 如果简单 pip install dask， 会缺失 dataframe 和相关的依赖包
> 如果运行报错，重启一下 jupyter

```python

import dask.dataframe
data = dask.dataframe.read_csv('center_aircon_result.csv',header=None)
```

![](https://i.loli.net/2019/08/23/dKp8tRcVE71mSTN.png)

* 观察到数据没有完全导入到内存，但是一些命令可以进行操作。
> 因为根据 Dask 和他的dataframe 结构， 你可以像 在pandas 一样创建 dataframe，但是却不将数据导入到 pandas 之中。这个方法像是将 dataframe 当作一个 指针， 指向 数据文件， 直到你确实需要调用文件的时候才会调用。

* 当我们需要观察一下数据的时候
```python
data.head(5)
```

结果：

|   |0 | 1          | 2         | 3         | 4         | 5         | 6         | 7         | 8         | 9         | ...       | 16  | 17        | 18        | 19        | 20        | 21        | 22        | 23        | 24        | 25        |
|---|------------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
| 0 | 9.774259   | 6.723340  | 3.433748  | 4.477244  | 5.121342  | 6.240951  | 5.532705  | 4.041220  | 3.745095  | 5.114573  | ... | 8.210764  | 3.125931  | 2.456657  | 3.063085  | 3.789527  | 4.997346  | 8.206258  | 8.713318  | 6.194705  | 5.741860  |
| 1 | 0.003013   | 0.015709  | 0.007943  | 0.013434  | 0.009495  | 0.010528  | 0.013471  | 0.017320  | 0.021691  | 0.025860  | ... | 0.022540  | 0.018457  | 0.014490  | 0.011089  | 0.008737  | 0.007894  | 0.008965  | 0.012304  | 0.018200  | 0.026889  |
| 2 | 0.003013   | 0.015709  | 0.007943  | 0.013434  | 0.009495  | 0.010528  | 0.013471  | 0.017320  | 0.021691  | 0.025860  | ... | 0.022540  | 0.018457  | 0.014490  | 0.011089  | 0.008737  | 0.007894  | 0.008965  | 0.012304  | 0.018200  | 0.026889  |
| 3 | -1.486605  | -0.733541 | -0.573640 | -0.472945 | -0.801993 | -0.895850 | -0.911962 | -0.928055 | -0.940195 | -0.923094 | ... | -0.302893 | -0.252738 | -0.243902 | -0.271441 | -0.325001 | -0.394334 | -0.471228 | -0.548795 | -0.620001 | -0.677035 |
| 4 | -12.913480 | 3.632711  | -2.092087 | -2.074929 | -1.803755 | -1.022063 | -0.002563 | 0.492652  | 0.599776  | 0.693982  | ... | 1.486177  | 1.648478  | 0.086656  | -0.278142 | 0.052715  | 0.008679  | -0.068357 | -0.253500 | -0.331164 | -0.353837 |


* 我们对列进行一下重命名

```python
data = data.rename(columns={c: 'feature_' + str(c) for c in data.columns})
```

|         |feature_0 |  feature_1 | feature_2 | feature_3 | feature_4 | feature_5 | feature_6 | feature_7 | feature_8 | feature_9 |    ...    | feature_16 | feature_17 | feature_18 | feature_19 | feature_20 | feature_21 | feature_22 | feature_23 | feature_24 | feature_25 |
|:---------:|:----------:|:---------:|:---------:|:---------:|:---------:|:---------:|:---------:|:---------:|:---------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:---------:|
|     0     |  9.774259  |  6.723340 |  3.433748 |  4.477244 |  5.121342 |  6.240951 |  5.532705 |  4.041220 |  3.745095 |  5.114573 |     ...    |  8.210764  |  3.125931  |  2.456657  |  3.063085  |  3.789527  |  4.997346  |  8.206258  |  8.713318  |  6.194705  |  5.741860 |
|     1     |  0.003013  |  0.015709 |  0.007943 |  0.013434 |  0.009495 |  0.010528 |  0.013471 |  0.017320 |  0.021691 |  0.025860 |     ...    |  0.022540  |  0.018457  |  0.014490  |  0.011089  |  0.008737  |  0.007894  |  0.008965  |  0.012304  |  0.018200  |  0.026889 |
|     2     |  0.003013  |  0.015709 |  0.007943 |  0.013434 |  0.009495 |  0.010528 |  0.013471 |  0.017320 |  0.021691 |  0.025860 |     ...    |  0.022540  |  0.018457  |  0.014490  |  0.011089  |  0.008737  |  0.007894  |  0.008965  |  0.012304  |  0.018200  |  0.026889 |
|     3     |  -1.486605 | -0.733541 | -0.573640 | -0.472945 | -0.801993 | -0.895850 | -0.911962 | -0.928055 | -0.940195 | -0.923094 |     ...    |  -0.302893 |  -0.252738 |  -0.243902 |  -0.271441 |  -0.325001 |  -0.394334 |  -0.471228 |  -0.548795 |  -0.620001 | -0.677035 |
|     4     | -12.913480 |  3.632711 | -2.092087 | -2.074929 | -1.803755 | -1.022063 | -0.002563 |  0.492652 |  0.599776 |  0.693982 |     ...    |  1.486177  |  1.648478  |  0.086656  |  -0.278142 |  0.052715  |  0.008679  |  -0.068357 |  -0.253500 |  -0.331164 | -0.353837 |

**当我们进行上述操作的时候， dask没有将所有的数据都导入到 内存中**

* 提取特征6 的数据

```python
data_feature_6 = data["feature_6"]
data_feature_6.count()

####### 返回值 #######
dd.Scalar<series-..., dtype=int64>
```

* 如果想要真实的计算， 需要调用 compute 获取 dask 计算结果

```python
data_feature_6.compute()
```

| 0       5.532705 |
|------------------|
| 1       0.013471 |
| 2       0.013471 |
| 3      -0.911962 |
| 4      -0.002563 |
| ...              |
| 3187    0.013471 |
| 3188    0.013471 |
| 3189    0.013471 |
| 3190    0.013471 |
| 3191    0.013471 |



* 运行时间比较：

```python
size = data.size
size, type(size)
#######
(dd.Scalar<size-ag..., dtype=int64>, dask.dataframe.core.Scalar)

%%time
size.compute()

#######Output ########
CPU times: user 31.9 ms, sys: 8.74 ms, total: 40.7 ms
Wall time: 56.2 ms
82992

```

计算时间稍有些长， 因为在计算size 之前要将数据导入到内存

* 如果我们有足够大的内存

```python
data = data.persist() # 数据导入到内存
%%time
df.size.compute()

######output ####

CPU times: user 2.36 ms, sys: 909 µs, total: 3.27 ms
Wall time: 3.01 ms
82992
```

* Another approach is to setup a whole bunch of deferred computations, and to compute out of core. Then dask will intelligently load data and process all the computations once by figuring out the various dependencies. This is a great approach if you don't have a lot of RAM available.



## 2. read_csv()

1. 如果要读取的数据很大的话， 输入参数的时候添加一行内容 **nrows = 5**, 只读取表格的一小部分，检查一些读取的错误： 例如： 分隔符有时候可能不是逗号。


2. 提取 表格的列名 df.columns.tolist()

```python

col_names = data.columns.to_list()
col_names

['User ID', 'Gender', 'Age', 'EstimatedSalary', 'Purchased']

```

3. 使用 usecols 调用自己需要的列

```python
usecols = [ 'EstimatedSalary', 'Purchased']
train_data = data[usecols]
```

---

## 3. select_dtypes 筛选数据类型

```python
data.dtypes.value_counts()

##########

int64     4
object    1
dtype: int64
```

筛选需要的数据类型
```python

gender_data =data.select_dtypes(include="object")
gender_data
```

---

## 4. copy

使用 k 表示薪水 ， 薪水一览统一除以 1000 但是不希望改变原数据。
```python
data_2 = data
data_2["EstimatedSalary"]  = data_2["EstimatedSalary"] /1000
```
但是如果按照上面的代码， 原始数据 data 也会发生改变
```python
data_copy = data.copy()

```



----

## 5. Map
使用 map 函数快速数据变换

```python
level_map = {"Male": 0, "Female": 1}
data["Gender_level"] = data["Gender"].map(level_map)
```
增加了一列， Male ， Female

|          |User ID |  Gender  |   Age  | EstimatedSalary | Purchased | Gender_level |
|:-------:|:--------:|:------:|:---------------:|:---------:|:------------:|:-:|
|    0    | 15624510 |  Male  |        19       |   0.0019  |       0      | 0 |
|    1    | 15810944 |  Male  |        35       |   0.0020  |       0      | 0 |
|    2    | 15668575 | Female |        26       |   0.0043  |       0      | 1 |
|    3    | 15603246 | Female |        27       |   0.0057  |       0      | 1 |
|    4    | 15804002 |  Male  |        19       |   0.0076  |       0      | 0 |



----

## 6. 按照数据分布的百分比分组

```python
data.drop(columns=['User ID', 'num_nulls'])
cut_points = [np.percentile(data['EstimatedSalary'], i) for i in [50, 80, 95]]
data['group'] = 1
for i in range(3):
    data['group'] = data['group'] + (data['EstimatedSalary'] < cut_points[i])
```

* top5% : 第一组
* 5-20% ： 第二组
* 20%-50% ： 第三组
* bottom 50% : 第四组



|          |User ID |  Gender  |   Age  | EstimatedSalary | Purchased | num_nulls | group |
|:-------:|:--------:|:------:|:---------------:|:---------:|:---------:|:-----:|:-:|
|    0    | 15624510 |  Male  |        19       |   19000   |     0     |   0   | 4 |
|    1    | 15810944 |  Male  |        35       |   20000   |     0     |   0   | 4 |
|    2    | 15668575 | Female |        26       |   43000   |     0     |   0   | 4 |
|    3    | 15603246 | Female |        27       |   57000   |     0     |   0   | 4 |
|    4    | 15804002 |  Male  |        19       |   76000   |     0     |   0   | 3 |


---

## 7. to_csv

到处数据的时候使用 **float_format=‘%.0f’** 将所有的浮点数转化成整数， 避免数据后面的 .0


---


## 8.  groupby 数据聚合

原始数据 ：

|   |  User ID | Gender | Age | EstimatedSalary | Purchased |
|:-:|:--------:|:------:|:---:|:---------------:|:---------:|
| 0 | 15624510 |  Male  |  19 |      19000      |     0     |
| 1 | 15810944 |  Male  |  35 |      20000      |     0     |
| 2 | 15668575 | Female |  26 |      43000      |     0     |
| 3 | 15603246 | Female |  27 |      57000      |     0     |
| 4 | 15804002 |  Male  |  19 |      76000      |     0     |


* 希望按照年龄进行聚合

```python
data_2 = data.copy()
data_2 = data_2.filter(['Age', 'Purchased'])
data_2_group = data_2.groupby('Age').mean()
data_2_group.tail()
```

|     | Purchased |
|:---:|:---------:|
| Age |           |
|  56 |  1.000000 |
|  57 |  1.000000 |
|  58 |  1.000000 |
|  59 |  0.714286 |
|  60 |  1.000000 |

* 按照薪水聚合

|                 | Purchased |
|:---------------:|:---------:|
| EstimatedSalary |           |
|      15000      |    0.0    |
|      16000      |    0.0    |
|      17000      |    0.0    |
|      18000      |    0.0    |
|      19000      |    0.0    |

----------

|                 | Purchased |
|:---------------:|:---------:|
| EstimatedSalary |           |
|      146000     |    1.0    |
|      147000     |    1.0    |
|      148000     |    1.0    |
|      149000     |    1.0    |
|      150000     |    1.0    |

--------


## 9. Pandas 中循环加速

不使用 map 函数， 手动给年龄分段

1. 基本循环

```python
data = pd.read_csv("Social_Network_Ads.csv")
data_2 = data.copy()

data_2['new_col'] = '999'
def loop_test(df):
    for i in range(df.shape[0]):
        if df['Age'][i] < 23 :
            df['new_col'][i] = 'low'
        elif df['Age'][i] >= 23 and df['Age'][i] < 40:
            df['new_col'][i] = 'medium'
        else:
            df['new_col'][i] = 'high'

%%timeit
loop_test(data_2)

##### Output ######
57.5 ms ± 4.67 ms per loop (mean ± std. dev. of 7 runs, 10 loops each)
```


2. 使用 内置函数 iterrows()

iterrows 对于每一行返回一个series，以Series的形式遍历目标列。这使得它比标准循环更快：

```python
data = pd.read_csv("Social_Network_Ads.csv")
data_2 = data.copy()

def soc_iter(age):
    if age < 23 :
        result = 'low'
    elif age >= 23 and age < 40:
        result = 'medium'
    else:
        result = 'high'
    return result

%%timeit
result_series = []
for index ,row in data_2.iterrows():
    result_series.append(soc_iter(row['Age']))
data_2['new_col']   = result_series

##### Output ######
39.4 ms ± 249 µs per loop (mean ± std. dev. of 7 runs, 10 loops each)
```

3. 使用 apply() 方法


```python
data = pd.read_csv("Social_Network_Ads.csv")
data_2 = data.copy()

def soc_iter(age):
    if age < 23 :
        result = 'low'
    elif age >= 23 and age < 40:
        result = 'medium'
    else:
        result = 'high'
    return result

%%timeit
data_2['new_col'] = data_2.apply(lambda row : soc_iter(row['Age']), axis= 1)#  * 1 or 'columns': apply function to each row.


##### Output ######
5.88 ms ± 147 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)
```


4. Pandas Vectorization

避免上面的循环

```python
data = pd.read_csv("Social_Network_Ads.csv")
data_2 = data.copy()

def soc_iter_vect(df,age):
    df['new_col'] = 'none'
    df.loc[age < 23,'new_col'] = 'low'
    df.loc[(age >= 23) & (age < 40), 'new_col'] = 'medium'
    df.loc[age >=40,'new_col'] = 'high'

%%timeit
data_2['new_col'] = soc_iter_vect(data_2, data_2['Age'])

##### Output ######
5.07 ms ± 43.8 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)
```


5. Numpy Vectorization

上面使用的是 pandas series， 我们可以使用 numpy array

```python
%%timeit
data_2['new_col'] = soc_iter_vect(data_2, data_2['Age'].values)
# 简单修改为使用 numpy

##### Output ######
3.08 ms ± 75.4 µs per loop (mean ± std. dev. of 7 runs, 100 loops each)
```



**总结： 如果必须要使用循环，建议使用 apply； 否则的话尽量适应向量化的方法**


---

# 参考：

1. [The most (time) efficient ways to import CSV data in Python](https://medium.com/casual-inference/the-most-time-efficient-ways-to-import-csv-data-in-python-cc159b44063d)


2. [Multi-Processing With Pandas](http://gouthamanbalaraman.com/blog/distributed-processing-pandas.html)

3. [Multi-Processing with Pandas and Dask](http://gouthamanbalaraman.com/blog/distributed-processing-pandas-dask.html)

4. [进程池 Pool](https://morvanzhou.github.io/tutorials/python-basic/multiprocessing/5-pool/)

5. [Dask – A better way to work with large CSV files in Python](https://pythondata.com/dask-large-csv-python/)

6. [Reshaping Pandas DataFrames, A guide to DataFrame manipulation using groupby, melt, pivot tables, pivot, transpose, and stack.](https://hackingandslacking.com/reshaping-pandas-dataframes-6aceb55c8c27)


7. [How To Make Your Pandas Loop 71803 Times Faster](https://towardsdatascience.com/how-to-make-your-pandas-loop-71-803-times-faster-805030df4f06)











