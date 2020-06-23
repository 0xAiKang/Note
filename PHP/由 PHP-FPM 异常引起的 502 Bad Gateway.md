## 前言
最近项目升级遇到了一个奇怪的问题，因为是MVC 的项目结构，在实际的请求地址中如果中包含了几个不存在的静态资源地址，则会导致每次请求都会使`php-fpm`异常终止，然后网站就会出现`502 Bad Gateway`。

起初我是很不能理解，为什么引入了几个不存在的静态资源，就会直接把`php-fpm`搞挂了呢？

经过一系列的日志分析排查，我把范围缩小到以下两个方面：
1. 项目配置或者项目本身存在bug，导致带参数的URL时出现请求异常。
2. 和`PHP-FPM`配置有直接关联

根据这两点展开讨论，以下是具体的分析过程。

### PHP-FPM
在之前的这篇笔记中，着重讲解过`PHP-FPM`是什么，所以这里就不过多介绍了。

简单点理解`PHP-FPM`是一个的`PHP FastCGI`管理器，而`PHP FastCGI`就是处理PHP 进程的。

在了解如何优化`PHP-FPM`之前，先来看一下`php-fpm.conf`中的常用参数。

#### 进程模式
有两种模式：
1. static(静态) ：表示在 fpm 运行时直接 fork 出 `pm.max_chindren个worker` 进程
2. dynamic(动态)：表示运行时 fork 出 `start_servers` 个进程，随着负载的情况，动态的调整，最多不超过 `max_children` 个进程。

一般推荐用static，优点是不用动态的判断负载情况，提升性能，缺点是多占用些系统内存资源。

#### max_children
最大子进程数量，理论上，这个值是越大越好，因为`php-cgi`的进程多了就会处理的很快，排队的请求就会很少。

但是也需要服务器的实际性能来进行权衡，一般来说一台服务器正常情况下每一个`php-cgi`所耗费的内存在`20M`左右，假设`max_children`设置成100个，那么在峰值的时所有`PHP-CGI`所耗内存在2000M (20M*100=2000M) 以内。

当`max_children`设置的很小，而请求数量又很多时，就会造成处理速度慢，等待时间长。
进而如果长时间没有得到请求结果，那么就会出现`504 Gateway Time-out`，如果`php-cgi`遇到问题则会出现`502 Bad gateway`。

#### max_requests
最大处理请求数是指一个`php-fpm`的`worker`进程在处理多少个请求后就终止掉，`master`进程会重新起一个新的`worker`进程。

> 为什么要控制每个`worker`进程处理请求的数量？

其主要目的是避免php解释器或程序引用的第三方库造成的内存泄露。

当一个 PHP-CGI 进程处理的请求数累积到 max_requests 个后，自动重启该进程。

总所周知，502 异常是服务端产生的，也就是`PHP-FPM` 不可用造成的，而间歇性 502 异常，一般是认为由于`PHP-FPM` 进程重启造成的。

> PHP-FPM 为什么要自动重启呢？

上面说过， `PHP-FPM` 作为 `PHP-CGI` 的管理器，会对请求达到指定次数的 `PHP-CGI` 进程进行重启，以保证内存使用量不增长。所以当这个值如果设置很小，在高并发时，可能就会引起频繁重启。

#### request_terminate_timeout&max_execution_time
最长执行时间和请求终止超时间。

这两项都是用来配置一个PHP脚本的最大执行时间的。当超过这个时间时，`PHP-FPM`不只会终止脚本的执行，还会终止执行脚本的`Worker`进程。

当`Nginx` 会发现与自己通信的连接断掉了，就会返回给客户端502 异常。

#### 开启慢日志
在了解了`PHP-FPM`的一些常用参数之后，接下来就要开始具体分析了。

在解决此类问题时，需要做的第一件事情就是查看日志，日志信息是唯一能够让我们了解，程序的“各种情绪”的方式。

`PHP-FPM` 默认没有开启慢日志，这会导致输出的日志信息不够详细，不便于定位问题，所以在调试时，可以开启慢日志功能。

开启输出`php-fpm`慢日志，阀值为2秒：
```
// mac
vim /usr/local/etc/php/7.3/php-fpm.d/www.conf

// linux
vim /etc/php/7.3/fpm/pool.d/www.conf

// 编辑以下内容
request_slowlog_timeout = 2
slowlog = log/$pool.log.slow
```

#### 开启PHP-FPM 状态监控
如果想要实时查看`php-fpm`的当前状态，那么可以通过编辑`php-fpm.conf`配置文件来开启。

```
// vim /etc/php/7.3/fpm/php-fpm.conf
pm.status_path = /status
```

配置Nginx 
```
location /status {
    fastcgi_pass  127.0.0.1:9000;
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $fastcgi_script_name;
}
```
重启`nginx`和`php-fpm`，再去查看`php-fom.log`或者`http://your_website.com/status`，就可以看到更多信息了。

### 日志分析

在慢日志的情况下，再次请求会产生502 异常的链接，可以看到，成功记录到了，引起`php-fpm`异常的全过程。

![image.png](https://i.loli.net/2020/06/23/eQzmbF64vsMGnih.png)

`php-fpm`似乎就是因为遇到了问题，无法自动完成重启，导致子进程停止了。

![image.png](https://i.loli.net/2020/06/23/Xi985EP61g7CGkh.png)

经过几分钟等待之后，网页恢复了正常，这时再去看`php-fpm`的当前状态，可以看到一些很有用的信息，结合这些信息，也许就能找到导致`502 Bad Gateway`的原因。

因为空闲的子进程数量本来就很少，当发生异常时，可能没有新的空闲子进程顶上去，就会导致恶性循环，本来就忙不过来的其他几个进程，于是也纷纷倒下了。

为了验证我的猜想，我在那个请求地址中，只保留了一个不存在的路径，结果是：只是样式丢了，但是`PHP-FPM`并没有挂掉。

这似乎暗示了，这个问题的解决办法是什么，那就是优化`php-fpm`配置，增加最大子进程数量，当然还需要配合其他参数来一起优化。

虽然找到了解决办法，但是似乎还是没有从根本上解决这个问题。那就是前端的静态资源文件，如果不存在的话，应该会直接返回给前端啊，应该不至于长时间去请求后端。

目前还没有从根本上解决这个问题，暂时先放下，后面如果有新的进展，再补充。

### 参考链接
* [php-fpm 与 nginx 优化总结](https://www.kancloud.cn/digest/php-src/136260)
* [php-fpm 进程优化](https://zhuanlan.zhihu.com/p/103062893)
* [Example configuration of php-fpm](https://github.com/perusio/php-fpm-example-config)
* [nginx + php-fpm 请求超时](https://groups.drupal.org/node/229898)
* [linux 查看nginx状态和php-fpm状态](https://blog.csdn.net/z13615480737/article/details/84030207)