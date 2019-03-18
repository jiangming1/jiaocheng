## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

## 4-5 user modesl.py设计

循环引用:

>设计app时每个app都有model

![mark](http://upload-images.jianshu.io/upload_images/1779926-5567413eaaf7b8e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如图：我们在user中定义usercourse记录用户学习的课程。会有两个外键：user和course。
我们就会`import Courses.models`

如果用户对课程的评论：会放在 `Courses.models`当中。评论我们需要保存相应的用户。
我们就会`import User.models`

循环import会出错。a与b相互调用，造成等待。

### 解决循环引用: 分层设计

目前已有app：users courses organization

另外一个app高于这些app的层级。`operation`.上一层app可以import下层的app。

![mark](http://upload-images.jianshu.io/upload_images/1779926-e7fb3deca731eaa6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上节中: 自定义`userprofile` 覆盖默认`user`表

user中还需要添加的(前提是这些功能比较独立):

- EmailVerifyRecord - 邮箱验证码
- PageBanner - 轮播图

观察轮播图:

>1. 图片 2. 点击图片地址 3. 轮播图序号(控制前后)

![mark](http://upload-images.jianshu.io/upload_images/1779926-705519f6228b3534.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

users/models.py中添加代码:

```python
from datetime import datetime

# 邮箱验证码model


class EmailVerifyRecord(models.Model):
    SEND_CHOICES = (
        ("register", u"注册"),
        ("forget", u"找回密码")
    )
    code = models.CharField(max_length=20, verbose_name=u"验证码")
    # 未设置null = true blank = true 默认不可为空
    email = models.EmailField(max_length=50, verbose_name=u"邮箱")
    send_type = models.CharField(choices=SEND_CHOICES, max_length=10)
    # 这里的now得去掉(),不去掉会根据编译时间。而不是根据实例化时间。
    send_time = models.DateTimeField(default=datetime.now)

    class Meta:
        verbose_name = "邮箱验证码"
        verbose_name_plural = verbose_name

# 轮播图model


class Banner(models.Model):
    title = models.CharField(max_length=100, verbose_name=u"标题")
    image = models.ImageField(
        upload_to="banner/%Y/%m",
        verbose_name=u"轮播图",
        max_length=100)
    url = models.URLField(max_length=200, verbose_name=u"访问地址")
    # 默认index很大靠后。想要靠前修改index值。
    index = models.IntegerField(default=100, verbose_name=u"顺序")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"轮播图"
        verbose_name_plural = verbose_name
```

从上往下: 第一块区域import官方包，第二块import第三方。(PEP8)

![mark](http://upload-images.jianshu.io/upload_images/1779926-abd2d7f09efe9f66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如下图: 我们一共创建了三个数据表: Structure可以查看到

![mark](http://upload-images.jianshu.io/upload_images/1779926-c6dc3109c246ddad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>与用户相关的评论啊，点赞啊。学习的课程啊并没有放进来，因为那些独立性不高。
容易产生循环引用。我们把那些放到operation中。

本小节完成，对应commit:

> Usermodel添加邮箱验证码，首页轮播图。对应4-5

## 4-6 course models.py编写

点击 `Tools 菜单下 Run manage.py Task `

```
startapp courses
```

course中需要那些表:

- 课程本身需要一张表

![mark](http://upload-images.jianshu.io/upload_images/1779926-92038503766c3af9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-71fab8dbef729412.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 点进去之后点击开始学习。

- 课程基本信息需要一张表, 章节表与课程表存在(一个课程对应多个章节)
- 章节表中：章节的名称。 章节与视频(一个章节对应多个视频)

结构: 课程本身--(一对多)>章节-(一对多)->视频信息

资源下载放在课程里面的。一个课程对应多个资源

共四张表：课程本身--(一对多)>章节-(一对多)->视频信息 & 资源表

![mark](http://upload-images.jianshu.io/upload_images/1779926-c3d1698b9a143b86.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

courses/models.py:

```python
from datetime import datetime

# 课程信息表
class Course(models.Model):
    DEGREE_CHOICES = (
        ("cj", u"初级"),
        ("zj", u"中级"),
        ("gj", u"高级")
    )
    name = models.CharField(max_length=50, verbose_name=u"课程名")
    desc = models.CharField(max_length=300, verbose_name=u"课程描述")
    # TextField允许我们不输入长度。可以输入到无限大。暂时定义为TextFiled，之后更新为富文本
    detail = models.TextField(verbose_name=u"课程详情")
    degree = models.CharField(choices=DEGREE_CHOICES, max_length=2)
    # 使用分钟做后台记录(存储最小单位)前台转换
    learn_times = models.IntegerField(default=0, verbose_name=u"学习时长(分钟数)")
    # 保存学习人数:点击开始学习才算
    students = models.IntegerField(default=0, verbose_name=u"学习人数")
    fav_nums = models.IntegerField(default=0, verbose_name=u"收藏人数")
    image = models.ImageField(
        upload_to="courses/%Y/%m",
        verbose_name=u"封面图",
        max_length=100)
    # 保存点击量，点进页面就算
    click_nums = models.IntegerField(default=0, verbose_name=u"点击数")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"课程"
        verbose_name_plural = verbose_name
```

下面来编写章节 & 视频 & 课程资源:`courses/models.py`

一对多, 多对一都可以使用django的外键来完成。

```python
# 章节
class Lesson(models.Model):
    # 因为一个课程对应很多章节。所以在章节表中将课程设置为外键。
    # 作为一个字段来让我们可以知道这个章节对应那个课程
    course = models.ForeignKey(Course, verbose_name=u"课程")
    name = models.CharField(max_length=100, verbose_name=u"章节名")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"章节"
        verbose_name_plural = verbose_name


# 每章视频
class Video(models.Model):
    # 因为一个章节对应很多视频。所以在视频表中将章节设置为外键。
    # 作为一个字段来存储让我们可以知道这个视频对应哪个章节.
    lesson = models.ForeignKey(Lesson, verbose_name=u"章节")
    name = models.CharField(max_length=100, verbose_name=u"视频名")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"视频"
        verbose_name_plural = verbose_name


# 课程资源
class CourseResource(models.Model):
    # 因为一个课程对应很多资源。所以在课程资源表中将课程设置为外键。
    # 作为一个字段来让我们可以知道这个资源对应那个课程
    course = models.ForeignKey(Course, verbose_name=u"课程")
    name = models.CharField(max_length=100, verbose_name=u"名称")
    # 这里定义成文件类型的field，后台管理系统中会直接有上传的按钮。
    # FileField也是一个字符串类型，要指定最大长度。
    download = models.FileField(
        upload_to="course/resource/%Y/%m",
        verbose_name=u"资源文件",
        max_length=100)
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"课程资源"
        verbose_name_plural = verbose_name
```

通过Structure可以看到我们刚才设计的四张表

![mark](http://upload-images.jianshu.io/upload_images/1779926-94ba3a808fdec0f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

本小节完毕, 对应commit:

>设计完成课程app中四张数据表: 课程，章节，视频，资源。对应4-6

## 4-7 organization modesl.py设计

新建课程机构app:

点击`Tools 菜单下 Run manage.py Task `

```
startapp organization
```

课程是属于机构的, 机构有机构类别，城市等字段。讲师实体。
我要学习的提交表单会与用户关联，存放在机构。

![mark](http://upload-images.jianshu.io/upload_images/1779926-efb1a03acd57083e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-e5e3c6377d96ba35.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中课程数，学习人数可以动态统计。机构地址，机构经典课程。

>机构讲师，机构课程可以通过外键获取到, 不保存到机构中。

![mark](http://upload-images.jianshu.io/upload_images/1779926-3a69dab026732db0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>讲师大概所需要的字段如图所示。

organization/models.py 代码如下：

```python
# encoding : utf-8
from datetime import datetime

from django.db import models

# Create your models here.


# 城市字典
class CityDict(models.Model):
    name = models.CharField(max_length=20, verbose_name=u"城市")
    # 城市描述：备用不一定展示出来
    desc = models.CharField(max_length=200, verbose_name=u"描述")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"城市"
        verbose_name_plural = verbose_name


# 课程机构
class CourseOrg(models.Model):
    name = models.CharField(max_length=50, verbose_name=u"机构名称")
    # 机构描述，后面会替换为富文本展示
    desc = models.TextField(verbose_name=u"机构描述")
    click_nums = models.IntegerField(default=0, verbose_name=u"点击数")
    fav_nums = models.IntegerField(default=0, verbose_name=u"收藏数")
    image = models.ImageField(
        upload_to="org/%Y/%m",
        verbose_name=u"封面图",
        max_length=100)
    address = models.CharField(max_length=150, verbose_name=u"机构地址")
    # 一个城市可以有很多课程机构，通过将city设置外键，变成课程机构的一个字段
    # 可以让我们通过机构找到城市
    city = models.ForeignKey(CityDict, verbose_name=u"所在城市")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"课程机构"
        verbose_name_plural = verbose_name


# 讲师
class Teacher(models.Model):
    # 一个机构会有很多老师，所以我们在讲师表添加外键并把课程机构名称保存下来
    # 可以使我们通过讲师找到对应的机构
    org = models.ForeignKey(CourseOrg, verbose_name=u"所属机构")
    name = models.CharField(max_length=50, verbose_name=u"教师名称")
    work_years = models.IntegerField(default=0, verbose_name=u"工作年限")
    work_company = models.CharField(max_length=50, verbose_name=u"就职公司")
    work_position = models.CharField(max_length=50, verbose_name=u"公司职位")
    points = models.CharField(max_length=50, verbose_name=u"教学特点")
    click_nums = models.IntegerField(default=0, verbose_name=u"点击数")
    fav_nums = models.IntegerField(default=0, verbose_name=u"收藏数")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"教师"
        verbose_name_plural = verbose_name
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-9a112dc8027287f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>可以看到我们一共创建了三张表：分别是城市，课程机构，讲师。

本小节对应commit:

>课程机构app：城市，机构，讲师表书写完毕。对应4-7

##  4-8 operation models.py设计

分析需要那些表:

- 用户可以提交我要学习的个人需求。
- 学员的课程评论信息
- 收藏：可以收藏公开课, 授课讲师, 授课机构, 用户消息提醒。
- 个人中心：我的课程说明用户和课程之间的学习关系也需要保存。

![mark](http://upload-images.jianshu.io/upload_images/1779926-284ea94fb8409605.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

新建操作app:

点击`Tools 菜单下 Run manage.py Task `

```
startapp operation
```
operation/models.py添加代码:

```python
# encoding: utf-8
from datetime import datetime

# 引入我们CourseComments所需要的外键models
from users.models import UserProfile
from courses.models import Course

# 用户我要学习表单
class UserAsk(models.Model):
    name = models.CharField(max_length=20, verbose_name=u"姓名")
    mobile = models.CharField(max_length=11, verbose_name=u"手机")
    course_name = models.CharField(max_length=50, verbose_name=u"课程名")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"用户咨询"
        verbose_name_plural = verbose_name


# 用户对于课程评论
class CourseComments(models.Model):

    # 会涉及两个外键: 1. 用户， 2. 课程。import进来
    course = models.ForeignKey(Course, verbose_name=u"课程")
    user = models.ForeignKey(UserProfile, verbose_name=u"用户")
    comments = models.CharField(max_length=250, verbose_name=u"评论")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"评论时间")

    class Meta:
        verbose_name = u"课程评论"
        verbose_name_plural = verbose_name


# 用户对于课程,机构，讲师的收藏
class UserFavorite(models.Model):
    # 会涉及四个外键。用户，课程，机构，讲师import
    TYPE_CHOICES = (
        (1, u"课程"),
        (2, u"课程机构"),
        (3, u"讲师")
    )

    user = models.ForeignKey(UserProfile, verbose_name=u"用户")
    # course = models.ForeignKey(Course, verbose_name=u"课程")
    # teacher = models.ForeignKey()
    # org = models.ForeignKey()
    # fav_type =

    # 机智版
    # 直接保存用户的id.
    fav_id = models.IntegerField(default=0)
    # 表明收藏的是哪种类型。
    fav_type = models.IntegerField(
        choices=TYPE_CHOICES,
        default=1,
        verbose_name=u"收藏类型")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"评论时间")

    class Meta:
        verbose_name = u"用户收藏"
        verbose_name_plural = verbose_name


# 用户消息表
class UserMessage(models.Model):
        # 因为我们的消息有两种:发给全员和发给某一个用户。
        # 所以如果使用外键，每个消息会对应要有用户。很难实现全员消息。

        # 机智版 为0发给所有用户，不为0就是发给用户的id
    user = models.IntegerField(default=0, verbose_name=u"接收用户")
    message = models.CharField(max_length=500, verbose_name=u"消息内容")

    # 是否已读: 布尔类型 BooleanField False未读,True表示已读
    has_read = models.BooleanField(default=False, verbose_name=u"是否已读")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"用户消息"
        verbose_name_plural = verbose_name


# 用户课程表
class UserCourse(models.Model):
    # 会涉及两个外键: 1. 用户， 2. 课程。import进来
    course = models.ForeignKey(Course, verbose_name=u"课程")
    user = models.ForeignKey(UserProfile, verbose_name=u"用户")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"用户课程"
        verbose_name_plural = verbose_name
```

至此：我们的五张operation下的数据表models设计完成

![mark](http://upload-images.jianshu.io/upload_images/1779926-1cfc808f763b3f0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### setting中配置添加app

![mark](http://upload-images.jianshu.io/upload_images/1779926-4a84b68d93dad2ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

本小节对应commit:

>operation下的models设计,用户: 课程&消息&收藏&评论&我要学习.并在setting中进行了注册。对应4-8

## 4-9 数据表生成以及apps目录建立

>学习如何通过刚才设计的models生成数据库对应的表

点击`Tools 菜单下 Run manage.py Task `:

### Python2与Python3不同:

Python2下可能会报一些`noASCII`错误:

只需要在对应你写了中文的第一行加上:

```
# encoding: utf-8
```

### Python3(django2.0.1)会报错:

```
org = models.ForeignKey(CourseOrg, verbose_name=u"所属机构")
TypeError: __init__() missing 1 required positional argument: 'on_delete'
```

>这是因为在2.0.1中，外键关系必须指明删除时的操作。

比如：出租车都归属于出租车公司。如果出租车公司倒闭了，那这些汽车该怎么处理。
必须自己指明: 我觉得可以直接进行级联删除。

django提供了:

- `CASCADE`
- `PROTECT`
- `SET_NULL`
- `SET_DEFAULT`

等选项。我选择了`CASCADE`删除。

将(dajngo 2.0.1)项目中所有的外键修改为如下面代码所示：
>也就是添加了`on_delete=models.CASCADE`使其级联删除。

```python
org = models.ForeignKey(CourseOrg, on_delete=models.CASCADE, verbose_name=u"所属机构")
```

### makemirgration & migrate生成表

```
makemirgration
migrate
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-72af8a176cc0ca6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图为makemirgration过程中输出的信息。可以看到我们做出的改动

![mark](http://upload-images.jianshu.io/upload_images/1779926-05f4da79fba34810.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时我们查看app目录中migrations文件夹可以看到产生的新文件。

`operation/migrations/0001_initial.py:`

>可以看到里面也是Python的语法。他会帮我们生成数据表。
以后每次`migrations`时都会生成新的initial文件。这是很重要的变动文件，不能随意删除。

打开Navicat可以看到django的数据库中有它默认的django_migrations表

![mark](http://upload-images.jianshu.io/upload_images/1779926-2be8480fd8630f19.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

双击django_migrations表可以看到我们migration的记录。

![mark](http://upload-images.jianshu.io/upload_images/1779926-18975fd4c3868464.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>会记录哪个app下的哪个initial.py已经运行了。

进入Navicat进行成功性验证:

![mark](http://upload-images.jianshu.io/upload_images/1779926-41b491e298b596bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>可以看到我们的表已经生成成功,命名规则为: app名称 + 我们的类名变成小写

### 把我们的四个app放到一个文件夹下。

- 新建Python的package: apps 

![mark](http://upload-images.jianshu.io/upload_images/1779926-943ec56f138a8f34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 把四个app都拖进apps中去。

![mark](http://upload-images.jianshu.io/upload_images/1779926-543645434819c386.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

去掉`searchfor`的勾选。拖进去之后会报错，说找不到那些import的模块了。

解决方案：右键`Mark`为`sourceRoot`。根目录下找不到的，会去apps下搜索。

但是这时候cmd下还是会报错。

解决方案(图来源于我的`DjangoGetStarted`教程):

![mark](http://upload-images.jianshu.io/upload_images/1779926-c4b79722d00b575b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

同理,插入第0是希望它先搜索我们app下东西：

![mark](http://upload-images.jianshu.io/upload_images/1779926-f27d3d0ad2e33424.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 成功性验证

![mark](http://upload-images.jianshu.io/upload_images/1779926-07d1f1bef064b92d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-f0dff0639a90c3d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>可以看到Django已经可以正常run成功了。

### 第四章总结

- 我们设计了app

![mark](http://upload-images.jianshu.io/upload_images/1779926-bc657c55ceb3ad44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 设计了user models.py

![mark](http://upload-images.jianshu.io/upload_images/1779926-b9cbfa72c05f33a1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 循环引用

![mark](http://upload-images.jianshu.io/upload_images/1779926-fdfe7756dddaf4df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>得出我们需要创建一个更高层次的app。分层设计，operation在更高层。

- Courses models.py 

![mark](http://upload-images.jianshu.io/upload_images/1779926-2ff568ff25743f2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- organization models.py

![mark](http://upload-images.jianshu.io/upload_images/1779926-72f72f1f1f84b0a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- operation models.py

![mark](http://upload-images.jianshu.io/upload_images/1779926-738d3f1c13b73ecd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过makemigrations 生成表的变动 & migrate 
每个app下的migration目录的用途，和数据库中django_migration
将所有app放到同一个目录之下。

本章结束对应commit:

>数据表全部生成，migration目录&表django_migration。将app放到apps目录。对应4-9.
第四章结束！撒花。
