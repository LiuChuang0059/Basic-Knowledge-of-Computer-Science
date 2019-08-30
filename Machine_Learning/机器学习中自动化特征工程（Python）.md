原文 ： Automated Feature Engineering in Python ---How to automatically create machine learning features

link ： https://towardsdatascience.com/automated-feature-engineering-in-python-99baf11cc219

---




机器学习模型正在由手动设计模型开始慢慢转向机器自动优化模型，使用 TOPT， auto-sklearn 等工具库，不使用人工的情况下，完成模型的选择， 参数优化， 得到一个最好的模型。 然而 特征工程作为机器学习中重要的一环，仍然还是主要是由人工来完成。

特征工程主要是从存在的数据中挖掘构建新的特征，这一步通常比选择什么模型更重要，因为 模型算法学习的根本来源就是我们提供给他的数据。

特征工程是一个耗时的手工过程，依赖于领域知识、直觉和数据操作。整个过程极其的枯燥， 最终的提取到的特征的好坏取决于 人工能力和时间，存在很大的不确定性。

自动特征工程主要是为了帮助数据科学家自动的从数据集中提取合适的特征用于模型的训练。


-----


## 1. 基本特征工程

构建特征操作主要分为两种：

转换和聚合 （transformations and aggregations.）



### 1. 转换

转换是在一个单独的表格上面的操作

![](https://i.loli.net/2019/08/02/5d43dea09c83681908.png)

对于上面的表格， 我们对 joined 列操作，提取出月份信息， 对于 income 列， 计算一下 log 结果。得到下面的结果。

![](https://i.loli.net/2019/08/02/5d43df251f3bf41582.png)



### 2. 聚合

聚合是跨越表格的操作

Pandas 的 Groupby 等方法和操作





上述介绍的操作本身没有很困难， 但是如果变量增多的话， 操作将变得困难，不再适合手动进行操作。
理想的， 我们希望有一个解决办法能够自动化实现上述的转换和聚合操作， 之后将结果聚合在一个简单的表格里面。


----

## 3. 特征工具 Featuretools


Featuretools 是一个开源的 Python 库， 基于 Deep Feature Synthesis （叠加多个特性， 和深度学习没有关系）方法，自动的从一组相关表格数据中提取特征。


给定三个数据表格

![](https://i.loli.net/2019/08/02/5d43e7cf988cb69560.png)


* clients ： 客户的基本信息表格

* loans ： 客户贷款信息 ： 一个 clients 可能存在多条 laons

* payments ： 客户偿还贷款的信息 ： 一个 loan 可能存在多条偿还信息


假设我们现在存在一个机器学习任务，预测 客户是否会偿还贷款。 我们需要将关于客户信息的几个表聚合在一起， 手动操作可能需要一系列的操作， 可以使用 teaturetools 自动化上面的操作进程。






###  1. 实体和 实体集 ( Entities and EntitySets)


一个 实体简单来说就是一个 表格或者 （Dataframe）

一个实体集就是 一系列表格的集合以及他们之间存在的关联


1. 创建一个实体集


```python

import featuretools as ft
# Create new entityset
es = ft.EntitySet(id = 'clients')

```


2. 向实体集里面添加实体， 每个实体都有个 index


```python

import pandas as pd
clients = pd.read_csv('data/clients.csv', parse_dates = ['joined'])

es = es.entity_from_dataframe(entity_id = 'clients', dataframe = clients,
                              index = 'client_id', time_index = 'joined')

```

3. 对于  payments， 没有特殊的 index （类似于数据库的 primary key ）
需要 make_index

```python
loans = pd.read_csv('data/loans.csv', parse_dates = ['loan_start', 'loan_end'])
payments = pd.read_csv('data/payments.csv', parse_dates = ['payment_date'])

es = es.entity_from_dataframe(entity_id = 'payments',
                              dataframe = payments,
                              variable_types = {'missed': ft.variable_types.Categorical},
                              make_index = True,
                              index = 'payment_id',
                              time_index = 'payment_date')


```

![](https://i.loli.net/2019/08/02/5d43f42453c3e40061.png)




### 2. 数据表之间的关系


```python



# Relationship between clients and previous loans
r_client_previous = ft.Relationship(es['clients']['client_id'],
                                    es['loans']['client_id'])

# Add the relationship to the entity set
es = es.add_relationship(r_client_previous)

# Relationship between previous loans and previous payments
r_payments = ft.Relationship(es['loans']['loan_id'],
                                      es['payments']['loan_id'])

# Add the relationship to the entity set
es = es.add_relationship(r_payments)

```

为了表现表格之间的关系，我们需要找到连接两个表格的变量， 例如 client 和 loans 之间通过 client_id 连接； loans 和 payment_s 之间 通过 loan_id 连接

![](https://i.loli.net/2019/08/02/5d43f57d7f20991824.png)

现在实体集中包含三个实体表格， 以及这些实体之间的关系,

做完这些之后，实体集已经完整， 可以产生特征了


----


## 4. 特征基元


就是之前我们介绍的 基本操作： 聚合和转换

* 聚合： 在不同表之间的操作
* 转换： 单个表格上面的操作


一些示例操作，

```python
ft.list_primitives()

```

![](https://i.loli.net/2019/08/02/5d43f7098e20c30394.png)


![](https://i.loli.net/2019/08/02/5d4417db1bc1246499.png)


也可以自定义

```python


from featuretools.primitives import make_agg_primitive, make_trans_primitive
from featuretools.variable_types import Text, Numeric

def absolute(column):
    return abs(column)

Absolute = make_trans_primitive(
    function=absolute, input_types=[Numeric], return_type=Numeric)


def maximum(column):
    return max(column)

Maximum = make_agg_primitive(
    function=maximum, input_types=[Numeric], return_type=Numeric)

feature_matrix, feature_defs = ft.dfs(
    entityset=es,
    target_entity="clients",
    agg_primitives=[Maximum],
    trans_primitives=[Absolute],
    max_depth=2)
```





```python

#from featuretools.primitives import (Count, Sum, Mean, Median, Std, Min, Max)
features, feature_names = ft.dfs(entityset = es, target_entity = 'clients',
                                 agg_primitives = ['mean', 'max', 'percent_true', 'last'],
                                 trans_primitives = ['cum_sum','cum_min',"time_since_previous","month"]

                                )

```

ft.dfs (deep feature synthesis) 函数

1. target_entity = 'clients' 目标表格

2. agg_primitives  ： 聚合操作

输出是一个 dataframe

![](https://i.loli.net/2019/08/02/5d44148e1833e15331.png)


即使我们只指定几个特征基元， featuretools 会创造出很多新的特征通过结合和堆叠特征基元


----

上面是我们手动选定一些特征基元，我们也可以让 featuretools 自动帮助我们选择


![](https://i.loli.net/2019/08/02/5d4419402f84f47585.png)


看到有 96 种组合结果

其中 max_depth 是 deep feature synthesis 的深度。


deep feature 就是基元特征的堆叠， deep feature 的深度就是使用到的特征基元的数目。

例如 MEAN(payments.payment_amount) 深度就是 1

LAST(loans(MEAN(payments.payment_amount)) 深度就是 2

------



## 5.  对得到的特征编码 --- Encoding categorical features


![](https://i.loli.net/2019/08/02/5d441d80f07f713545.png)

存在类别特征, 运行下面的代码，可以对类别进行 One-hot 编码

```python

feature_matrix_enc, features_enc = ft.encode_features(feature_matrix, feature_defs)
feature_matrix_enc

```

![](https://i.loli.net/2019/08/02/5d441de009a3680705.png)




-----


## 6. 进一步的问题

1. featuretools 能够自动创建特征，但是存在一个问题，创造了太多的特征。
模型中输入太多的特征，反而可能会导致模型表现变差， 因为一些没有用的特征会影响削弱重要特征的表现。


2. 而且， 模型表现较好，需要的数据量和特征是指数型的关系， 特征输入多了， ，造成维度灾难

3. 需要进行特征筛选 feature selection

* PCA

* selectKBest

* feature importants : GBDT

* Auto encoder --DNN

-----






# 参考

1. https://docs.featuretools.com/automated_feature_engineering/primitives.html


2.  The Hitchhiker’s Guide to Feature Extraction     https://towardsdatascience.com/the-hitchhikers-guide-to-feature-extraction-b4c157e96631































