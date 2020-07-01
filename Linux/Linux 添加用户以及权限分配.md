# 前言
写这篇笔记的目的是：在 Linux 下经常为用户的权限问题而头疼，要么是权限不足，要么是权限太大，导致结果往往不是自己想要的。

另外还有一个促使我写这篇笔记的原因就是：今天在 本地的 Ubuntu上，竟然把用户玩坏了… 为了避免这种事情在服务器上发生，还是得深入研究下这一块。

## 添加用户
在 Linux 上，添加用户有两种方式：`useradd`和`adduser`，其区别就是：
* useradd 是一个Linux 命令，它提供很多参数给用户根据自己的需要进行设置。
* adduser 则是一个perl 脚本，在使用时通过简单的人机交互界面，供用户进行个性设置。

### adduser
相比 useradd，adduser的使用要简单很多。

使用adduser 添加一个用户：
```
$ adduser boo
```
然后根据提示填写相应的内容，需要注意的是，该命令会自动的在 `/home` 目录下创建一个与用户同名的目录。

用 adduser 这个命令创建的账号是系统账号，可以用来登录到 ubuntu系统。

### useradd
useradd 命令有大量的参数供我们进行个性设置，常用参数如下：
* -d<登入目录>：指定用户登入时的启始目录，并赋予用户对该目录的的完全控制权
* -g<群组>：指定用户所属的群组；
* -G<群组>：指定用户所属的附加群组；
* -m：在 /home 目录下自动建立用户的登入目录；
* -r：建立系统帐号；
* -s<shell>：指定用户登入后所使用的shell；
* -u<uid>：指定用户的 id

使用 useradd 创建用户的一般步骤如下：
```
$ useradd -m boo -s /bin/bash
$ passwd boo
$ ls /home/
boo
```
其中要注意的有：
1. useradd 命令如果不带任何参数（useradd boo），表示只是创建一个用户，既没有 /home 目录下的同名文件夹，也没有设置密码，但是可以在 /etc/passwd 文件的最后一行看到刚才添加的用户。
2. useradd ~~这个命令创建的是普通账号，并不能用来登录系统~~。加上参数`-r`，将该用户加入到系统用户，系统用户为 id在 1000以下的用户，而普通用户则是id 在 1000以上。事实证明 无论是普通用户还是系统用户 只要密码输入正确都能登入系统。
3. 当使用参数`-m`的时候，系统会自动地在 /home 目录下建立一个与新建用户同名的用户主文件夹；如果不使用`-m`的话，那么就默认是使用`-M`参数，不创建主文件夹，即使你使用了`-d`这个参数。所以如果想要自己选择主文件夹，需要同时加上`-m`和`-d`参数。
4. 误区：很都时候刚拿到一台新的机器，会发现用户目录下只有一个当前用户的文件夹，不要误以为该系统只有你一个用户，是因为很多系统用户的主目录并不在 /home 下。

## 权限分配
### 提权
无论是使用 adduser 还是 useradd 创建的用户，都试着执行一下以下命令：
```
$ sudo apt-get install vim
```
不出意外，你肯定会得到这样一个错误：
```
[sudo] password for boo:
boo is not in the sudoers file.  This incident will be reported.
```
这个错误的意思是说该用户并不在 sudoers 文件中，那么该如何解决呢？

使用如下命令：
```
$ sudo visudo

# Members of the admin group may gain root privileges
%admin ALL=(ALL) ALL

# 找到该注释，在其下增加一行 
%yourusername ALL=(ALL) ALL
```
然后保存退出，就会发现可以使用 sudo 提权了。

### 赋予 root 权限
这里有三种方式，先来看看最简单的方式：

方式一：
```
$ sudo vim /etc/passwd

# 将用户id 改为 0
testuser1:x:0:1001::/home/testuser1:/bin/sh
```
需要注意的是：
1. 该方法适用于普通用户以及管理员用户
2. 使用 testuser1 账户登录后，直接获取的就是 root 帐号的权限。

方式二：（这里以ubuntu 系统为例）
```
$ sudo visudo # sudo vim /etc/sudoers

#  Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# 在其后面增加一行
%wheel ALL=(ALL:ALL) ALL
```
然后修改该用户，使其属于 root 组（wheel）：
```
$ usermod -g root boo
```
修改完成之后，使用boo 用户登入，执行命令：`su -`，输入 root 账户的密码，即可获得root 权限。

方式三：（这里以ubuntu 为例）
```
$ sudo visudo # sudo vim /etc/sudoers

# User privilege specification
root    ALL=(ALL:ALL) ALL
boo ALL=(ALL:ALL) ALL
```
修改完成之后，使用boo 用户登入，执行命令：`su -`，输入 root 账户的密码，即可获得root 权限。

方式二、方式三和方式一的区别就是：前者需要知道root 账户的密码，而后者可以直接以普通用户的身份或者管理员身份获取root 权限。

另外还有一个需要注意的地方就是：使用第一种方式获取 root 权限，其实也有弊端，弊端就是 ~~远程使用该用户登入时，还是需要输入 root 密码，才能验证身份成功，是的 必须输入 root 用户的密码。~~

事实证明，并非上面所述，ssh 连接时的确需要输入密码验证，但不是 root 用户的密码，之前之所以一直看到 `Permission denied, please try again.`这样的错误，只是因为 没有开启允许 root 用户远程登入的权限。如何开启，见下文扩展补充。

### 扩展补充
#### 在Ubuntu中如何修改 root 密码
默认情况下，出于安全原因，root用户帐户密码在Ubuntu Linux 中被锁定。因此，无法使用root用户登录或使用诸如`su - `之类的命令成为超级用户。

但可以借助其他方式，使用`passwd`命令来修改。因为普通用户只能更改其帐户的密码。超级用户（root）可以更改任何用户帐户的密码（包括它自己）。

使用以下命令成为 root用户：
```
$ sudo -i
$ passwd root
```
如果在sudo 命令使用不了的情况下，可以进入单用户模式，再进行修改
```
$ passwd root
```


#### 在Ubuntu中如何远程 root 登入

在Ubuntu中，默认是不能使用 root 账户登入到系统的，如果一定想要用 root账户登入，可以编辑 sshd 配置，执行如下操作：
```
$ sudo vim /etc/ssh/sshd_config

# PermitRootLogin prohibit-password
# 修改为：
# PermitRootLogin yes

# 重启sshd 服务
$ sudo systemctl restart sshd
```

#### 

### 参考链接
* [adduser 和 useradd 的区别](https://blog.csdn.net/li_101357/article/details/46778827)
* [Ubuntu 如何进入单用户模式](https://www.cnblogs.com/noway-neway/p/5235300.html)
* [ssh-如何远程以root 登入](https://raspberrypi.stackexchange.com/questions/48056/how-to-login-as-root-remotely)
* [如何在Ubuntu Linux 中更改 root 密码](https://www.cyberciti.biz/faq/change-root-password-ubuntu-linux/)
* [如何使用su / sudo成为Ubuntu Linux的超级用户？](https://www.cyberciti.biz/faq/become-superuser-on-ubuntu-linux/)
* [Ubuntu Linux root 用户默认密码](https://www.cyberciti.biz/faq/ubuntu-linux-root-password-default-password/)