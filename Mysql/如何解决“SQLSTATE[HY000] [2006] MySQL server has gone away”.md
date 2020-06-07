## 前言
整理一下最近遇到的一个关于Mysql 的常见错误。

> 问题描述：所有跟数据库有关的操作，接口返回信息都是：SQLSTATE[HY000] [2006] MySQL server has gone away 。

其实会出现这个错误的常见原因只有两个：
1. Mysql 服务器超时并关闭了连接。
2. mysqld 收到的数据包太大或不正确，则会认为客户端出了问题，并关闭了连接。

由于PHP脚本执行时间太长，服务器过载，庞大的请求或MySQL配置错误，都会造成MySQL服务器的连接超时。

### 解决方式
通过配置`my.cnf`配置文件，并重启Mysql 服务。

```
# 在 my.cnf 配置文件中，加入以下内容：

# 数据包大小限制
max_allowed_packet = 16M
# 等待超时的时间，单位为秒，最大为28800 
wait_timeout = 1800 
connect_timeout = 120
```

### 参考链接
* [How to fix SQLSTATEHY000 2006 MySQL server has gone away](https://magento.stackexchange.com/questions/42176/geting-fatal-error-sqlstatehy000-general-error-2006-mysql-server-has-gone-a)