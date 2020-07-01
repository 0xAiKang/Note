# 前言
在Mysql 中删除数据以及数据表非常的容易，但是需要特别小心，因为一旦删除所有数据都会消失。

## 删除数据
删除表内数据，使用`delete`关键字。

### 删除指定条件的数据
删除用户表内id 为1 的用户：
```
delete from User where id = 1;
```

### 删除表内所有数据
删除表中的全部数据，表结构不变。

对于 MyISAM 会立刻释放磁盘空间，InnoDB 不会释放磁盘空间。
```
delete from User;
```

释放磁盘空间
```
optimize table User; 
```

### 删除数据表
删除数据表分为两种方式：
1. 删除数据表内数据以及表结构
2. 只删除表内数据，保留表结构

#### drop
使用`drop`关键词会删除整张表，啥都没有了。
```
drop table User;
```

#### truncate
`truncate` 关键字则只删除表内数据，会保留表结构。

```
truncate table User;
```

> 思考题：如何批量删除前缀相同的表？

想要实现 `drop table like 'wp_%'`，没有直接可用的命令，不过可以通过Mysql 的语法来拼接。

```
-- 删除”wp_”开头的表：
SELECT CONCAT( 'drop table ', table_name, ';' ) AS statement
FROM information_schema.tables
WHERE table_schema = 'database_name' AND table_name LIKE 'wp_%';
```
其中`database_name`换成数据库的名称，`wp_`换成需要批量删除的表前缀。

> 注意只有`drop`命令才能这样用：

```
drop table if exists tablename`;
```

> `truncate`只能这样使用：

```
truncate table `tp_trade`.`setids`;
```

### 总结
- 当你不再需要该表时， 用`drop`;

- 当你仍要保留该表，但要删除所有记录时， 用`truncate`;

- 当你要删除部分记录时， 用`delete`。