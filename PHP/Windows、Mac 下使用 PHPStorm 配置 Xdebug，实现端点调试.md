# 前言
搭建过很多次开发环境了，但每次在调试这一块还是会多少耗费一点时间。
所以便有了这篇关于`PHPSTORM`调试的笔记。

> 在进行调试之前，首先要做的是下载并安装`Xdebug`，然后再做相应的配置。如果使用集成环境，那么可以跳过这一步，通常都自带了`Xdebug`。

## 下载Xdebug（Windows）

* [xdebug官网](https://xdebug.org/download.php)

> 如何选择符合自己PHP的版本的Xdebug，可以通过下面这种方法来判断。

使用Xdubug官方提供的一个[检测工具](https://xdebug.org/wizard.php)

在命令行中输入：
```
# Mac
$ php -i | pbcopy

# Linux
$ php -i | xsel 

# Windows
$ php -i | clip
```

![image](https://note.youdao.com/yws/api/personal/file/8C67A50DB938479D81F36CB324EFA158?method=download&shareKey=cbfdae6d343cc1d6a664f05865e869c8)

将输出的`phpinfo`信息填入，然后就会自动检测该版本的PHP 所对应的Xdebug，如下图（这里以Windows 为例）：

![image](https://note.youdao.com/yws/api/personal/file/E08134E0216248E9B1B24B14DC293472?method=download&shareKey=3387387960345b3f06b185101a6b4063)

点击下载相应的文件。

## 安装并配置Xdebug
1. 将下载好的文件放进指定目录 `..\php\ext\`
2. 配置`php.ini`文件，这里需要注意的是：要找到正确的`php.ini`文件。如果你不确定是哪一个，可以参考下面这个方法：

打印出`phpinfo()`，找到字段`Loaded Configuration File`根据后面的路径去找就没错了。

打开找到的php.ini配置文件，在最后面加上以下代码：
```
# Windows
[XDebug]
zend_extension = "C:\xampp\php\ext\php_xdebug-2.6.1-7.2-vc15.dll"  #这个地址指向 xdebug所在的文件路径
xdebug.profiler_enable = 1
xdebug.remote_enable = 1
xdebug.remote_port=9001
xdebug.idekey=PHPSTROM
xdebug.remote_host = localhost 
```
其中：
1. `xdebug.remote.host`如果是本地调试，填`localhost`就好。
2. `xdebug.remote_port`为调试所监听的端口，通常默认使用 `9001` ，需要和PHPStorm 中的 Debug port 相同。

## 下载并安装Xdebug（Mac）
Mac 下安装Xdebug，有两种方式：
1. 使用`pecl`命令
2. 通过源码编译

### 使用 pecl
> Pecl 是 PHP 的包管理器。

这里以`PHP5.6`为例，需要安装最新`2.5.x`版本的Xdebug，因为这是`PHP5.6`提供支持的最后一个版本。
```
$ pecl install xdebug-2.5.5
```
### 源码编译
源码获取的方式和上面Windows 的方式是一样的，将输出的`phpinfo`粘贴至输入框，然后下载对应版本的Xdebug。

```
$ tar -xvzf xdebug-2.9.4.tgz
$ cd xdebug-2.9.4.tgz
$ phpize
$ ./configure
$ make
$ cp modules/xdebug.so /usr/lib/php/extensions/xdebug
```

#### 启用Xdebug
无论是通过哪种方式安装，在正式使用之前，都需要手动启用该模块。

找到对应版本的 php.ini 文件并编辑，在配置文件中的最后部分加上以下内容：
```
[XDebug]
zend_extension="/usr/local/lib/php/extensions/xdebug/xdebug.so"
xdebug.profiler_enable = 1
xdebug.remote_enable = 1
xdebug.remote_port=9001
xdebug.idekey=PHPSTORM
xdebug.remote_host = localhost
```

重启PHP即可。

> 如何检查Xdebug 是否启用？

```
$ php -m | grep xdebug
xdebug
```

### 在PHPStorm中配置Xdebug
#### Mac
1. `File->Setting->PHP->Debug`，确保`PHPStorm` 已经找到了`Xdebug`。

在刚才的配置没错的前提下，这里是可以看到已经成功安装了`Xdebug`的。

![image.png](https://i.loli.net/2020/03/24/yw6EbgcujoFILXU.png)

如果显示没有安装，请检查上面两步操作有无问题。

2. `File->Setting->PHP->Debug`

![PHPStorm 2.jpg](https://i.loli.net/2020/03/24/iKkmq3AsIy2UOWX.jpg)

Debug port 与`php.ini`配置文件中的`xdebug.remote_port`的对应参数保持一致。

3. `File->Setting->PHP->Server`，这三个参数的值和`php.ini`中的保持一致。

![PHPStorm 3.jpg](https://i.loli.net/2020/03/24/sgzX4G957CuTWPj.jpg)

4. 配置域名

![PHPStorm 4.jpg](https://i.loli.net/2020/03/24/kecrgBb1IfWaVYR.jpg)

这里根据实际情况配置，我本地使用80 端口作为项目访问端口，所以这里填的是80。

5. 配置调试参数

![PHPStorm 5.jpg](https://i.loli.net/2020/03/24/pShQCstHl7N32na.jpg)

6. `Run->Web Server Debug Validation`，检查是否配置成功。

![PHPStorm 6.jpg](https://i.loli.net/2020/03/24/Plxvs6gKOcqIGV1.jpg)

确保项目文件路径和本地域名能正常访问，如果一切正常则能看到输出。

### Windows
Windows 下的PHPStorm 配置和Mac 几乎差不多，保证一下几点是正常的基本上没啥问题。

1. 确保PHPStorm 启用了对应版本的 Xdebug。
2. PHPStorm 的调试信息与`php.ini`文件中保持一致。
3. 项目文件路径和本地域名能正常访问。

> Xdebug 调试端口并非一定要用9001，只要保持`php.ini`与`PHPStorm` 的保持一致就好了。

#### 在PHPStorm中使用Xdebug
有两种方式使用Xdebug：
1. 直接在编辑器中开始调试。
2. 通过在请求地址中附加xdebug 的请求参数来调试，这招通常用来处理一些前后端分离的联动调试。

### 参考链接
* [PhpStrom Xdebug 配置与使用](https://laravel-china.org/articles/16770/the-first-chapter-of-laravel-phpstrom-xdebug-configuration-and-use)
* [如何在Mac 上为不同版本的PHP 开启Xdebug](https://getgrav.org/blog/macos-mojave-apache-mysql-vhost-apc)
* [配置Xdebug-官方教程](https://www.jetbrains.com/help/phpstorm/configuring-xdebug.html)
* [Xdebug 官网](https://xdebug.org/)
* [Xdebug 检测工具](https://xdebug.org/wizard.php)