# 前言
记录一个最近遇到的Mysql 问题。

> 问题描述：
在本地项目中，部分SQL 语句执行起来，总是会报一个错。
而同样的SQL，在线上的服务器中执行起来没有任何问题。

错误提示内容：
```
ORDER BY子句不在SELECT列表中导致MySQL 5.7带有SELECT DISTINCT和ORDER BY
```

我的第一反应就是检查Mysql 的版本，很巧的是本地Mysql
的版本确实比服务器的版本低一些。很快我就想到一定是版本存在差异性，导致语法不兼容。

## 升级Mysql
既然是版本不一的问题，那就升级本地的Mysql 好了。

因为我的Mysql 是之前通过Homebrew 安装的，所以如需要升级，根本不用我自己手动去寻找安装包，直接通过Homebrew 的Upgrade 命令自动升级就好了。

起初我还担心自动升级会不会把我的Mysql 的版本更新的`5.7`以上，后来证明是我想多了。

不过在正式更新之前需要做好以下几件事情：
* 对数据库做好必要的备份
* 停止本地Mysql 服务
* 确定所要更新的Mysql 版本

做好以上三件事之后，就可以开始升级了。
```
$ brew search mysql
mysql@5.7 ✔
$ brew upgrade mysql@5.7
...
```
终于安装好之后，再次开启Mysql 的服务，我发现还是没有解决我的问题，还是会提示相同的错误。

这时候我才意识到这个问题和Mysql 的版本没有关系，有关系应该是相关的模块。

通过查阅一番资料，我发现真正需要做的事情是开启服务器的SQL 模式，
## 开启sql-mode 模式
```
$ vim /usr/local/etc/my.cnf

# 增加如下内容
[mysqld]
sql_mode = ""
```
重启Mysql 服务器，即可。

### 参考链接
* [ 如何解决 MySQL 5.7带有SELECT DISTINCT和ORDER BY的问题 | stack voerflow ](https://stackoverflow.com/questions/36829911/how-to-resolve-order-by-clause-is-not-in-select-list-caused-mysql-5-7-with-sel/39353160)
* [Mysql 服务器SQL 模式](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html)