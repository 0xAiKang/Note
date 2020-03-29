### 什么是Shell 
Shell 是一个程序，其作用是将用户输入的命令发送到OS（系统内核）。

据说它起源于作为存在于OS 内部和用户之间的外壳的依附着。所以为形象的称作为 壳（Shell）。

### Shell 的种类
Linux Shell 的种类很多，目前流行的Shell 包括ash、bash、ksh、csh、zsh等，种类多了，也就有了标准化的要求，这就是POSIX的由来。

POSIX 表示可移植操作系统接口（UNIX的可移植操作系统接口，缩写为POSIX），POSIX标准定义了操作系统应该为应用程序提供的接口标准。

通过以下命令来查看文件中的内容来查看自己主机中当前有哪些种类的Shell：
```
$ cat /etc/shells
/bin/sh
/bin/bash
/bin/ksh
/bin/pdksh
/bin/tcsh
/bin/zsh
/bin/dash
/bin/posh
/usr/bin/sh
/usr/bin/bash
/usr/bin/ksh
/usr/bin/pdksh
/usr/bin/tcsh
/usr/bin/zsh
/usr/bin/dash
/usr/bin/posh
```

如何查看当前正在使用的Shell 类型：
```
$ echo $SHELL
/bin/bash
```
`$SHELL`是一个环境变量，它记录了Linux 当前用户所使用的Shell类型。

用户可以通过直接输入各种Shell的二进制文件名（因为这些二进制文件本身是可以被执行的），来进入到该Shell下，比如进入`zsh`可以直接输入：
```
$ /bin/zsh
```

这个命令为用户又启动了一个Shell，这个Shell在最初登录的那个Shell之后，称为下级的Shell或子Shell。

#### 最标准的Shell
##### Bash
`sh`是Unix 上最古老的Shell，在`sh`的基础上添加了各种扩展功能的是`bash`，它成为Linux标准Shell。有如下的特点：
* 使用上下键快速查看历史命令
* Tab 键自动补全

#### 其他Shell
##### ash
`ash`是Linux 中占用系统资源最少的一个小Shell，它只包含24个内部命令，因而使用起来很不方便。

##### csh
`csh`是Linux 比较大的内核，共有52个内部命令。该Shell其实是指向/bin/tcsh这样的一个Shell，也就是说，csh其实就是tcsh。

##### zsh
zch是Linux 最大的Shell之一，共有84 个内部命令。 zsh具有如下特性：
* 更好的自动补全、更高效
* 更好的文件名展开（通配符展开）
* 可定制性高

### 参考链接
* [什么是Shell以及常见Shell种类](https://academy.gmocloud.com/keywords/20170324/4010)