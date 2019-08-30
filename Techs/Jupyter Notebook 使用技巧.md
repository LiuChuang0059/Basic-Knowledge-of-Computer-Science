---
title: Jupyter Notebook 使用高效小技巧
tags:
  - Tech
categories:
  - Tech
toc: true

---


![](https://github.com/LiuChuang0059/large_file/blob/master/pic/dizsp.jpg)


---
## 0. 安装 笔记本扩展 nbextention


```python
conda install -c conda-forge jupyter_nbextensions_configurator

# 或者
pip install jupyter_contrib_nbextensions && jupyter contrib nbextension install --user

```

安装之后，可以在里面选取你需要的快捷增强--- ☑️勾选之后 自动加入。

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/lfzqn.jpg)


常用的几个：

Variable Inspector --- 显示变量的值，类型，大小的

code prettify --- 一键优化代码结构，看起来美观标准不少

Tabel of contents --  显示文章目录的

Toggle all line numbers --- 显示行号： 便于报错检查

Snippets ---  快速导入经常使用的 库

> 注意修改 snippets.json. 文件的时候， 不要按照文档给的路径，先自行查找一下 **jupyter --paths**
看 jupyter 使用的哪个路径， 再去路径下查找 snippets.json文件进行修改。

![](http://localhost:8888/nbextensions/snippets/snippets-demo.gif)



**详细可以在主页下面有介绍**


-----

## 1. jupyter-notebook 写可以执行代码的幻灯片

### rise增强
RISE: "Live" Reveal.js Jupyter/IPython Slideshow Extension---[github](https://github.com/damianavila/RISE)

**效果预览**

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/c02qi.gif)



### 安装--base-root 下安装

```python
$ conda install -c conda-forge rise
```

* 如果报错
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/cvm3x.jpg)

```python
$ sudo conda install -c conda-forge rise

```

* 其他安装---[github](https://github.com/damianavila/RISE)


### 使用

1. 添加 slide


2. 预览幻灯片

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/fjdv9.jpg)


* 基本操作视频---https://youtu.be/sXyFa_r1nxA

### 问题
* 无论在哪个环境下安装，只能在anaconda 的 base-root 环境下打开 jupyter 才有用


------

## 2. jupyter notebook 转换为 github  的 README 文件



### Let's convert this notebook to a README for the GitHub project's title page:

```python
!jupyter nbconvert --to markdown seq2seq.ipynb
!mv seq2seq.md README.md
```


或者直接：
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/rtyfj.jpg)

二者效果稍有不同

---

## 3. 打开 .ipynb 文件

有时候在 GitHub中 打开 .ipynb 文件会 打不开

可以将文件链接 复制到 [jupyter viewer](https://nbviewer.jupyter.org/)

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/idgwd.jpg)


----


## 4. jupyter notebook 嵌入链接

```python

from IPython.display import IFrame
IFrame('https://en.wikipedia.org/wiki/HTTPS', width=800, height=450)
```

效果

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/yu8m5.jpg)

----

## 5. jupyter notebook 嵌入 PDF

```python
from IPython.display import IFrame
IFrame('https://arxiv.org/pdf/1406.2661.pdf', width=800, height=450)
```

效果

![](https://github.com/LiuChuang0059/large_file/blob/master/pic/8mies.jpg)

----

## 6. jupyter notebook 不小心删除之后的 恢复

不小心删除一段代码 找回的方法

```python
for line in locals()['In']:
    print(line)
```

---


# 参考：

1. https://towardsdatascience.com/bringing-the-best-out-of-jupyter-notebooks-for-data-science-f0871519ca29

2. https://mp.weixin.qq.com/s/S_YUXFemUkcuBsXJj6EbSA

3. https://github.com/ipython-contrib/jupyter_contrib_nbextensions/issues/1279

















