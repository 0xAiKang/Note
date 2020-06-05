## 前言
如标题所示，在团队项目开发中这是两个很常见的问题，记录一下。

### 局域网内共享项目地址
有时候会有这样一种需求，自己在本地项目做开发，还没放到服务器上，但是其他人希望能在他的电脑上访问项目。

这个时候就需要这两台电脑在同一个局域网内，也就是连接相同的WiFi 。

然后查看自己的外网IP 地址是多少：
```
# mac/linux
ifconfig

# windows
ipconfig
```
外网IP 地址通常是以`192.168.x.xxx`打头的IP ，然后把这个IP 配置到对应的域名。

```
# mac/linux
vim /usr/etc/hosts

# windows
C:\Windows\ System32 \drivers\etc\hosts

# hosts 
127.0.0.1 example.com
192.168.x.xxx example.com
```
配置完成之后，直接把`example.com`这个域名丢给对方，对方就在他自己的电脑上可以访问了。

### 局域网内连接Mysql 
想要在局域网内，让别人能连接到我的数据库，需要注意以下两点：
1. 对本地Mysql 授权，允许其他用户连接
2. Mysql 开放外网访问

对于第一点，可以以下命令来完成：
```
1. mysql -hlocalhost -uroot -p;
2. use mysql;
# 修改权限，允许其他人连接：
3. update user set host='%' where user="root";
4. flush privileges;
```

通常完成第一步，就可以连接了，如果连接异常，可以尝试第二步：
```
# 打开Mysql 配置文件
vim /usr/local/etc/my.cnf

bind-address = 0.0.0.0
# 127.0.0.1 替换成 0.0.0.1;
```
然后重启Mysql 数据库即可。

> 其他人怎么连接我的数据库？

把这个丢给他：
```
host：你的外网IP 地址
user：你的Mysql 用户
pwd：你的Mysql 密码
```

