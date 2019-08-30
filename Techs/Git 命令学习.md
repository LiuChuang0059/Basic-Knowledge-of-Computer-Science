---
title:  Git 命令基础学习
tags:
  - Git
categories:
  - Tech
toc: true
mathjax: true
---
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/h0rkd.jpg)
----


## 1 Windows
### 1 创建版本库
#### 1 创建空目录
```
$ mkdir learngit ##创建一个目录
$ cd learngit  ## 切换到当前目录
$ pwd   ## 显示当前目录
```

#### 2 目录变成Git可以管理的仓库：
```
$ git init
Initialized empty Git repository in /Users/dell/learngit/.git/
```
### 2把文件添加到版本库
#### 1 文件要在 learnGit目录或者子目录（carefor 记事本 格式错误）
#### 2用命令git add告诉Git，把文件添加到仓库
```
$ git add readme.txt
```
#### 3用命令git commit告诉Git，把文件提交到仓库
```
$ git commit -m "wrote a readme file"
```

### 3 修改文件
#### 1 用git diff这个命令查看修改
```
$ git diff  new1.txt

diff --git a/new1.txt b/new1.txt
index d8036c1..013b5bc 100644
--- a/new1.txt
+++ b/new1.txt
@@ -1,2 +1,2 @@
-Git is a version control system.
+Git is a distributed version control system.
 Git is free software.
\ No newline at end of file

```

#### 2 将要提交修改（add）的状态查看
```
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   new1.txt
```
#### 3 提交修改后状态
```
dell@dell-PC MINGW32 ~/learngit (master)
$ git commit -m "add distributed"
[master 4f3c193] add distributed
 1 file changed, 1 insertion(+), 1 deletion(-)

dell@dell-PC MINGW32 ~/learngit (master)
$ git status
On branch master
nothing to commit, working tree clean
```

### 4 git log 查看修改记录
```
$ git log
commit 4f3c1930f58d228ff63f4b5abc3e39497db6642d (HEAD -> master)
Author: LiuChuang0059 <1659608216@qq.com>
Date:   Sun Jun 3 01:05:22 2018 +0800

    add distributed

commit b0150898143c926e5802ab62d1ae845747e4e5b0
Author: LiuChuang0059 <1659608216@qq.com>
Date:   Sun Jun 3 00:37:14 2018 +0800

    wrote a new1 file

```

### 4  退回前面版本

> 在Git中，用HEAD表示当前版本，也就是最新的提交（注意我的提交ID和你的肯定不一样），上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100
```
dell@dell-PC MINGW32 ~/learngit (master)
$ git reset --hard HEAD^
HEAD is now at b015089 wrote a new1 file
```

### 5 退回后 还想回到前面
Git提供了一个命令git reflog用来记录你的每一次命令
```
$ git reflog
b015089 (HEAD -> master) HEAD@{0}: reset: moving to HEAD^
4f3c193 HEAD@{1}: commit: add distributed
b015089 (HEAD -> master) HEAD@{2}: commit (initial): wrote a new1 file

dell@dell-PC MINGW32 ~/learngit (master)
$ git reset --hard 4f3c1 ##3找回commit id  回到前面
HEAD is now at 4f3c193 add distributed
```
### 6工作区和暂存区
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/8cstd.jpg)

> 第一步是用git add把文件添加进去，实际上就是把文件修改添加到暂存区(stage)；
第二步是用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支(master)



### 7撤销修改
```git
$ cat new1.txt  ## 查看当前文件内容
Git is a distributed version control system.
Git is free software.
I like it.
```
#### 1 只修改了工作区 没有add到stage
```
$ git checkout -- new1.txt###撤销修改
```
#### 2 已经add 到stage
```
$ git reset HEAD new1.txt  ##既可以回退版本，也可以把暂存区的修改回退到工作区
Unstaged changes after reset:
M       new1.txt

dell@dell-PC MINGW32 ~/learngit (master)
$ git status
On branch master
Changes not staged for commit: ##暂存区是干净的，工作区有修改
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   new1.txt


###撤销工作区修改
$ git checkout -- new1.txt

dell@dell-PC MINGW32 ~/learngit (master)
$ git status
On branch master
nothing to commit, working tree clean

```
#### 3 改错了东西，还从暂存区提交到了版本库，，未提交到远程库
退回版本 的方法（参照###4）


### 8 删除
首先删除工作区文件
```
rm test.txt
###或者 直接删除
```
#### 1 删除 本地和版本库
```
$ git rm test.txt
rm 'test.txt'

dell@dell-PC MINGW32 ~/learngit (master)
$ git commit -m "remove test.txt"
[master 55bfb9b] remove test.txt
 1 file changed, 1 deletion(-)
 delete mode 100644 test.txt

```

#### 2 误删 恢复工作区文件
```
$ git checkout -- test.txt
```
**git checkout其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”**

### 9 添加远程库

> 把本地库的内容推送到远程，用git push命令，实际上是把当前分支master推送到远程。
由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令

从现在起，只要本地作了提交，就可以通过命令
```
$ git push origin master
```

### 10 从远程库克隆到 本地
```
$ git clone git@github.com:LiuChuang0059/Test-report.git
Cloning into 'Test-report'...
Warning: Permanently added the RSA host key for IP address '13.229.188.59' to the list of known hosts.
remote: Counting objects: 73, done.
remote: Total 73 (delta 0), reused 0 (delta 0), pack-reused 73
Receiving objects: 100% (73/73), 227.71 KiB | 338.00 KiB/s, done.
Resolving deltas: 100% (22/22), done.
```

> 多个人协作开发，那么每个人各自从远程克隆一份就可以
Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快

### 11 c创建 合并分支
1.Git用master指向最新的提交，再用HEAD指向master，就能确定当前分支，以及当前分支的提交点：
![此处输入图片的描述][1]
2.当我们创建新的分支，例如dev时，Git新建了一个指针叫dev，指向master相同的提交，再把HEAD指向dev，就表示当前分支在dev上：
![此处输入图片的描述][2]
3.现在开始，对工作区的修改和提交就是针对dev分支了，比如新提交一次后，dev指针往前移动一步，而master指针不变
![此处输入图片的描述][3]
4.Git合并？就是直接把master指向dev的当前提交，就完成了合并：
![此处输入图片的描述][4]

5 代码实现
```
##切换路径
dell@dell-PC MINGW32 ~
$ cd learngit
####创建新的分支
dell@dell-PC MINGW32 ~/learngit (master)
$ git branch dev
####切换到新的分支
dell@dell-PC MINGW32 ~/learngit (master)
$ git checkout dev
Switched to branch 'dev'
### 查看分支 * 当前分支
dell@dell-PC MINGW32 ~/learngit (dev)
$ git branch
* dev
  master

## 修改add 文件
dell@dell-PC MINGW32 ~/learngit (dev)
$ git add test.txt
###提交分支修改
dell@dell-PC MINGW32 ~/learngit (dev)
$ git commit -m "branch test" ### test 与文件名无关
[dev d9bd384] branch test
 1 file changed, 2 insertions(+), 1 deletion(-)
#### 切换到主分支
dell@dell-PC MINGW32 ~/learngit (dev)
$ git checkout master
Switched to branch 'master'
Your branch is up to date with 'origin/master'.
查看文件
dell@dell-PC MINGW32 ~/learngit (master)
$ cat test.txt
test  ###主分支文件没有改变

### 合并分支
dell@dell-PC MINGW32 ~/learngit (master)
$ git merge dev
Updating 960c8c8..d9bd384
Fast-forward
 test.txt | 3 ++-
 1 file changed, 2 insertions(+), 1 deletion(-)

dell@dell-PC MINGW32 ~/learngit (master)
$ cat test.txt
test
new
####删除分支
dell@dell-PC MINGW32 ~/learngit (master)
$ git branch -d dev
Deleted branch dev (was d9bd384).

dell@dell-PC MINGW32 ~/learngit (master)
$ git branch
* master
```

> 查看分支：git branch
创建分支：git branch <name>
切换分支：git checkout <name>
创建+切换分支：git checkout -b <name>
合并某分支到当前分支：git merge <name>
删除分支：git branch -d <name>


### 12 解决冲突
[解决分支冲突 ](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001375840202368c74be33fbd884e71b570f2cc3c0d1dcf000)

### 13自定义git
#### 1
[自定义命令缩写](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000/001375234012342f90be1fc4d81446c967bbdc19e7c03d3000)

  [1]: https://cdn.liaoxuefeng.com/cdn/files/attachments/0013849087937492135fbf4bbd24dfcbc18349a8a59d36d000/0
  [2]: https://cdn.liaoxuefeng.com/cdn/files/attachments/001384908811773187a597e2d844eefb11f5cf5d56135ca000/0
  [3]: https://cdn.liaoxuefeng.com/cdn/files/attachments/0013849088235627813efe7649b4f008900e5365bb72323000/0
  [4]: https://cdn.liaoxuefeng.com/cdn/files/attachments/00138490883510324231a837e5d4aee844d3e4692ba50f5000/0


# 参考

廖雪峰老师Git教学