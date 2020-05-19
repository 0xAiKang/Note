## 前言
这篇文章浅谈一下 Linux 的管道符、重定向和环境变量。

### 输入输出重定向
在了解什么是输入输出重定向之前，我们先要搞清楚以下两种输出信息的区别：
* 标准输出信息：
包括该文件的一些相关权限、所有者、所属组、文件大小及修改时间等信息。
* 错误输出信息：
Bash终端显示的报错提示信息
```
[max@localhost 桌面]$ ls
testdir   test.txt
[max@localhost 桌面]$ cat test.txt
Hello Linux!                        # 标准输出信息
[max@localhost 桌面]$ cat xxx
cat: xxx: 没有那个文件或目录        # 错误输出信息 因为不存在xxx文件
```
* 标准输入重定向（STDIN，文件描述符为0）：默认从键盘输入，也可从其他文件或命令中输入。
* 标准输出重定向（STDOUT，文件描述符为1）：默认输出到屏幕。
* 错误输出重定向（STDERR，文件描述符为2）：默认输出到屏幕。

之所以花这么大力气，理解这个概念，是因为待会有个很重要的知识点要用到这个概念。

### 输出重定向

```
命令 > 文件	        将标准输出重定向到一个文件中（清空原有文件的数据）
命令 2> 文件	    将错误输出重定向到一个文件中（清空原有文件的数据）
命令 >> 文件	    将标准输出重定向到一个文件中（追加到原有内容的后面）
命令 2>> 文件   	将错误输出重定向到一个文件中（追加到原有内容的后面）
命令 >> 文件 2>&1 或
命令 &>> 文件	将标准输出与错误输出共同写入到文件中（追加到原有内容的后面）
```
实例：
```
[max@localhost 桌面]$ cat test.txt
Hello Linux!
[max@localhost 桌面]$ echo "测试输出重定向(追加模式)" >> test.txt
[max@localhost 桌面]$ cat test.txt 
Hello Linux!
测试输出重定向(追加模式)

[max@localhost 桌面]$ echo "测试输出重定向(清除模式)" > test.txt
[max@localhost 桌面]$ cat test.txt
测试输出重定向(清除模式)
```

### 输入重定向

```
命令 < 文件	将文件作为命令的标准输入
命令 << 分界符	从标准输入中读入，直到遇见分界符才停止
命令 < 文件1 > 文件2	将文件1作为命令的标准输入并将标准输出到文件2
```
输入重定向相对于输出重定向较使用的少一些，可以理解为：**输入重定向的作用是把文件直接导入到命令中**。
例子：
```
# 将文件text.txt导入给 `wc -l`命令，统计行数。
[max@localhost 桌面]$ wc -l < test.txt
1
```

### 管道符
管道符的概念就是：把前一个命令原本要输出到屏幕的标准正常数据当作是后一个命令的标准输入。

举个例子，把`etc`目录下的所有文件的属性信息，作为标准输入传递给 `more`命令。
```
[max@localhost 桌面]$ ls -l /etc/ | more
总用量 1396
drwxr-xr-x.  3 root root       97 8月  24 04:35 abrt
-rw-r--r--.  1 root root       16 8月  24 04:43 adjtime
-rw-r--r--.  1 root root    21929 1月  29 2014 brltty.conf
drwxr-xr-x.  2 root root        6 1月  29 2014 chkconfig.d
-rw-r--r--.  1 root root     1157 2月   6 2014 chrony.conf
--More--
```

### 命令行中的通配符
* 星号（*）代表匹配零个或多个字符
* 问号（?）代表匹配单个字符
* 中括号内加上数字[0-9]代表匹配0～9之间的单个数字的字符
* 而中括号内加上字母[abc]则是代表匹配a、b、c三个字符中的任意一个字符
```
[max@localhost test]$ ls
file1  file2  file3  file99  filex
[max@localhost test]$ ls -l file?
-rw-rw-r--. 1 max max 0 10月 10 22:49 file1
-rw-rw-r--. 1 max max 0 10月 10 22:49 file2
-rw-rw-r--. 1 max max 0 10月 10 22:49 file3
-rw-rw-r--. 1 max max 0 10月 10 22:49 filex
[max@localhost test]$ ls -l file*
-rw-rw-r--. 1 max max 0 10月 10 22:49 file1
-rw-rw-r--. 1 max max 0 10月 10 22:49 file2
-rw-rw-r--. 1 max max 0 10月 10 22:49 file3
-rw-rw-r--. 1 max max 0 10月 10 22:49 file99
-rw-rw-r--. 1 max max 0 10月 10 22:49 filex
[max@localhost test]$ ls -l file[1-2]
-rw-rw-r--. 1 max max 0 10月 10 22:49 file1
-rw-rw-r--. 1 max max 0 10月 10 22:49 file2
[max@localhost test]$ ls -l file[x]
-rw-rw-r--. 1 max max 0 10月 10 22:49 filex
```
### 常用的转义字符
> 反斜杠（\）：使反斜杠后面的一个变量变为单纯的字符串。
>
> 单引号（''）：转义其中所有的变量为单纯的字符串。
>
> 双引号（""）：保留其中的变量属性，不进行转义处理。
>
> 反引号（``）：把其中的命令执行后返回结果。

```
[max@localhost test]$ PRICE=5
[max@localhost test]$ echo "The price of this shirt is $PRICE"
The price of this shirt is 5
```
上面的输出看上去挺对的，但是并不完美，我们希望能够输出“The price of this shirt is $5”，于是我们试着这样写：
```
[max@localhost test]$ echo "The price of this shirt is $$PRICE"
The price of this shirt is 9944PRICE
```
不幸的是美元符号和变量提取符号合并后`$$`作用是显示当前程序的进程ID。

要想让第一个`$`乖乖地作为美元符号，那么就需要使用反斜杠`\`来进行转义，将这个命令提取符转义成单纯的文本，去除其特殊功能。
```
[max@localhost test]$ echo "The price of this shirt is \$$PRICE"
The price of this shirt is $5
```

如果只需要某个命令的输出值时，可以像`命令`这样，将命令用反引号括起来，达到预期的效果.
```
[max@localhost test]$ echo `uname -a` >> file1
[max@localhost test]$ cat file1
Linux localhost.localdomain 3.10.0-123.el7.x86_64 #1 SMP Mon May 5 11:16:57 EDT 2014 x86_64 x86_64 x86_64 GNU/Linux
```

> 思考：如何将普通变量转换为全局变量？

使用命令：`export [变量名称]`，需要在拥有管理员权限时才能正常使用。

```
[root@localhost home]# WORKDIR=/home/workdir
[root@localhost home]# mkdir $WORKDIR 
[root@localhost home]# cd $WORKDIR
[root@localhost workdir]# pwd
/home/workdir
[root@localhost workdir]# exit
exit
[max@localhost home]$ cd $WORKDIR
[max@localhost ~]$ echo $WORKDIR
[max@localhost ~]$ su root
密码：
[root@localhost max]# export WORKDIR
[root@localhost /]# su max
[max@localhost /]$ cd $WORKDIR
[max@localhost workdir]$ pwd
/home/workdir
```

#### 重点一：
在上面的命令中有一个很重要的知识点：
> 关于如何在Linux中创建一个变量的问题？有两个地方需要注意。
1. 所有字母都需要大写
2. 变量与赋值符号(=)之间不能存在空格
3. 无论是系统环境变量还是自定义变量还是全局变量，在调用时 都需要使用`$`符号来标识。

#### 重点二
* 在Linux 系统中当普通用户身份时命令提示符的前缀标识是：`$`。
* 在Linux 系统中当为管理员身份时命令提示符的前缀标识是：`#`。