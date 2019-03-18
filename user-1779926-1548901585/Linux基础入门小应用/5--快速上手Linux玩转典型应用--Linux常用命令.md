6- Linux常用命令

## 软件操作命令

| 执行操作 | 命令 |
| ------------- | ------------- |
| 软件包管理器 | yum |
| 安装软件 | yum install xxx |
| 卸载软件 | yum remove xxx |
| 搜索软件 | yum search xxx |
| 清除缓存 | yum clean packages |
| 列出已安装 | yum list |
| 软件包信息 | yum info xxx |

## 服务器硬件资源和磁盘操作

| 查询对象 | 命令 |
| ------------- | ------------- |
| 内存 | free -m(MB) |
| 硬盘| df -h(人类看懂) |
| 负载 | w/top |
| cpu个数和核数 | /proc/cpuinfo |

![mark](http://myphoto.mtianyan.cn/blog/180127/824E9FBedl.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180127/gKkjgIA47l.png?imageslim)

最近一分钟 & 五分钟 & 十五分钟。健康指标。 

变为1已经是满负载。0.6,0.7标准。

![mark](http://myphoto.mtianyan.cn/blog/180127/dL60AhJcfj.png?imageslim)

```
cat /proc/cpuinfo
```

fdisk 格式化磁盘。

## 文件和文件夹操作命令

Linux全部都是文件。

### Linux文件的目录结构

- 根目录`/`
- 家目录`/home` 下面会有好几个小用户。
- 临时目录 `/tmp`
- 配置文件 `/etc`
- 用户程序目录 `/usr`

![mark](http://myphoto.mtianyan.cn/blog/180127/6EII9h0dkH.png?imageslim)

>家目录下的各种目录。

没有生成用户，所以现在home目录下啥都没有。

配置文件/etc/yun.repos.d/ 可以查看到我们配置过的163仓库。

临时目录我们尽量不要放东西，系统会定时清理该目录

### 文件基本操作

| 命令 | 解释 |
| ------------- | ------------- |
| ls | 查看目录下的文件 |
| touch | 新建文件 |
| mkdir | 新建文件夹 |
| cd | 进入目录 |
| rm | 删除文件和目录|
| cp | 复制|
| mv | 移动 |
| pwd | 显示路径 |

>centos中 ll 也会以列表方式展示、 ls -al

新建文件 touch mtianyan
新建文件夹 mkdir 

d表示是一个文件夹

```
mkdir -p mtianyan/mtianyanSon
```

删除文件； rm mtianyan
删除文件夹； rm -r mtianyan (r代表循环)

不会让你一个劲yes决定的删除

```
rm -rf mtianyan
cp ./mtianyan ~/mtianyan2
mv ./mtianyan ~/mtianyan2
pwd
```
### 文本编辑神器vim

服务器上没有类windows的操作界面 所有的GUi界面都无法使用

Vim是最出名的Linux文本编辑器。可以对文件修改操作

Vim 安装: `yum install vim`

菜鸟教程vim

shift +G 行尾 gg 行首

dd 删除一行 u 撤销删除
yy 复制     p粘贴 

esc 模式。 :wq 写入退出。

### 文件权限421 默认权限777

![mark](http://myphoto.mtianyan.cn/blog/180128/7JD30j26Km.png?imageslim)

r 读 w 写 x 可执行

![mark](http://myphoto.mtianyan.cn/blog/180128/BKe1GikC3a.png?imageslim)

-rw-r--r--: 644权限的文件

### 文件搜索，查找，读取

| 命令 | 解释 |
| ------------- | ------------- |
| tail | 从文件尾部开始读 |
| head | 从文件头部开始读 |
| cat | 读取整个文件 |
| more | 分页读取 |
| less | 可控分页 |
| grep | 搜索关键字 |
| find | 查找文件 |
| wc | 统计个数 |


当文件有1两个g不可以使用cat读全部

```
grep "222" mtianyan2
grep -n "222" mtianyan2
grep -n 222 mtianyan2
```

大文件搜索关键字。-n显示行数。

```
cat mtianyan2 | wc -l
```

显示文件一共多少行。

`:set number` 显示行数。

`|` 管道

`grep "111" mtianyan | wc -l`

查找文件命令:

```
find . # 列出当前目录所有文件
find . -name "*ooc" 
find . -type f //f代表文件
find . -type d //d代表文件夹
find . -ctime -20 //20天之内修改过的文件
```

### 文件压缩 & 解压

tar命令

man tar 查询命令的使用方法

```
tar -cf mtianyantar.tar mtianyan mtianyan2
```

>将mtianyan 和mtianyan2压缩至 mtianyantar.tar

```
tar -tf mtianyantar.tar
```
```
tar -tvf mtianyantar.tar
```

查看文件夹里面有什么东西。v参数显示详情。

```
tar -xf mtianyantar.tar
```

x表示抽取。r大多表示循环删除。

```
tar -czvf mtianyangz.tar.gz mtianyan2 mtianyan
```

`c*f`表示压缩。z表示gz模式。v表示显示详情

```
tar -tzvf mtianyangz.tar.gz
```

```
tar -xzvf mtianyangz.tar.gz
```

## 系统用户操作命令

| 命令 | 解释 |
| ------------- | ------------- |
| useradd | 添加用户 |
| adduser | 添加用户 |
| userdel | 删除用户 |
| passwd | 设置密码 |

ubuntu下 useradd 和 adduser区别较大。

![mark](http://myphoto.mtianyan.cn/blog/180128/7hH4m07787.png?imageslim)

可以看到添加用户后home目录多了个人文件夹

这时候它是没有密码的、所以我们要为它设置密码

```
passwd mtianyan
```

```
userdel -r mtianyan
```

将其相关都删掉。

## 防火墙相关设置

作用: 保护服务器安全
设置防火墙规则
 	- 开放80, 22端口
关闭防火墙

线上服务器必须使用防火墙。443 https端口。

安装 & 启动 & 检查状态 & 关闭或禁用

```
yum install firewalld
service firewalld start
service firewalld status
service firewalld stop/disable
```

```
yum list | grep firewall
```

查看防火墙包是否安装过了？

```
ps -ef | grep firewall
```

查看防火墙是否在运行


```
firewall-cmd 
```

上面命令可以让我们对于服务器防火墙进行操作

```
firewall-cmd --version

firewall-cmd --state

firewall-cmd --get-zones

firewall-cmd --get-default-zone

firewall-cmd --list-all-zones
```
![mark](http://myphoto.mtianyan.cn/blog/180128/HkBHcFHELF.png?imageslim)


![mark](http://myphoto.mtianyan.cn/blog/180128/a5mem81DKG.png?imageslim)

>列出所有区域信息

查询服务是否通行

```
firewall-cmd --query-service=ssh
firewall-cmd --remove-service=ssh
firewall-cmd --add-service=ssh
firewall-cmd --list-service
```
![mark](http://myphoto.mtianyan.cn/blog/180128/hH6eGCFJA7.png?imageslim)


以端口号为标志查询

```
firewall-cmd --query-port=22/tcp
firewall-cmd --add-port=22/tcp
```

服务进行了remove。但是端口放开还是能继续ssh的。服务可能有很多个端口，所以我们一般禁用服务。 服务和端口只要开启一个就能工作

如果本地你觉得很麻烦。直接先把防火墙服务关闭了

```
sudo service firewalld stop
```

## 提权操作sudo 和文件传输操作

提取: sudo
	- visudo 将用户加入可提权

文件下载:
 	wget curl

文件上传
	scp

提示:

```
Loaded plugins: fastestmirror
You need to be root to perform this command.
```

使用普通用户登录之后，安装软件会要求使用root执行。

sudo 为其提权。

```
[sudo] password for mtianyan:
mtianyan is not in the sudoers file.  This incident will be reported.
```

可以看到mtianyan并不在提权者名单中。我们得返回root用户添加其为提权。

使用`visudo` 命令,然后G到最后一行

![mark](http://myphoto.mtianyan.cn/blog/180128/70518If637.png?imageslim)

添加mtianyan为提权用户

线上使用普通权限账号。

```
wget https://www.baidu.com
curl -o baidu.html http://www.baidu.com
```

可以指定文件的文件名。

```
scp mtianyan.txt mtianyan@192.168.0.7:/tmp/
```

将本地文件上传到指定服务器用户的指定目录。

这个也是通过ssh协议来运输的。

```
scp mtianyan@192.168.0.7:/tmp/mtianyan.txt mtianyan.txt
```

下载下来同样使用命令scp 可以指定路径

```
scp mtianyan@192.168.0.7:/tmp/mtianyan.txt ./mtianyan2.txt
```

xshell文件传输

```
yum install lrzsz
```

然后在xshell中可以直接使用rz命令将文件上传到当前目录。

```
sz index.html
```

sz命令下载文件。

