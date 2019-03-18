## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

>使用Python3.6与Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

项目支持支付宝支付(暂不支持微信支付),支持手机短信验证码注册， 支持第三方登录。集成了sentry错误监控系统。

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节内容: model设计与资源引入

## 资源初始化

数据库设计，数据表结构

### 新建虚拟环境

```
mkvirtualenv -p=D:\softEnvDown\Anaconda2\envs\py36\python.exe mxshop36
```

- 安装django 和 django framework(基于django)

http://www.django-rest-framework.org/

>已经支持django2.0了。

```
pip install djangorestframework
pip install django
pip install markdown       # Markdown support for the browsable API.
pip install django-filter  # Filtering support
```

新建django项目的时候必须指明一个里面有django的环境。

![mark](http://upload-images.jianshu.io/upload_images/1779926-584819f1af5becf1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 新建django项目

![mark](http://upload-images.jianshu.io/upload_images/1779926-039df5af739365ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

指定virtualenv 新建目录

```
path 中workon_home
```

- 新建app: `users`

```
startapp users
```

- setting中注册users

后面分析的时候我们会来看源码

![mark](http://upload-images.jianshu.io/upload_images/1779926-f600d00197632581.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击run，查看是否启动成功。

![mark](http://upload-images.jianshu.io/upload_images/1779926-30a376235e745deb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到我们成功的安装运行。

### 安装一些必要的包

database 默认sqllite

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'vue_shop',
        'USER': 'root',
        'PASSWORD': 'tp158917',
        'HOST':'127.0.0.1',
        'OPTIONS':{'init_command': 'SET storage_engine=INNODB'},
    }
}

```

mysql的数据库引擎有InnoDB 和 myisam

>第三方登录的库要求使用innodb 否则会migration出错。

![mark](http://upload-images.jianshu.io/upload_images/1779926-7786b3ec11423e1a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用Navicat新建数据库

安装mysql驱动

```
pip install mysqlclient
```

此时运行抛出异常:

```
super(Connection, self).__init__(*args, **kwargs2)
django.db.utils.OperationalError: (1193, "Unknown system variable 'storage_engine'")
```

修改为：

```
"OPTIONS":{"init_command":"SET default_storage_engine=INNODB;"}
```

可能的出错:

>前往网址下载。本地安装。

https://www.lfd.uci.edu/~gohlke/pythonlibs/

- 安装图片处理包

```
pip install pillow
```

>上传图片，处理图片。

## 整理项目结构

- apps包保存所有的app，将user移入
- extra_apps 包保存本地安装的源码，修改源码

新建文件夹

- media 存放上传的图
- db_tools 数据库的初始化等

右键将apps & extra_apps mark成为sources root

```
import sys

sys.path.insert(0,BASE_DIR)
sys.path.insert(0,os.path.join(BASE_DIR, 'apps'))
sys.path.insert(0,os.path.join(BASE_DIR, 'extra_apps'))
```

## User models 设计

通过需求分析设计数据表。看一下系统长什么样子

```
cnpm install
npm run dev
```

首页分析有哪些实体，需要新建哪些app来完成。

![mark](http://upload-images.jianshu.io/upload_images/1779926-21a5c0583034d194.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

导航栏包括商品大类

![mark](http://upload-images.jianshu.io/upload_images/1779926-24e46d9b953955d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

更全的商品大类，大类的下面有小类。

左边是商品类别，价格筛选搜索。排序，分页。

商品轮播图，促销价格，富文本编辑。热卖商品显示到详情页。

app设计的思想，归类。

goods 商品管理 交易管理

用户的操作凌驾于app之上，可以避免循环引入。用户的收藏，用户的操作。

一般根据经验划分。

```
startapp goods
startapp trade
startapp user_operation
```

并将这三个app拖入apps中。

![mark](http://upload-images.jianshu.io/upload_images/1779926-5a14e90a97b117fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时的项目结构

### 针对app设计model。

自带的user一般都是无法满足要求的，需要我们自行进行扩展。

users/models.py:

```
from datetime import datetime

from django.db import models
from django.contrib.auth.models import AbstractUser

# Create your models here.


class UserProfile(AbstractUser):
    """
    用户表，新增字段如下
    """
    GENDER_CHOICES = (
        ("male", u"男"),
        ("female", u"女")
    )
    # 用户注册时我们要新建user_profile 但是我们只有手机号
    name = models.CharField(max_length=30, null=True, blank=True, verbose_name="姓名")
    # 保存出生日期，年龄通过出生日期推算
    birthday = models.DateField(null=True, blank=True, verbose_name="出生年月")
    gender = models.CharField(max_length=6, choices=GENDER_CHOICES, default="female", verbose_name="性别")
    # mobile = models.CharField(null=True, blank=True, max_length=11, verbose_name="电话")
    mobile = models.CharField(max_length=11, verbose_name="电话")
    email = models.EmailField(max_length=100, null=True, blank=True, verbose_name="邮箱")

    class Meta:
        verbose_name = "用户"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.username


class VerifyCode(models.Model):
    """
    短信验证码,回填验证码进行验证。可以保存在redis中
    """
    code = models.CharField(max_length=10, verbose_name="验证码")
    mobile = models.CharField(max_length=11, verbose_name="电话")
    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = "短信验证码"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.code
```

注意：datetime.now 不加直接调用。

此时我们的userprofile并没有生效

setting中

```
# 此处重载是为了使我们的UserProfile生效
AUTH_USER_MODEL = "users.UserProfile"
```

## goods models设计

- 大类 小类 更小类

三个类之间有其从属关系。

- Goods 商品类别

>注意：以下代码中一些暂时没有接触到的参数在以后会介绍

- help_text: 是生成接口测试文档时会用到的。
- related_name: 在后面进行查询的时候会用到

在教育平台中我们的从属关系通过外键来完成的。

这里有三个相关的类，是否意味我们要建三个model。model之间有从属的外键关系。

但是我们如果要去做一个无限分类，即可扩展。

>分级别。目录树等都可以应用这个。

1. 商品的多级分类

![mark](http://upload-images.jianshu.io/upload_images/1779926-12aca64ad2e17a89.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
class GoodsCategory(models.Model):
    """
    商品多级分类
    """
    CATEGORY_TYPE = (
        (1, "一级类目"),
        (2, "二级类目"),
        (3, "三级类目"),
    )

    name = models.CharField(default="", max_length=30, verbose_name="类别名", help_text="类别名")
    code = models.CharField(default="", max_length=30, verbose_name="类别code", help_text="类别code")
    desc = models.TextField(default="", verbose_name="类别描述", help_text="类别描述")
    # 设置目录树的级别
    category_type = models.IntegerField(choices=CATEGORY_TYPE, verbose_name="类目级别", help_text="类目级别")
    parent_category = models.ForeignKey("self", null=True, blank=True, verbose_name="父类目级别", help_text="父目录",
                                        related_name="sub_cat")
    is_tab = models.BooleanField(default=False, verbose_name="是否导航", help_text="是否导航")
    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

        class Meta:
        verbose_name = "商品类别"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.name
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-ff7fabb04895b23c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>商品的某一个类下又会有多个宣传的商标

```python
class GoodsCategoryBrand(models.Model):
    """
    某一大类下的宣传商标
    """
    category = models.ForeignKey(GoodsCategory, related_name='brands', null=True, blank=True, verbose_name="商品类目")
    name = models.CharField(default="", max_length=30, verbose_name="品牌名", help_text="品牌名")
    desc = models.TextField(default="", max_length=200, verbose_name="品牌描述", help_text="品牌描述")
    image = models.ImageField(max_length=200, upload_to="brands/")
    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = "宣传品牌"
        verbose_name_plural = verbose_name
        db_table = "goods_goodsbrand"

    def __str__(self):
        return self.name
```


- 商品，默认生成的id是数据库做关联，查询用的，但是实际商品还有自己的sn码。

将xadmin和ueditor拷贝进extra apps中。

![mark](http://upload-images.jianshu.io/upload_images/1779926-2e26f388a7af4956.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

settings中install app中加入

```
 'users',
    'goods',
    'user_operation',
    'trade',
    'xadmin',
    'crispy_forms',
    'DjangoUeditor',
```

商品数据models

```python
from DjangoUeditor.models import UEditorField

class Goods(models.Model):
    """
    商品
    """
    category = models.ForeignKey(GoodsCategory, verbose_name="商品类目")
    goods_sn = models.CharField(max_length=50, default="", verbose_name="商品唯一货号")
    name = models.CharField(max_length=100, verbose_name="商品名")
    click_num = models.IntegerField(default=0, verbose_name="点击数")
    sold_num = models.IntegerField(default=0, verbose_name="商品销售量")
    fav_num = models.IntegerField(default=0, verbose_name="收藏数")
    goods_num = models.IntegerField(default=0, verbose_name="库存数")
    market_price = models.FloatField(default=0, verbose_name="市场价格")
    shop_price = models.FloatField(default=0, verbose_name="本店价格")
    goods_brief = models.TextField(max_length=500, verbose_name="商品简短描述")
    goods_desc = UEditorField(verbose_name=u"内容", imagePath="goods/images/", width=1000, height=300,
                              filePath="goods/files/", default='')
    ship_free = models.BooleanField(default=True, verbose_name="是否承担运费")
    goods_front_image = models.ImageField(upload_to="goods/images/", null=True, blank=True, verbose_name="封面图")
    is_new = models.BooleanField(default=False, verbose_name="是否新品")
    is_hot = models.BooleanField(default=False, verbose_name="是否热销")
    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = '商品'
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.name
```

>可能的改进，将运费的bool值改了，或添加一个字段邮费。

商品详情页轮播图models和首页轮播的商品图，为适配首页大图

![mark](http://upload-images.jianshu.io/upload_images/1779926-4be79c4e0ef1769b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
class GoodsImage(models.Model):
    """
    商品详情页轮播图
    """
    goods = models.ForeignKey(Goods, verbose_name="商品", related_name="images")
    image = models.ImageField(upload_to="", verbose_name="图片", null=True, blank=True)
    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = '商品轮播图'
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.goods.name


class Banner(models.Model):
    """
    首页轮播的商品图，为适配首页大图
    """
    goods = models.ForeignKey(Goods, verbose_name="商品")
    image = models.ImageField(upload_to='banner', verbose_name="轮播图片")
    index = models.IntegerField(default=0, verbose_name="轮播顺序")
    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = '首页轮播商品'
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.goods.name
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-ab6eb2bbfe8998a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

首页类别旁边的商品广告位。

搜索栏下方热搜词

```
class HotSearchWords(models.Model):
    """
    热搜词
    """
    keywords = models.CharField(default="", max_length=20, verbose_name="热搜词")
    index = models.IntegerField(default=0, verbose_name="排序")
    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = '热搜词'
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.keywords
```

## Trade交易的model设计

- 购物车 & 订单概念

1. 对于一个商品买多个，不会在购物车里显示多个，只会增加数量
2. 点击去结算，变成订单了

**前后端分离的项目，参数名要保持一致**

订单的基本信息存储在表OrderInfo

订单的订购商品存储在表ordergoods
一对多的关系。一个订单会有多个商品。

```python
class OrderInfo(models.Model):
    """
    订单信息
    """
    ORDER_STATUS = (
        ("TRADE_SUCCESS", "成功"),
        ("TRADE_CLOSED", "超时关闭"),
        ("WAIT_BUYER_PAY", "交易创建"),
        ("TRADE_FINISHED", "交易结束"),
        ("paying", "待支付"),
    )
    PAY_TYPE = (
        ("alipay", "成功"),
        ("wechat", "微信"),
    )

    user = models.ForeignKey(User, verbose_name="用户")
    # unique订单号唯一
    order_sn = models.CharField(max_length=30, null=True, blank=True, unique=True, verbose_name="订单编号")
    # 微信支付可能会用到
    nonce_str = models.CharField(max_length=50, null=True, blank=True, unique=True, verbose_name="随机加密串")
    # 支付宝支付时的交易号与本系统进行关联
    trade_no = models.CharField(max_length=100, unique=True, null=True, blank=True, verbose_name=u"交易号")
    # 以防用户支付到一半不支付了
    pay_status = models.CharField(choices=ORDER_STATUS, default="paying", max_length=30, verbose_name="订单状态")
    # 订单的支付类型
    pay_type = models.CharField(choices=PAY_TYPE, default="alipay", max_length=10, verbose_name="支付类型")
    post_script = models.CharField(max_length=200, verbose_name="订单留言")
    order_mount = models.FloatField(default=0.0, verbose_name="订单金额")
    pay_time = models.DateTimeField(null=True, blank=True, verbose_name="支付时间")

    # 用户的基本信息
    address = models.CharField(max_length=100, default="", verbose_name="收货地址")
    signer_name = models.CharField(max_length=20, default="", verbose_name="签收人")
    singer_mobile = models.CharField(max_length=11, verbose_name="联系电话")

    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = u"订单基本信息"
        verbose_name_plural = verbose_name

    def __str__(self):
        return str(self.order_sn)


class OrderGoods(models.Model):
    """
    订单内的商品详情
    """
    # 一个订单对应多个商品，所以添加外键
    order = models.ForeignKey(OrderInfo, verbose_name="订单信息", related_name="goods")
    # 两个外键形成一张关联表
    goods = models.ForeignKey(Goods, verbose_name="商品")
    goods_num = models.IntegerField(default=0, verbose_name="商品数量")

    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = "订单内商品项"
        verbose_name_plural = verbose_name

    def __str__(self):
        return str(self.order.order_sn)
```

## 用户操作的model设计

- 典型操作，用户对于商品进行收藏。
- 用户收货地址添加
- 用户留言

```python
class UserFav(models.Model):
    """
    用户收藏操作
    """
    user = models.ForeignKey(User, verbose_name="用户")
    goods = models.ForeignKey(Goods, verbose_name="商品", help_text="商品id")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = '用户收藏'
        verbose_name_plural = verbose_name

        # 未知
        unique_together = ("user", "goods")

    def __str__(self):
        return self.user.username


class UserAddress(models.Model):
    """
    用户收货地址
    """
    user = models.ForeignKey(User, verbose_name="用户" )
    province = models.CharField(max_length=100, default="", verbose_name="省份")
    city = models.CharField(max_length=100, default="", verbose_name="城市")
    district = models.CharField(max_length=100, default="", verbose_name="区域")
    address = models.CharField(max_length=100, default="", verbose_name="详细地址")
    signer_name = models.CharField(max_length=100, default="", verbose_name="签收人")
    signer_mobile = models.CharField(max_length=11, default="", verbose_name="电话")
    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = "收货地址"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.address


class UserLeavingMessage(models.Model):
    """
    用户留言
    """
    MESSAGE_CHOICES = (
        (1, "留言"),
        (2, "投诉"),
        (3, "询问"),
        (4, "售后"),
        (5, "求购")
    )
    user = models.ForeignKey(User, verbose_name="用户")
    message_type = models.IntegerField(default=1, choices=MESSAGE_CHOICES, verbose_name="留言类型",
                                      help_text=u"留言类型: 1(留言),2(投诉),3(询问),4(售后),5(求购)")
    subject = models.CharField(max_length=100, default="", verbose_name="主题")
    message = models.TextField(default="", verbose_name="留言内容", help_text="留言内容")
    file = models.FileField(upload_to="message/images/", verbose_name="上传的文件", help_text="上传的文件")
    add_time = models.DateTimeField(default=datetime.now, verbose_name="添加时间")

    class Meta:
        verbose_name = "用户留言"
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.subject
```

>tips: type是python中的关键词，请使用msg_type代替。


## migrations原理及表生成

- 前提: 将我们的app都放入了列表中。

此时运行migrations，我们会报错。future模块找不到之类。
因为我们拷贝的源码中没有安装依赖包


```
pip install git+git://github.com/sshwsfc/xadmin.git@django2
```

因为使用的django2.0.2最新版

所以需要对所有的外键关系加上删除时的操作，我这里为了方便统一
将删除时操作，设置为级联删除。

在setting中 `goods` 与 `goods.apps.GoodsConfig`是一样的。

```
makemigrations
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-c96d5196e786458c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运行`makemigrations` 他就会生成我们每次数据库变动的py脚本。

![mark](http://upload-images.jianshu.io/upload_images/1779926-32fc5d6aa6b28a10.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

它只是用来生成这个的。真正的生成数据表必须运行`migrate`

>这个命令才会去执行py脚本去数据库生成数据表。

```
migrate appname
```

就只会生成这个app表的记录。什么都不填会生成所有。

![mark](http://upload-images.jianshu.io/upload_images/1779926-2c8e3844f09d0ef6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以在Navicat中查看到我们生成的表

不再生成auth user表，而是生成userProfile表。而admin只会傻傻的去找auth user就会报错

1. 坑：修改某一张表字段。

会多一个文件。

2. 如何检测该运行哪个文件呢？

>django_migration这张表记录了之前运行过的文件。查询到运行过了就不会运行了。

![mark](http://upload-images.jianshu.io/upload_images/1779926-62cc0767e53f3f63.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果出现问题，将goods相关表删除，将migration中goods的记录删除，重新
运行 migrate

不要Navicat和migrate混用。

## xadmin后台管理系统配置

xadmin文件配置显示字段等。

每个app一个adminx文件。

```python
import xadmin
from .models import UserFav, UserLeavingMessage, UserAddress


class UserFavAdmin(object):
    list_display = ['user', 'goods', "add_time"]


class UserLeavingMessageAdmin(object):
    list_display = ['user', 'message_type', "message", "add_time"]


class UserAddressAdmin(object):
    list_display = ["signer_name", "signer_mobile", "district", "address"]

xadmin.site.register(UserFav, UserFavAdmin)
xadmin.site.register(UserAddress, UserAddressAdmin)
xadmin.site.register(UserLeavingMessage, UserLeavingMessageAdmin)
```

```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/2/13 0013 20:26'

import xadmin
from .models import Goods, GoodsCategory, GoodsImage, GoodsCategoryBrand, Banner, HotSearchWords
from .models import IndexAd


class GoodsAdmin(object):
    list_display = ["name", "click_num", "sold_num", "fav_num", "goods_num", "market_price",
                    "shop_price", "goods_brief", "goods_desc", "is_new", "is_hot", "add_time"]
    search_fields = ['name', ]
    list_editable = ["is_hot", ]
    list_filter = ["name", "click_num", "sold_num", "fav_num", "goods_num", "market_price",
                   "shop_price", "is_new", "is_hot", "add_time", "category__name"]
    style_fields = {"goods_desc": "ueditor"}

    class GoodsImagesInline(object):
        model = GoodsImage
        exclude = ["add_time"]
        extra = 1
        style = 'tab'

    inlines = [GoodsImagesInline]


class GoodsCategoryAdmin(object):
    list_display = ["name", "category_type", "parent_category", "add_time"]
    list_filter = ["category_type", "parent_category", "name"]
    search_fields = ['name', ]


class GoodsBrandAdmin(object):
    list_display = ["category", "image", "name", "desc"]

    def get_context(self):
        context = super(GoodsBrandAdmin, self).get_context()
        if 'form' in context:
            context['form'].fields['category'].queryset = GoodsCategory.objects.filter(category_type=1)
        return context


class BannerGoodsAdmin(object):
    list_display = ["goods", "image", "index"]


class HotSearchAdmin(object):
    list_display = ["keywords", "index", "add_time"]


class IndexAdAdmin(object):
    list_display = ["category", "goods"]


xadmin.site.register(Goods, GoodsAdmin)
xadmin.site.register(GoodsCategory, GoodsCategoryAdmin)
xadmin.site.register(Banner, BannerGoodsAdmin)
xadmin.site.register(GoodsCategoryBrand, GoodsBrandAdmin)

xadmin.site.register(HotSearchWords, HotSearchAdmin)
xadmin.site.register(IndexAd, IndexAdAdmin)
```

```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/2/14 0014 01:16'


import xadmin
from .models import UserFav, UserLeavingMessage, UserAddress


class UserFavAdmin(object):
    list_display = ['user', 'goods', "add_time"]


class UserLeavingMessageAdmin(object):
    list_display = ['user', 'message_type', "message", "add_time"]


class UserAddressAdmin(object):
    list_display = ["signer_name", "signer_mobile", "district", "address"]

xadmin.site.register(UserFav, UserFavAdmin)
xadmin.site.register(UserAddress, UserAddressAdmin)
xadmin.site.register(UserLeavingMessage, UserLeavingMessageAdmin)
```

```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/2/14 0014 01:17'


import xadmin
from xadmin import views
from .models import VerifyCode


class BaseSetting(object):
    enable_themes = True
    use_bootswatch = True


class GlobalSettings(object):
    site_title = "mtianyan慕课小店"
    site_footer = "shop@mxonline.cn"
    # menu_style = "accordion"


class VerifyCodeAdmin(object):
    list_display = ['code', 'mobile', "add_time"]


xadmin.site.register(VerifyCode, VerifyCodeAdmin)
xadmin.site.register(views.BaseAdminView, BaseSetting)
xadmin.site.register(views.CommAdminView, GlobalSettings)
```

xadmin配置url

```
    path('xadmin/', xadmin.site.urls),
```

```
createsuperuser

```

```
pip install xlwt
```
修改app的英文名称

![mark](http://upload-images.jianshu.io/upload_images/1779926-dde132aa1334b570.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 修改setting中app名称为补全名称

```
	'users.apps.UsersConfig',
    'goods.apps.GoodsConfig',
    'trade.apps.TradeConfig',
    'user_operation.apps.UserOperationConfig',
```

然后在apps中添加

```python
from django.apps import AppConfig


class UsersConfig(AppConfig):
    name = 'users'
    verbose_name = "用户管理"
```

配置富文本:

```
    # 富文本相关url
    path('ueditor/', include('DjangoUeditor.urls')),
```

## 导入商品类别数据

将数据进行填充。手动录数据太慢了。

分类很多，商品很多。

dbtools中有data和导入的脚本。

图片复制进media

为了让大家进行更方便的修改保持字段一致

**知识点:单独使用django的model**

![mark](http://upload-images.jianshu.io/upload_images/1779926-b065db6afb801546.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-0fc5b89099041d2a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

两条线两个sub画出三个类

访问图片设置

```
# 设置上传文件，图片访问路径
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

```
    # 处理图片显示的url,使用Django自带serve,传入参数告诉它去哪个路径找，我们有配置好的路径MEDIAROOT
    re_path('media/(?P<path>.*)', serve, {"document_root": MEDIA_ROOT }),
```

小数据自己添加。
