## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

十三:  xadmin进阶开发

# xadmin的进阶开发

## static目录问题。

xadmin的static文件在自己的目录下所以我们找不到

1- url下的static路由配置注释掉
2- setting中的StaticRoot注释掉

![mark](http://myphoto.mtianyan.cn/blog/180115/blD3Cgd9jG.png?imageslim)

```
from xadmin.plugins.auth import UserAdmin

class UserProfileAdmin(UserAdmin):
    pass

xadmin.site.register(UserProfile,UserProfileAdmin)

from django.contrib.auth.models import User
xadmin.site.unregister(User)
```

点进UserProfileAdmin中可以看官方是怎么实现布局的，我们可以重载

get_form_layout

```
自定义的显示方式
```

get_user_model 获取用户: User = 

django权限赋值方式：赋给用户。没一个表的增删改查。

auth_permission 组权限

## 自定义icon

model_icon = 'fa fa-group'

xadmin/static/xadmin/vendor/font-awesome

官网下载然后替换css和font

## 默认排序
adminx中

```
    ordering = ['-click_nums']
    readonly_fields =['click_nums','fav_nums']
        exclude = ['fav_nums']
```

两个字段是冲突的。

## 下拉框搜索：

```
relfield_style = 'fk-ajax'
```

当有外键指向他，会以ajax方式加载

数据量过大时很有用

## inlines添加数据。

在一个页面直接完成章节。

```
# 课程直接添加章节
class LessonInline(object):
    model = Lesson
    extra = 0

        # 课程直接添加章节
    inlines = [LessonInline]
```

没法完成在章节中再嵌套视频
但是可以有多个inline。在添加课程时添加课程资源

## 一张表分两个model来管理

```python
class BannerCourse(Course):
    class Meta:
        verbose_name = "轮播课程"
        verbose_name_plural = verbose_name
        proxy = True

```

设置proxy = true会具有model的功能，但不会生成表

```python

# Course的admin管理器
class BannerCourseAdmin(object):
    list_display = [
        'name',
        'desc',
        'detail',
        'degree',
        'learn_times',
        'students']
    search_fields = ['name', 'desc', 'detail', 'degree', 'students']
    list_filter = [
        'name',
        'desc',
        'detail',
        'degree',
        'learn_times',
        'students']
    ordering = ['-click_nums']
    readonly_fields =['click_nums']
    exclude = ['fav_nums']
    # 课程直接添加章节
    inlines = [LessonInline,CourseResourceInline]
# 将管理器与model进行注册关联
xadmin.site.register(BannerCourse, BannerCourseAdmin)
```

admin中重载queryset方法。

```
    # 过滤列表中的数据
    def queryset(self):
        qs = super(BannerCourseAdmin, self).queryset()
        qs = qs.filter(is_banner=True)
        return qs
    # 过滤列表中的数据
    def queryset(self):
        qs = super(CourseAdmin, self).queryset()
        qs = qs.filter(is_banner=False)
        return qs
```

## xamdin其他常见功能：

### 可以在列表上快速修改内容、

```
  list_editable = [ 'degree','desc',]
```

### 自定义函数作为列

```
    def get_zj_nums(self):
        return self.lesson_set.all().count()
    get_zj_nums.short_description = "章节数"
```

### 显示自定义的html代码

```
      def go_to(self):
        from django.utils.safestring import mark_safe
        # 如果不mark safe。会对其进行转义
        return  mark_safe("<a href='http://blog.mtianyan.cn'>跳转</>")
    go_to.short_description = "跳转"

```


### xadmin的工具: refresh

xadmin/plugins/refresh.py

列表页定时刷新的工具

```
    refresh_times = [3,5]
```

### 字段联动

应用场景: 新增一门课程之后，courseorg的课程数+1

```
  def save_models(self):
        # 在保存课程的时候统计课程机构的课程数
        # 字段联动
        obj = self.new_obj
        # 新增课程还没有保存，统计的课程数少一个
        obj.save()
        # 必须确定存在。
        if obj.course_org is not None:
            # obj实际是一个course对象
            course_org = obj.course_org
            course_org.course_nums = Course.objects.filter(course_org = course_org).count()
            course_org.save()

```

不用在课程里面添加章节数。

## xadmin自行探究

- local 语言包
- migration 数据表的记录
- plugins 每一个后台页面都是一个plugin 插件机制
- static文件。js css
- template xadmin自己用到的html文件
- 对django admin的封装

### 下载源码包

添加插件使得可以识别ueditor field

记得在init中注册。

![mark](http://myphoto.mtianyan.cn/blog/180115/jIJ1A5EijI.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180115/kF99c72b0K.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180115/lcH1c4Bb11.png?imageslim)

关闭自动转义。

## 导入excel

1. 如何注入导入excel代码到菜单
2. 如何只在课程列表显示
3. 如何接收文件对文件进行处理


![mark](http://myphoto.mtianyan.cn/blog/180115/H92HC8eJ1D.png?imageslim)

adminx 文件中的变量覆盖插件内部变量。

只需要重载`block_top_toolbar`，就会把这个放到页面里面
默认使用bootstrap

`init`确定是否启用插件，

```
    def post(self, request, *args , **kwargs):
        if 'excel' in request.FILES:
            pass
```

adminx中重写post方法。上传文件会被放在`request.FILES`中。

中间pass步骤不管做什么事情，都要最后return父类的

```
return super(CourseAdmin, self).post(request, args, kwargs)
```
