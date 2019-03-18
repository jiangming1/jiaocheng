## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

## 配置全局导航

让index页面也继承base页面

base页面的导航栏配置

![mark](http://upload-images.jianshu.io/upload_images/1779926-aecfdab8c06909fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

但是现在我们不知道当前是哪一个页面，因为后端没有传值过来

后台的每个view中添加current nav字段。然后向上传递到base页面

为了满足前台有current view的值，我们写的每个view都得加上这个字段。

小技巧：根据request的地址中的前几位来判断在哪一个区域之下

request.path

修改url中

```
    # 访问机构讲师
    url(r'^org_teacher/(?P<org_id>\d+)/$', OrgTeacherView.as_view(), name="org_teacher"),
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-ad1b338cfe2ef8b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 全局搜索功能开发

搜索跳到列表展示

courselist后加参数keywords

搜索的代码放在deco-common js中


课程的搜索功能：


```
        # 搜索功能
        search_keywords = request.GET.get('keywords','')
        if search_keywords:
            # 在name字段进行操作,做like语句的操作。i代表不区分大小写
            # or操作使用Q
            all_course = all_course.filter(Q(name__icontains=search_keywords)|Q(desc__icontains=search_keywords)|Q(detail__icontains=search_keywords))


```

```
            "search_keywords":search_keywords,
```

## 个人中心信息展示

将用户中心相关的六个页面，全部拷贝进template


新建usercenter base页面

![mark](http://upload-images.jianshu.io/upload_images/1779926-224ecf2256279c96.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

配置url

```
    # user app的url配置
    url(r"^users/", include('users.urls', namespace="users")),
```

```
# encoding: utf-8
from .views import UserInfoView

__author__ = 'mtianyan'
__date__ = '2018/1/14 0014 04:00'


from django.conf.urls import url

urlpatterns = [
    # 用户信息
    url(r'^info/$', UserInfoView.as_view(), name="user_info"),
]
```

```
# 用户个人信息view
class UserInfoView(LoginRequiredMixin,View):
    login_url = '/login/'
    redirect_field_name = 'next'
    def get(self, request):
        return render(request, "usercenter-info.html", {

        })

```

user app 下新建url

django自带的filter 

request.user.mobile|default_if_none:''


## 修改密码和修改头像

新建url 和 view


小技巧:

>django的xadmin和admin当中，实际上是可以对form定义为文件的时候，是可以自动对上传的文件做保存的。
使用form的一个字段定义一个文件类型。把字段取出来就是内存中的文件。
赋值到user.image 就完成图片的一个存储。

users/forms.py：

```
# 用于文件上传，修改头像
class UploadImageForm(forms.ModelForm):

    class Meta:
        model = UserProfile
        fields = ['image']
```

实例化时，传进来是post 和 文件类型的request 存放地址

![mark](http://upload-images.jianshu.io/upload_images/1779926-98121b14dfe4925e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-2b282e75b097eaf8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上传文件时通过一个form完成的。

![mark](http://upload-images.jianshu.io/upload_images/1779926-9fe3ac2194c79af7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

必须指明enctype，才能把文件类型传递到后台

url 和view

```
    # 用户头像上传
    url(r'^image/upload/$', UploadImageView.as_view(), name="image_upload"),
```

```
# 用户上传图片的view:用于修改头像
class UploadImageView(LoginRequiredMixin, View):
    login_url = '/login/'
    redirect_field_name = 'next'
    def post(self, request):
        # 这时候用户上传的文件就已经被保存到imageform了
        image_form = UploadImageForm(request.POST, request.FILES)
        if image_form.is_valid():
            pass
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-1ea10d80002ec1b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里的name必须和form中的一样

![mark](http://upload-images.jianshu.io/upload_images/1779926-35c4e451ed148950.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-74740b8a8c7039c7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>所有验证通过的字段放在cleaned data

```
# 用户上传图片的view:用于修改头像
class UploadImageView(LoginRequiredMixin, View):
    login_url = '/login/'
    redirect_field_name = 'next'
    def post(self, request):
        # 这时候用户上传的文件就已经被保存到imageform了 ，为modelform添加instance值直接保存
        image_form = UploadImageForm(request.POST, request.FILES, instance=request.user)
        if image_form.is_valid():
            image_form.save()
            # # 取出cleaned data中的值,一个dict
            # image = image_form.cleaned_data['image']
            # request.user.image = image
            # request.user.save()
            return HttpResponse('{"status":"success"}', content_type='application/json')
        else:
            return HttpResponse('{"status":"fail"}', content_type='application/json')
```

修改密码功能:

```
# 在个人中心修改用户密码
class UpdatePwdView(View):
    def post(self, request):
        modiypwd_form = ModifyPwdForm(request.POST)
        if modiypwd_form.is_valid():
            pwd1 = request.POST.get("password1", "")
            pwd2 = request.POST.get("password2", "")
            # 如果两次密码不相等，返回错误信息
            if pwd1 != pwd2:
                return HttpResponse('{"status":"fail", "msg":"密码不一致"}', content_type='application/json')
            # 如果密码一致
            user =request.user
            # 加密成密文
            user.password = make_password(pwd2)
            # save保存到数据库
            user.save()
            return HttpResponse('{"status":"success"}', content_type='application/json')
        # 验证失败说明密码位数不够。
        else:
            return HttpResponse('{"status":"fail", "msg":"填写错误请检查"}', content_type='application/json')
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-91d82f714461fffa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

必须与我们的form中定义的一致

实现的js代码在deco-user.js

js文件中的url就一定不能用template的模板语言了

## 修改邮箱提交form表单

有两个接口需要完成。点击获取验证码时，后台需要向用户新邮箱发送验证码。
邮箱如果出错，会返回错误信息。

输入了邮箱和验证码，验证是否匹配。

### 获取验证码接口:

配置url:

```
    # 专用于发送验证码的
    url(r'^sendemail_code/$', SendEmailCodeView.as_view(), name="sendemail_code"),
```

新增邮箱验证码model类型

```
SEND_CHOICES = (
        ("register", u"注册"),
        ("forget", u"找回密码"),
        ("update_email", u"修改邮箱")
    )
           max_length=20,
```

发送邮箱验证码view

```
class SendEmailCodeView(LoginRequiredMixin, View):
    def get(self,request):
        # 取出需要发送的邮件
        email = request.GET.get("email", "")

        # 不能是已注册的邮箱
        if UserProfile.objects.filter(email=email):
            return HttpResponse('{"email":"邮箱已经存在"}', content_type='application/json')
        send_register_eamil(email, "update_email")
        return HttpResponse('{"status":"success"}', content_type='application/json')
```

发送邮箱验证码的功能放在deco-user.js中

```
    elif send_type == "update_email":
        code = random_str(4)
        email_title = "mtianyan慕课小站 修改邮箱验证码"
        email_body = loader.render_to_string(
            "email_update_email.html",  # 需要渲染的html模板
            {
                "active_code": code  # 参数
            }
        )
        msg = EmailMessage(email_title, email_body, EMAIL_FROM, [email])
        msg.content_subtype = "html"
        send_status = msg.send()
```

### 修改邮箱的view

```
# 修改邮箱的view:
class UpdateEmailView(LoginRequiredMixin, View):
    login_url = '/login/'
    redirect_field_name = 'next'
    def post(self, request):
        email = request.POST.get("email", "")
        code = request.POST.get("code", "")

        existed_records = EmailVerifyRecord.objects.filter(email=email, code=code, send_type='update_email')
        if existed_records:
            user = request.user
            user.email = email
            user.save()
            return HttpResponse('{"status":"success"}', content_type='application/json')
        else:
            return HttpResponse('{"email":"验证码无效"}', content_type='application/json')
```

修改邮箱的url

```
    url(r'^update_email/$', UpdateEmailView.as_view(), name="update_email"),
```

为userInfo view增加post方法。使用modelform完成直接提交

```
    def post(self,request):
        # 不像用户咨询是一个新的。需要指明instance。不然无法修改，而是新增用户
        user_info_form = UserInfoForm(request.POST, instance=request.user)
        if user_info_form.is_valid():
            user_info_form.save()
```

user_info_form

```
# 用于个人中心修改个人信息
class UserInfoForm(forms.ModelForm):

    class Meta:
        model = UserProfile
        fields = ['nick_name','gender','birthday','address','mobile']
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-80221e9b67ebdf40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

配置url和view

```
    # 用户中心我的课程
    path('mycourse/', MyCourseView.as_view(), name="mycourse"),
```

usercenter base中添加链接

```
# 个人中心页我的课程

class MyCourseView(LoginRequiredMixin, View):
    login_url = '/login/'
    redirect_field_name = 'next'

    def get(self, request):
        user_courses = UserCourse.objects.filter(user=request.user)
        return render(request, "usercenter-mycourse.html", {
            "user_courses":user_courses,
        })
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-4974e972f6b69fcd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 配置url和view

```
    # 我收藏的课程机构
    path('myfav/org/', MyFavOrgView.as_view(), name="myfav_org"),
```

```
class MyFavOrgView(LoginRequiredMixin, View):
    login_url = '/login/'
    redirect_field_name = 'next'

    def get(self, request):
        org_list = []
        fav_orgs= UserFavorite.objects.filter(user=request.user, fav_type=2)
        # 上面的fav_orgs只是存放了id。我们还需要通过id找到机构对象
        for fav_org in fav_orgs:
            # 取出fav_id也就是机构的id。
            org_id = fav_org.fav_id
            # 获取这个机构对象
            org = CourseOrg.objects.get(id=org_id)
            org_list.append(org)
        return render(request, "usercenter-fav-org.html", {
            "org_list": org_list,
        })

```

```
# 我收藏的授课讲师

class MyFavTeacherView(LoginRequiredMixin, View):
    login_url = '/login/'
    redirect_field_name = 'next'

    def get(self, request):
        teacher_list = []
        fav_teachers= UserFavorite.objects.filter(user=request.user, fav_type=3)
        # 上面的fav_orgs只是存放了id。我们还需要通过id找到机构对象
        for fav_teacher in fav_teachers:
            # 取出fav_id也就是机构的id。
            teacher_id = fav_teacher.fav_id
            # 获取这个机构对象
            teacher = Teacher.objects.get(id=teacher_id)
            teacher_list.append(teacher)
        return render(request, "usercenter-fav-teacher.html", {
            "teacher_list": teacher_list,
        })
```

url

```
    # 我收藏的课程机构
    path('myfav/org/', MyFavOrgView.as_view(), name="myfav_org"),

    # 我收藏的授课讲师
    path('myfav/teacher/', MyFavTeacherView.as_view(), name="myfav_teacher"),
```

### 配置view和url

```
# 我收藏的课程

class MyFavCourseView(LoginRequiredMixin, View):
    login_url = '/login/'
    redirect_field_name = 'next'

    def get(self, request):
        course_list = []
        fav_courses = UserFavorite.objects.filter(user=request.user, fav_type=1)
        # 上面的fav_orgs只是存放了id。我们还需要通过id找到机构对象
        for fav_course in fav_courses:
            # 取出fav_id也就是机构的id。
            course_id = fav_course.fav_id
            # 获取这个机构对象
            course = Course.objects.get(id=course_id)
            course_list.append(course)
        return render(request, "usercenter-fav-course.html", {
            "course_list": course_list,
        })
```

```
    # 我收藏的课程
    path('myfav/course/', MyFavCourseView.as_view(), name="myfav_course"),
```

## 取消收藏

templates/usercenter-fav-course.html

![mark](http://upload-images.jianshu.io/upload_images/1779926-46e2bc9cc88378d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![mark](http://upload-images.jianshu.io/upload_images/1779926-c02313ef5c1c1ad0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

取消收藏的代码在base页面中。三个js。

## 我的消息页面

![mark](http://upload-images.jianshu.io/upload_images/1779926-781dd84189d4a0b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 配置url和view

```
    # 我收藏的课程
    path('my_message/', MyMessageView.as_view(), name="my_message"),
```

```
# 我的消息
class MyMessageView(LoginRequiredMixin, View):
    login_url = '/login/'
    redirect_field_name = 'next'

    def get(self, request):
        all_message = UserMessage.objects.filter(user= request.user.id)
        # 对课程机构进行分页
        # 尝试获取前台get请求传递过来的page参数
        # 如果是不合法的配置参数默认返回第一页
        try:
            page = request.GET.get('page', 1)
        except PageNotAnInteger:
            page = 1
        # 这里指从allorg中取五个出来，每页显示5个
        p = Paginator(all_message, 4)
        messages = p.page(page)
        return  render(request, "usercenter-message.html", {
        "messages":messages,
        })
```

### 注册时发生欢迎消息

```
 # 写入欢迎注册消息
            user_message = UserMessage()
            user_message.user = user_profile.id
            user_message.message = "欢迎注册mtianyan慕课小站!!"
            user_message.save()
```

### 页面顶部小喇叭

所有页面都要读取一个共同的变量：未读消息的数量。我们需要向request中注入这个变量
所有页面都有request.user对象。所以我们在userprofile中自定义方法，

```
    # 获取用户未读消息的数量
    def unread_nums(self):
        from operation.models import UserMessage
        return  UserMessage.objects.filter(user=self.id).count()
```
