## 前言
最近用到了 `PHP PDO`相关的知识，整理总结一下。

### PDO 是什么？
PDO（PHP Data Object）

PHP 数据对象 （PDO） 扩展为PHP访问数据库定义了一个轻量级的一致接口。

### PDO 能做什么？
PDO 提供了一个数据访问抽象层，这意味着，不管使用哪种数据库，都可以用相同的函数（方法）来查询和获取数据。

在 PHP 使用 MySQL 数据库前，你需要先将它们连接。

PHP 5 及以上版本有两种方式连接 MySQL :
* MySQLi extension ("i" 意为 improved)
* PDO (PHP Data Objects)

> 关于是选择 Mysqli，还是 PDO？

MySQLi 和 PDO 有它们自己的优势：PDO 应用在 12 种不同数据库中， MySQLi 只针对 MySQL 数据库。

如果项目需要在多种数据库中切换，建议使用 PDO，因为只需要修改连接字符串和部分查询语句即可。

### PDO 安装
在 PHP5 系列版本中，PDO不是默认支持的，需要手工配置才可以使用。打开 php.ini 文件，开启扩展。

```
// php.ini
extension=php_pdo.dll
extension=php_pdo_mysql.dll
```
上述配置只打开了对 MySQL 的 PDO 支持，如果需要对别的数据库类型进行支持，可以分别打开对应的不同配置（去掉前面的分号）：
```
;extension=php_pdo_oci.dll
;extension=php_pdo_oci8.dll
;extension=php_pdo_odbc.dll
;extension=php_pdo_pgsql.dll
;extension=php_pdo_sqlite.dll
```

### PDO 创建连接
在使用 PDO 操作数据库之前，需要创建 PDO 连接对象。

```
new PDO(DSN, username, password);<?php

$dsn = "mysql:host=localhost; dbname=databasename";
$stmt = new PDO($dsn, 'user', 'pwd');
```

不同的数据库，其 DSN(Data Source Name) 构造方式是不一样的。常见数据库 DSN 语法如下：
```
//MySQL:
$dsn = mysql:host=hostname;dbname=db_name)

//SQLite:
$dsn = sqlite:db_name

//PGSQL
$dsn pgsql:host=hostname port=port_id dbname=db_name user=username password=password
```

### PDO Mysql 预处理语句

**预处理语句及绑定参数**
预处理语句用于执行多个相同的 SQL 语句，并且执行效率更高。

预处理语句的工作原理如下：

1. 预处理：创建 SQL 语句模板并发送到数据库。预留的值使用参数 "?" 标记 。例如：INSERT INTO MyGuests (firstname, lastname, email) VALUES(?, ?, ?)
2. 数据库解析，编译，对SQL语句模板执行查询优化，并存储结果不输出
3. 执行：最后，将应用绑定的值传递给参数（"?" 标记），数据库执行语句。应用可以多次执行语句，如果参数的值不一样。

相比于直接执行SQL语句，预处理语句有两个主要优点：
* 预处理语句大大减少了分析时间，只做了一次查询（虽然语句多次执行）
* 绑定参数减少了服务器带宽，你只需要发送查询的参数，而不是整个语句
* 预处理语句针对SQL注入是非常有用的，因为 参数值发送后使用不同的协议，保证了数据的合法性。

PDO的直接查询和预处理分别是PDO 的 query类和 prepare 类。
* PDO::prepare — 备要执行的SQL语句并返回一个 PDOStatement 对象
* PDO::query — 执行 SQL 语句，返回PDOStatement对象,可以理解为结果集
* 前者其实就是执行 sql 语句，返回一个结果集对象（PDOStatement），然后操作 PDOStatement类，从结果集中取出相应的数据。
* 后者虽然也会返回一个 PDOSTatement 对象，但区别就在于两者的处理方式不同。query 是直接执行 sql 语句，而 prepare 是通过预处理的方式执行 sql 语句（更安全，更高效）。

### 错误处理
PDO 默认开启的是错误码模式，如果发生了错误，只会简单地输出错误码，这对于调试或者测试来说，不是很友好，不利用快速定位异常所在。

所以PDO 还为我们提供了另外两种方式：

#### PDO::ERRMODE_WARNING
除设置错误码之外，PDO 还将发出一条传统的 E_WARNING 信息。如果只是想看看发生了什么问题且不中断应用程序的流程，那么此设置在调试/测试期间非常有用。

#### PDO::ERRMODE_EXCEPTION
除设置错误码之外，PDO 还将抛出一个 PDOException 异常类并设置它的属性来反射错误码和错误信息。此设置在调试期间也非常有用，因为它会有效地放大脚本中产生错误的点，从而可以非常快速地指出代码中有问题的潜在区域（记住：如果异常导致脚本终止，则事务被自动回滚）。

创建 PDO 实例并设置错误模式：
```
<?php
$dsn = 'mysql:dbname=testdb;host=127.0.0.1';
$user = 'dbuser';
$password = 'dbpass';

try {
    $dbh = new PDO($dsn, $user, $password);
    // 开启ERRMODE_EXCEPTION 模式
    $dbh->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
} catch (PDOException $e) {
    echo 'Connection failed: ' . $e->getMessage();
}

?>
```

### 参考链接：
* [PHP PDO](http://www.5idev.com/p-php_pdo.shtml)
* [PHP PDO->query类](http://www.5idev.com/p-php_pdo_query.shtml)
* [PHP Mysql 预处理语句](https://www.w3cschool.cn/php/php-mysql-prepared-statements.html)
* [PHP 多种方式连接Mysql ](https://www.w3cschool.cn/php/php-mysql-connect.html)