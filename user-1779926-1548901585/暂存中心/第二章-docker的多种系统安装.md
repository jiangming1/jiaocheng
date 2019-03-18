社区版 和 企业版的选择。

社区版又有不同的版本号。

1.x直到1.13 变成年份+月份的规划。

17.09版本，一个季度发布一个版本。

docker支持mac windows win10

云平台，aws Microsoft Azure Google也是支持的。

企业版阿里云安装后面介绍。

Linux的发行版都支持安装。

分各种情况，各种平台，各种操作系统。

安装好docker的Linux系统。安装虚拟化系统，云上服务器。

virtualbox vigrant 拥有一台安装好docker的机器。

## docker for mac

stable 稳定版 推荐下载。

https://docs.docker.com/

dmg的文件，点击这个文件就可以开始我们的安装。拖进APPlication

打开launch，点击docker。输入用户名密码。几秒钟之后启动docker。

等待变成running。打开终端。

```
docker --version
```

打印出版本号。

```
docker version
```

打印出server和client的版本号。

![image.png](https://upload-images.jianshu.io/upload_images/1779926-9b2c7f554a5a192a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

安装包形式，安装完了之后提供了很多工具。docker-compose和machine kit图形化界面来运行管理docker。

安装kit，图形化界面创建container

## docker在windows10 pro上安装

docker是一个Linux的应用，mac上面安装docker，unix系统。

win下安装Linux，必须有一些条件。

- win10 或者 win server 2016
- 64位win10 有 hyper-v 技术 win10 pro

下一步下一步。桌面创建一个快捷方式。双击快捷方式。

重启笔记本，就可以使用docker

```
docker version
```

验证docker是否安装成功。

## win中虚拟机安装docker

vagrant干啥。

演示展示方便之处。虚拟化工具 virtualbox。手工创建虚拟机，可行但是繁琐。

使用vagrant 可以方便的创建虚拟机。

>前提是virtual box

安装好vagrant

```
vagrant version
```
通过vagrant 创建虚拟机。

```
vagrant init centos/7
```

安装vagrant，然后命令行创建。

![mark](http://myphoto.mtianyan.cn/blog/180521/1GFeJFKmIj.png?imageslim)

```
vagrant init centos/7
```

使用vagrant up进行系统安装

```
vagrant up
```

使用vagrant ssh进入这台虚拟机。

之后就可以运行linux的命令了。

```
vagrant ssh
```

报错:

```
==> default: Adding box 'centos/7' (v1804.02) for provider: virtualbox
    default: Downloading: https://vagrantcloud.com/centos/boxes/7/versions/1804.02/providers/virtualbox.box
    default: Download redirected to host: cloud.centos.org
    default:
An error occurred while downloading the remote file. The error
message, if any, is reproduced below. Please fix this error and try
again.

The requested URL returned error: 404 Not Found
```

因为没有更新box的地址

```
vagrant init geerlingguy/centos7
```

然后重新运行up命令即可。


这样下载速度还是太慢了。

前往 https://cloud.centos.org/centos/7/vagrant/x86_64/images/

下载对应的VirtualBox.box文件。

然后使用命令

```
vagrant box add centos/7 CentOS-7-x86_64-Vagrant-1804_02.VirtualBox.box
```

然后重新init

## centos上创建linux机器。

virtualbox 为什么没有使用VMware产品。不免费。

vagrant使用VMware也是收费的。为了免费选择了vagrant+virtual box

使用也是很简单的

```
vagrant up --provider=vmware_fusion
```

docker-ce中centos

https://docs.docker.com/install/linux/docker-ce/centos/#prerequisites

移除旧版本的docker

```
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
```

安装必要的仓库与依赖

```
sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

```
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

开始安装

```
sudo yum install docker-ce
```

输入两次y之后即可成功。

启动docker

```
sudo systemctl start docker
```

查看docker的版本信息

```
sudo docker version
```

```
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.37/version: dial unix /var/run/docker.sock: connect: permission denied
```

不加sudo就会报错

![mark](http://myphoto.mtianyan.cn/blog/180521/DE81kIbc67.png?imageslim)

运行hello-world来查看是否能正确运行

```
sudo docker run hello-world
```

![mark](http://myphoto.mtianyan.cn/blog/180521/0k52Db9dba.png?imageslim)

vagrant文件配置安装好执行命令，一步到位。

```
config.vm.provision "shell", inline: <<-SHELL
     sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-selinux \
                  docker-engine-selinux \
                  docker-engine
     sudo yum install -y yum-utils \
          device-mapper-persistent-data \
          lvm2
     sudo yum-config-manager \
          --add-repo \
          https://download.docker.com/linux/centos/docker-ce.repo
     sudo yum install -y docker-ce
     sudo systemctl start docker
     sudo docker run hello-world
  SHELL
```

注意-y 是帮我们输入y

## docker-machine的本地使用

如果mac下安装。已经有docker-machine了

win10 家庭版安装docker toolbox也默认安装了docker-machine

![mark](http://myphoto.mtianyan.cn/blog/180521/7mC7CgJC11.png?imageslim)

使用vagrant destroy 快速删除

```
vagrant destroy
```
![mark](http://myphoto.mtianyan.cn/blog/180521/KGfHeE76jg.png?imageslim)

通过docker-machine create demo

```
docker-machine create demo
```
帮我们在virtualbox中安装一个已经有docker的非常小巧的虚拟机

dockermachine 是自动在虚拟机上安装docker的工具。

单独安装docker-machine

通过docker-machine创建的虚拟机我们如何使用呢？

```
docker-machine ls
```

会帮我们列出有哪些正在运行或创建好了的机器。

![mark](http://myphoto.mtianyan.cn/blog/180522/mfmGiBE78I.png?imageslim)


如何进到某台机器呢？

```
docker-machine ssh demo
```

![mark](http://myphoto.mtianyan.cn/blog/180522/KLFh92DCcl.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180522/C4bgKJ4hHH.png?imageslim)

exit退出虚拟机

stop rm start等一系列命令

我们在本地运行docker version


```
docker version
```

会发现我们只有client连不上server。

因此我们需要运行

```
docker-machine env demo
# 后面会提示shell中应该运行的命令
```

win下:

```
@FOR /f "tokens=*" %i IN ('docker-machine env demo') DO @%i
```

![mark](http://myphoto.mtianyan.cn/blog/180522/bGa7mF0haI.png?imageslim)

这样就可以连接上server了。

通过我们本地的docker client来管理docker server

远程创建安装好docker的机器。

https://docs.docker.com/machine/get-started-cloud/#examples

如何通过docker-machine创建一台阿里云上的docker机器。

## docker-machine云端虚拟机

需要driver的支持。

https://docs.docker.com/machine/drivers/

有Google 微软等。阿里云不在官方支持中。在第三方中。

https://docs.docker.com/machine/get-started-cloud/#3rd-party-driver-plugins

https://github.com/docker/docker.github.io/blob/master/machine/AVAILABLE_DRIVER_PLUGINS.md

https://github.com/AliyunContainerService/docker-machine-driver-aliyunecs

如何通过docker-machine和阿里云的driver来创建docker机器。

win下使用docker-machine 

推荐直接下载二进制包

![mark](http://myphoto.mtianyan.cn/blog/180522/ID67Cg0CBI.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180522/IC2g7eIbja.png?imageslim)

解压之后重命名加上exe，并将其放入任意一个已添加的path目录。

https://www.alibabacloud.com/help/zh/doc-detail/44778.htm

```
docker-machine create -d aliyunecs --help
```

![mark](http://myphoto.mtianyan.cn/blog/180522/3CDjJcLC68.png?imageslim)

苹果和linux重命名和加上执行权限。

### 使用

需要一个阿里云的账号。

点击个人头像access_key

会获取到Access Key ID和Access Key Secret

![mark](http://myphoto.mtianyan.cn/blog/180522/kK7B24b1ba.png?imageslim)

账号要有钱才行

```
docker-machine ssh mtianyan
```

连接到阿里云上的machine

```
docker version
```

同样可以通过

```
docker-machine env mtianyan
```

然后根据提示，运行命令之后。

```
docker version
```

这时候的client是我们的本地机器，而服务器变成了这台阿里云

不使用远程的，而使用我们本地的

```
docker-machine env --help
```

其中的unset命令就是用来使用本地server的

按照提示运行之后，我们的server就会被切换

## AWS美国

aws美国与中国不互通。信用卡绑定。

https://docs.docker.com/machine/examples/aws/#step-1-sign-up-for-aws-and-configure-credentials

```
docker-machine rm
```











