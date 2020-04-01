## 前言

之前在搭建 SSH 环境时，遇到了这样一个问题：
> 使用命令：`eval$(ssh-agent)`去创建一个代理进程。
但是会提示：`No Such file or directory` 。

就很纳闷，之前都用着好好的，为什么在新的环境中就不行了？

后来，了解到原来一直使用的 `eval$(ssh-agent)` ，其中的`$()` 原来在`Linux`中有特殊的意义。

所以这篇笔记专门用来了解 `eval` 和 `反引号` 以及 `$()`之间的区别。 它们的作用都是**命令替换**。

### 当时的情况
```
$ `ssh-agent`
sh.exe": SSH_AUTH_SOCK=/tmp/ssh-myYvgp1404/agent.1404;: No such file or directory

$ eval ssh-agent
SSH_AUTH_SOCK=/tmp/ssh-zIQZKN6080/agent.6080; export SSH_AUTH_SOCK;
SSH_AGENT_PID=1092; export SSH_AGENT_PID;
echo Agent pid 1092;

$ eval `ssh-agent`
Agent pid 4288

直到我输入 eval `ssh-agent` 时，似乎就对了。
```

#### 命令代换
这三种不同的方式都是`shell`脚本中的命令代换。

命令代换是指`shell`能够将一个命令的标准输出插在一个命令行中任何位置。

### eval

首先要介绍的是: `eval`

它的作用是：**重新运算求出参数的内容**。

该命令使用于那些一次扫描无法实现其功能的变量。该命令对变量进行两次扫描。

```
$ touch test.txt
$ vim test.txt      
// 写入 Hello eval

$ var="cat test.txt"
// 注意：中间没有空格，前面没有美元符号。

$ echo $var
cat test.txt
$ eval $var
Hello eval
```

### 反引号与`$()`

实例一：
```
$ DATE1=$(date)
$ DATE2=`date`
$ DATE3=`eval date`

$ echo $DATE1
2019年01月23日 21:20:36
$ echo $DATE2
2019年01月23日 21:20:36
$ echo $DATE3
2019年01月23日 21:20:36
```

实例二：
```
$ echo `echo '\\'` 
\
$ echo $(echo '\\')
\\
```

暂时没太明白这三者的实际应用场景，不过了解到了 它们之间的一些区别与联系。

#### 参考链接：
* https://kyle.io/2012/09/ssh-agent-messiness-solving-it/
* [shell脚本中命令代换：反引号、$()、eval区别](https://blog.csdn.net/Y1730008223CONG/article/details/74136055)
* [shell脚本中命令代换：反引号、$()、eval区别2](https://blog.csdn.net/if9600/article/details/74221548)