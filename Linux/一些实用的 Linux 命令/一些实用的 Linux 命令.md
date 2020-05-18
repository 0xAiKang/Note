## 前言
这篇笔记的目的是用来整理那些不常用但又很实用的Linux 命令。

### sudo!!
有时候我们好不容易输完一长串命令，却被提示”权限不足”，如果这个时候有一个命令记住上一次的输入内容那该多好。

还真有，`!!`命令可以获取最后一次输入的命令，所以我们直接输入下面这个命令就可以了。
```
$ sudo!!
```

### nl
`nl` 命令类似`cat`命令，都是查看文件内容，但不同之处在于：`nl`命令会在文本内容的每一行前面，添加行号。

```
$ cat test.txt
boo
mac
$ nl test.txt
1. boo
2. mac
```

### tree
以树状的形式返回当前目录的文件夹结构，这个命令很好用。

```
$ tree 
.
└── test.txt

0 directories, 1 file
```

### pstree
和`tree`类似，不过它是返回当前运行的所有进程及其相关的子进程的树状结构。
```
$ pstree | grep php
|-+= 01365 boo nginx: master process /usr/local/opt/nginx/bin/nginx -g daemon off;
 | \--- 01410 boo nginx: worker process
 | |     \--- 73098 boo grep --color=auto nginx
```

### <空格> 命令
这是一个有趣的命令，总所周知，用户在终端上键入的每一个命令都会被记录到`history`中，那么有没有一个命令可以骗过`history`，而不被记入呢？答案是有的。

在终端，只需要在键入命令之前输入一个或多个空格，这样你的命令就不会被记录了。
```
$ hisotry
8874  pstree | grep nginx
$  date
2020年 5月18日 星期一 21时09分03秒 CST
$ history
8874  pstree | grep nginx
```

### 一些其他命令
查看系统信息
```
$ uname -a 
```

查看当前日期
```
$ date
```

立即关机
```
$ shutdown -h now
```

重新启动
```
$ reboot
```

输出文件类型信息
```
$ file test.txt
test.txt: ASCII text
```

在终端中进行简单的算数运算
```
$ expr 1 + 3
4 
```

### 参考链接
* [鲜为人知而又实用的 Linux 命令](https://blog.csdn.net/zhu_xun/article/details/17018799)

