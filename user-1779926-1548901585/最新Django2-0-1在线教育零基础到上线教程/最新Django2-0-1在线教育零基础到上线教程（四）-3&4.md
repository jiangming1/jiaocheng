## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

## 4-3 新建项目

### Python2.7 创建虚拟环境。

```
mkvirtualenv mxonline2
```

安装django

```
pip install django==1.9.8 
```

>注意Python2下此处必须用1.9.8

![mark](http://upload-images.jianshu.io/upload_images/1779926-aa007f6478cb9287.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Python3.x 创建虚拟环境

如果你已经通过我的博文《Python开发环境搭建指南(Anaconda2,3共存)》
搭建了完美的共存环境使用下面命令创建虚拟环境

```
mkvirtualenv -p D:\softEnvDown\Anaconda2\envs\py3\python.exe mxonline3
```
>-p后面路径为自己的Python3的exe文件路径。

![mark](http://upload-images.jianshu.io/upload_images/1779926-084353c420e07062.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>官方说明的最新稳定版为2.0.1(2018-01-08 19:37:06)

```
workon mxonline3
pip install django==2.0.1
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-01841ea7347976d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

至此我们的两个虚拟环境都已经准备好了。

### 新建Python2 下Project

为Mxonline2 配置环境 `mxonline2`

![mark](http://upload-images.jianshu.io/upload_images/1779926-2cc8b038430c3f52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意一直定位到Python.exe。


#### 安装mysql驱动。

下载https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysql-python中
mysqlclient‑1.3.12‑cp34‑cp34m‑win_amd64.whl进行本地安装

```
workon mxonline2
pip install mysqlclient-1.3.12-cp27-cp27m-win_amd64.whl

```

![mark](http://upload-images.jianshu.io/upload_images/1779926-6d33ce0c36d0c7fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 新建Python3 下Project


为Mxonline3 配置环境 `mxonline3`

![mark](http://upload-images.jianshu.io/upload_images/1779926-e24bdd525bcbae56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>注意一直定位到Python.exe。


#### 安装mysql驱动。

```
workon mxonline3
pip install mysqlclient
```

### setting中配置

Mxonline2/settings.py:
Mxonline3/settings.py:

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mxonline2',
        'USER': 'root',
        'PASSWORD': '你的密码',
        'HOST':'127.0.0.1'

    }
}
```

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mxonline3',
        'USER': 'root',
        'PASSWORD': '你的密码',
        'HOST':'127.0.0.1'

    }
}
```

### 前往Navicat新建数据库

mxonline2 & mxonline3

#### 进行数据库初始化makemigrations

点击`Tools 菜单下 Run manage.py Task `

```
makemigrations
migrate
```

>2,3操作一致

点击 RUn edit

![mark](http://upload-images.jianshu.io/upload_images/1779926-40e1ffa31c855e69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以为2,3配置不同的port。比如2: 8002 & 3: 8003

2: 点击run运行: django1.9.8成功画面如下。

![mark](http://upload-images.jianshu.io/upload_images/1779926-2f7206af43024664.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3: 点击run运行: django2.0.1成功画面如下。

![mark](http://upload-images.jianshu.io/upload_images/1779926-9531c9622fe1297e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这时我们的项目就新建成功。

此处对应commit:

>项目初始化成功: 完成数据库Migration初始化。 对应4-3

## 4-4 自定义userprofile

点击`Tools 菜单下 Run manage.py Task `

```
startapp users
```

### 编写我们的model设计user表。

系统自动生成的user表如下:

![mark](http://upload-images.jianshu.io/upload_images/1779926-abb43f7f24706c9d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- id: 主键, password 密码, last_login Django自动记录用户最后登录时间,。
- is_superuser 表明用户是否是超级用户(后台管理会用到)。
- username 用户名字段不要随便改动, email 邮箱, 
- is_staff 表示是否是员工(后台管理会用到)。
- is_active 用户是否是激活状态, date_joined 注册时间。

>我们需要扩展我们自己的需求字段。

个人中心页面中:

![mark](http://upload-images.jianshu.io/upload_images/1779926-70a372bc2212db8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到我们还需要的有：

- 昵称: nickname
- 生日: birthday
- 性别: gender

User表的自定义方法可以查看django官方文档。
我们既想保留原有字段，又想有新字段。

users/models.py添加代码:

```python
from django.contrib.auth.models import AbstractUser

class UserProfile(AbstractUser):
    # 自定义的性别选择规则
    GENDER_CHOICES = (
        ("male", u"男"),
        ("female", u"女")
    )
    # 昵称
    nick_name = models.CharField(max_length=50, verbose_name=u"昵称", default="")
    # 生日，可以为空
    birthday = models.DateField(verbose_name=u"生日", null=True, blank=True)
    # 性别 只能男或女，默认女
    gender = models.CharField(
        max_length=5,
        verbose_name=u"性别",
        choices=GENDER_CHOICES,
        default="female")
    # 地址
    address = models.CharField(max_length=100, verbose_name="地址", default="")
    # 电话
    mobile = models.CharField(max_length=11, null=True, blank=True)
    # 头像 默认使用default.png
    image = models.ImageField(
        upload_to="image/%Y/%m",
        default=u"image/default.png",
        max_length=100
    )
    
    # meta信息，即后台栏目名
    class Meta:
        verbose_name = "用户信息"
        verbose_name_plural = verbose_name

    # 重载Unicode方法，打印实例会打印username，username为继承自abstractuser
    def __unicode__(self):
        return self.username
```

点进`AbstractUser`可以看到这个models里面就有我们默认表的那些字段。

因为Image字段需要用到`pillow`所以需要安装该库

```
pip install pillow
```
**注意：CharField必须有max_length, Imagefield实际也是charfield所以也要有max_length**

#### setting设置INSTALLED_APPS & AUTH_USER_MODEL。

- INSTALLED_APPS注册app

```
'users'
```

- 重载AUTH_USER_MODEL

```
# 此处重载是为了使我们的UserProfile生效
AUTH_USER_MODEL = "users.UserProfile"
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-fae669e967b33db6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击`Tools 菜单下 Run manage.py Task `

```
makemigrations users
migrate users
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-5a89f222454e2dfe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>上图中可以看到数据库做出的改动。输入: yes

### 进入Navicat进行验证

![mark](http://upload-images.jianshu.io/upload_images/1779926-7fecdd923d3224d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图可以看到我们的表已经生成成功。

### 附加Python3下不同与报错：

将Unicode方法改为str方法
```
    # 重载__str__方法，打印实例会打印username，username为继承自AbstractUser
    def __str__(self):
        return self.username
```


```
django.db.migrations.exceptions.InconsistentMigrationHistory: Migration
admin.0001_initial is applied before its dependency users.0001_initial on
database 'default'
```

解决方案:

```
删除数据库中 除了auth_user的其他表
```

然后执行命令:

```
makemigrations
migrate
makemigrations users
migrate users
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-6c625648b91d639e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>共11张表，同期django1.9.8会产生13张表

![mark](http://upload-images.jianshu.io/upload_images/1779926-ac6f081fc4a4ebe8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>我推测是因为在django2.0版本中。我们如果自定义了userProfile并且在setting中进行了设置。那么auth_user将不再拥有多的表。

下次不要再初始化时执行makemigrations & migrate。当我们设计userProfile完成之后再执行。

本小节完成对应commit:

>完成USerProfile models书写。makemigrations & migrate 建表成功。对应4-4
