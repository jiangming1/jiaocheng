## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

## 剩余app model注册

### courses注册

新建courses/adminx.py:

```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/1/9 0009 20:10'

from .models import Course, Lesson, Video, CourseResource
import xadmin

# Course的admin管理器


class CourseAdmin(object):
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


class LessonAdmin(object):
    list_display = ['course', 'name', 'add_time']
    search_fields = ['course', 'name']

    # __name代表使用外键中name字段
    list_filter = ['course__name', 'name', 'add_time']


class VideoAdmin(object):
    list_display = ['lesson', 'name', 'add_time']
    search_fields = ['lesson', 'name']
    list_filter = ['lesson', 'name', 'add_time']


class CourseResourceAdmin(object):
    list_display = ['course', 'name', 'download', 'add_time']
    search_fields = ['course', 'name', 'download']
    # __name代表使用外键中name字段
    list_filter = ['course__name', 'name', 'download', 'add_time']


# 将管理器与model进行注册关联
xadmin.site.register(Course, CourseAdmin)
xadmin.site.register(Lesson, LessonAdmin)
xadmin.site.register(Video, VideoAdmin)
xadmin.site.register(CourseResource, CourseResourceAdmin)

```

![mark](http://upload-images.jianshu.io/upload_images/1779926-f3510deb0ab9f053.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>注意：对应后台显示英文的字段自行检查`verbosename`，自行加上。
>注意: py2下重载`__unicode__`方法，py3下重载`__str__`方法

如(注意缩进):

```python
    def __str__(self):
        return '《{0}》课程的章节 >> {1}'.format(self.course,self.name)
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-06d10922c497bf04.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`int`类型后台会生成如下图区间取值:

![mark](http://upload-images.jianshu.io/upload_images/1779926-53cf61feb2e0b659.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到有外键关系的会有一个小符号。

![mark](http://upload-images.jianshu.io/upload_images/1779926-88f99c2cae136db2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 注册机构app的adminx

新建`organization/adminx.py`:

```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/1/9 0009 21:01'

import xadmin

from .models import CityDict, CourseOrg, Teacher


# 机构所属城市名后台管理器
class CityDictAdmin(object):
    list_display = ['name', 'desc', 'add_time']
    search_fields = ['name', 'desc']
    list_filter = ['name', 'desc', 'add_time']


# 机构课程信息管理器
class CourseOrgAdmin(object):
    list_display = ['name', 'desc', 'click_nums', 'fav_nums','add_time' ]
    search_fields = ['name', 'desc', 'click_nums', 'fav_nums']
    list_filter = ['name', 'desc', 'click_nums', 'fav_nums','city__name','address','add_time']


class TeacherAdmin(object):
    list_display = [ 'name','org', 'work_years', 'work_company','add_time']
    search_fields = ['org', 'name', 'work_years', 'work_company']
    list_filter = ['org__name', 'name', 'work_years', 'work_company','click_nums', 'fav_nums', 'add_time']


xadmin.site.register(CityDict, CityDictAdmin)
xadmin.site.register(CourseOrg, CourseOrgAdmin)
xadmin.site.register(Teacher, TeacherAdmin)
```

>注意：对应后台显示英文的字段自行检查verbosename，自行加上。
>注意: py2下重载`__unicode__`方法，py3下重载`__str__`方法

如(注意缩进):

```python
      def __str__(self):
        return "[{0}]的教师: {1}".format(self.org, self.name)
```

如果注册后没有显示： 重新登录，或重启项目

![mark](http://upload-images.jianshu.io/upload_images/1779926-c06bd797f91b0556.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### operation app注册xadmin
新建`operation/adminx.py`

```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/1/9 0009 22:12'

import xadmin

from .models import UserAsk, UserCourse, UserMessage, CourseComments, UserFavorite


# 用户表单我要学习后台管理器
class UserAskAdmin(object):
    list_display = ['name', 'mobile', 'course_name', 'add_time']
    search_fields = ['name', 'mobile', 'course_name']
    list_filter = ['name', 'mobile', 'course_name', 'add_time']


# 用户课程学习后台管理器
class UserCourseAdmin(object):
    list_display = ['user', 'course', 'add_time']
    search_fields = ['user', 'course']
    list_filter = ['user', 'course', 'add_time']


# 用户消息后台管理器
class UserMessageAdmin(object):
    list_display = ['user', 'message', 'has_read', 'add_time']
    search_fields = ['user', 'message', 'has_read']
    list_filter = ['user', 'message', 'has_read', 'add_time']


# 用户评论后台管理器
class CourseCommentsAdmin(object):
    list_display = ['user', 'course', 'comments', 'add_time']
    search_fields = ['user', 'course', 'comments']
    list_filter = ['user', 'course', 'comments', 'add_time']


# 用户收藏后台管理器
class UserFavoriteAdmin(object):
    list_display = ['user', 'fav_id', 'fav_type', 'add_time']
    search_fields = ['user', 'fav_id', 'fav_type']
    list_filter = ['user', 'fav_id', 'fav_type', 'add_time']


# 将后台管理器与models进行关联注册。
xadmin.site.register(UserAsk, UserAskAdmin)
xadmin.site.register(UserCourse, UserCourseAdmin)
xadmin.site.register(UserMessage, UserMessageAdmin)
xadmin.site.register(CourseComments, CourseCommentsAdmin)
xadmin.site.register(UserFavorite, UserFavoriteAdmin)
```
>注意：对应后台显示英文的字段自行检查`verbosename`，自行加上。
>注意: py2下重载`__unicode__`方法，py3下重载`__str__`方法

如(注意缩进):

```python
      def __str__(self):
        return "[{0}]的教师: {1}".format(self.org, self.name)
```

成功性验证:

![mark](http://upload-images.jianshu.io/upload_images/1779926-0b386d9eb341a89c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

本小节结束对于commit:

>5:4 注册完成operation,机构，课程app。注意:自行重载str/unicode。补全verbosename。

## xadmin全局配置

将全局配置修改:

- 如左上角：django Xadmin。下面的我的公司
- 主题修改，app名称汉化，菜单收叠。

### 使用Xadmin的主题功能。

把全站的配置放在users\adminx.py中:

```python
from xadmin import views
# 创建X admin的全局管理器并与view绑定。
class BaseSetting(object):
    # 开启主题功能
    enable_themes = True
    use_bootswatch = True

# 将全局配置管理与view绑定注册
xadmin.site.register(views.BaseAdminView, BaseSetting)
```

### 解决django1.9(python2)下Xadmin主题不生效问题。

https://my.oschina.net/u/2396236/blog/1083251

- 安装requests

```
pip install requests
```
- /xadmin/plugins/themes.py 引入requests

```
import requests
```

- 修改block_top_navmenu方法：

```
 def block_top_navmenu(self, context, nodes):

        themes = [
            {'name': _(u"Default"), 'description': _(u"Default bootstrap theme"), 'css': self.default_theme},
            {'name': _(u"Bootstrap2"), 'description': _(u"Bootstrap 2.x theme"), 'css': self.bootstrap2_theme},
            ]
        select_css = context.get('site_theme', self.default_theme)

        if self.user_themes:
            themes.extend(self.user_themes)

        if self.use_bootswatch:
            ex_themes = cache.get(THEME_CACHE_KEY)
            if ex_themes:
                themes.extend(json.loads(ex_themes))
            else:
                ex_themes = []
                try:
                    flag = False#假如为True使用原来的代码，假如为Flase，使用requests库来访问
                    if flag:
                        h = httplib2.Http()
                        resp, content = h.request("http://bootswatch.com/api/3.json", 'GET', '',
                            headers={"Accept": "application/json", "User-Agent": self.request.META['HTTP_USER_AGENT']})
                        if six.PY3:
                            content = content.decode()
                        watch_themes = json.loads(content)['themes']
                    else:
                        content = requests.get("https://bootswatch.com/api/3.json")
                        if six.PY3:
                            content = content.text.decode()
                        watch_themes = json.loads(content.text)['themes']

                    ex_themes.extend([
                        {'name': t['name'], 'description': t['description'],
                            'css': t['cssMin'], 'thumbnail': t['thumbnail']}
                        for t in watch_themes])
                except Exception as e:
                    print(e)

                cache.set(THEME_CACHE_KEY, json.dumps(ex_themes), 24 * 3600)
                themes.extend(ex_themes)

        nodes.append(loader.render_to_string('xadmin/blocks/comm.top.theme.html', {'themes': themes, 'select_css': select_css}))
```

### 修改django admin 和下面的我的公司收起菜单

```
# x admin 全局配置参数信息设置
class GlobalSettings(object):
    site_title = "天涯明月笙: 慕课后台管理站"
    site_footer = "mtianyan's mooc"
    # 收起菜单
    menu_style = "accordion"
# 将头部与脚部信息进行注册:
xadmin.site.register(views.CommAdminView, GlobalSettings)
```

### apps.py配置app的显示名称

每个app下执行同样操作:

```python
# encoding: utf-8
from django.apps import AppConfig


class CoursesConfig(AppConfig):
    name = 'courses'
    verbose_name = u"课程"
```

**注意自行找猫画虎为每个app添加中文名**

>新建app时并没有引用apps的配置

在app下的init.py中添加:

```python
default_app_config = "operation.apps.OperationConfig"
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-b821d00da425bc93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意对应关系。

**注意为每个都添加对应的default_app_config**

最终大功告成：

![mark](http://upload-images.jianshu.io/upload_images/1779926-c2ef7e0ef0af864d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 自定义导航菜单顺序

```
class GlobalSetting(object):
     def get_site_menu(self):
        return (
            {'title': '课程管理', 'menus': (
                {'title': '课程信息', 'url': self.get_model_url(Course, 'changelist')},
                {'title': '章节信息', 'url': self.get_model_url(Lesson, 'changelist')},
                {'title': '视频信息', 'url': self.get_model_url(Video, 'changelist')},
                {'title': '课程资源', 'url': self.get_model_url(CourseResource, 'changelist')},
                {'title': '课程评论', 'url': self.get_model_url(CourseComments, 'changelist')},
            )},
            {'title': '机构管理', 'menus': (
                {'title': '所在城市', 'url': self.get_model_url(CityDict, 'changelist')},
                {'title': '机构讲师', 'url': self.get_model_url(Teacher, 'changelist')},
                {'title': '机构信息', 'url': self.get_model_url(CourseOrg, 'changelist')},
            )},
            {'title': '用户管理', 'menus': (
                {'title': '用户信息', 'url': self.get_model_url(UserProfile, 'changelist')},
                {'title': '用户验证', 'url': self.get_model_url(EmailVerifyRecord, 'changelist')},
                {'title': '用户课程', 'url': self.get_model_url(UserCourse, 'changelist')},
                {'title': '用户收藏', 'url': self.get_model_url(UserFavorite, 'changelist')},
                {'title': '用户消息', 'url': self.get_model_url(UserMessage, 'changelist')},
            )},


            {'title': '系统管理', 'menus': (
                {'title': '用户咨询', 'url': self.get_model_url(UserAsk, 'changelist')},
                {'title': '首页轮播', 'url': self.get_model_url(Banner, 'changelist')},
                {'title': '用户分组', 'url': self.get_model_url(Group, 'changelist')},
                {'title': '用户权限', 'url': self.get_model_url(Permission, 'changelist')},
                {'title': '日志记录', 'url': self.get_model_url(Log, 'changelist')},
            )},

xadmin.site.register(views.CommAdminView, GlobalSetting)
```

最终成型菜单:

![mark](http://upload-images.jianshu.io/upload_images/1779926-f1017d51c901e81b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 日志记录的使用

>日志记录会记录下我们进行过什么操作。

![mark](http://upload-images.jianshu.io/upload_images/1779926-df49695b6184d0fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过点击动作，进入当时修改的某条信息

第五章完结对应commit:

>5-5 (第五章完结),配置了页头页脚信息，修改了菜单的顺序，配置apps中文名，修复Python2下，xadmin主题不生效问题。 完结撒花。
