# 前言
如标题所示，这片笔记主要目的是加深对`Linux`的`Shell`、`Shell脚本`、`Shell环境`的理解。

## 什么是Shell？
* 在回答这个问题之前，我们先来考虑一个问题：人是如何跟计算机打交道的？或者说怎样让计算机按照我们的要求完成某个任务？

    - 现在和计算机交互的方式很简单，直接用图形界面的工具就好了，想要计算机完成某个任务，通过操作图形界面的工具就能到达目的。
    - 那么在以前呢？在那个计算机还没有这么先进的时代呢？人们又是如何让计算完成某个任务。通过“命令”的方式告诉计算机我需要你帮你完成这件事。这个“命令”又是怎么告诉计算机的呢？通过一个交互工具。这个工具可以实现与计算机之间的“你问我答，你说我做”的功能。
    
* `Shell`就是一种应用程序（注意：我这里用的是一种）。
* 这个应用程序提供了一个界面（方便我们与计算机进行交互），用户通过这个界面访问操作系统内核的服务。

## 什么是Shell脚本？
`Shell` 脚本（`Shell Script`），是一种为 `Shell` 编写的脚本程序。

> Shell 脚本编程有两种方式
1. 交互式（Interactive）：用户每输入一条命令就立即执行。
2. 批处理（Batch）：由用户事先编写好一个完整的`Shell`脚本，`Shell`会一次性执行脚本中诸多的命令。

## 什么是Shell环境
`Shell`编程跟`java`、`php`编程一样，只要有一个**能编写代码的文本编辑器**和一个**能解释执行的脚本解释器**就可以了。

### 0x01 Linux
* `Linux` 默认安装了 `Shell` 解释器。
* 在`Linux`中，主流的 `Shell` 是 `Bash`。

在一般情况下，人们并不区分 `Bourne Shell` 和 `Bourne Again Shell`，所以，像 `#!/bin/sh`，它同样也可以改为 `#!/bin/bash`。

### 0x02 Mac OS
* Mac OS不仅带了sh、bash这两个最基础的解释器，还内置了ksh、csh、zsh等不常用的解释器。

### 0x03 Windows
Windows 出厂时没有内置 `Shell` 解释器，通常我们都是安装`cygwin`或者`mingw` 模拟器来Linux环境。
* [Cygwin](http://www.cygwin.com/)
* [Mingw](http://www.mingw.org/)

如Git的交互界面就是由`Mingw`模拟器提供的`Bash`。

#### 脚本解释器
```
bash => Bourne Again Shell（/bin/bash）
sh =>  Bourne Shell（/usr/bin/sh或/bin/sh）
csh => C Shell（/usr/bin/csh）
ksh => K Shell（/usr/bin/ksh）
Shell for Root（/sbin/sh）
```
#### 第一个Shell脚本
打开Bash或者任何一个文本编辑器，新建一个文件 Hello.sh，扩展名为`sh`(sh代表shell)。

```
#!/bin/bash
#第一个Shell脚本
#作用是列出当前目录下的所有文件的详情信息
PWDS=echo `pwd`
cd $PWDS
ls -l
```
上面这个脚本中，有三种不同的元素：
1. 第一行的脚本声明（`#!`）用来告诉系统使用**哪种 Shell 解释器**来执行该脚本；
2. 第二行的注释信息（`#`）是对脚本功能和某些命令的介绍信息，使得看到脚本时能快速反应是做什么的。
3. 剩下没有前缀标识的就是 所要执行的脚本具体命令了。

#### 运行Shell脚本
有两种方式：
##### 1. 作为可执行程序
```
$ chmod +x example.sh    # 使脚本具有执行权限
$ ./example.sh           # 执行脚本
```
##### 2. 作为解释器参数
这种运行方式是，直接运行解释器，其参数就是shell脚本的文件名:
```
# 执行脚本
$ /bin/sh example.sh
$ bash example.sh
$ bash example.php       
```
使用这种方式时，可以不用在脚本第一行声明解释器信息。
```
$ cat example.php
#这是一个用php写的Shell脚本，有两个作用
#1.确认是否用解释器参数执行shell脚本可以不用写声明
#2.确认如何用php写shell脚本
string="php shell"
echo $string
$ bash example.php
php shell
```

> 思考两个问题：

* `Linux Bash` 编程，`Shell` 编程，指的是同一回事吗？
* 为什么说扩展名并不影响脚本的执行，比如可以用`PHP`写`Shell`脚本？