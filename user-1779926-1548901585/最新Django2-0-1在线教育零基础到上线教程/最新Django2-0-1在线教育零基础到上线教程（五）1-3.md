## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

## django admin介绍

上一章我们进行了需求分析和数据库设计。本章我们来快速搭建一个可用的后台管理系统。

后台管理系统特点：

- 权限管理
- 少前端样式。(样式一般不是很看重)，
- 快速开发

django的后台管理系统是一套智能的管理系统。
django的杀手锏之一就是admin管理系统。

admin在项目新建时就已经为我们生成好了。

![mark](http://upload-images.jianshu.io/upload_images/1779926-5aa06c8c79d15cbf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Django的admin也是一个app，在我们新建项目时就创建好了。
而且会自动在url中配置好了链接。

![mark](http://upload-images.jianshu.io/upload_images/1779926-f37a7487342b26ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

访问:http://127.0.0.1:8000/admin/

可以看到admin的登录窗口。

Django是不会自动生成admin的用户的，需要我们自己去命令生成。

### createsuperuser 

点击`Tools 菜单下 Run manage.py Task `

```
createsuperuser 
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-46a2e0614f3cdf73.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

输入自己的用户名密码。

报错:

```
django.db.utils.DataError: (1406, "Data too long for column 'gender' at row 1")
```

>gender中female是6位。而我们最大长度只有5.

![mark](http://upload-images.jianshu.io/upload_images/1779926-2a3359e969bab10a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

修改后

```
makemigrations users
migrate users
```

然后重新`createsuperuser`

使用自己定义的用户名密码可以登进系统。

![mark](http://upload-images.jianshu.io/upload_images/1779926-d85de4d08045b8f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>默认是用户名 + 密码。后面会讲到如何实现用户名 或 邮箱和密码登录。

### 修改setting中对应语言，时区，以及数据库写入时间。

修改

```
# 语言改为中文
LANGUAGE_CODE = 'zh-hans'

# 时区改为上海
TIME_ZONE = 'Asia/Shanghai'
# 数据库存储使用时间，True时间会被存为UTC的时间
USE_TZ = False
```

点击运行可以看到如下图被换成汉语的效果:

![mark](http://upload-images.jianshu.io/upload_images/1779926-0b3b8c5b3973944c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意: django 2.0.1 并不会看到汉化后的默认页面。只有admin被汉化了。

组对应数据表: auth_group

在Django的admin中可以把上章的表都注册进来。对于表进行任意的增删改查。

默认其实会把user也注册进来的，但是因为我们通过userProfile覆盖了user。所以没有显示。

### 注册UserProfile进来

users/admin.py:

```python
# encoding: utf-8

# 因为同一个目录，所以可以直接.models
from .models import UserProfile


# 写一个管理器:命名, model+Admin
class UserProfileAdmin(admin.ModelAdmin):
    pass


# 将UserProfile注册进我们的admin中, 并为它选择管理器
admin.site.register(UserProfile,UserProfileAdmin)
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-02dff7c386326065.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>可以看到我们的用户信息就注册进来了。

`USERS` 是用户所在表名称。

![mark](http://upload-images.jianshu.io/upload_images/1779926-526f82bed5087fe1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

进入页面可以看到Django为我们把每个不同类型的字段生成了不同的前端样式。

![mark](http://upload-images.jianshu.io/upload_images/1779926-bc1a4481faaca573.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>Django会自动帮我们把密码加密，而且不能反解。单向性。

如果出现错误, 可能是`initial`文件在我们拖入apps时路径被改变。之后我们添加了环境变量, 前面再加上apps就会报错。

这时把`initial.py` 中路径进行修改。

错误2：

```
新增用户信息提示：
Cannot add or update a child row: a foreign key constraint fails
(1452, 'Cannot add or update a child row: a foreign key constraint fails 
`mxonline`.`django_admin_log`, CONSTRAINT `django_admin_log_user_id_c564eba6_fk_auth_user_id` 
FOREIGN KEY (`user_id`) REFERENCES `auth_user` (`id`))')
```

>解决方案1: 不用解决，之后换Xadmin就好了。

解决方案2: 在setting的databases中添加以下代码取消外键检查

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'mxonline2',
        'USER': 'root',
        'PASSWORD': '你的密码',
        'HOST':'127.0.0.1',
        'OPTIONS': {
          "init_command": "SET foreign_key_checks=0;",
      }

    },

}

```
![mark](http://upload-images.jianshu.io/upload_images/1779926-e0d3c69dd47e7e99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

实验成功为了不影响后面，把options删除

本小节结束对应commit:

>admin中添加管理器&注册。时区,语言,utc(False).数据库中选项参数。female的长度修改, createsuperuser.对应5-1

## xadmin的安装

一套基于admin, 比admin更强大的系统。

1. 通过pip安装

```
pip install xadmin
```

### Python3 & Django2.0.1安装官方适配Django2.0的包

```
pip install git+git://github.com/sshwsfc/xadmin.git@django2
```

xadmin可以把我们的后台做的很强大，可扩展。

![mark](http://upload-images.jianshu.io/upload_images/1779926-8bfb6251451e1e9e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到它同时下载了很多其他依赖包。

### 注册Xadmin 与 crispy-forms

Mxonline2/settings.py的INSTALLED_APPS中

```
    'xadmin',
    'crispy_forms'
```

然后把urls中默认admin指向Xadmin


```python
# 导入x admin，替换admin
import xadmin
urlpatterns = [
    url(r'^xadmin/', xadmin.site.urls),
]
```

Python3 Django2.0.1 的url的配置中

```
path('xadmin/', xadmin.site.urls),
```

**注意：Django 2.0.1中不需要加`r`也不需要加`^`**

将我们原来写的user/admin.py中代码注释掉。

此时直接运行项目会报错，因为我们Xadmin的默认数据表并没有migarte

```
ProgrammingError: (1146, "Table 'mxonline2.xadmin_usersettings' doesn't exist")
[09/Jan/2018 06:40:27] "GET /xadmin/ HTTP/1.1" 500 150414
```

点击`Tools 菜单下 Run manage.py Task `

```
makemigrations
migrate
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-dc6c9b59b839366a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>可以看到已经被应用成功。

前往Navicat进行验证。

![mark](http://upload-images.jianshu.io/upload_images/1779926-12561778ec508abe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>可以看到新增的表。

Xadmin的后台采用的是bootstrap。

![mark](http://upload-images.jianshu.io/upload_images/1779926-ad5b3b9e64d632a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>后面我们会介绍如何制作插件

### 源码安装：

github: https://github.com/sshwsfc/xadmin

下载或`git clone`将源码下载到本地。

![mark](http://upload-images.jianshu.io/upload_images/1779926-05b8537d841e2be0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>解压后将Xadmin文件夹复制到我们的项目中。

![mark](http://upload-images.jianshu.io/upload_images/1779926-d040c9780a5df162.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### Python3版本源码安装:与url配置不同

```
git clone -b django2 https://github.com/sshwsfc/xadmin.git
```

其余操作一样。



### 新建extra_apps，并在setting中注册地址

新建new package:  extra_apps

>使用该目录存放我们的第三方插件，将Xadmin移入。
右键mark为SourceRoot, 但是这时候cmd下回报错。

所以在setting.py中加入。

```python
sys.path.insert(0,os.path.join(BASE_DIR, 'extra_apps'))
```

>因为我们的source目录已经有Xadmin了，就不会再去系统环境中找了。这时候卸载我们的Xadmin。

```
workon mxonline2
pip uninstall xadmin
```

但是他的依赖包我们还需要，所以只需要卸载Xadmin。此时我们运行会报错

```
    from future.utils import iteritems
ImportError: No module named future.utils
```

安装必要的包:

```
pip install future
pip install six
pip install httplib2
pip install django-import-export
```

此时又可以成功运行了

![mark](http://upload-images.jianshu.io/upload_images/1779926-2c29d7a165212d68.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

日志记录：后台管理人员做的操作都会生成一条记录。

源码安装优点:

- xadmin新特性
- 对于源码进行自己的修改。



本小节结束对应commit:

>Xadmin的安装与源码安装，配置setting中extra_apps. 对应5-2

## users app 的model注册

### 遗留问题: django2.0.1使用xadmin时。如验证码等带dateTimefield区域出错。
xadmin/widgets.py

![mark](http://upload-images.jianshu.io/upload_images/1779926-d9dcbf92b6597bfc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
input_html = [ht for ht in super(AdminSplitDateTime, self).render(
    name, value, attrs).split('/><') if ht != '']
        if (len(input_html) > 1):
            input_html[0] = input_html[0] + "/>"
            input_html[1] = "<" + input_html[1]
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-8994c08336be0362.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>此时可以看到已经运行正常

### 真正开始

Xadmin是基于Django的admin来开发的，所以Xadmin也继承了许多admin的用法。

- 比如: models的注册。

UserProfile已经被自动注册进去了，我们从验证码开始注册。

我们需要新建一个`adminx.py`文件，Xadmin会自动搜寻这种命名的文件。

### 新建py文件的初始化模板

![mark](http://upload-images.jianshu.io/upload_images/1779926-a3dd75931f8532c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

新建users/adminx.py：

```
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/1/9 0009 08:02'

import xadmin

from .models import EmailVerifyRecord


# 创建admin的管理类,这里不再是继承admin，而是继承object
class EmailVerifyRecordAdmin(object):
    pass


xadmin.site.register(EmailVerifyRecord, EmailVerifyRecordAdmin)
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-31760622e7bec026.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>可以看到这时候访问已经有邮箱验证码了。

邮箱验证码这几个字就是我们代码中Meta中verbose_name定义的:

```python
    class Meta:
        verbose_name = "邮箱验证码"
        verbose_name_plural = verbose_name
```

`verbose_name_plural`是`verbose_name`的复数形式。

字段的verbose_name会直接显示在后台。`sendtype`和`sendtime`没有设置所以直接显示了英文。

![mark](http://upload-images.jianshu.io/upload_images/1779926-2fb1460d1674d429.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>可以看到我们添加验证码成功。注意:上节版本中我们进行了: makemigaration & migrate。
但是它是pip安装的Xadmin的数据表生成。我们卸载之后，源码安装需要重新运行进行数据迁移。(django需要通过app文件夹下的init文件来记录表的更改记录，pip的都卸了，所以就没法找到了)

会报错:

>Xadmin_log不存在错误。只需要运行这两条命令即可。

![mark](http://upload-images.jianshu.io/upload_images/1779926-83120b99fa4666c9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 解决后台部分英文显示

>全部models中字段自行添加verbose_name

![mark](http://upload-images.jianshu.io/upload_images/1779926-bcfa0c58625ccc7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里就不贴出来了，自行检查都加上(没写出的请自行修改全部加上verbose_name)。

#### 解决EmailVerifyRecord object显示

>**全部**(没写出的请自行修改)model，py2:重载`__unicode` py3:重载`__str__`

```python
    # 重载Unicode方法使后台不再直接显示object
    def __unicode__(self):
        return '{0}({1})'.format(self.code,self.email)
```

>上面代码是python的自身基础语法。

![mark](http://upload-images.jianshu.io/upload_images/1779926-9a2d02e31100977a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-49bbd5ccbb78da73.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 配置显示列

![mark](http://upload-images.jianshu.io/upload_images/1779926-68fe48f93c7c3d1d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

users/adminx.py的管理器中设置list_display:

```
# 创建admin的管理类,这里不再是继承admin，而是继承object
class EmailVerifyRecordAdmin(object):
    # 配置后台我们需要显示的列
    list_display = ['code', 'email','send_type', 'send_time']
```

>list_display可以使用列表或元祖，建议使用列表。否则元组只有一个元素，忘记加逗号就会报错。

![mark](http://upload-images.jianshu.io/upload_images/1779926-8f1035058ba71256.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

选择框的生成是因为我们加上了`choices`

#### 配置搜索searchfield

users/adminx.py的管理器中EmailVerifyRecordAdmin添加

```python
    # 配置搜索字段,不做时间搜索
    search_fields =  ['code', 'email','send_type']
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-07df1fb711f96f68.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

再添加一条数据验证搜索功能

![mark](http://upload-images.jianshu.io/upload_images/1779926-52fe52b84bf3cfa2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-8fff5e8274e44db8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### xadmin导出csv中文乱码解决

![mark](http://upload-images.jianshu.io/upload_images/1779926-6207b26cfc631271.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>将`charset=utf-8` 改为`charset=gbk`

#### xadmin导出xml报错

```
TypeError at /xadmin/users/emailverifyrecord/
unicode argument expected, got 'str'
```
>io.StringIO这个库新版本的python3直接往这个库中加入了一些新的内容，使得该库在Python2.7中较为混乱。


![mark](http://upload-images.jianshu.io/upload_images/1779926-d745ef46b08ce8a8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>将StringIo变为BytesIO

#### 通过时间筛选字段。

users/adminx.py的管理器中EmailVerifyRecordAdmin添加

```
    # 配置筛选字段
    list_filter =  ['code', 'email','send_type', 'send_time']
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-41b8cd756a0aa17a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### Django的admin, Xadmin和其他系统区别

>不像php等其他语言是一个功能模块一个功能设计的。
Django是对于每张表增删改查的管理器，我们可以在增删改成的基础上加上我们自己的后台逻辑。
因此某种程度可以说他是不依赖于具体业务的。不管啥系统后台都是由表组成。

不依赖于后台逻辑，又可以加上逻辑。

### user/models的注册

users/adminx.py中

```
# 创建banner的管理类
class BannerAdmin(object):
    list_display = ['title', 'image', 'url','index', 'add_time']
    search_fields = ['title', 'image', 'url','index']
    list_filter = ['title', 'image', 'url','index', 'add_time']
```

```
# 将model与admin管理器进行关联注册
xadmin.site.register(Banner, BannerAdmin)
```

此时后台页面。

![mark](http://upload-images.jianshu.io/upload_images/1779926-46b033174d6d3dee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>可以自行测试轮播图是否可以新建成功。

本小节结束对应commit:

>usersmodels三张表注册进xadmin, 配置搜索过滤展示字段，修复xadmin导出xml错误,导出csv乱码，Unicode重载。对应5-3

py3(django2.0.1):

>usersmodels三张表注册进xadmin, 配置搜索过滤展示字段，修复xadmin导出csv乱码，修复django2.0.1的indexError, str重载。对应5-3
