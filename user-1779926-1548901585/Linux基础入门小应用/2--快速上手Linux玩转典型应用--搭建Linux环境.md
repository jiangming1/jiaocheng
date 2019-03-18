3- 搭建Linux环境

## 虚拟机是什么？

个人电脑与服务器的最大区别是没有公网ip。

![mark](http://myphoto.mtianyan.cn/blog/180127/FKBDGF4Lhj.png?imageslim)

virtualBox 和 VMwarestation

virtualBox  & xshell 下载安装

注意: 电脑支持虚拟化 ，BIos中开启虚拟化

virtualBox 一直下一步下一步。信任。

xshell 点击下一步下一步。

## 虚拟机中安装操作系统

http://mirrors.163.com/centos/7/isos/x86_64/CentOS-7-x86_64-Minimal-1708.iso

- 点击新建

![mark](http://myphoto.mtianyan.cn/blog/180127/GDfB57gLdB.png?imageslim)

>类型Linux ，版本Redhat

- 内存分配1-2g

一直默认点击下一步: 将8改为30g硬盘 

- 点击启动:

然后选择我们下载好的ios镜像，点击启动。然后直接回车

服务器一般使用英文版本，我们也保持一致。

![mark](http://myphoto.mtianyan.cn/blog/180127/ICCcIfCE1H.png?imageslim)

进入上图界面，点击安装地点选择进入界面之后点击done。

点击rootpassword设置密码。然后静静等待安装完成。

安装完成会有一个重启按钮。点击重启后按enter键

输入root+自己设置的密码可以进入虚拟机命令行

## 云服务器的平台

域名解析到公网ip

- 阿里云
- 腾讯云
- 网易蜂巢

产品 ： 云服务器 & 云虚拟主机

一个服务器可以分成许多个虚拟主机。一般一个虚拟主机只能运行一种环境。

个人服务器推荐配置： 1G内存1G带宽。

公网ip






