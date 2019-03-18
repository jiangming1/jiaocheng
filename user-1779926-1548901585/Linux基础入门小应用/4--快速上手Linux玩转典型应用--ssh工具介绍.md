5-ssh工具

## ssh是什么

![mark](http://myphoto.mtianyan.cn/blog/180127/2c9dmCDb1F.png?imageslim)

SSH：secure shell 安全外壳协议

建立在应用层基础上的安全协议

可靠, 专为远程登录会话和其他网络服务提供安全性的协议。

![mark](http://myphoto.mtianyan.cn/blog/180127/633217bCmg.png?imageslim)

客户端服务端都基本支持全平台

## 服务器安装ssh服务

- 安装ssh

```
yum install openssh-server
```

- 启动ssh

```
service shhd start
```

- 设置开机启动

```
chkconfig sshd on
```

服务器版本的操作系统默认安装了ssh服务。桌面化的操作系统一般没有安装。
如果提前不安装好我们没得连接。、

```
ps -ef |grep ssh
```

是用来查看我们进程在不在的命令。

![mark](http://myphoto.mtianyan.cn/blog/180127/EdAEfmi58I.png?imageslim)

## 客户端安装ssh客户端

windows平台下Xshell。 Linux下

```
yum install openssh-clients
```

![mark](http://myphoto.mtianyan.cn/blog/180127/BbihfKI1dj.png?imageslim)

在执行安装server端命令时已经将客户端安好了。

你的本地Linux链接你的阿里云。

## ssh客户端连接服务器

```
ssh root@192.168.0.7
```

![mark](http://myphoto.mtianyan.cn/blog/180127/jJ2Fhmk6ce.png?imageslim)

点击新建创建一个本地shell。输入上面的命令，然后会弹出窗口。

账号@ip。

![mark](http://myphoto.mtianyan.cn/blog/180127/I5Il3B15cF.png?imageslim)

可以看到我们在我们的linux虚拟机中通过ssh连接到了我们的腾讯云。
注意:密码不会显示。

mac平台命令也一模一样。

## SSHconfig用法详解

- config为了方便我们批量管理多个ssh。
- config 存放在~/.ssh/config
- config 配置语法

~表示家目录。.表示家路径。

语法关键字:

| 关键字 | 名称 |
| ------------- | ------------- |
| Host	 | 别名 |
| HostName| 主机名 |
| Port | 端口 |
| User | 用户名 |
| IdentityFile | 密钥文件的路径 |

主机名也可以是ip。ssh服务默认22端口。

```
host "mtianyan"
		HostName 192.168.0.7
		User mtianyan
		Port 22
		IdentityFile ~/.ssh/id_rsa.pub
		IdentitiesOnly yes
```

![mark](http://myphoto.mtianyan.cn/blog/180127/eFmAjmd0Df.png?imageslim)

cd进入目录之后使用touch config命令创建配置文件。

使用vim 编辑

```
host "mtianyan"
		HostName 192.168.0.7
		User mtianyan
		Port 22
```

![mark](http://myphoto.mtianyan.cn/blog/180127/HA0DBLImFh.png?imageslim)

可以看到这时我们是可以直接使用名字进行连接的。

![mark](http://myphoto.mtianyan.cn/blog/180127/dACcgjic4F.png?imageslim)




## 免密码登录方案之SSHkey

- sshkey 使用非对称加密方式生成公钥和私钥。 公钥可以传播给别人，私钥我们要自行存放。

- 私钥存放在本地~/.ssh目录

- 公钥可以对外公开, 放在服务器`~/.ssh/authorized_keys`

实现从本地到服务器的免密登录

在.ssh目录下`touch authorized_keys`

将刚才我们使用xshell生成的公钥复制进来。

三步走策略:

- 1. 在windows上使用xshell生成密钥，将公钥保存下来
- 2. 在Linux的ssh目录下创建authorized_keys文件
- 3. 将公钥内容粘贴进去。

>此时可以通过windows直接登录linux


## Linux生成sshkey

```
ssh-keygen -t rsa
ssh-keygen -t dsa
```

![mark](http://myphoto.mtianyan.cn/blog/180127/EmeCla816B.png?imageslim)

点击用户密钥管理者: 点击生成密钥

.pub文件是公钥。另一个是私钥

如何使用？

![mark](http://myphoto.mtianyan.cn/blog/180127/Ac77aB64fH.png?imageslim)

点击用户身份验证: pubkey


![mark](http://myphoto.mtianyan.cn/blog/180127/Km094BAAEB.png?imageslim)

当报错: Could not open a connection to your authentication agent.

使用 `ssh-agent bash` 之后再add。


## SSH端口安全

端口安全指的是尽量避免服务器远程连接端口被不法分子知道。为此而改变默认服务端口号的操作。

如何改变ssh服务端口

修改/etc/ssh/sshd_config配置

![mark](http://myphoto.mtianyan.cn/blog/180127/CkDB45G2Ai.png?imageslim)

设置监听两个端口

```
service sshd restart
```

将服务进行重启

1. sshagent bash打开
2. ssh-add 添加
3. ssh host登录

>妈的每次都要三步走。我还是ssh host算了。
