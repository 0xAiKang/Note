# 前言
最近面临一个需求，需要使用Mysql 写一段存储过程，对数据库中的数据表做批量操作。

应该算是知识盲区了，花了一些时间去学习如何写好一个存储过程，最终也顺利写出来了，记录一下。

以下两点是其中比较重要的部分：
1. 关于变量的使用
2. 在存储过程中使用动态SQL 语句

## 存储过程中的变量
MySQL存储过程常见的变量：局部变量、用户变量、系统变量。

### 局部变量
在过程体中，可以声明局部变量，用来临时保存一些值。
```
DECLARE  var_name[, var_name] ...  type [DEFAULT value];
```
其中，type为MySQL的数据类型，如:int、float、date、varchar(length) 。

使用局部变量时，需要注意以下两点：
1. DECLARE用来声明局部变量，且DECLARE仅被用在BEGIN ... END复合语句里，并且必须在复合语句的开头，在任何其它语句之前；可以被用在嵌套的块中，除了那些用相同名字声明变量的块。
2. 如果要给变量提供一个默认值，使用DEFAULT子句(值可以是常数，也可以指定为一个表达式)；如果没有DEFAULT子句，初始值为NULL。

### 用户变量
用户变量与数据库连接有关：在当前连接中声明的变量，在连接断开的时候，就会消失；在此连接中声明的变量无法在另一连接中使用。

用户变量使用`@`关键字去定义。

## 在存储过程中动态执行SQL
其实这个理解成一套模版，只要按照标准去执行这套模版，就可以了。

```
-- 连接数据库
use databaseName;
-- 定义结束符为 $$
delimiter $$
-- 判断是否存在该名称的存储过程，如果存在就删除
drop procedure if exists wk;
-- 创建新的存储过程
create procedure wk()
begin
        -- 声明变量
        declare days int default 366;
        declare dates int;
-- 循环体
WHILE days - 1 > 0 DO
        -- 为变量赋值
	    SET dates = DATE_FORMAT(DATE_SUB(CURDATE(), INTERVAL dayofyear(now())- days DAY), "%Y%m%d");
		SET days = days - 1;
		-- 拼接表名
		set @table_name = CONCAT("tableName", dates);
        -- 拼接需要执行SQL 语句，后面的内容需要根据实际情况替换掉
        SET @sql = CONCAT("ALTER TABLE ", @table_name, "
          -- 需要执行的SQL 
          ");
        -- 预处理动态SQL 语句，其中stmt 是一个变量
        PREPARE stmt FROM @sql;
        -- 执行SQL 语句
        EXECUTE stmt ;
        -- 释放prepare
        deallocate prepare stmt;
-- 结束循环
end WHILE;
-- 结束定义语句
end $$
delimiter ;
call wk();
```
大致上就是这样，至此，一个完整的Mysql 存储过程就完成了。

> 如何在终端执行Mysql 文件？

SQL 脚本准备好了，有两种方式可以执行它。
1. 方式一：不进入Mysql 终端，直接在命令行终端执行
2. 方式二：进入Mysql 终端，在Mysql 终端中执行

这两种方式的共同点就是都需要已知Mysql 密码。

对于方式一，可以使用以下命令来执行：

```
mysql -u root -p < ./modify_user_table.sql
```
可以指定数据库：
```
mysql -u root -p databaseName < ./modify_user_table.sql
```

对于方式二，可以使用以下命令来执行：

```
// 进入Mysql 终端
mysql -uroot -p 

// 执行SQL 文件
source ./modify_user_table.sql
```

### 参考链接
* [Mysql 终端执行SQL 文件](https://pein0119.github.io/2014/10/22/MySQL%E7%BB%88%E7%AB%AF%E6%89%A7%E8%A1%8CSQL%E6%96%87%E4%BB%B6/)
* [Mysql 存储过程中的变量定义](https://www.cnblogs.com/geaozhang/p/6803423.html)
* [Mysql 中的变量定义和赋值](https://www.cnblogs.com/easonjim/p/7966918.html)
* [Mysql 存储过程中使用动态SQL 语句](https://my.oschina.net/u/2331760/blog/3065637)

