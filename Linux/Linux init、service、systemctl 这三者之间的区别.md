# 前言
在接触到Linux 的服务之后，我所知道的管理服务的方式有三种，分别是`init`、`service`、`systemctl`。

至于这三者之间的区别不得而知，所以整理这片笔记的目的就是了解这三者之间的区别。

## init
历史上，Linux 的启动一直采用init 进程。

在类Unix 的计算机操作系统中，Init（初始化的简称）是在启动计算机系统期间启动的第一个进程。

Init 是一个守护进程，它将持续运行，直到系统关闭。它是所有其他进程的直接或间接的父进程。

因为init 的参数全在`/etc/init.d`目录下，所以使用 init 启动一个服务，应该这样做：
```
$ sudo /etc/init.d/nginx start
```

## service 
通过查看man 手册页可以得知，service是一个运行`System V init`的脚本命令。

> 那么什么是 System V init 呢？

也就是`/etc/init.d` 目录下的参数。

所以分析可知service 是去`/etc/init.d`目录下执行相关程序，服务配置文件的存放目录就是`/etc/init.d`.

使用 service 启动一个服务
```
$ service nginx start
```

可以理解成 service 就是`init.d` 的一种实现方式。
所以这两者启动方式（或者是停止、重启）并没有什么区别。
```
$ sudo /etc/init.d/nginx start
// 等价于
$ service nginx start
```

但是这两种方式均有如下缺点：
1. 启动时间长。init 进程是串行启动，只有前一个进程启动完，才会启动下一个进程。
2. 启动脚本复杂。init进程只是执行启动脚本，不管其他事情。脚本需要自己处理各种情况，这往往使得脚本变得很长。

## systemd
Systemd 就是为了解决这些问题而诞生的。它包括 System and Service Manager，为系统的启动和管理提供一套完整的解决方案。
Systemd 是Linux 系统中最新的初始化系统（init），它主要的设计目的是克服 `System V init `固有的缺点，提高系统的启动速度。

根据 Linux 惯例，字母d是守护进程（daemon）的缩写。 Systemd 这个名字的含义，就是它要守护整个系统。

使用了 Systemd，就不需要再用init 了。Systemd 取代了initd（Initd 的PID 是0） ，成为系统的第一个进程（Systemd 的PID 等于 1），其他进程都是它的子进程。

Systemd 的优点是功能强大，使用方便，缺点是体系庞大，非常复杂。

查看Systemd 的版本信息
```
$ systemctl --version
```

### 系统管理
**Systemd 并不是一个命令，而是一组命令**，涉及到系统管理的方方面面。

#### systemctl 
systemctl是 Systemd 的主命令，用于管理系统。

```
// 重启系统
$ sudo systemctl reboot

// 启动进入救援状态（单用户状态）
$ sudo systemctl rescue

// 管理服务
$ sudo systemctl start nginx
```

#### hostnamectl
hostnamectl命令用于查看当前主机的信息。

```
// 显示当前主机信息
$ hostnamectl

// 设置主机名
$ sudo hostnamectl set-hostname BoodeUbuntu
```

#### localectl
localectl命令用于查看本地化设置。

```
// 查看本地化设置
$ localectl

// 设置本地化参数。
$ sudo localectl set-locale LANG=en_GB.utf8
$ sudo localectl set-keymap en_GB
```

#### timedatectl
timedatectl命令用于查看当前时区设置。
```
// 查看当前时区设置
$ timedatectl

// 显示所有可用的时区
$ timedatectl list-timezones                                                                                   

// 设置当前时区
$ sudo timedatectl set-timezone America/New_York
$ sudo timedatectl set-time YYYY-MM-DD
$ sudo timedatectl set-time HH:MM:SS
```

总结一下，`init` 是最初的进程管理方式，`service` 是`init` 的另一种实现，而 `systemd` 则是一种取代 `initd` 的解决方案，其中 `systemctl` 是 `systemd` 的主命令，用于管理系统以及服务。

### 参考链接
* [Linux Init - 维基百科](https://en.wikipedia.org/wiki/Init)
* [Systemd 入门教程 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html)
* [init、service、systemctl 的区别](https://blog.csdn.net/lineuman/article/details/52578399)
* [Linux 守护进程的启动方式](http://www.ruanyifeng.com/blog/2016/02/linux-daemon.html)