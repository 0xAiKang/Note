## 前言

### 生成并部署SSH KEY
1. 无论在一个电脑上添加几个Git账号，最终的目的都是与远程库进行交互。所以配置的第一步需要做的就是生成多个SSH密钥。
2. 打开**Git Bash**，输入下面的命令，生成**SSH KEY** .
*            ssh-keygen -t rsa -C "youremail"    # 这个命令是将ssh key生成在默认的文件下。也就是userss/pc/.ssh/id_rsa下。
      ssh -T url   # 查看是否部署成功 urleg：git@bitbucket.org or urleg: git@39.108.138.45
url 这里填你所使用的平台的地址。（如``GitLab:39.108.138.45``、``BitBucket:bitbucket.org``）           
### 一台电脑下生成多个Git账号：
1. 因为需要在一台电脑上允许登录多个Git 账号。所以存放SSH密钥的文件就不能重复了，否则前者的密钥就会被后者覆盖。
2. 所以我们要做的第一步仍然是生成``ssh key``，只是生成其他ssh key的命令和上面那个有点区别：
*            ssh-keygen -t rsa  -f ~/.ssh/id_rsa_bitbucket -C "youremail" # 这里的id_rsa_bitbucket 只是存储你新的ssh key的文件，
所以后缀你自己改正什么样子都行。
3. 然后将生成的公钥填到你的平台的``SSH Key``上。不知道怎么添加``ssh key``？[点击这里](https://confluence.atlassian.com/bitbucket/set-up-an-ssh-key-728138079.html#SetupanSSHkey-#installpublickeyStep2.AddthepublickeytoyourBitbucketsettings)

### 将SSH密钥添加到SSH Agent 
> 为什么要这么做？
*  因为Git默认只读取``~/.ssh/id_rsa``中的``ssh``，所以为了让Git识别新的``ssh``，需要将其添加到``SSH Agent`` 中。
*            eval $(ssh-agent) # 与代理建立联系。Mac OS/ Linux： eval `ssh-agent -s`
      ssh-add ~/.ssh/id_rsa_bitbucket # 将SSH密钥添加到Agent列表中。
      ssh-add -l  # 查看SSH密钥是否加载。

命令执行的正常顺序就是上面那个。
### 创建并配置Config文件（这一步不知道是否必须）
> 在.ssh目录下创建一个config文本文件,添加相关配置。

```
# 配置github.com
Host github.com
	IdentityFile ~/.ssh/id_rsa_github
	HostName github.com
	PreferredAuthentications publickey
	User Holiday
	
# 配置git.bitbucket.org
Host git.bitbucket.org
	HostName bitbucket.org
	IdentityFile ~/.ssh/id_rsa_bitbucket
	PreferredAuthentications publickey
	User Max
# 配置gitlab
Host 39.108.138.45:8888
	HostName 39.108.138.45:8888
	IdenttityFile C:\\Users\\pc\\.ssh\\id_rsa
	PreferredAuthentications publickey
	User aikang
```
如果验证是否部署``SSHKey``成功呢？
在终端输入：
*               ssh -T url   #url eg: ssh -T git@39.108.138.45 or ssh -T git@bitbucket.org

### 用户名和邮箱配置
> 因为在一台电脑中配置多个Git账号，所以就不能还采用全局变量的用户名和邮箱了。而是在不同的仓库下，需要链接不同的Git账号，配置相应的用户名和邮箱就好了。
> 如果之前配置过全局用户名和邮箱，需要取消配置：
*               git config --global --unset user.name # 重置之前的config配置
      git config --global --unset user.email
> 现在配置局部变量，直接去相应的仓库下，建立属于这个仓库的用户名和邮箱就好了。
*               git config "user.name"
      git confug "user.email"
> 什么？你还没搞懂为什么要为各个项目单独配置用户名和邮箱？

* 因为为了保证日志的准确性和提交时的无误。所以需要配置多个用户名和邮箱。

## 使用多账号配置，后期可能会遇到的问题。
 1. 在本地每创建一个``git``仓库，就需要重新配置一次用户名和邮箱。
 2. 可能会遇到一种情况，
    * 在``GitLab``上，需要重新配置用户名和邮箱，需要重新添加一个``ssh公钥``在对应的终端。使用``fetch``、``push``、``pull``、``clone``、时都会提示输入密码（但是输入正确的密码也没用，提示权限被拒绝。），这时就需要添加``SSH Agent``作用是告诉``GitLab``，本地``SSH``公钥的路径在哪里。然后添加``SSHKey``地址，这个时候输入``ssh - T url``是可以看到``Welcome to Git! your name ``这样的提示，就表示你以后就能自由的推送拉取了。
    ![image](https://timgsa.baidu.com/timg?image&quality=80%20&size=b10000_10000&sec=1524754219871&di=90b27593f386c6050b99e4cf691e8462&imgtype=jpg&src=http%3A%2F%2Fb.hiphotos.baidu.com%2Fimage%2Fpic%2Fitem%2F42a98226cffc1e175365a56c4690f603728de991.jpg)
    * 在``GitHub``上，需要重新配置用户名和邮箱，需要重新添加一个``ssh公钥``在对应的终端。使用``fetch``、``push``、``pull``、``clone``、时都会提示输入密码（但是输入正确的密码也没用，提示权限被拒绝。），这时就需要添加``SSH Agent``作用是告诉``GitLab``，本地``SSH``公钥的路径在哪里。然后添加``SSHKey``地址，这个时候输入``ssh - T url``是可以看到``Welcome to Git! your name ``这样的提示，就表示你以后就能自由的推送拉取了。
    * 在``Bitbucket``上，也需要上面这些步骤。

### 一劳永逸的配置方式
> 上面那个通过`config`文件获取配置信息的方式，在我的电脑上始终没成功过，而我一直用的是通过`ssh agent`将解密的私钥缓存起来的方式。这种方式并不可取，实属下策。每次关闭窗口都需要重新输入命令。

下面介绍的配置可以做到自动运行`ssh agent`:  
```
$ echo 'eval$(ssh-agent)' >> /.bash_profile
```
然后打开 ~/.bash_profile这个文件，添加一行：
```
sss-add
```
