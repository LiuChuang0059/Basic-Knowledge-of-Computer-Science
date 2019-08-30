---
title: GitHub 使用技巧
tags:
  - Tech
  - Github
categories:
  - Tech
toc: true

---



![](https://github.com/LiuChuang0059/large_file/blob/master/pic/ruu1q.jpg)


Num | Tip
---- | ---
2 | 访问html文件
3 | 调整GitHub图片格式 --- 图片的大小，位置，一行摆放几个  👍
4 | 自动生成目录 [TOC]
5 | GitHub-markdown 添加 latex 公式，显示 Latex
6 | Github 绘制表格--踩坑




# 2 访问 html 文件

在 github 上面 html 的链接前面添加上https://htmlpreview.github.io/?


*  比如这个链接https://github.com/ruanyf/jstraining/blob/master/demos/backbone-demo/index.html

* 你打开看到的是 html 页面，

* 加上https://htmlpreview.github.io/?  之后就可以直接显示网页了，如下
https://htmlpreview.github.io/?https://github.com/ruanyf/jstraining/blob/master/demos/backbone-demo/index.html




----------
# 3 调整 GitHub 图片格式

## 1. 加上适当的空格
* 在中文和英文数字之间加上空格可以大大提高文档的可读性。


## 2. 图片引用转换
* Markdown 文档可以使用 `![]()` 这种方式来引用图片，但是无法设置大小，只有 `<img src="" width=""/>` 才支持设置大小。
过大的图片会造成文档内容过于粗糙，图片大小的选取以图片内的文字和文档文字大约一致为宜。

* 图片居中显示可以使得文档阅读体验更好，因此除了将图片引用转换为 img 标签之外，也会将图片居中显示：`<div align="center"> <img src="" width=""/> </div><br>`

* 工具链接---https://cyc2018.github.io/Text-Typesetting/

* 或者直接`<div align="center"> <img src="添加图片链接" width="添加图片大小，一般400-500"/> </div><br>`

## 参考---https://github.com/CyC2018/Text-Typesetting
---------------------



# 4 自动生成目录


## 1. 安装
* 1.安装 homebrew

* 2.利用 homebrew 安装 wget

  ```
  brew search wget
  brew install wget
  ```

* 3.安装 toc

  ```
  $ wget https://raw.githubusercontent.com/ekalinin/github-markdown-toc/master/gh-md-toc
  $ chmod a+x gh-md-toc
  ```

## 2. 使用

### 1. 直接网页--效果不好
```
./gh-md-toc https://github.com/ekalinin/envirius/blob/master/README.md

```

### 2. 下载本地 ----有少量错误，需要微调。

1. readme 文件下载到 gh-md-toc目录

2. 在文件路径执行代码

```
./gh-md-toc README.md
```

3. 生成的目录代码拷贝到 README.md 中

4. 效果预览
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/wcejj.jpg)



## 3 参考
* https://www.jianshu.com/p/302abe331dcb
* https://github.com/ekalinin/github-markdown-toc


## 4 缺点

* 有少量错误，不准确
* 不能自动更新
----------


# 5 GitHub-markdown 添加 latex 公式

* 可以在codecogs先编辑好公式，然后右击公式，选择图象属性，复制其中“源文件”一栏中的url，在markdown文档中以图片格式发布。

* 可以在Chrome中安装GitHub-latex增强插件----[GitHub with mathjax](https://chrome.google.com/webstore/detail/github-with-mathjax/ioemnmodlmafdkllaclgeombjnmnbima)

* 公式中不含有空格时，可通过'<img src="http://latex.codecogs.com/gif.latex?\alpha_A+\beta" alt="" title="" />'来发布公式，将连接中的\alpha_A+\beta替换为想输入的公式即可。

## 参考
* [蔡浩老师-计算物理课程](https://www.evernote.com/shard/s140/sh/e24ec31a-f5a4-4b89-a040-7dd5f6038850/09e367128b7edc54a16d78f9b943c421)

--------
