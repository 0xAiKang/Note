## 前言
在上一篇笔记中，我们知道了时区相关的概念，以及如何在PHP 获取设置默认时区。

这篇笔记就来学习一下如何在Mysql 上获取设置默认时区。

### 查看默认时区
```
mysql> show variables like "%time_zone%";
+------------------+--------+
| Variable_name    | Value  |
+------------------+--------+
| system_time_zone | CST    |
| time_zone        | SYSTEM |
+------------------+--------+
2 rows in set (0.01 sec)
```

### 设置默认时区
#### 设置当前会话
```
mysql> SET time_zone = "+8.00";
mysql> show variables like "%time_zone%";
```
此修改只会对当前会话有效。

#### 全局设置
```
mysql> SET global time_zone = "+8.00";
```
需要重启该会话，该配置才生效。

#### 编辑 my.ini 
```
# 打开Mysql 的配置文件 my.ini
[mysqld]
default-time_zone = '+8:00'
```
需要重启Mysql 服务

##### 时间格式
GMT（Greenwich Mean Time）：格林威治标准时间
UTC：世界标准时间
CST（China Standard Time）：中国标准时间

GMT + 8 = UTC + 8 = CST

### 参考链接
* [Mysql 查看修改时区 time_zone](https://majing.io/posts/10000004161181)

