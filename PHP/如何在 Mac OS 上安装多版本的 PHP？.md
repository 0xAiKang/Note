# 前言
最近在Mac 上做开发，起初在搭建环境时 遇到了部分问题，加上Mac 预装的那个PHP 版本，实在是不好用，`php-fpm` 总是启不动，最后索性决定在本地自己装个多版本，可以随时自由切换。

> 是否需要清除旧版本？

因为需要在Mac 上安装其他版本，所以预装的那个版本的PHP 的存在就没啥意义了。
考虑到本机的其他软件可能会依赖它，为了给以后省些事，最后还是决定将预装的版本给移除掉。

事实证明移除了也没关系。

## 移除旧版本
这里说的旧版本指的是Mac 自带的PHP版本。

```
# /private/etc/
$ sudo rm -rfi php-fpm.conf.default php-fpm.conf php.ini.default php-fpm.d/
# /usr/bin/
$ sudo rm -rfi php php-config phpize
# /usr/lib/
$ sudo rm -rf php/
# /usr/sbin/
$ sudo rm -rf php-fpm
# /usr/share/
$ sudo rm -rf php
# /usr/share/man/man1/
$ sudo rm -rf php-config.1 php.1 phpize.1
```
执行完上面这些命令就能将旧版本的PHP 彻底的从你的Mac 上移除了。

## 安装多版本
直到2018年3月底，所有PHP 相关的brew 都由 `homebrew/php` tab 处理，但是已经弃用了，所以现在我们使用`homebrew/core`包中的可用的内容。这应该是一个更好维护但是不太完整的包。

由于`PHP5.6`和`PHP7.0`在 Homebrew 上已被弃用，因为以不被支持，虽然不建议在生产环境中使用，但还是可以在开发环境中使用这些不受支持的版本，可以参考：[PHP支持的版本](https://php.net/supported-versions.php)。

请记住，Homebrew 正式支持PHP7.1 到 7.3 ，因此如果要安装 PHP5.6或PHP7.0，则需要执行如下命令：
```
$ brew tap exolnet/homebrew-deprecated
Updating Homebrew...
==> Auto-updated Homebrew!
Updated 1 tap (homebrew/core).
……
```
接下来正式开始安装PHP 的各种版本，并使用简单的脚本来进行版本之间的切换。
```
$ php install php@5.6
$ php install php@7.0
$ php install php@7.1
$ php install php@7.2
$ php install php@7.3
```
第一个安装所花费的时间长一些，因为需要安装一堆brew 的依赖，随后其他版本的安装的将很快。

所安装各版本的PHP都在该目录下：
```
$ ls /usr/local/etc/php
5.6 7.0 7.1 7.2 7.3

# php.ini 配置文件目录
/usr/local/etc/php/x.x/php.ini
```

安装完以上版本的PHP 之后，执行：
```
$ php -v 
PHP 7.3.5 (cli) (built: May  2 2019 12:40:36) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.3.5, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.3.5, Copyright (c) 1999-2018, by Zend Technologies
```
可以看到目前所使用的PHP 版本是7.3（最后安装完的那个），现在试图切换到第一个安装的PHP 版本：
```
$ brew unlink php@7.3 && brew link --force --overwrite php@5.6
```
`unlick` 安装PHP 版本之间不再需要联系，因为默认情况下他们是没有符号链接。

再次查看当前版本：
```
$ php -v
PHP 5.6.40 (cli) (built: Apr 23 2019 11:14:34)
Copyright (c) 1997-2016 The PHP Group
Zend Engine v2.6.0, Copyright (c) 1998-2016 Zend Technologies
    with Zend OPcache v7.0.6-dev, Copyright (c) 1999-2016, by Zend Technologies
```

切换的挺顺利的，但如果每次需要切换时都需要这样输入就变得很麻烦了，幸运的是，一些勤劳的人已经为我们完成了艰苦的工作，并编写了一个非常方便的脚本——[PHP切换器脚本](https://gist.github.com/rhukster/f4c04f1bf59e0b74e335ee5d186a98e2)。

将`sphp`脚本安装到 brew 的标准中`/usr/local/bin`：
```
$ curl -L https://gist.githubusercontent.com/rhukster/f4c04f1bf59e0b74e335ee5d186a98e2/raw > /usr/local/bin/sphp
$ chmod +x /usr/local/bin/sphp
```

### 多版本切换
完成这些步骤后，就能够使用脚本命令切换PHP版本：
```
$ sphp 7.2
```
使用时会需要提供管理员密码，相比长长的命令这已经省事很多了。

好了，到这里就顺利的完成了多版本的PHP 安装以及切换。

### 管理 PHP 服务
在不需要切换版本时，使用`brew services`命令可以对该版本的PHP 进行管理：

启动/停止/重启 PHP服务：
```
$ brew services start/stop/restart php
```
当PHP 服务启动时，通过查看进程列表，可以发现多了几个名为`php-fpm` 的进程。

`php-fpm`的进程所在目录：`/usr/local/opt/php/sbin/php-fpm`

这个进程很重要，在与 Nginx 交互时，如果没有启动它，通常会收到 `502 Bad Gateway` 的错误。

#### 启动 php-fpm
尽管不需要刻意的去管理这个进程，但如果这个进程意外停止运行了，还是要知道该如何启动它。

##### 前台启动
```
$ cd /usr/local/opt/php/sbin/
$ ./php-fpm
```
用这种方式启动，当使用`⌃ C`退出时，进程也会跟着退出。

##### 后台启动
```
# /usr/local/opt/php/sbin/
$ ./php-fpm &
```
如果用这种方式启动，就算退出了当前会话，进程会以守护进程的方式运行着。

### 检查PHP 版本
最后再啰嗦两句，如果需要把当前5.6版本切换成7.2，那么需要分别做两件事：

```
# 第一步
$ sphp 7.2

# 第二步
$ brew services stop php@5.6
$ brew services start php@7.2
```
如果只做了这一步，那么你会发现 `php -v`的版本输出的确是 7.2，但`php_info()`所打印的结果却还是 5.6。

这是因为机器上安装了多个PHP 版本，当使用`php -v`命令时，它将显示默认`PHP CLI`的版本，而该版本可能不是网站所使用的版本。

所以找出用于特定网站的PHP 版本的最可靠方法是使用`phpinfo()`函数。

### 参考链接
* [Mac 下 Nginx、PHP、MySQL 和 PHP-fpm 的安装和配置](https://segmentfault.com/a/1190000005090828#articleHeader0)
* [如何在Mac 上安装多版本的PHP](https://getgrav.org/blog/macos-mojave-apache-multiple-php-versions)
* [如何卸载Mac 预装的PHP](https://mengkang.net/343.html)
* [如何检查PHP 版本](https://linuxize.com/post/how-to-check-php-version/)