## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

## 6-1 首页和登录页面的配置

用户访问我们的根目录，我们需要把html文件返回给用户。因此我们第一步把html文件放入template目录。

![mark](http://upload-images.jianshu.io/upload_images/1779926-80baef7d234665dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在html中找到首页的html。拷贝到我们的template目录

![mark](http://upload-images.jianshu.io/upload_images/1779926-a6460a70549f735a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 新建static目录

>用来存放css, js等静态文件

![mark](http://upload-images.jianshu.io/upload_images/1779926-6e48bd832c6d6363.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 配置处理静态文件的url。

Django2.0.1版本下:

Mxonline3/urls.py：

```
from django.views.generic import TemplateView

urlpatterns = [
    path('xadmin/', xadmin.site.urls),
    # TemplateView.as_view会将template转换为view
    path('', TemplateView.as_view(template_name= "index.html"), name=  "index")
]
```
Django1.9.8版本下:

Mxonline2/urls.py：

```
from django.views.generic import TemplateView

urlpatterns = [
    url(r'^xadmin/', xadmin.site.urls),
    url('^$', TemplateView.as_view(template_name="index.html"), name="index")

]

```

此时运行访问就可以访问到我们的index页面，不过会没有样式。

### 设置static文件

```python
# 说明静态文件放在哪个目录

STATIC_URL = '/static/'

STATICFILES_DIRS = (
    os.path.join(BASE_DIR, "static"),
)
```

### 修改index页面中前端样式的引用地址

![mark](http://upload-images.jianshu.io/upload_images/1779926-9134b02548d0b467.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用ctrl+f查找出所有`../`,全部替换为`/static/`

然后点击运行，刷新页面可以看到我们的页面已经显示正常了。

### 拷贝登录页面到template

![mark](http://upload-images.jianshu.io/upload_images/1779926-8ea321030e812b2c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用ctrl+f查找出所有`../`,全部替换为`/static/`

>将css，js，图片全部替换完。

#### url配置跳转登录页面

Mxonline2/urls.py：

```python
    # 登录页面跳转url
    url('^login/$', TemplateView.as_view(template_name="login.html"), name="login")
```
Mxonline3/urls.py:

```python
    # TemplateView.as_view会将template转换为view
    path('', TemplateView.as_view(template_name= "index.html"), name=  "index"),
    path('login/', TemplateView.as_view(template_name= "login.html"), name=  "login")
```
在index页面，ctrl+f找到登录。将a标签中地址替换为login的url。

![mark](http://upload-images.jianshu.io/upload_images/1779926-de4ca5693f6584da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

取消注释后，将login.html改为`/login/`

![mark](http://upload-images.jianshu.io/upload_images/1779926-7f4d921c1acc1aba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击左侧减号收起。然后使用`<!--`与`-->`将个人中心暂时注释。

![mark](http://upload-images.jianshu.io/upload_images/1779926-247dd63df8fe669c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到登录注册，点击登录。

>根路径下的所有url都不需要斜杠

>此时我们的首页已经可以成功显示，通过首页点击登录也可以成功跳转登录页面

本小节完成对应commit:

>6-1: 完成首页与登录页面配置，设置了STATICFILES_DIRS。注意：dirs是一个元组，不要少逗号。删除了前面上传头像等直接传到根目录的目录。

## 6-2 用户登录-1

>配置url之前我们要书写好对应处理的view

Django的view实际就是一个函数，接收request请求对象，处理后返回response对象。

users/views.py:

```python
# encoding: utf-8
# 当我们配置url被这个view处理时，自动传入request对象.
def login(request):
    # 前端向后端发送的请求方式: get 或post

    # 登录提交表单为post
    if request.method == "POST":
        pass
    # 获取登录页面为get
    elif request.method == "GET":
        # render就是渲染html返回用户
        # render三变量: request 模板名称 一个字典写明传给前端的值
        return render(request, "login.html", {})
```
django1.9.8/urls.py
```python
from users.views import user_login
    # 登录页面跳转url login不要直接调用。而只是指向这个函数对象。
    url('^login/$',login, name="login")
```
django2.0.1/urls.py:

```python
from users.views import login
    path('login/', login, name="login")
```


在两行返回语句的位置打上断点:

![mark](http://upload-images.jianshu.io/upload_images/1779926-047b69d5d8e5a37c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击debug，进入首页后点击登录。可以看到

![mark](http://upload-images.jianshu.io/upload_images/1779926-6be2081e295c5ed5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

说明确实是通过get请求请求页面的。

通过值浏览器窗口可以看到这是一个`<WSGIRequest: GET '/login/'>`对象

![mark](http://upload-images.jianshu.io/upload_images/1779926-5442aba08cd0f9dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`path:`是指向的地址。

`f8`继续运行。跳转到登录页面。

## 6-3 用户登录2

### html form基础知识
templates/login.html:

>可以看到form表单中有input。点击提交会把值提交到后台。我们需要修改action让它指向我们的后台相应地址。

![mark](http://upload-images.jianshu.io/upload_images/1779926-4c37916def6e1d44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-15511a36382b57f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>input中的name值会被传递到后台。回组成键值对形式。

submit类型的input

![mark](http://upload-images.jianshu.io/upload_images/1779926-436e6a65d0dce481.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

只保留post这里的断点。输入用户名密码。查看debug情况

![mark](http://upload-images.jianshu.io/upload_images/1779926-cf21ee94698c8ff4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`403禁止访问`错误: html页面内必须加上`crsf token` 才能传值到后台。

>我会随机的给前端发一串符号，你必须把这串符号带回来，我才允许你post。

![mark](http://upload-images.jianshu.io/upload_images/1779926-d247a9e0dff3a019.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

from表单之前写上`crsf token`

此时我们查看前端页面：

![mark](http://upload-images.jianshu.io/upload_images/1779926-67c595200cfd69df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到html中登录下面有一个隐藏着的值：crsf token, 不会显示。

此时点击登录跳转到pass位置。

![mark](http://upload-images.jianshu.io/upload_images/1779926-b2886f41856f931d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-26d0bd2219ab674c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到request中的POST中是一个queryset的对象。我们可以把它当成一个字典来用。
来取到前端的数据

```python
    if request.method == "POST":
        # 取不到时为空，username，password为前端页面name值
        user_name = request.POST.get("username", "")
        pass_word = request.POST.get("password", "")
```

取到用户名和密码我们就要开始进行验证登录。使用Django自带的`auth`方法。

```python
from django.contrib.auth import authenticate, login

    # 登录提交表单为post
    if request.method == "POST":
        # 取不到时为空，username，password为前端页面name值
        user_name = request.POST.get("username", "")
        pass_word = request.POST.get("password", "")

        # 成功返回user对象,失败返回null
        user = authenticate(username= user_name, password= pass_word)

        # 如果不是null说明验证成功
        if user is not None:
            # login_in 两参数：request, user
            # 实际是对request写了一部分东西进去，然后在render的时候：
            # request是要render回去的。这些信息也就随着返回浏览器。完成登录
            login(request, user)
            # 跳转到首页 user request会被带回到首页
            return render(request, "index.html")
        # 没有成功说明里面的值是None，并再次跳转回主页面
        else:
            return render(request, "login.html", {})
```

>authenticate调用只需要传入用户名和密码。成功会返回user对象，失败返回null


html中通过

![mark](http://upload-images.jianshu.io/upload_images/1779926-d5c74e43c07916fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>设置成如果登录显示个人中心那段，未登录显示登录注册

打上断点

![mark](http://upload-images.jianshu.io/upload_images/1779926-e62a050ac9919337.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击debug后可以看到

![mark](http://upload-images.jianshu.io/upload_images/1779926-1c1d492a5643c146.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>我们成功的取到了值。

Django默认我们使用用户名和密码来登录

成功的登录user值如下

![mark](http://upload-images.jianshu.io/upload_images/1779926-de8e08349f204159.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

但是继续执行报错:

```
login() takes exactly 1 argument (2 given)
```

这时因为我们处理登录的自定义函数也叫login。就直接调用了自身，而不是调用Django提供的login。**所以我们一定不要把自定义view函数命名与Django提供的冲突**

>解决方案：将我们的login改为`def user_login(request):`

并且前往urls.py中将login也一并改了

此时运行可以看到我们的个人中心已经出来了。

### 改造为使用邮箱用户名均可。Setting中重载变量

>自定义authenticate方法

```python
from django.contrib.auth.backends import ModelBackend
from .models import UserProfile
# 并集运算
from django.db.models import Q

# 实现用户名邮箱均可登录
# 继承ModelBackend类，因为它有方法authenticate，可点进源码查看
class CustomBackend(ModelBackend):
    def authenticate(self, username=None, password=None, **kwargs):
        try:
            # 不希望用户存在两个，get只能有一个。两个是get失败的一种原因 Q为使用并集查询

            user = UserProfile.objects.get(Q(username=username)|Q(email=username))

            # django的后台中密码加密：所以不能password==password
            # UserProfile继承的AbstractUser中有def check_password(self, raw_password):

            if user.check_password(password):
                return user
        except Exception as e:
            return None
```
Mxonline2/settings.py

```python
# 设置邮箱和用户名均可登录
AUTHENTICATION_BACKENDS = (
    'users.views.CustomBackend',

)
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-6895f984ac22fd96.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>使用xadmin的退出，注销当前用户的退出。

此时我们可以通过邮箱和用户名都可以完成登录。

### 用户提示：return页面时提供它的错误信息

![mark](http://upload-images.jianshu.io/upload_images/1779926-1bef417166caa3ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
return render(request, "login.html", {"msg":"用户名或密码错误! "})
```

Html中如何取到这个值;

login html中这段是用来做错误提示的。

![mark](http://upload-images.jianshu.io/upload_images/1779926-5eb58fa3b4ff2c6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
<div class="error btns login-form-tips" id="jsLoginTips">{{ msg }}</div>
```

验证：

![mark](http://upload-images.jianshu.io/upload_images/1779926-af2e44b26db5c314.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

本小节结束对应commit:

>6-2&3 完成了用户登录，登录验证自定义：邮箱用户名均可。错误信息返回前端。设置登录显示个人中心判断，注意不要把自定义方法写成login。
