最终项目上线演示地址: http://search.mtianyan.cn
- 第二节：开始做项目搭环境之前，点上面链接看看我们能做一个什么。
这节我们开始搭建环境。
Github地址: https://github.com/mtianyan/ArticleSpider (欢迎先点star后上车)
# 开发环境搭建

1. IDE: pycharm
2. 数据库: mysql, redis, elasticsearch
3. 开发环境 virtualenv

这次的数据库只介绍mysql,后面两个会放到要用到的章节。

## Pycharm的安装和使用

Pycharm在windows和linux下的安装：

1. Pycharm的安装(linux 和windows)

**一定要下载专业版，社区免费版有很多功能缺失**

### windows下pycharm安装。

>PyCharm 2017.3.2

pycharm官方下载链接：https://www.jetbrains.com/pycharm/download/#section=windows

**我们要选择专业版（Professional）**因为只有专业版才能够新建django项目,免费社区版不能。

**为Pycharm添加解释器：**

`setting` - `Project Interpreter`：

![mark](http://upload-images.jianshu.io/upload_images/1779926-4adfef8647b9651c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-b4170dc981a81dc3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

一直定位到 `python.exe` 点击确认。

设置keymap: 设置快捷键。

### Linux下pycharm使用。

同样下载专业版。 Linux下的版本实际是一个绿色版本。

```
cd pycharm
cd bin/
./pycharm.sh
 
```

设置通过命令直接启动。

```
vim ~/.bashrc

alias pycharm="bash /home//pycharm.sh"

source ~./bashrc
```
## mysql和Navicat的安装和使用

### Mysql

百度"mysql for windows" 直接在百度软件中心下载即可

![mark](http://upload-images.jianshu.io/upload_images/1779926-88847ddd5e79bd3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果你的电脑跟我电脑一样空，推荐遵循我的：

1. 点击接受协议
2. 选择Custom选项。(如果默认选项，会发生必要条件缺失：如我电脑没有VS和py3.4)

![mark](http://upload-images.jianshu.io/upload_images/1779926-3aa192b19229e70e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-3d3de47e1236749a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 下图页面点击`next`会显示我们不满足的条件，`back`后点击绿色箭头移除。

![mark](http://upload-images.jianshu.io/upload_images/1779926-72369cc46cf661a0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 所有条件都达成，点击`Execute`，等待安装完成。

![mark](http://upload-images.jianshu.io/upload_images/1779926-ffdcedf918e8a9f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>均为绿色代表安装完成。

- 一直默认选择直到下图页面。设置密码，添加用户(可选)

>**注意：记住自己设置的mysql密码**

![mark](http://upload-images.jianshu.io/upload_images/1779926-254a905d97303105.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>之后全部默认下一步。直到安装完成`Finish`

这时Navicat已经可以正常连接了。如果想让`mysql`命令在cmd下可使用。

`C:\Program Files\MySQL\MySQL Server 5.7\bin` (自行替换为自己的mysql.exe地址)加入环境变量中。

![mark](http://upload-images.jianshu.io/upload_images/1779926-58833cf3b24fdbeb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过`mysql -uroot -p`命令可以进行登入mysql控制台。

![mark](http://upload-images.jianshu.io/upload_images/1779926-286d4628c8d52533.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf

```

修改bind adress: `0.0.0.0`

```
sudo service mysql restart
```
 授予所有权限其实可以细化，on`*.*`是一个正则表达式，表示所有的表赋权限。`root `代表通过root用户连接过来。 `%` 代表所有外部的ip ：当然也可以指明某一个ip.所有ip上只要是通过root 用户 Identity by`密码 `都可以访问我的所有表
```
grant all privileges on *.* to 'root'@'%' identified by 'ty158917' with grant option;

flush privileges;
```

Linux 安装 mysql

```
sudo apt-get install mysql-server
ps aux|grep mysql

mysql-uroot -p

show databases;
```

通过set py3获取py3安装路径。


### Navicat

安装指南：下一步下一步。

下载地址：http://www.navicat.com.cn/download/navicat-for-mysql

我的安装目录: `C:\software\Navicat Premium 12`

## virtualenv和virtualenvwrapper安装和配置


### virtualenv介绍
>每个应用可能需要各自拥有一套`独立`的Python运行环境。virtualenv就是用来为一个应用创建一套`隔离`的Python运行环境。

**virtualenv优点：**

![mark](http://upload-images.jianshu.io/upload_images/1779926-5e55cb4a46f42de3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

它是将全局Python解释器进行私有化复制。
如果不使用虚拟环境，默认的`pip`安装都会安装到同一个目录(java是把自己需要的包放到自己项目目录)，不同项目使用起来会产生问题

### 安装virtualenv
进入cmd，（确保自己的pip已经可用）

```python
pip install virtualenv
virtualenv testvir
# 在当前用户目录(win+r %HOMEPATH%可查看)生成
cd %homepath%
cd testvir
cd Scripts
activate.bat #激活
pip list 
deactivate.bat
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-45866027d9b13d0a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

默认使用`virtualenv testvir`该命令，会将虚拟环境创建在我们当前用户目录。

**注意：**我的目录在桌面是我的cmder设置的、还请自行`cd %homepath%`前往自己的目录


这样直接使用步骤有写过于繁琐。所以我们使用`virtualenvwrapper`

### virtualenvwrapper安装

```python
pip install virtualenvwrapper-win
pip install virtualenvwrapper(Linux)
```

- 创建虚拟环境

```
mkvirtualenv DjangoTest
```
会创建在`C:\Users\mtian\Envs`当前用户目录下的Envs目录。

修改`mkvirtualenv`创建的目录：新增环境变量`WORKON_HOME`

![mark](http://upload-images.jianshu.io/upload_images/1779926-a9a7f6d9c077e719.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 退出激活状态
```
deactivate
```
- 知道有哪些虚拟环境
```
workon
```

Linux下安装:

```
sudo apt-get install python-virtualenv

virtualenv venv --python=python3.5
virtualenv venv --python=python2.7

安装pip

sudo python get-pip.py

pip install virtualenvwrapper

sudo find / -name virtualenvwrapper.sh
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-aa5c877a411623cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-1e0073a6de33e93e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
mkvirtualenv articlespider --python=python3.5

rm -rf py3scrapy : 删除目录 
```
