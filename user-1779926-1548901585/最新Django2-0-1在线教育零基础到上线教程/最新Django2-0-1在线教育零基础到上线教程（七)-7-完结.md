## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

## 7-7 modelform 提交我要学习咨询1

对应表`userask`

`form`会对字段先做验证，然后保存到数据库中。

可以看到我们的forms和我们的model中有很多内容是一样的。我们如何让代码重复利用呢？

使用modelform解决这个问题。

```python
# encoding: utf-8
from django import forms

from operation.models import UserAsk

__author__ = 'mtianyan'
__date__ = '2018/1/12 0012 03:20'

# 普通版本的form
# class UserAskForm(forms.Form):
#     name = forms.CharField(required=True, min_length=2, max_length=20)
#     phone = forms.CharField(required=True, max_length=11, min_length=11)
#     course_name = forms.CharField(required=True, min_length=5, max_length=50)

# 进阶版本的modelform：它可以向model一样save
class AnotherUserForm(forms.ModelForm):
    # 继承之余还可以新增字段

    # 是由哪个model转换的
    class Meta:
        model = UserAsk
        # 我需要验证的字段
        fields = ['name','mobile','course_name']
```

### include的机制配置应用自己的url

django1.9.8 创建organization/urls.py:

```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/1/12 0012 06:20'

# encoding: utf-8
from organization.views import OrgView
from django.conf.urls import url

urlpatterns = [
    # 课程机构列表url
    url('list/&', OrgView.as_view(), name="org_list"),
```

django1.9.8 Mxonline2/urls.py:
删掉orglist,新增如下

```python
  # 课程机构app的url配置
    url(r"^org/", include('organization.urls',namespace="org")),
```


django2.0.1: 新建organization/urls.py

```python
# encoding: utf-8
from organization.views import OrgView

__author__ = 'mtianyan'
__date__ = '2018/1/12 0012 03:28'

from django.urls import path, re_path

urlpatterns = [
    # 课程机构列表url
    path('list/', OrgView.as_view(), name="org_list"),
]
```

django2.0.1: urls.py中:

删掉org_list，新增include

```
    # 课程机构app的url配置
    path("org/", include('organization.urls',namespace="org")),
```

使用命名空间防止重复


![mark](http://upload-images.jianshu.io/upload_images/1779926-f1e131c3c0a0abe9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


解决Django2.0.1报错:

```python
    'Specifying a namespace in include() without providing an app_name ' 
 django.core.exceptions.ImproperlyConfigured: Specifying a namespace in 
 include() without providing an app_name is not supported. Set the app_name 
 attribute in the included module, or pass a 2-tuple containing the list of 
 patterns and app_name instead. 
```

在自己的app下的urls中写上appname

```
# encoding: utf-8
from organization.views import OrgView

__author__ = 'mtianyan'
__date__ = '2018/1/12 0012 03:28'

from django.urls import path, re_path

app_name = "organization"

urlpatterns = [

    # 课程机构列表url
    path('list/', OrgView.as_view(), name="org_list"),

]
```

html中使用命名空间方式:

![mark](http://upload-images.jianshu.io/upload_images/1779926-ed075c1077b21a8a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 使用modelform做提交

比较合理的操作是异步的，不会对整个页面进行刷新。
如果有错误，显示错误。一种ajax的异步操作。

因此我们此时不能直接render一个页面回来。
应该是给前端返回json数据，而不是页面

`HttpResponse`类指明给用户返回哪种类型数据

```
from django.http import HttpResponse
```

配置一个modelform的view

```python
# 用户添加我要学习
class AddUserAskView(View):
    # 处理表单提交当然post
    def post(self,request):
        userask_form = UserAskForm(request.POST)
        # 判断该form是否有效
        if userask_form.is_valid():
            # 这里是modelform和form的区别
            # 它有model的属性
            # 当commit为true进行真正保存
            user_ask = userask_form.save(commit=True)
            # 这样就不需要把一个一个字段取出来然后存到model的对象中之后save

            # 如果保存成功,返回json字符串,后面content type是告诉浏览器的,
            return HttpResponse("{'status': 'success'}", content_type='application/json')
        else:
            # 如果保存失败，返回json字符串,并将form的报错信息通过msg传递到前端
            return HttpResponse("{'status': 'fail', 'msg':{0}}".format(userask_form.errors),  content_type='application/json')

```

### 配置相应的url

```
    # 添加我要学习
    path('add_ask/', AddUserAskView.as_view(), name="add_ask")
```
## 7-8 modelform 提交我要学习

### 分析ajax请求

```
<script>
    $(function(){
        $('#jsStayBtn').on('click', function(){
            $.ajax({
                cache: false,
                type: "POST",
  				url:"{% url "org:add_ask" %}",
                data:$('#jsStayForm').serialize(),
                async: true,
                success: function(data) {
                    if(data.status == 'success'){
                        $('#jsStayForm')[0].reset();
                        alert("提交成功")
                    }else if(data.status == 'fail'){
                        $('#jsCompanyTips').html(data.msg)
                    }
                },
            });
        });
    })

</script>
```

>监听这个button，用户如果点击了button。我们来向这个url进行post请求。
将我们的表单进行序列化。

form表单添加crsf_token

如果后台返回的状态值为success，那么我们将弹出提交成功。
失败，就会在错误提示框中写入。

### 手机号码正则表达式验证:

organization/forms.py

```
# 手机号的正则表达式验证
    def clean_mobile(self):
        mobile = self.cleaned_data['mobile']
        REGEX_MOBILE = "^1[358]\d{9}$|^147\d{8}$|^176\d{8}$"
        p = re.compile(REGEX_MOBILE)
        if p.match(mobile):
            return mobile
        else:
            raise forms.ValidationError(u"手机号码非法", code="mobile_invalid")
```

本小节完毕对应提交commit:

>7-7&8 使用modelform完成表单我要学习的异步提交

## 7-9 机构详情

- 机构首页
- 机构课程
- 机构介绍
- 机构讲师

登录xadmin添加基础的必要数据。添加课程与讲师。

课程中应该有一个外键指向它是哪个机构的。

courses/models.py

Django1.9.8中:

```python
from organization.models import CourseOrg

course_org = models.ForeignKey(CourseOrg, verbose_name=u"所属机构",null=True,blank=True)
```

django2.0.1下；

```python
    course_org = models.ForeignKey(CourseOrg,on_delete=models.CASCADE, verbose_name=u"所属机构",null=True,blank=True)
```

新增外键字段应该`null=true,blank=true。`

>因为历史数据中没有这个外键字段

```
makemigration course
migrate course
```

将前端给我们的org相关的四个页面拷进template

![mark](http://upload-images.jianshu.io/upload_images/1779926-e84178ba30796d0f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

新建org_base页面

将org_home页面内容拿过去。

![mark](http://upload-images.jianshu.io/upload_images/1779926-d1ac59bf53507cfe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-0ed04eb784fd710c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


- loadstaticfiles -> 改css文件路径->改js文件路径->改图片路径
- 改已经实现的url。->将子页面继承后需要改得地方使用block包裹。

将home页面清空

![mark](http://upload-images.jianshu.io/upload_images/1779926-b12422d8d71faaed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

完成替换之后添加访问的view以及URl

organization/views.py:

```
class OrgHomeView(View):
    """
   机构首页
    """
    def get(self, request, org_id):
        # 根据id取到课程机构
        course_org = CourseOrg.objects.get(id= int(org_id))
        # 通过课程机构找到课程。内建的变量，找到指向这个字段的外键引用
        all_courses = course_org.course_set.all()[:4]
        all_teacher = course_org.teacher_set.all()[:2]

        return render(request, 'org-detail-homepage.html',{
           'all_courses':all_courses,
            'all_teacher':all_teacher,
        })
```

Django2.0.1下url

```
       # home页面,取纯数字
    re_path('home/(?P<org_id>\d+)/', OrgHomeView.as_view(), name="org_home"),
```

django1.9.8下url:

```
    # home页面,取纯数字
    url(r'home/(?P<org_id>\d+)/$', OrgHomeView.as_view(), name="org_home")
```

html中使用for循环遍历:

![mark](http://upload-images.jianshu.io/upload_images/1779926-3904e74bdf0f37f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-6ecfdedeb67e23de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>如上图可以直接通过外键字段再找到外键对象的字段

templates/org-list.html

配置里面跳转到详情页的url

![mark](http://upload-images.jianshu.io/upload_images/1779926-916cfee6a62b1a93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
        return render(request, 'org-detail-homepage.html',{
           'all_courses':all_courses,
            'all_teacher':all_teacher,
            'course_org': course_org,
        })
```

将`course_org`也return回来，就可以把网页里这部分值替换掉

templates/org_base.html

数值会随继承链向上传递。

![mark](http://upload-images.jianshu.io/upload_images/1779926-44bca4fc2f2370e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-a84aafa89037174b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 为讲师增加头像字段
organization/models.py

```
    image = models.ImageField(
        default= '',
        upload_to="teacher/%Y/%m",
        verbose_name=u"头像",
        max_length=100)
```

然后

```
makemgration organization
migrate organization
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-785deb9fdc5f2647.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用for循环填充数据

![mark](http://upload-images.jianshu.io/upload_images/1779926-191fcde311217246.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-124c00e49522243b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

同理可得，我们把机构课程页不同的部分拿出来即可

### 配置访问的view和url
organization/urls.py:

Django 2.0.1：

```
    # 访问课程
    re_path('course/(?P<org_id>\d+)/', OrgCourseView.as_view(), name="org_course"),
```
Django 1.9.8：

```
    # 访问课程
    url(r'^course/(?P<org_id>\d+)/$', OrgCourseView.as_view(), name="org_course"),
```

organization/views.py
```
class OrgCourseView(View):
    """
   机构课程列表页
    """
    def get(self, request, org_id):
        # 根据id取到课程机构
        course_org = CourseOrg.objects.get(id= int(org_id))
        # 通过课程机构找到课程。内建的变量，找到指向这个字段的外键引用
        all_courses = course_org.course_set.all()

        return render(request, 'org-detail-course.html',{
           'all_courses':all_courses,
            'course_org': course_org,
        })
```

base页面的left链接修改

>这里能直接用到course_org.id。是因为子页面render时都向上传递了course_org对象

![mark](http://upload-images.jianshu.io/upload_images/1779926-091c34151ad3f216.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用for循环，填充内容。

![mark](http://upload-images.jianshu.io/upload_images/1779926-a5f100c4c1896e95.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 左侧active修改

因为现在没有值能判断当前是哪个页面。所以在orghomeview中传值回来current page
![mark](http://upload-images.jianshu.io/upload_images/1779926-2ed070392f05f939.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![mark](http://upload-images.jianshu.io/upload_images/1779926-0bf3f5c25a4bf24a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

写两个view

```
class OrgDescView(View):
    """
   机构描述详情页
    """
    def get(self, request, org_id):
        # 向前端传值，表明现在在home页
        current_page = "desc"
        # 根据id取到课程机构
        course_org = CourseOrg.objects.get(id= int(org_id))
        # 通过课程机构找到课程。内建的变量，找到指向这个字段的外键引用
        # 向前端传值说明用户是否收藏
        has_fav = False
        # 必须是用户已登录我们才需要判断。
        if request.user.is_authenticated:
            if UserFavorite.objects.filter(user=request.user, fav_id=course_org.id, fav_type=2):
                has_fav = True
        return render(request, 'org-detail-desc.html',{
            'course_org': course_org,
            "current_page":current_page,
            "has_fav":has_fav,
        })

class OrgTeacherView(View):
    """
   机构讲师列表页
    """
    def get(self, request, org_id):
        # 向前端传值，表明现在在home页
        current_page = "teacher"
        # 根据id取到课程机构
        course_org = CourseOrg.objects.get(id= int(org_id))
        # 通过课程机构找到课程。内建的变量，找到指向这个字段的外键引用
        all_teachers = course_org.teacher_set.all()
        # 向前端传值说明用户是否收藏
        has_fav = False
        # 必须是用户已登录我们才需要判断。
        if request.user.is_authenticated:
            if UserFavorite.objects.filter(user=request.user, fav_id=course_org.id, fav_type=2):
                has_fav = True
        return render(request, 'org-detail-teachers.html',{
           'all_teachers':all_teachers,
            'course_org': course_org,
            "current_page":current_page,
            "has_fav":has_fav
        })

```

加两个url

Django2.0.1下:

```
    # 访问机构描述
    re_path('desc/(?P<org_id>\d+)/', OrgDescView.as_view(), name="org_desc"),

    # 访问机构讲师
    re_path('teacher/(?P<org_id>\d+)/', OrgTeacherView.as_view(), name="org_teacher"),
```

Django1.9.8下：

```
    # 访问机构描述
    url(r'^desc/(?P<org_id>\d+)/$', OrgDescView.as_view(), name="org_desc"),

    # 访问机构讲师
    url(r'^teacher/(?P<org_id>\d+)/$', OrgTeacherView.as_view(), name="org_teacher"),
```

修改base页面相关跳转链接，注意点：加上course_org.id

重载我们的pagepath，使得面包屑动态显示

![mark](http://upload-images.jianshu.io/upload_images/1779926-5e965efd1df7eb13.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-a607a5b377f66e73.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 7-10 课程机构收藏功能

书写收藏的后台逻辑:


url配置

django2.0.1下:

```python
    # 机构收藏
    path('add_fav/', AddFavView.as_view(), name="add_fav"),
```

django1.9.8下；

```
    # 机构收藏
    url(r'^add_fav/$', AddFavView.as_view(), name="add_fav"),
```

配套的view；

```python
class AddFavView(View):
    """
    用户收藏与取消收藏功能
    """
    def post(self, request):
        # 表明你收藏的不管是课程，讲师，还是机构。他们的id
        # 默认值取0是因为空串转int报错
        id = request.POST.get('fav_id', 0)
        # 取到你收藏的类别，从前台提交的ajax请求中取
        type = request.POST.get('fav_type', 0)

        # 收藏与已收藏取消收藏
        # 判断用户是否登录:即使没登录会有一个匿名的user
        if not request.user.is_authenticated:
            # 未登录时返回json提示未登录，跳转到登录页面是在ajax中做的
            return HttpResponse('{"status":"fail", "msg":"用户未登录"}', content_type='application/json')
        exist_records = UserFavorite.objects.filter(user=request.user, fav_id=int(id), fav_type=int(type))
        if exist_records:
            # 如果记录已经存在， 则表示用户取消收藏
            exist_records.delete()
            return HttpResponse('{"status":"success", "msg":"收藏"}', content_type='application/json')
        else:
            user_fav = UserFavorite()
            # 过滤掉未取到fav_id type的默认情况
            if int(type) >0 and int(id) >0:
                user_fav.fav_id = int(id)
                user_fav.fav_type = int(type)
                user_fav.user = request.user
                user_fav.save()
                return HttpResponse('{"status":"success", "msg":"已收藏"}', content_type='application/json')
            else:
                return HttpResponse('{"status":"fail", "msg":"收藏出错"}', content_type='application/json')
```

相关处理收藏的jQuery代码写在org base Html

![mark](http://upload-images.jianshu.io/upload_images/1779926-74ce9bf772f25479.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-c6cec339c23c0923.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

添加返回页面的收藏值。

```
 # 向前端传值说明用户是否收藏
        has_fav = False
        # 必须是用户已登录我们才需要判断。
        if request.user.is_authenticated:
            if UserFavorite.objects.filter(user=request.user, fav_id=course_org.id, fav_type=2):
                has_fav = True

 # return redener加上值
             "has_fav": has_fav
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-86511eb4e6fd4f29.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

前台 org_base.html中

![mark](http://upload-images.jianshu.io/upload_images/1779926-380d114294efbc07.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

同理添加剩下的几个页面的。

第七章完结撒花。对应commit:

>7-9&10&11&12将机构详情展示完毕，为课程机构添加了收藏功能。修复了index未登录状态下爆炸的错误
