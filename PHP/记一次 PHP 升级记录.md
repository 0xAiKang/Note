# 前言
昨天晚上把服务器的PHP 给升级了一下，服务器的系统是`Ubuntu`。
本以为会很快就完成的，没想到还是额外花了一些时间。

因为系统使用的是`Ubuntu`，所以我没有选择源码安装的方式，而是直接使用`apt get`命令。

首先把`5.6`的版本卸载干净，然后`apt get install php7.3`。
因为`mcrypt`这个扩展在`php7.2`版本被弃用了，`php7.3`中还能使用，但是需要手动安装这个扩展。

所以这里产生了几个问题：
* `php.ini`配置文件使用哪个
* 手动编译安装第三方扩展
* Nginx 与 `php-fpm`的交互方式
* 管理PHP 服务

问题还是有一些多的，一个个来说吧。

## 卸载PHP5.6
在`Ubuntu`上卸载一个工具，很简单。
```
$ sudo apt-get --purge remove <program name>

// 卸载和php5.6 有关的所有内容
$ sudo apt-get --purge remove php5.6*
```
## 安装PHP7.3
因为版本迭代的关系，部分扩展在最新的版本中没有办法直接安装。

```
$ sudo apt-get install php7.3 php7.3-fpm php7.3-cli php7.3-cgi php7.3-mysqli php7.3-curl php7.3-GD php7.3-mbstring php7.3-redis php7.3-bcmath php7.3-xml 

// 安装php扩展工具，如果没有安装这个工具，那就没有办法使用 phpize
$ sudo apt-get install php7.3-dev
```
比如：
* `PHP7.3`中使用`mysqli`替代了`mysql`
* `mcrypt`从`PHP7.2`开始不能直接安装了，需要手动安装扩展。
* `swoole`需要通过扩展安装。

### 安装mcrypt
在安装之前，首先去`PECL`官网，下载对应的安装包。
```
$ tar -xzvf mcrypt.tar.gz 
$ cd mcrypt
$ phpize
$ ./configure --with-php-config=/usr/bin/php-config
$ make
```
因为本地存在多个版本的PHP，所以需要指明`php-config`的路径。

> 如果不知道`php-config`的路径在哪里，可以使用命令查看：

```
$ whereis php-config
php-config: /usr/bin/php-config
```

#### 编辑 php.ini
手动启用扩展的最后一步就是在`php.ini`文件中添加配置。

因为我是通过`apt-get`命令安装的，
```
$ ls /etc/php/7.3
cgi  cli  fpm  mods-available
```
`cgi`、`cli`、`fpm` 这三个目录下都存在`php.ini`文件。
* `cgi`：暂不清楚
* `cli`：`php-cli`的配置内容
* `fpm`：`phpinfo()`的配置内容

所以如果想要网站上开启扩展，那么就需要编辑`fpm`下的`php.ini`文件。

我开始一直在编辑`cli`下的配置文件，然后通过`php -m | grep mcrypt`查看，是可以看到`mcrypt`扩展是启用的。
但是网站上却一直提示没有开启该扩展。

这算是一个坑吧。

另外，这两种写法都是等效的：
```
extension = mcrypt.so

extension = "/usr/.../mcrypt.so"
```

## 管理PHP 服务
在`Ubuntu`中，并不能直接通过`service`命令来管理PHP 服务。
或者说`php-fpm`服务就是`php`的服务。

就目前而已，我是这么做的：
```
// 在后台启动php-fpm 服务
$ ./php-fpm7.3 &

// 停止php-fpm 服务
$ ps aux | grep php-fpm
$ kill -9 pid
```

## 重启PHP 的正确方式
某些时候，我们会因为不同的业务需求，需要重新启动PHP 服务，这时就需要知道如何管理PHP 服务了。

如果是修改了php.ini 的配置文件（Ubuntu 系统一般有三个php.ini，通常是修改fpm 目录下的），则需要先停止php服务。

而php 的服务，通常是由`php-fpm`来管理，所以需要做的第一件事情就是停止`php-fpm`服务。
```
$ ps ef | grep php-fpm
root     10518  0.0  0.1 451696 14244 ?        Ss   09:50   0:00 php-fpm: master process (/etc/php/7.3/fpm/php-fpm.conf)
www-data 10519  0.0  0.1 453996 13748 ?        S    09:50   0:00 php-fpm: pool www
www-data 10520  0.0  0.1 453996 13748 ?        S    09:50   0:00 php-fpm: pool www
root     10522  0.0  0.0  16148  1048 pts/0    R+   09:50   0:00 grep --color=auto php-fpm
$ kill -9 10518 10519 10520
```
然后进入`php-fpm`的二进制文件目录，启动`php-fpm`的服务。

```
$ whereis php-fpm
php-fpm: /usr/sbin/php-fpm7.3
$ cd /usr/sbin
# 已后台运行的方式启动服务
$ ./php-fpm7.3 &
```
这种方式可能不是最好的，因为`kill -9`的这种停止服务方式太暴力了。
如果有更好的方式再记录。

