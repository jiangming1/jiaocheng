## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

老话总是没错的，工欲善其事，必先利其器

## 第二章：windows下搭建开发环境
教你安装pycharm,mysql,navicat,python相关环境。

教程仓库地址1: [https://github.com/mtianyan/DjangoGetStarted](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2Fmtianyan%2FDjangoGetStarted)
教程仓库地址2: [https://github.com/mtianyan/Mxonline2](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2Fmtianyan%2FMxonline2)
教程仓库地址3: [https://github.com/mtianyan/Mxonline3](https://link.jianshu.com/?t=https%3A%2F%2Fgithub.com%2Fmtianyan%2FMxonline3)


# windows下搭建开发环境

## 2-1 pycharm,mysql,Navicat安装。

**环境搭建：**

>- pycharm (我：PyCharm 2017.3.2)
>- mysql for windows(mysql-installer-community-5.7.20)
>- navicat for mysql(我：Navicat Premium）
>- python2.7


 **提醒：记住自己设置的mysql密码**

### Mysql

百度"mysql for windows" 直接在百度软件中心下载即可

![mark](http://upload-images.jianshu.io/upload_images/1779926-798086b7459bc581.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果你的电脑跟我电脑一样空，推荐遵循我的：

1. 点击接受协议
2. 选择Custom选项。(如果默认选项，会发生必要条件缺失：如我电脑没有VS和py3.4)

![mark](http://upload-images.jianshu.io/upload_images/1779926-c1ebc48f8ee21bf6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-231dbb61402a4fd3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 下图页面点击`next`会显示我们不满足的条件，`back`后点击绿色箭头移除。

![mark](http://upload-images.jianshu.io/upload_images/1779926-17d76e92723a0183.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 所有条件都达成，点击`Execute`，等待安装完成。

![mark](http://upload-images.jianshu.io/upload_images/1779926-0caa7afae39bc8ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>均为绿色代表安装完成。

- 一直默认选择直到下图页面。设置密码，添加用户(可选)

>**注意：记住自己设置的mysql密码**

![mark](http://upload-images.jianshu.io/upload_images/1779926-50b7e498d8f2c06a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>之后全部默认下一步。直到安装完成`Finish`

这时Navicat已经可以正常连接了。如果想让`mysql`命令在cmd下可使用。

`C:\Program Files\MySQL\MySQL Server 5.7\bin` (自行替换为自己的mysql.exe地址)加入环境变量中。

![mark](http://upload-images.jianshu.io/upload_images/1779926-0b8b4330de181ec6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过`mysql -uroot -p`命令可以进行登入mysql控制台。

![mark](http://upload-images.jianshu.io/upload_images/1779926-9da3a8218095dbf8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
### Navicat

安装指南：下一步下一步。

下载地址：http://www.navicat.com.cn/download/navicat-for-mysql

我的安装目录: `C:\software\Navicat Premium 12`
### PyCharm 2017.3.2

pycharm官方下载链接：https://www.jetbrains.com/pycharm/download/#section=windows

**我们要选择专业版（Professional）**因为只有专业版才能够新建django项目,免费社区版不能。

**为Pycharm添加解释器：**

`setting` - `Project Interpreter`：

![mark](http://upload-images.jianshu.io/upload_images/1779926-ad82850e9c788b7b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-6ea222b5a3d54d90.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

一直定位到 `python.exe` 点击确认。

### Python2.7安装

推荐阅读：**Python开发环境搭建指南(Anaconda2,3共存)**
>推荐选择进阶版本, 方便升级到3.6。

http://blog.mtianyan.cn/post/230a7ad6.html

## 2-2 virtualenv安装和配置

### virtualenv介绍
>每个应用可能需要各自拥有一套`独立`的Python运行环境。virtualenv就是用来为一个应用创建一套`隔离`的Python运行环境。

**virtualenv优点：**

![mark](http://upload-images.jianshu.io/upload_images/1779926-f82c55c6f16d45cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

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
![mark](http://upload-images.jianshu.io/upload_images/1779926-b7096383780957fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

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

![mark](http://upload-images.jianshu.io/upload_images/1779926-993c825141fd6ba1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 退出激活状态
```
deactivate
```
- 知道有哪些虚拟环境
```
workon
```
- 直接进入虚拟环境
```
workon DjangoTest
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-40406d89a057911e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**注意**前面的`(DjangoTest)`代表进入了虚拟环境。

执行`workon`命令之后，执行`pip install django==1.9.8`安装。

## 2-3 Pycharm和Navicat的简单使用

### pycharm简单使用：

`Setting -> reopen`取消默认打开上一次项目

#### 新建项目并验证成功运行
- 如何新建django项目：

[图片上传失败...(image-2c61b2-1515671732711)]

>选择好自己的项目的解释器为我们新建的虚拟环境。

新建`project`->`djangotestProj` 。别忘了为我们的虚拟环境安装`Django`


- 检查django环境是否安装好。`interpreter` 

![mark](http://upload-images.jianshu.io/upload_images/1779926-31b68bc633ec19c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 点击导航栏的`run`可以直接运行我们的django项目

![mark](http://upload-images.jianshu.io/upload_images/1779926-f7b996c2bceaf278.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>上图说明我们的django已经安装并且可以正常运行。

点击浏览器打开`http://127.0.0.1:8000/`进行验证。

[图片上传失败...(image-a7562d-1515671732711)]

出现上画面代表我们**大功告成**

#### 设置eclipse快捷键 - keymap

选择`setting`搜索`keymap`设置`eclipse`快捷键

>比如 `ctrl + H` 全局搜索

#### Run edit配置修改

![mark](http://upload-images.jianshu.io/upload_images/1779926-5a3d99ed30cc7534.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击上图中`run edit` 可对Django运行时的一些设置进行修改。

比如修改host为`0.0.0.0`，然后就可以设置监听本机ip。然后点击`run`

进入`cmd`下输入`ipconfig`查询自己的ip

![mark](http://upload-images.jianshu.io/upload_images/1779926-1f177054bd68b361.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>例如我的是`192.168.0.4`

```
192.168.0.4:8000/ 来访问。
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-6c94b980ab40d354.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 目录颜色不同的原因

![mark](http://upload-images.jianshu.io/upload_images/1779926-1a4fbae86ce5d0a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到不同的目录颜色不同。这是我们可以进行设置的，为了可以做到智能提示。

[图片上传失败...(image-2a9068-1515671732711)]

右键可以将`template`目录`unmark`

![mark](http://upload-images.jianshu.io/upload_images/1779926-539b3b1184549a9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到上图目录是灰色的。但是我们`右键mark`为`source Root`目录，会变为蓝色。

![mark](http://upload-images.jianshu.io/upload_images/1779926-d1ed909597ff9d8d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>这意味着我们在`import`时pycharm会根据设置智能提示。
如果不mark可能会出现很多我们在pycharm中报红色，
但是cmd确可以运行的情况。

### navicat基本使用

### 新建连接

![mark](http://upload-images.jianshu.io/upload_images/1779926-730970010636d81c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>点击新建一个mysql的连接。

![mark](http://upload-images.jianshu.io/upload_images/1779926-711acd14605584eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>连接名自行设置，密码填自己安装mysql时设置的密码。

### 右键新建数据库

![mark](http://upload-images.jianshu.io/upload_images/1779926-5bf217ed632a82bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>数据库名自行设置，`utf-8` `utf_general_ci` 
**注意：这里请与图中选择一致。否则保存中文可能出错**

### 新建数据表

双击数据库`testdjango`使他变绿，然后选中表，然后右键新建表。或使用右侧`新建表`按钮

![mark](http://upload-images.jianshu.io/upload_images/1779926-962759c16e7c8987.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

输入必要的字段然后使用`ctrl + s` 进行保存并输入表名。

### 增加数据

>双击表，可以展示我们的数据，这时候我们可以自行修改值。
点击左下角可以新增更多行。并且状态栏会显示一些sql语句信息

![mark](http://upload-images.jianshu.io/upload_images/1779926-e748ecd6f1f17590.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 设计表

右键设计表：我们可以添加字段

![mark](http://upload-images.jianshu.io/upload_images/1779926-84623760948a6a79.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Sql语句查询

点击查询，新建查询。我们可以输入Sql语句进行查询。

![mark](http://upload-images.jianshu.io/upload_images/1779926-3f0fa0e6eba098d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 表的复制粘贴与数据库传输。数据库导入导出。

>Navicat支持我们把不同数据库的表之间的复制粘贴操作。
>支持数据传输：点击工具数据传输

导出：在数据库上右键我们可以`转储SQL文件`: 可以选择只转存结构。或连带数据一起。
导入：右键点击运行SQL文件。
对于表的操作：删除，清空等，在点击表的右键菜单里。
