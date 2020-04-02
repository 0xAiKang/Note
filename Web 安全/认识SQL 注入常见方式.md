# 前言
最近需要做一个检测`SQL 注入`的功能，无奈发现自己于对`SQL注入`竟有点陌生，本着搞清楚原理才能更好的理解Bug 产生的原因，于是便有了这篇笔记。

## SQL 注入是什么？
SQL 注入是一种将SQL 语句添加到REQUEST 参数中，传递到服务器并执行的一种攻击手段。

SQL 注入攻击是REQUEST 引數未经过过滤，然后直接拼接到SQL 语句中，解析并执行，而达到预想之外的一种行为。

## SQL 注入是怎样产生的
1. WEB 开发人员无法保证所有的输入都已经完美过滤。
2. 数据库未做安全配置，存在安全隐患。

## 如何进行SQL 注入
这里以`PHP`、`Mysql`为例，介绍一下完整的SQL 注入攻击是如何产生的。

### 回显注入
```
<?php
$db_host = "localhost";
$db_user = "root";
$db_pwd = xxxxxx;
$db_name = "User";
$db_table = "Student";

echo '<h1>';
echo 'Test ErrorBased Injections';
echo '</h1>';
error_reporting(E_ALL ^ E_DEPRECATED);

// 测试连接
$conn = mysqli_connect($db_host, $db_user, $db_pwd);
if (!$conn){
    echo 'Mysql 连接失败:'.mysqli_error($conn);
}else {
    echo 'Mysql 连接成功';
}
echo '<hr>';

//连接数据库
mysqli_select_db($conn, $db_name) or die ("无法连接到数据库: ".$db_name);
mysqli_query($conn, 'set names utf-8');

// 获取参数
if(isset($_GET['id'])){
    $id=$_GET['id'];
}

// 拼接SQL语句
$sql= "SELECT * FROM $db_table WHERE id = {$id} ";
echo '查询SQL 语句:'.$sql;
echo '<hr>';

//执行
$result=mysqli_query($conn, $sql);
$row=mysqli_fetch_array($result, MYSQLI_BOTH);
if($row) {
  echo 'Your Login name:'.$row['username'];
  echo '<hr>';
  echo 'Your Password:'.$row['password'];
}
?>
```
调用地址是`http://127.0.0.1/sqli.php?id=1`，使用`GET`传入参数`id`，输出的SQL 语句如下：
```
SELECT * FROM Student WHERE id = '1' 
```
正常情况下，会返回`id = 1` 的学生信息。

#### 1. 数字注入
如果在浏览器中输入：`http://127.0.0./sqli.php?id=1' union select 1,2--+`会怎样呢？输出的SQL 语句如下：

```
SELECT * FROM Student WHERE id = -1 or 1=1 
```
这会导致所有的学生信息都被输出了，为什么会这样呢？这是因为`id = -1`是一个不存在的条件，而`1 = 1`却是一个永远存在的条件，这就相当于没有加 Where 条件。

#### 2. 字符串注入
现在有这样一种场景：`http://127.0.0./login.php`模拟用户登录。假设正确的用户名和密码是`Boo`、`122410`，那么在正常的登录情况下所执行的SQL 语句如下：
```
SELECT * FROM Student WHERE username = 'Boo' ADN password = '122410'
```
由于用户名和密码都是字符串，所以SQL 注入会把参数携带的数据变成`Mysql`中的注释。Mysql 中的注释有两种。

#### 1. `#`
假设`POST` 传递的参数分别是：`username = Boo'#`、`password = xxxxxx`，那么产生的SQL 语句则是：

```
SELECT * FROM Student WHERE username = 'Boo'#'ADN password = 'xxxxxx'
```
因为`#`号 后的所有字符串都会被当成注释来处理，所以上面的SQL 语句等价于：
```
SELECT * FROM Student WHERE username = 'Boo'
```

#### 2. `--`
假设`POST`请求传递的参数分别是：`username = Boo'--`、`password = xxxxxx`，那么产生的SQL 语句则是：

```
SELECT * FROM Student WHERE username = 'Boo'-- 'AND password = 'xxxxxx'
```
因为`--`号 后面的所有内容都会被当成注释处理，所以上面的SQL 语句等价于：

```
SELECT * FROM Student WHERE username = 'Boo'
```
无论是上面的哪一种情况，攻击者都能在不知道具体密码的情况下而成功登录。

这大概就是一个简单的SQL注入产生的完整过程了，这里只是抛砖引玉的介绍了下原理，而实际场景中的SQL 注入当然远远不止这两种。

### 参考链接
[SQL 注入常见方式以及检测方法](https://blog.csdn.net/github_36032947/article/details/78442189)