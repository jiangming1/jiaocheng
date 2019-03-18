## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

>使用Python3.6与Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

项目支持支付宝支付(暂不支持微信支付),支持手机短信验证码注册， 支持第三方登录。集成了sentry错误监控系统。

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节内容: 环境搭建

## pycharm的安装和简单使用

下载专业版。

Linux下pycharm使用。
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

![mark](http://upload-images.jianshu.io/upload_images/1779926-ea3f68882548e07b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果你的电脑跟我电脑一样空，推荐遵循我的：

1. 点击接受协议
2. 选择Custom选项。(如果默认选项，会发生必要条件缺失：如我电脑没有VS和py3.4)

![mark](http://upload-images.jianshu.io/upload_images/1779926-97aea1dbf8cea6d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-cd2af0fc1966ed1e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 下图页面点击`next`会显示我们不满足的条件，`back`后点击绿色箭头移除。

![mark](http://upload-images.jianshu.io/upload_images/1779926-88d82e6653d7e947.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 所有条件都达成，点击`Execute`，等待安装完成。

![mark](http://upload-images.jianshu.io/upload_images/1779926-72640dcf8d2b3530.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>均为绿色代表安装完成。

- 一直默认选择直到下图页面。设置密码，添加用户(可选)

>**注意：记住自己设置的mysql密码**

![mark](http://upload-images.jianshu.io/upload_images/1779926-ab6d6c4ad0caf28a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>之后全部默认下一步。直到安装完成`Finish`

这时Navicat已经可以正常连接了。如果想让`mysql`命令在cmd下可使用。

`C:\Program Files\MySQL\MySQL Server 5.7\bin` (自行替换为自己的mysql.exe地址)加入环境变量中。

![mark](http://upload-images.jianshu.io/upload_images/1779926-47b92d8d7df502bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过`mysql -uroot -p`命令可以进行登入mysql控制台。

![mark](http://upload-images.jianshu.io/upload_images/1779926-be78eeea8752d131.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf

```

修改bind adress: `0.0.0.0`

```
sudo service mysql restart
```

```
# 授予所有权限其实可以细化，on`*.*`是一个正则表达式，表示所有的表赋权限。`root `代表通过root用户连接过来。 `%` 代表所有外部的ip ：当然也可以指明某一个ip.所有ip上只要是通过root 用户 Identity by`密码 `都可以访问我的所有表
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

![mark](http://upload-images.jianshu.io/upload_images/1779926-a5e452ccb670ff53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

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
![mark](http://upload-images.jianshu.io/upload_images/1779926-df1c019b3b2a89b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

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

![mark](http://upload-images.jianshu.io/upload_images/1779926-f90ee939acae417c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

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

![mark](http://upload-images.jianshu.io/upload_images/1779926-6c659132a571fe91.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-ebe5e0c16f4b351e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
mkvirtualenv articlespider --python=python3.5

rm -rf py3scrapy : 删除目录 
```

## Vue开发环境搭建

- webstorm
- node.js
- cnpm

自行安装下一步下一步

验证安装成功node.js:

![mark](http://upload-images.jianshu.io/upload_images/1779926-90cb0784be12bae4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

cnpm:

https://npm.taobao.org/

![mark](http://upload-images.jianshu.io/upload_images/1779926-63e9bbb8c344eb34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

online_store 前端vue项目的源码

进入根目录`cnpm install`

```
npm run dev
```

可以在浏览器里直接访问我们的前端项目

地址：127.0.0.1:8000

报错: 删除node_modules，然后重新install run

问题:

提问首选截图方式，拷贝运行。选择语言python
