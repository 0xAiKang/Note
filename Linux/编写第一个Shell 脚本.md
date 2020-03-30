# 前言
这篇笔记用来记录编写 Shell 脚本的一些基础知识。

## 创建第一个shell 脚本
> 什么是 shell 脚本呢？

shell 脚本就是将一堆的 shell 命令以及指定执行 shell ，通过放在一个文件中来执行。

下面我们来创建第一个 shell 脚本：
```
$ vim showdate

#! /bin/bash
# this script displays the date and who's logged on
date
who
```
大功告成！这样就完成了一个简单的 shell 脚本的创建，是不是很简单！不过有以下几点需要注意：
1. shell 脚本的名称不是一定需要用 .sh 来结尾，只是用 .sh 结尾会让其他人一目了然知道这是一个 shell 脚本文件。
2. 在创建shell 脚本时，必须在第一行指定要使用的 shell，且格式固定为：`#! `开头。
3. 第二行的井号作为注释行。

运行shell 脚本：
```
$ ls
showdate
$ ./showdate
bash: permission denied: ./showdate
$ sudo ./showdate
sudo: ./showdate: command not found
$ chmod u+x showdate
$ ./showdate
```
创建完 shell 脚本，想要运行，有两种方案：
1. 将 shell 脚本所处的目录添加到 PATH 环境变量中;
2. 在提示符中用绝对路径或者是相对路径来引用 shell 脚本文件;

在上面的例子中，用的是绝对路径的方式来执行shell 脚本，使用单点操作符表示当前目录下的文件。

需要注意的是，因为文件夹权限的关系，而不能直接用 sudo 命令去执行，因为sudo 命令会检查showdate 并不在sudo 命令列表中。

所以正解是：修改该文件的文件夹权限。
