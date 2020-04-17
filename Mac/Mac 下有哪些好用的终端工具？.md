# 前言
这篇笔记的目的主要是用来整理自己一直在使用的一些较为好用的终端工具/扩展。

因为我个人的终端配置是`ZSH` + `iTerm2`，所以本文的部分`ZSH` 扩展可能不适用于其他`Shell`用户。

## brew 
经常与终端打交道的用户，对这个一定不陌生，它就是类似`Linux`下的`apt-get`这样的包管理工具。

通常我需要搭建一个全新的开发环境时，它一定是第一个需要安装的工具。

安装 brew（[brew 官网](https://brew.sh/)）
```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

常用命令如下：
命令 | 描述
--- | ---
brew search package | 搜索软件包
brew install package | 安装软件包
brew uninstall package | 卸载软件包
brew list | 列出已安装清单
brew help | 获取帮助

## OSX 扩展
`osx` 扩展是`zsh` 提供的一个控制终端和访达（功能之一）的扩展工具。

其中最为常用是`ofd`命令，将当前`shell`窗口在访达中打开。

另一个较为常用的命令是`cdf`，可在`shell`中直接跳转至当前访达窗口所在的路径（如果存在多个访达窗口，那么跳转至最前面的那个）。

其他常用命令如下：

命令 | 描述
---|---
tab | 在当前目录打开一个新窗口
split_tab | 在当前窗口打开一个水平窗口
vsplit_tab | 在当前窗口打开一个垂直窗口
ofd | 在访达窗口中打开当前目录
pfd | 返回最前面的访达窗口的路径
pfs | 返回当前查找程序选择
cdf | cd 到当前访达窗口所在的路径
pushdf | pushed 到当前访达目录
quick-look | 快速查看指定文件
man-preview | 在预览应用程序中打开特定的手册页
showfiles | 显示隐藏文件
hidefiles | 隐藏隐藏的文件
rmdsstore | 以递归方式删除目录中的.DS_Store文件

## tmux
`tmux` 是一个终端下窗口分割的工具，有关它的具体介绍，请查阅[这篇笔记](https://github.com/0xAiKang/Note/blob/master/Skill/Tmux%20%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md)。

## autojump
autojump - 目录快速跳转命令行工具，从此告别`cd... cd...`。

autojump 是一个`Windows`、`Linux`、`macOS` 都能使用的命令行工具，这是仅介绍`macOS` 的安装方式。

```
brew install autojump
```
使用`brew`安装完成之后，还需要进行配置，以下方法二选一：
* 在 `~/.bash_profile` 文件中加入语句 `[[ -s $(brew --prefix)/etc/profile.d/autojump.sh ]] && . $(brew --prefix)/etc/profile.d/autojump.sh`。
* 在 `~/.zshrc` 文件中，修改 `plugins=(git)` 插件配置行，以开启 `zsh` 对 `autojump` 插件的支持 `plugins=(git autojump)`。

### 常用命令
命令 | 描述
--- | ---
j foo | 跳转到包含 foo 的目录
jc bar | 跳转到包含 bar 的子目录
jo file | 在访达中打开包含 file 的目录
autojump --help | 打开帮助列表

## Spaceship ZSH
Spaceship ZSH - 是一个极简、强大和可定制的`ZSH`提示符。

我是在无意间发现的这个终端工具的，先来看一下实际效果。

![image](https://user-images.githubusercontent.com/10276208/36086434-5de52ace-0ff2-11e8-8299-c67f9ab4e9bd.gif)

### 特点
Spaceship ZSH 有很多很棒的特点，这里仅仅列举一些我所看见的。

* 颜值即正义
* 展示当前Git 仓库的状态
* 展示各种语言的当前版本
* 展示最后一条命令的总执行时间

### 安装
Spaceship ZSH 的安装方式有多种，这里仅介绍通过`oh-my-zsh`的安装方式，其他方式可参考[官网](https://denysdovhan.com/spaceship-prompt/)。

1. 克隆仓库
```
git clone https://github.com/denysdovhan/spaceship-prompt.git "$ZSH_CUSTOM/themes/spaceship-prompt"
```
2. 将`spaceship.zsh-theme` 链接到`oh-my-zsh` 的主题目录
```
ln -s "$ZSH_CUSTOM/themes/spaceship-prompt/spaceship.zsh-theme" "$ZSH_CUSTOM/themes/spaceship.zsh-theme"
```
3. 编辑`~/.zshrc`
```
ZSH_THEME="spaceship"
```

### 参考链接
* [安装 zsh](https://github.com/robbyrussell/oh-my-zsh)
* [如何启用 zsh 的插件](https://github.com/ohmyzsh/ohmyzsh#enabling-plugins)
* [OSX 插件](https://github.com/robbyrussell/oh-my-zsh/tree/master/plugins/osx)
* [autojump](https://github.com/wting/autojump)
* [Spaceship ZSH](https://github.com/denysdovhan/spaceship-prompt#installing)