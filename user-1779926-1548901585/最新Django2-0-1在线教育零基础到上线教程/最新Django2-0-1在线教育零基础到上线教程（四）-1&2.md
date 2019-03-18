## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

## 4-1 使用py3.6和django1.11开发系统前注意事项

直接通过Python3.6和django最新版本来开发我们的系统的一些注意事项。

原版本: Python 2.7 & django 1.9.8
现在版本：Python 3.6 & django 1.11

直接从3.6上手，开始工作，而不用做完2.7再转换。

>代码几乎100%兼容2.7 & 3.6

### 虚拟环境问题

>Python2.7 与 Python3.x共存并创建虚拟环境。

```
mkvirtualenv -p C:\...\python.exe mxonline
```

### 设计model的时候的`__unicode__`方法

Python2.7 中:

```python
def __unicode__:
	return self.name
```
Python 3.x中:
```python
def __str__(self):
	return self.name
```

>3.x下重载Unicode不会报错，但是会在后台显示有问题。

### 安装Python的mysql驱动时不能用之前的 MYSQL python

>这个网址是windows下python包安装的居家必备良品，建议收藏。

https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysql-python

应该改用`Mysqlclient`来替换我们的`MySQl-python`

>接口是一样的。所以以后建议直接用Mysqlclient，因为它2, 3版本都有。

### 通过源码方式安装xadmin时。

>Github 搜索 mxonline_resources，将里面的Xadmin放进extras_apps中。
就不用官方的了。

>也可以直接使用官方的新版，已经支持了Python3.6

Xadmin安装一定要安装依赖包

```
django-crispy-forms~=1.6.0
django-import-export>=0.5.1
django-reversion~=2.0.0
django-formtools
future==0.15.2
httplib2==0.9.2
six==1.10.0
```
### 使用DjangoUeditor

官方的不支持Python3, 去mxonline_resource目录下载兼容Python3的版本。
放入`extras_apps`

## 4-2 django-app 设计

>数据库设计

### 根据app设计 models

### 数据表生成与修改

授课机构提供讲师录制课程，学员完成在线学习。

- 全局头部：用户消息 & 个人中心: 没有登录时，就是登录注册
- 对于公开课，授课讲师，授课机构进行搜索。
- 轮播图，课程，机构，页脚
- 公开课：分页公开课，右边热门推荐。
- 点进课程：课程详情页。详情: 后台富文本。右边是课程机构的介绍。收藏 或学习
- 章节信息 & 课程资源下载 & 评论
- 授课讲师: 授课讲师列表页,  讲师排行榜。分页。
- 点进讲师: 看到课程。
- 授课机构: 类别筛选，机构性质，所在地区 & 排序。用户提交表单，我要学习, 机构排名.
- 个人中心: 修改密码, 修改头像, 个人信息, 我的课程, 我的收藏, 我的消息。

app大致会有`用户模块`,`课程模块`,`授课教师`与`授课机构`。

![mark](http://upload-images.jianshu.io/upload_images/1779926-27237bda6b963c7b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

多一个operation app 是因为数据库的需要。后面会讲。
