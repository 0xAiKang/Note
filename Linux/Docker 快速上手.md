# 前言
这篇笔记的主要目的是用来记录学习 `Docker` 的过程。`Docker`这个词并不是第一次听说了，印象中好久以前就听说过这个东西了，只是一直没有真正去了解。

## 诞生
软件开发最大的麻烦事之一，就是环境配置。

开发者常常说的一句话：它在我的机器上可以跑了。言下之意就是，其他机器可能跑不了。因为可以正常跑的前提是：操作系统的设置，各种软件和组件、库的安装，只有它们都正确了，软件才能正常运行。

配置环境如此麻烦，换一台机器，就得重来一次，旷日费时。因此，聪明的人们就想到，能不能从根本上解决问题。软件可以带环境安装。（这里说的软件是指最终要运行的工程）
## 虚拟机
虚拟机（virtual machine，简称VM）就是带环境安装的一种解决方案。它可以在一个操作系统中运行另外一种操作系统。比如在Windows系统中运行Linux 系统。应用程序对此毫无感觉，因为虚拟机看上去跟真是系统一模一样。而对于底层系统来说，虚拟机就是一个普通文件，不需要就删掉，对其他部分没有影响。

**虚拟机（VM）是物理硬件的抽象，** 将一台服务器转变为多台服务器。

虽然用户可以通过虚拟机还原软件的原始环境，但是这个方案有几个缺点。在后面会做比较。

## 容器
由于虚拟机存在一些缺点，Linux 发展出了另一种轻量级的操作系统虚拟化解决方案，Linux 容器（Linux Containers，缩写为 LXC）。

**Linux 容器不是模拟一个完整的操作系统，而是对进程进行隔离。**

**容器是应用层的抽象，它将代码和依赖关系打包在一起。** 多个容器可以在同一台机器上运行，并与其他容器共享操作系统内核，每个容器在用户空间中作为独立进程运行。容器占用的空间比VM少（容器映像的大小通常为几十MB），可以处理更多的应用程序，并且需要更少的VM和操作系统。

由于容器是进程级别的，相比虚拟机有很多的优势。后面会做比较。

## Docker 是什么
**Docker 属于Linux 容器的一种封装，提供简单易用的容器使用接口。** 它是目前最流行的 Linux 容器解决方案。

### Docker 与虚拟机的区别
名称 | 占用资源 | 启动速度 | 级别
---|---|---|---
Docker | 占用资源少 | 启动快 | 轻量级
虚拟机 | 占用资源多 | 启动慢 | 重量级

### Docker CE 与 Docker EE
**Docker CE(Docker Community Edition)** 是社区版，简单理解是免费使用，提供小企业与小的IT团队使用,希望从Docker开始，并尝试基于容器的应用程序部署。

**Docker EE(Docker Enterprise Edition)** 是企业版，收费。提供功能更强。适合大企业与打的IT团队。为企业开发和IT团队设计，他们在生产中构建、交付和运行业务关键应用程序

Docker CE 有三种类型的更新通道：stable、test和 nightly
* Stable 提供一般可用性的最新版本
* Test 提供在一般可用之前准备好进行测试的预发布。
* Nightly 提供下一个主要版本的最新正在进行的工作。

## 安装 Docker-CE
这里以Ubuntu 18.04 为例：
```
1. sudo apt install apt-transport-https ca-certificates software-properties-common curl-transport-https ca-certificates software-properties-common curl
2. curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add --fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
3. sudo add-apt-repository "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu \-apt-repository "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu \
$(lsb_release -cs) stable"
4. sudo apt update
5. sudo apt install docker-ce
```
将当前用户添加到docker 用户组，可以不用sudo 运行docker
```
$ sudo groupadd docker
$ sudo usermod -aG docker $USER-aG docker $USER
```

### Docker 镜像
Docker 镜像就是一个只读的模板。

例如：一个镜像可以包含一个完整的 ubuntu 操作系统环境，里面仅安装了 Apache 或用户需要的其它应用程序。

镜像可以用来创建 Docker 容器。

### Docker 容器
Docker 利用容器来运行应用。

容器是从镜像创建的运行实例。它可以被启动、开始、停止、删除。每个容器都是相互隔离的、保证安全的平台。

可以把容器看做是一个简易版的 Linux 环境（包括root用户权限、进程空间、用户空间和网络空间等）和运行在其中的应用程序。

> 注：镜像是只读的，容器在启动的时候创建一层可写层作为最上层。

### Docker 仓库
仓库是集中存放镜像文件的场所。有时候会把仓库和仓库注册服务器（Registry）混为一谈，并不严格区分。实际上，仓库注册服务器上往往存放着多个仓库，每个仓库中又包含了多个镜像（image），每个镜像有不同的标签（tag）。

仓库分为公开仓库（Public）和私有仓库（Private）两种形式。

最大的公开仓库是 Docker Hub，存放了数量庞大的镜像供用户下载。 国内的公开仓库包括 Docker Pool 等，可以提供大陆用户更稳定快速的访问。

当然，用户也可以在本地网络内创建一个私有仓库。

当用户创建了自己的镜像之后就可以使用 push 命令将它上传到公有或者私有仓库，这样下次在另外一台机器上使用这个镜像时候，只需要从仓库上 pull 下来就可以了。

> 注：Docker 仓库的概念跟 Git 类似，注册服务器可以理解为 GitHub 这样的托管服务。

#### 镜像和容器的区别

容器和镜像的关系如下：

![](https://cdn.jsdelivr.net/gh/0xAiKang/CDN/blog/images/20200723123056.png)

`Dockerfile`用于定义镜像，依赖镜像来运行容器，仓库则是存放镜像的地方。

#### Dockerfile 是什么？
Dockerfile 是一个创建Docker 镜像所需的文件，其中会包含一组指令来告诉Docker 如何构建我们的镜像。

示例：
```
$ cat Dockerfile
# 使用官方Python运行时作为父映像
FROM python:2.7-slim

# 将工作目录设置为/app
WORKDIR /app

# 将当前目录内容复制到容器at /app
COPY . /app

# 安装requirements.txt中指定的任何需要的包
RUN pip install --trusted-host pypi.python.org -r requirements.txt

# #让80 端口号对外开放
EXPOSE 80

# 定义环境变量
ENV NAME World

# 在容器启动时运行app.py
CMD ["python", "app.py"]
```

#### 如何用镜像创建一个容器？
首先，我们需要一个镜像，然后才能创建容器。想要在Docker 上创建一个镜像，非常简单。
1. cd 到项目文件夹中
2. 使用 `docker build --tag=mydockerapp .` 命令，创建一个Docker 镜像。--tag 选项命名。
3. 使用 `docker run -d -p 4000:80 mydockerapp`命令，创建一个新容器。

该命令表示：Docker 以`mydockerapp`镜像创建一个新容器，同时以分离模式在后台运行该应用程序，将该容器的80端口映射到主机的4000端口。

其中：
`-d`：让容器在后台运行
`-p`：将容器内部端口映射到指定的主机端口上。
`-P` :是容器内部端口随机映射到主机的端口上。

#### Docker 网络端口映射
使用命令：
```
$ docker run -p 4000:80 mydocker
```
然后用`docker container ls`查看容器列表

下图的意思表示：将该容器的端口80映射到4000，从而生成正确的URL http://localhost:4000。

Docker 开放了 80 端口映射到主机端口 4000 上。

![](https://cdn.jsdelivr.net/gh/0xAiKang/CDN/blog/images/20200723123121.png)

#### Docker 容器连接
前面我们实现了通过网络端口来访问运行在 docker 容器内的服务。下面我们来实现通过端口连接到一个 docker 容器

#### 如何运行负载均衡应用？
在开始之前，你得首先满足以下条件：
1. 安装Docker 1.13或更高版本。
2. 了解如何创建容器。
3. 确保已经创建镜像并发布到注册表。我们在这里需要使用该共享镜像。
4. 确保镜像作为已部署的容器运行，并能访问。

确保有`docker-compose.yml`配置文件，然后依次执行以下命令
```
$ docker swarm init
$ docker stack deploy -c docker-compose.yml getstartedlab
# 顺利的话，就能直接部署成功了。使用docker container ls 可以看到正在运行的实例。
# 使用 curl http://localhost:4000 或者是刷新浏览器。
# 无论以哪种方式，容器ID 都会发生变化。从而证明负载均衡成功。
# 对于每个请求，以循环方式选择5个任务中的一个来响应。
# 容器ID与上一个命令（docker container ls -q）的输出匹配。
```
#### 关于服务
在分布式应用程序中，应用程序的不同部分称为“服务”。例如，如果您想象一个视频共享站点，它可能包括一个用于在数据库中存储应用程序数据的服务，一个用户在上传内容后在后台进行视频转码的服务，一个用于前端的服务，等等。

服务实际上只是“生产中的容器”。服务只运行一个镜像，但它编码了镜像运行的方式 - 它应该使用哪些端口，应该运行多少个容器副本，以便服务具有所需的容量，以及等等。扩展服务会更改运行该软件的容器实例的数量，从而为流程中的服务分配更多计算资源。

在服务中运行的单个容器称为任务。任务被赋予以数字递增的唯一ID，最多为replicas您定义 的数量docker-compose.yml。

幸运的是，使用Docker平台定义，运行和扩展服务非常容易 - 只需编写一个docker-compose.yml文件即可。

#### 如何在Docker上安装 Docker Machine？
Ubuntu 18.04 请看文末的参考链接。

MacOS 如果是从[`DockerHub`官网](https://hub.docker.com/)下载的`dmg` 安装的Docker，不用担心，`Docker-Machine` 已经安装好了。

#### 如何安装VirtualBox？
Ubuntu 18.04 请看文末的参考链接。

MacOS 则需要从[`virtualbox`官网](https://www.virtualbox.org/wiki/Downloads)下载dmg安装包。

你可能会遇到一个错误，参考解决：[如何在MacOS上安装VirtualBox](http://osxdaily.com/2018/12/31/install-run-virtualbox-macos-install-kernel-fails/)

### 了解Swarm集群
群由多个节点组成，可以是物理或虚拟机。基本概念很简单：运行`docker swarm init`以启用`swarm模式`并使当前计算机成为一个`swarm管理器`。

这个章节是这个文档系列中学的时间最长的，坑有点多，走了不少弯路，这一节也挺重要的 重点记下笔记。

在MacOS 下，部分命令需要 sudo 权限。
#### 创建一个集群（本地计算机的VM）
在开始这部分之前，需要提前安装好`Oracle VirtualBox`.
```
$ docker-machine create --driver virutalbox myvm1
```
如果你收到了这样的信息：
```
$ Error with pre-create check: 
"VBoxManage not found. Make sure VirtualBox is installed and VBoxManage is in the path"
```
说明你的`Vritualbox`还是没有安装好。

查看正在运行的VM
```
$ docker-machine ls
NAME    ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
myvm1   -        virtualbox   Running   tcp://192.168.99.104:2376           v17.06.2-ce
myvm2   -        virtualbox   Running   tcp://192.168.99.105:2376           v17.06.2-ce
```
这样就成功的创建了一台VM，接下来我们要将这台机器作为管理器，第二台作为工作者。

另外值得一提的是，尽管我在Ubuntu 18.04 上分别安装好了docker-machine、virtualbox，但当我创建 VM 时，总是会提示我计算机没有开启什么虚拟化（BOIS）。

后来我大概想明白了，可能是我的那台服务器的配置太低了，真的是某个设置项没有启动导致的。

今天在MacBook 上重新操作了一边，异常顺利。

记录一个问题：使用`docker-machine create --driver virtualbox myvm1`创建VM时，创建成功了，但是并不是我想要的实例。得到了以下信息：
```
(default) Creating a new host-only adapter produced an error: hostonlyif create failed:
(default) 0%...
(default) Progress state: E_FAIL
(default) VBoxManage.exe: error: Failed to create the host-only adapter
```
找了好久也没有找到答案，最后是怎么解决的呢？重启机器（加上 sudo）。

启动\停止 VM
```
$ docker-machine start Name
$ docker-machine stop Name
```

#### 初始化Swarm 并添加节点
这里是一个小坑，之前在这里栽了好久。

这里有两种方式初始化节点或者说操作 VM（推荐第一种）：
1. ssh 连接VM 实例，在Docker VM Cli 中执行命令
```
$ docker-machine ssh myvm1
docker@myvm1: $ docker swarm init --advertise-addr <myvm1 ip>"
# <myvm1 ip> 指docker-machine ls 对应的 ip

# 正常会得到这样一个输出
To add a worker to this swarm, run the following command:
   docker swarm join --token SWMTKN-1-1j5rwl5kvffwtptdl79vw30zfgqd51hrda8xmrkmv0lnozjii4-0njs1rk0zdplj70wjk6uhmkfo 192.168.99.103:2377
```
#### 将myvm2 实例作为工作者加入（方式一）
```
$ docker-machine ssh myvm1
docker@myvm1: $ docker swarm join 
--token SWMTKN-1-1j5rwl5kvffwtptdl79vw30zfgqd51hrda8xmrkmv0lnozjii4-0njs1rk0zdplj70wjk6uhmkfo 
192.168.99.103:2377

# 成功，会得到这样的输出
This node joined a swarm as a worker.
```

2. 直接通过 `docker-machine ssh myvm1` 执行相应命令
```
$ docker-machine ssh myvm1 "docker swarm init --advertise-addr <myvm1 ip>"
# 同上
```
#### 将myvm2 实例作为工作者加入（方式二）
执行上面得到的输出：
```
$ docker-machine ssh myvm2 " docker swarm join 
--token SWMTKN-1-1j5rwl5kvffwtptdl79vw30zfgqd51hrda8xmrkmv0lnozjii4-0njs1rk0zdplj70wjk6uhmkfo 
192.168.99.103:2377"

# 成功，会得到这样的输出
This node joined a swarm as a worker.
```
这样，我们就成功的创建了一个集群，并将一个工作者作为一个节点加入了。

##### 在管理器上查看集群中的节点：
```
docker@myvm1: $ docker node ls
ID                            HOSTNAME     STATUS       AVAILABILITY        MANAGER STATUS
rihwohkh3ph38fhillhhb84sk *   myvm1        Ready        Active              Leader
brtu9urxwfd5j0zrmkubhpkbd     myvm2        Ready        Active
```
为什么上面要介绍那两种与 VM 实例进行交互的方式呢？

因为会和后面的在集群部署应用程序有一定联系。

#### 在集群中部署应用程序
在开始部署之前，我们需要了解到有两种方式可以实现。

1. docker-machine 为Swarm 管理器配置Shell

到目前为止，我们与 VM 通信都是通过 `docker-machine ssh`这种方式，另一种更好的方式就是：将当前shell配置为与VM上的Docker守护程序通信。

这样我们就可以直接本地的`docker-compose.yml`文件远程部署应用程序，而无需将其复制到其他任何位置。

```
$ docker-machine env myvm1
export DOCKER_TLS_VERIFY="1"
export DOCKER_HOST="tcp://192.168.99.100:2376"
export DOCKER_CERT_PATH="/Users/sam/.docker/machine/machines/myvm1"
export DOCKER_MACHINE_NAME="myvm1"
# Run this command to configure your shell:
# eval $(docker-machine env myvm1)

# 运行最后一行命令以配置与之通信的 shell 
$ eval $(docker-machine env myvm1) # eval $(sudodocker-machine env myvm1)
```
运行docker-machine ls 已验证 myvm1 现在是活动的计算机。带有星号（*）表示配置成功
```
$ docker-machine ls
NAME    ACTIVE   DRIVER       STATE     URL                         SWARM   DOCKER        ERRORS
myvm1   *        virtualbox   Running   tcp://192.168.99.100:2376           v17.06.2-ce
myvm2   -        virtualbox   Running   tcp://192.168.99.101:2376           v17.06.2-ce
```
部署应用程序
```
$ ls
docker-compose.yml
$ docker stack deploy -c docker-compose.yml getstartedlab
```

2. 传统方式

传统的方式就是将`docker-compose.yml`文件拷贝到对应的管理器中。
```
# 使用scp 命令将文件拷贝到 vm 实例中
$ ls
docker-compose.yml
$ docker-machine scp docker-compose.yml myvm1:~
```
部署应用程序
```
# 这里就可以随意选择使用之前介绍的方式一或者方式二

# 方式一
$ docker-machine ssh myvm1 "docker stack deploy -c docker-compose.yml getstartedlab"

# 方式二
$ docker-machine ssh myvm1
docker@myvm1: $ docker stack deploy -c docker-compose.yml
```
耐心等待一会，就可以看到看到部署成功了。

#### 访问集群
在访问集群之前，你需要知道以下两件事：
* 访问集群的IP 地址是VM 的IP，使用`docker-machine ls`查看
* 是否存在端口号，取决于你的`docker-compose.yml`文件

## Docker 常用命令
### 容器的生命周期
* 创建一个新的容器并运行：
```
$ docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
$ docker run ubuntu:15.10 /bin/echo "Hello world"
# 解释：Docker以ubuntu15.10镜像创建一个新容器，然后在容器里执行 bin/echo "Hello world"，最后输出结果。

参数
* -d：让容器在后台运行
* -p：内部容器绑定到指定的主机端口上
* -P：内部容器端口随机映射到主机端口上
* --name：给容器命名，如果不加--name 参数，Docker 会自动命名。
```
* 杀掉一个运行中的容器：
```
$ docker kill -s KILL mydocker
# mydocker 表示Contianer ID或者Name
```
* 结束停止一个运行中的容器：
```
$ docker container stop mydocker
# mydocker 表示Container ID或者Name
```
* 查看正在运行的容器：
```
$ docker ps

参数
* -l：查询最后一次创建容器记录
* --all：查询所有创建容器记录
* -aq：查询所有创建容器的Container ID
```
* 停止Web 应用容器

这个只是停止该容器的运行，并没有杀死
```
$ docker stop mydocker 
```
* 启动Web 应用容器

已经停止的容器，可以使用命令 docker start 来启动。
```
$ docker start mydocker 
```
* 移除Web 应用容器
```
$ docker rm mydocker
mydocker
# 删除容器时，容器必须是停止状态，否者会报错。
```
### 镜像操作
* 如何创建一个Docker 镜像
```
$ docker build --tag=mydockerapp # 注意：标签名只能小写
```
* 列出下载到计算机中的镜像
```
$ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
hello-world         latest              fce289e99eb9        3 months ago        1.84kB

各个选项说明:
* REPOSITORY：表示镜像的仓库源

* TAG：镜像的标签

* IMAGE ID：镜像ID

* CREATED：镜像创建时间

* SIZE：镜像大小
```
* 查找镜像
```
$ docker search nginx 
NAME     DESCRIPTION                    STARS      OFFICIAL 
nginx    Official build of Nginx.       11154        [OK]

NAME:镜像仓库源的名称

DESCRIPTION:镜像的描述

OFFICIAL:是否docker官方发布
```
* 获取一个新镜像

如果我们决定使用上图中的 nginx 官方镜像，使用如下命令：
```
$ docker pull nginx
```
### 容器操作
* 列出下载到计算机中的 container 
```
$ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

### 仓库操作
* 登入`hub.docker.com`
```
$ docker login 
# 前提是先注册号账号
```
* 标记镜像，以便上传至目标位置
```
$ docker tag mydocker aikang/get-started:part1
# 最后上传至所登入的Docker Hub仓库
```
* 将标记的镜像上传到存储库：
```
$ docker push mydocker aikang/get-started:part1
```
* 从远程存储库中拉出并运行映像
```
$ docker run -d -p 4000:80 aikang/get-started:part1
```
注意：无论在哪里执行`docker run`，它都会提取你的镜像，以及Python和所有依赖项requirements.txt，并运行你的代码。它们都在一个整洁的小包中一起旅行，你不需要在主机上安装任何东西让Docker运行它。

### 服务操作
* 群集初始化，可以使节点变成群集管理器
```
$ docker swarm init
```
* 以服务运行
```
$ docker stack deploy -c docker-compose.yml getstartedlab
Creating network getstartedlab_webnet
Creating service getstartedlab_web
# 需要有一个docker-compose.yml 文件
```
* 列出与应用程序关联的正在运行的服务
```
$ docker service ls
```
* 查看与堆栈相关的所有服务
```
$ docker stack services getstartedlab
# getstartedlab 表示服务的Names
```
* 列出服务任务
```
$ docker service ps getstartedlab
# getstartedlab 表示服务的Names
```
* 关闭服务
```
$ docker stack rm getstartedlab
# getstartedlab 表示服务的Names
```
* 查看集群中的节点
```
$ docker node ls
```
### VM 交互
* 创建一个VM 实例（Win、Mac、Linux）
```
$ docker-machine create --driver virtualbox myvm1
```
* 使用ssh 连接VM 实例
```
$ docker-machine ssh myvm1
```
* 查看关于节点的基本信息
```
$ docker-machine env myvm1
```
* 使用scp命令将本地文件copy到VM实例中
```
$ docker-machine scp <filename> myvm1:~  
# 从当前目录拷贝到实例中的根目录下
```
* 删除指定VM
```
$ docker-machine rm myvm1
```
* 将Shell 与VM 连接
```
$ eval $(docker-machine env myvm1)
```
* 将Shell 与VM 断开，使用本地连接
```
$ eval $(docker-machine env -u)
```
### 集群操作
以下操作均需要在VM CLI 中运行
* 初始化集群
```
$ docker swarm init --advertise-addr <myvm1 ip>
```
* 将节点加入集群
```
$ docker swarm join --token <token> <ip>:2377"
```
* 让工作者离开集群
```
$ docker swarm leave
```
* 强制离开并关掉集群
```
$ docker swarm leave -f
```
* 查看该节点的详情信息
```
$ docker node inspect <node ID>
```
* 部署应用程序
```
$ docker stack deploy -c <file> <app>
```

### 杂项

* 查看Docker版本：
```
$ docker version
```
* 显示Docker系统信息，包括镜像和容器数：
```
$ docker info
```
* 查看Docker 容器的配置和状态信息。
```
$ docker inspect mydocker
# 表示容器的Container ID 或者Names
```
* 查看指定容器映射到宿主机的端口号。
```
$ docker port mydocker
80/tcp -> 0.0.0.0:4000
# mydocker 表示该应用的Container ID 或者Names
```
* 查看Web 应用程序日志
```
$ docker logs -f mydocker 
 * Running on http://0.0.0.0:80/ (Press CTRL+C to quit)
113.87.130.57 - - [01/Apr/2019 12:58:34] "GET / HTTP/1.1" 200 -
113.87.130.57 - - [01/Apr/2019 12:58:35] "GET / HTTP/1.1" 200 -
# mydocker 表示该应用的Container ID 或者是Names
```
* 查看Web 应用程序容器的进程
```
$ docker top mydocker 
# 
UID        PID       PPID        C     STIME        TTY         TIME                CMD
root      22358      22323       0     20:58        ?      00:00:00            python app.py
```

## 杂项
### 容器有哪些网络模式
##### 1. None
在该模式下容器没有对外网络，本地机只有一个回路地址

##### 2. Container
在该模式下，与另一个容器共享网络

##### 3. Host
在该模式下，与主机共享网络

##### 4. Bridge
该模式为Docker 默认的网络模式，在这种模式下，Docker 容器与外部的通信都是通过 iptable 实现的。

##### 5. Overlay
该模式为Docker 目前原生的跨主机多子网模型，主要是通过 vxlan 技术实现。

## 参考链接
* [Docker 入门教程 - 阮一峰网络日志](http://www.ruanyifeng.com/blog/2018/02/docker-tutorial.html)
* [Docker 入门 - 极客学院](wiki.jikexueyuan.com/project/docker-technology-and-combat)
* [安装Docker ce - 官方文档](https://docs.docker.com/install/)
* [Docker Swarm 入门教程](http://www.ityouknow.com/docker/2018/04/19/docker-swarm.html)
* [如何在Ubuntu 18.4上安装 Docker-ce](http://www.runoob.com/docker/ubuntu-docker-install.html)
* [如何在Ubuntu 18.04上安装Docker Machine](https://docs.docker.com/machine/install-machine/)
* [如何在Ubuntu 18.04上安装VirtualBox](https://linuxize.com/post/how-to-install-virtualbox-on-ubuntu-18-04/#disqus_thread)