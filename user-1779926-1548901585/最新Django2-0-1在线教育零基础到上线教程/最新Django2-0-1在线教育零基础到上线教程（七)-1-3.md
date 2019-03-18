## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

## 7-1 django templates模板继承1

- 机构可以筛选类别
- 机构可以根据所在地区进行分类

右侧我要学习功能: form表单提交
右下：授课机构排名

页面头部与底部为全局头和全局底部。

### Django template 共用头部底部机制

将head和foot放在两个html中，然后在写其他需要这两个部分的页面时include进来。

Django也是支持include机制的。

### include的问题

include的进来的死页面，这时候该怎么办？

解决这种问题：进行模板的继承机制。定义一个父类的框架，子类可以替换其中一部分block，子类只需要重写自己需要改变的block。

### template中新建base.html

将课程机构列表页。orglist拷贝进template目录

将orglist内容替换base内容。

![mark](http://upload-images.jianshu.io/upload_images/1779926-0269443b69f80570.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将div收起来

![mark](http://upload-images.jianshu.io/upload_images/1779926-5f1ccbc5794f87d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


loadstaticfiles & 修改静态文件路径为static

>这个步骤做过太多遍了，自行完成。耐心就行了。

### 定义父模板: 包含head & footer


title应该是可以被子页面替换的所以要包起来。

![mark](http://upload-images.jianshu.io/upload_images/1779926-9a1dbc2dbfe1fa24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

css有共用的部分，也有可以被子页面替换的部分。

![mark](http://upload-images.jianshu.io/upload_images/1779926-b6a74b2bb63653fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

js同理

![mark](http://upload-images.jianshu.io/upload_images/1779926-af7f46dd38bd5f8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

面包屑是需要被各个页面自己替换的。

![mark](http://upload-images.jianshu.io/upload_images/1779926-0f082eed1f72b00b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将正文内容包起来；

![mark](http://upload-images.jianshu.io/upload_images/1779926-20d387c6bff07d56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时base页面就制作好了

## 7-2 开始orglist编写

第一步:清空所有内容

- 继承base页面

![mark](http://upload-images.jianshu.io/upload_images/1779926-3e2e112cf2c39350.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 覆盖父类的title

![mark](http://upload-images.jianshu.io/upload_images/1779926-392bc373f442d09a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 书写课程机构view
organization/views.py

```
# encoding: utf-8
from django.views.generic.base import View
# 处理课程机构列表的view
class OrgView(View):
    def get(self,request):
        return render(request, "org-list.html", { })

```

- Django2.0.1配置课程机构首页url

```
    # 课程机构首页url
    path('org_list/', OrgView.as_view(), name="org_list"),
```
- Django1.9.8配置url：

```
    # 课程机构首页url
    url(r'^org_list/$', OrgView.as_view(), name="org_list"),
```

### 修改面包屑

- base中只保留首页
- org中重写block custom_bread
- block之间没有先后顺序。

- 将base中block content拿到orglist重写

![mark](http://upload-images.jianshu.io/upload_images/1779926-29b97ffccbb18776.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 然后将base中block中间section删除掉

![mark](http://upload-images.jianshu.io/upload_images/1779926-cab5c7f004fcd239.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>orglist开始loadstaticfiles

`ctrl+d`快速删除

![mark](http://upload-images.jianshu.io/upload_images/1779926-a863b3cbdeec1219.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

页面的继承关系使得变量也可以直接用

>比如user中的form数据传递到register文件当中.如果register继承的是base页面。
base页面当中也是可以用这些数据的。`参数的向上传递`

每个request对象都会传递到html中来，如果继承了base，request也会向上传递到base。
base中就可以加入我们的逻辑: 用户是否登录等。

小节结束对应commit:

>完成Django templates的继承关系了解，机构列表展示页。对应7-1 & 2

## 7-3 课程机构列表页数据展示1

确定由后台传过来的动态数据:

授课机构列表本身， 授课机构的排名，所在地区(后台取出所有地区), 机构类别写成静态，因为一般不怎么变动。

在xadmin中添加城市信息，课程信息。

添加城市

![mark](http://upload-images.jianshu.io/upload_images/1779926-00b5504640872151.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

添加机构。

插播知识点：

![mark](http://upload-images.jianshu.io/upload_images/1779926-1a4a3bb3db05e42e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里指定的路径是一个相对路径

setting中要配置我们把文件存放在哪个根目录之下


```
# 设置我们上传文件的路径

MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

在项目根目录创建media文件夹

在后台上传图片

![mark](http://upload-images.jianshu.io/upload_images/1779926-a6681f24ccb16635.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-920a803d08e95abe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

修改机构信息中封面图为logo

自行添加十个课程机构

### models中添加机构类别


organization/models.py：

```python
class CourseOrg(models.Model):
    ORG_CHOICES =(
        ("pxjg", u"培训机构"),
        ("gx", u"高校"),
        ("gr", u"个人"),
    )

    name = models.CharField(max_length=50, verbose_name=u"机构名称")
    # 机构描述，后面会替换为富文本展示
    desc = models.TextField(verbose_name=u"机构描述")
    # 机构类别:
    category = models.CharField(max_length=20, choices=ORG_CHOICES, verbose_name=u"机构类别", default="pxjg")
```

修改了models之后做数据库的变动:

```python
makemigrations organization
migrate organization
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-6d38fe7fbc30dcd9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

完成之后打开Navicat进行验证：

![mark](http://upload-images.jianshu.io/upload_images/1779926-fc3fadf0c3bf8e27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到新增了。

### 完善我们的view

将列表里的静态数据变成后台获取的动态数据

organization/views.py

```python
from .models import CourseOrg, CityDict


class OrgView(View):
    def get(self,request):
        # 查找到所有的课程机构
        all_orgs = CourseOrg.objects.all()
        # 取出所有的城市
        all_citys = CityDict.objects.all()

        return render(request, "org-list.html", {
            "all_orgs":all_orgs,
            "all_citys": all_citys,
        })
```
