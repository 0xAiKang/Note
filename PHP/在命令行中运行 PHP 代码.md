# 前言
众所周知，PHP是一门脚本语言，主要用于服务端（JavaScript 用于客户端）以通过HTTP 生成动态网页。

所以与其他脚本语言一样，可以直接在终端中不需要网页浏览器来运行PHP 代码。

## 获取安装信息
在安装完PHP 以及Nginx 之后，接下来我们通常需要做的是，在`/usr/local/var/www` (Mac 上的Nginx 工作目录)上创建一个内容为`<?php phpinfo(); ?>`，名为index.php的文件来测试PHP 是否安装正确。

执行以下命令即可：
```
# echo '<?php phpinfo(); ?>' > /usr/local/var/www/index.php
```

然后，使用浏览器访问`http://127.0.0.1/index.php`，不出意外可以看到：
![image.png](https://i.loli.net/2020/03/19/m62QoSYvKuGfDUF.png)

> 如何在终端中直接查看该信息？
```
# php -f /usr/local/var/www/index.php | less
```
![image.png](https://i.loli.net/2020/03/19/6wmv8h1RUa9d5jX.png)

如果你觉得上面这种方式太麻烦了，那么还有一种更简便的方式可以达到同样的效果。
```
# php -r 'php phpinfo();' | less
```
## 交互模式
有时候我们会遇到这样一种情况，想测试一小段代码，看看其运行结果，但是又不想重新创建一个文件，太麻烦了。

如果这个时候有一个地方可以直接运行这段代码且输出结果，那该多好啊。

PHP为我们提供了一个交互模式，在终端中使用`php -a` 即可进入：
```
# php -a
Interactive shell

php >echo "Hello PHP";
Hello PHP
php > echo 10+90;
100
```
实际上在这个交互模式中可以做的事情挺多的，这里仅仅是演示如何使用该功能。

输入`exit`或者`⌃ + c` 退出交互模式。

## PHP脚本
在终端中可以把PHP 脚本作为Shell 脚本来运行。

首先你需要创建一个PHP 脚本文件：
```
# echo -e '#!/usr/bin/php\n<?php phpinfo();?>' > phpscript.php
```
`-e` 表示激活转义字符。

注意，这个脚本文件中的第一行`#!/usr/bin/php`，就像是Shell 脚本中的`#!/bin/bash`。目的是告诉Linux 命令行使用PHP 解析器来解析该文件。

运行该脚本：
```
# chmod +x phpscript.php  // 使脚本具有执行权限
# ./phpscript.php   //执行脚本
```

### 总结
* `php -a`：进入交互模式
* `php -f`：解析和执行文件
* `php -h`：获取帮助
* `php -i`：查看PHP 信息和配置
* `php -m`：显示已经安装的模块
* `php -r`：运行PHP代码不使用脚本标签'<?..?>'
* `php -v`：查看PHP 版本
* `php -ini`：查看php.ini 配置文件

### 参考链接
* [在 Linux 命令行中执行和使用 PHP 代码](https://linux.cn/article-5906-1.html)
* [12 个 Linux 终端中有用的 PHP 命令行用法](https://www.tecmint.com/execute-php-codes-functions-in-linux-commandline/)



