## 前言
这篇文章主要介绍Mac 系统默认安装的Apache 如何使用。

起因是不久前在本地搭建环境的时候，发现本地的80端口是被Apache 一直占用着。

因为我一直使用的是Nginx，这个Apache并不是我安装的，而是系统默认自带的。

找了好一会，才找到Apache 默认的网站根目录，以及Apache 的安装、配置文件位置。

### 默认安装位置
```
$ find /etc/apache2
apache2
```

### 默认网站根目录
```
$ ls /Library/WebServer/Documents/
index.html.en
```

### 默认配置文件位置
```
$ ls /etc/apache2
httpd.conf
```

### 常用命令
查看Apache 版本
```
apachectl -version
```
检查Apache 的配置语法
```
sudo apachectl configtest
```
使用标准命令重启Apache
```
sudo apachectl restart
```
启动Apache
```
sudo apachectl start
```
停止Apache
```
sudo apachectl stop
```
### 后记
因为业务需要，把本地默认占用 80 端口的Apache，给改成了占用其他端口。

然后昨天在给系统升级时，发现桌面多出来了一个叫做“迁移的项目”的文件。打开一看发现原来是之前被我编辑过的Apache 配置文件。

因为与默认配置文件不一样，所以系统把这个文件给单独拎出来了。

### 参考链接
* [MacOS 下的Apache 服务器的使用](https://www.hangge.com/blog/cache/detail_2322.html)