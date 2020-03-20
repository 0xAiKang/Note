# 前言
本以为换了系统之后，就没有中文乱码的问题了，没想到还是出现了，结合之前遇到的问题，一起总结下。

## Gitk 中文显示乱码
gitk 是`git for Windows`下的一个图形化操作工具，可以很清晰的查看搜索本地提交历史以及做一些git 相关操作。

在git 仓库下直接使用`gitk`命令即可使用。

找到git 全局的配置文件，通常在用户目录下，Windows 是`.config`，Mac 下是`.gitconfig`。
或者输入`git config -e`。
```
# 添加这两行内容
[gui]
    encoding = utf-8
```

## git-gui 中文乱码
git gui 也是`git for Windows`下的一个图形化工具。

在终端中输入`git gui`，打开该工具，找到选择菜单：`Edit->Options`

找到红框的地方，换成utf-8的编码的就好了。
![image.png](https://i.loli.net/2020/03/19/Y17fp53ITonerB6.png)

或者直接在终端中输入以下命令：
```
 # git config --global gui.encoding utf-8
```

## git log 中文乱码
在终端中输入如下命令：

```
# git config --global i18n.commitencoding utf-8
# git config --global i18n.logoutputencoding gbk
```

## git status 中文乱码
在默认设置下，中文文件名在工作区状态输出，查看历史更改概要，以及在补丁文件中，文件名的中文不能正确地显示，而是显示为八进制的字符编码。

这个时候需要对Git 进行特殊设置：
```
# git config --global core.quotepath false
```