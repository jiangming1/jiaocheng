4- 准备工作

## 查看ip

```
ifconfig 
# 查看ip地址
ipaddr
# ip
vi /etc/sysconfig/network-scripts/ifcfg-xx

yum install net-tools
```

最小化安装的server版本没有ifconfig。使用ip addr

tab补全键一次补全。两次提示。

使用pageup pagedown键，翻动。

编辑我们的配置文件将onboot选项改为yes。

`:wq` w写 q退出

![mark](http://myphoto.mtianyan.cn/blog/180127/ejfdl888E6.png?imageslim)

这时我们的网卡信息依然没有显示。因此我们要重启network服务

![mark](http://myphoto.mtianyan.cn/blog/180127/AI1Fmki4E5.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180127/k1l4Fi3LKB.png?imageslim)

这个多出来的是我们的内网ip。

```
yum install net-tools
```

yum是centos中的包管理器,连续输入两次y进行安装完成。

ifconfig命令这时候就可以使用了。 ip addr是我们的备胎

- 1. 修改/etc/sysconfig/network-scripts/下的网卡ifcfg的配置文件onboot项
- 2. 将我们的network 服务重启
- 3. 安装net-tools 实现ifconfig。

 ## 替换默认源
http://mirrors.163.com/.help/centos.html

点击正常关闭。然后将网络方式换为桥接。将内网ip变成与我们物理机同
将虚拟机网络换成桥接式。然后启动

![mark](http://myphoto.mtianyan.cn/blog/180127/31c5gHg336.png?imageslim)

可以看到此时我们的ip变为了198xxx。

![mark](http://myphoto.mtianyan.cn/blog/180127/aFhkDeGkK3.png?imageslim)

xshell中输入刚才查看到的ip地址。接收并保存。输入用户名和密码。
进入xshell命令行界面，我们可以在工具选项中设置字体

通过cat 命令查看主机的版本

```
cat /etc/redhat-release
```

![mark](http://myphoto.mtianyan.cn/blog/180127/35Blfi9Agd.png?imageslim)

wegt命令是我们用来下载文件的。默认没有安装

```
yum install wget
```

复制网易的帮助文件中将原源配置文件进行备份。

```
cd /etc/yum.repos.d/
```

鼠标右键复制网页上的对应centos7的链接地址。

```
wget http://mirrors.163.com/.help/CentOS7-Base-163.repo
```

```
yum clean all
yum makecache
```

一次性复制执行两条命令

## 安装Vim

```
yum install vim
```

虚拟机重启 + 桥接。桥接方式就是在一个路由器下的虚拟主机都是一样的。

