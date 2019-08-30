---
title:  Mac OS 中的 iterm2  安装配置
tags:
  - Mac
categories:
  - Tech
toc: true
mathjax: true
---
![](https://github.com/LiuChuang0059/large_file/blob/master/pic/tjupg.jpg)
---

## 1. iterm2 安装（需要安装homebrew）
```
brew cask install iterm2
```

## 2. oh-my-zsh 安装（在下载的iterm2 中输入代码）![屏幕快照 2018-07-31 下午5.33.48.png-90.1kB][1]
```
curl -L https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh | sh
```

## 3. 安装 [powerline](https://github.com/powerline/fonts)(需要安装pip)
```
pip install powerline-status
```
## 4. 安装字体库
```
git clone https://github.com/powerline/fonts.git
cd fonts
./install.sh
```
![屏幕快照 2018-07-31 下午6.07.41.png-57.8kB][2]


## 5. 设置iTerm 2的Regular Font 和 Non-ASCII Font
  ![屏幕快照 2018-07-31 下午6.25.11.png-466.5kB][3]

## 6 设置配色方案
github 下载
```
git clone https://github.com/altercation/solarized
```
进入刚刚下载的工程的solarized/iterm2-colors-solarized 下双击 Solarized Dark.itermcolors 和 Solarized Light.itermcolors 两个文件就可以把配置文件导入到 iTerm2 里


![屏幕快照 2018-07-31 下午6.58.21.png-435.9kB][4]

## 7 使用[agnoster](https://github.com/fcamblor/oh-my-zsh-agnoster-fcamblor)主题
```
git clone https://github.com/fcamblor/oh-my-zsh-agnoster-fcamblor
cd oh-my-zsh-agnoster-fcamblor
./install
```
进入~/.zshrc打开.zshrc文件，然后将ZSH_THEME后面的字段改为agnoster。ZSH_THEME="agnoster"（agnoster即为要设置的主题）

## 8增加指令高亮效果——zsh-syntax-highlighting

```
git clone git://github.com/zsh-users/zsh-syntax-highlighting.git

```

1）cd到.zshrc所在目录

2）执行指令将工程克隆到当前目录

git clone git://github.com/zsh-users/zsh-syntax-highlighting.git

3）打开.zshrc文件，在最后添加下面内容

source XXX/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh

保存文件。

注意：xxx代表.zshrc所在目录

4）cd ~/.oh-my-zsh/custom/plugins

5）再次打开.zshrc文件，在最后面添加下面内容

plugins=(zsh-syntax-highlighting)

保存文件。
## 9 添加自动补全  类同
```
cd ~/.oh-my-zsh/custom/plugins/
git clone https://github.com/zsh-users/zsh-autosuggestions
vi ~/.zshrc
```

找到plugins，加上这个插件即可：


# 参考
https://www.jianshu.com/p/7de00c73a2bb
http://matt33.com/2016/07/09/mac-software/
https://github.com/sirius1024/iterm2-with-oh-my-zsh

  [1]: http://static.zybuluo.com/zzzxxxyyy/ntps6d27969t8eg4pbxd3bpo/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-07-31%20%E4%B8%8B%E5%8D%885.33.48.png
  [2]: http://static.zybuluo.com/zzzxxxyyy/8xfbidgbn5yjba4ii5vot3qn/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-07-31%20%E4%B8%8B%E5%8D%886.07.41.png
  [3]: http://static.zybuluo.com/zzzxxxyyy/lyzvtg5ovdohm3c6k1nj6agh/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-07-31%20%E4%B8%8B%E5%8D%886.25.11.png
  [4]: http://static.zybuluo.com/zzzxxxyyy/ihrbyhg59z7nnylgs0f7hp3x/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202018-07-31%20%E4%B8%8B%E5%8D%886.58.21.png