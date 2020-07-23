## 前言
这篇笔记用来收录那些常用的`sshd_config`配置项。

### 保持链接
保持客户端与服务端之间的连接保持活动状态似乎是最常见策略。

* `ServerAliveInterval`：客户端在向服务器发送空数据包之前（等待连接保持活动状态）将等待的秒数。
* `ClientAliveInterval`：服务器在向客户端发送空数据包之前（等待连接保持活动状态）将等待的秒数。

设置为0（默认值）将禁用这些功能，因此如果空闲时间太长，连接可能会断开。

```
Host myhostshortcut
     HostName myhost.com
     User barthelemy
     ServerAliveInterval 60
     ServerAliveCountMax 10
```

这么设置的作用是：客户端将等待空闲60秒钟（`ServerAliveInterval`时间），然后向服务器发送 ` no-op null `数据包，并期待响应。

如果没有响应，则它将继续尝试上述过程直到10次（ServerAliveCountMax 次数 10 * 60 = 600秒）。如果服务器仍然没有响应，则客户端将断开ssh连接。

### 参考链接
* [如何让ssh客户端与服务端保持连接](https://unix.stackexchange.com/questions/3026/what-options-serveraliveinterval-and-clientaliveinterval-in-sshd-config-exac)
* [sshd_config 参考手册](https://linux.die.net/man/5/sshd_config)
