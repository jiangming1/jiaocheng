4- 环境搭建

MOOC_VM.vdl.zip

解压之后，得到一个vdl文件。打开virtual box，新建选择类型linux
ubuntu 64位。

选择继续，分配2g。使用已有的虚拟硬盘文件，点击选择我们下载的文件。点击创建。

点击启动。已设置开机自动启动终端，也可以禁用。

左上角 活动(activities)tweaks工具。
可以配置桌面显不显示家目录，

startup application: terminal

![mark](http://myphoto.mtianyan.cn/blog/180322/gCH27B9C6d.png?imageslim)

`ls` 可以看到我们预先放进系统的文件。

用户名和密码都是: mooc

python2 & python3都有

## 安装virtual box

没啥说的自己安

## 安装ubuntu

![mark](http://myphoto.mtianyan.cn/blog/180210/FFaf5f1B21.png?imageslim)

桌面版 ubuntu 16.04: 长期支持版(LTS): long time support

unity gnome 其他

![mark](http://myphoto.mtianyan.cn/blog/180210/6Iee2I2iFA.png?imageslim)

unity 花哨 占空间多。

- 下载 & 安装 & 配置

点击desktop

点击 download

镜像文件中有gnome的就是gnome版。

https://wiki.ubuntu.com/UbuntuGNOME/GetUbuntuGNOME

下载torrent 然后使用迅雷打开。

双系统: win10 + ubuntu

wtf 斗大的熊猫

1. 新建虚拟机系统

![mark](http://myphoto.mtianyan.cn/blog/180210/l00878c1GL.png?imageslim)

2. 分配2个g的内存
3. 保持默认选项: 现在创建虚拟硬盘

![mark](http://myphoto.mtianyan.cn/blog/180210/lkc204259A.png?imageslim)

4. 默认 默认动态分配 15gb

>后期可以通过命令行进行修改的。

进行设置使它使用我们下载的镜像进行启动。

![mark](http://myphoto.mtianyan.cn/blog/180210/BHd7L2JjJa.png?imageslim)

选择一个控制器盘片，点击红色标志选择我们刚才下载的`ubuntu_gnome`

点击启动:

然后点击`install ubuntu Gnome`

安装更新和第三方软件都不勾选.

如果是在安装双系统,不要选择这个选项.

![mark](http://myphoto.mtianyan.cn/blog/180210/CbAkeBfGA0.png?imageslim)

点击continue,选择时区.shanghai 选择Chinese Chinese,点击continue

点击自动登录.点击重启。

关闭getstarted

## 配置ubuntu

知乎帖子：在ubuntu上有什么必装的实用软件。

输入法: 搜狗输入法 for linux

输出法: Google输入法

```
sudo apt install fcitx fcitx-googlepinyin im-config
sudo add-apt-repository ppa:tualatrix/ppa
sudo add-apt-repository ppa:tualatrix/next
sudo apt-get update
sudo apt install vim
sudo apt-get install gnome-tweak-tool

mkdir share_folder
sudo apt install virtualbox-guest-utils

sudo mount -t vboxsf share /home/mtianyan/shared_folder
ls shared
```

## 安装python

![mark](http://myphoto.mtianyan.cn/blog/180322/jh5iJ494L7.png?imageslim)

本课python版本

2.7.x

```
python -V
python3 -V
```

官网download 

## 安装TensorFlow

![mark](http://myphoto.mtianyan.cn/blog/180322/IBa3L9E19K.png?imageslim)

图像处理器版本有一定硬件要求。英伟达显卡

![mark](http://myphoto.mtianyan.cn/blog/180322/bbFljCHi2H.png?imageslim)

安装形式有很多种

![mark](http://myphoto.mtianyan.cn/blog/180322/KaG9JeF4Ae.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180322/Lh36KjAACD.png?imageslim)

anaconda 和源代码编译安装。

pip 安装 TensorFlow

![mark](http://myphoto.mtianyan.cn/blog/180322/b0kB0dA4je.png?imageslim)

```
pip install 软件包命名
pip uninstall 软件包名
```

ubuntu支持最丰富

cuda cudnn

官方推荐，virtualenv安装。隔离开发环境。

```
sudo apt-get install python-pip python-dev python-virtualenv
```

创建一个python的虚拟环境

```
virtualenv --system-site-packages tensorFlow-mtianyan
```

可以看到家目录中创建了tensorFlow-mtianyan文件夹

```
source ~/tensorFlow-mtianyan/bin/activate
```

要保证pip的版本大于8.1

```python
easy_install -U pip
```

安装TensorFlow

```
pip install tensorflow
```

验证TensorFlow是否被安装

输入python

```
import tensorflow
```

```
cd tensorflow/
du -sh
# 可以看到目录的大小
```

删除虚拟环境

```
rm -rf tensorflow/
```
### 系统型安装

```
sudo apt-get install python-pip python-dev
```

```
pip install tensorflow
```

输入python

```
import tensorflow
```

## 安装python类库

```
pip install numpy
```





