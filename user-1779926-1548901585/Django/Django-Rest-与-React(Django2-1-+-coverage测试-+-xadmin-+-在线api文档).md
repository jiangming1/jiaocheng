原文: https://www.valentinog.com/blog/tutorial-api-django-rest-react

翻译版实践教程: Django Rest 与 React(Django2.1 加 一点小测试 加一点译者的小额外功能)

>最终构建了一个有后台管理 + 提供api服务 + Mysql数据库 + 在线api文档的Lead系统。

一个实用(自认为)的介绍: 实用Django REST 与 React，特性！ Django2.1！

![mark](http://myphoto.mtianyan.cn/mdblog/20190107/YUgllcEYkqMg.png?imageslim)

如今(可供开发选择的)web框并不匮乏。

想要构建一个API服务? 这里列举出一些你可以立马想到说出名字的:  Laravel, Rails, Node.js and Koa 2, Phoenix。

但是摆在我们面前的现实是: 客户想要一个尽可能快的原型，这时我该如何做？

我选择了一个web 框架,他:

- 让我可以写更少的代码
- 让我可以尽可能遵循MVP模式
- 为扩展项目提供了一个稳定的基础

请信我！ 当涉及到开发速度时，Django是很好的一个选择。 但是如何去创建一个简单的Django Rest API呢？如何去 组织构建 一个Django 项目 与 React呢？

不要害怕，我们将在下面的教程中一起探索。

## 目录

- Django REST with React: 我们将会学到什么
- Django REST with React: requirements(环境依赖)
- Django REST with React: 为项目创建一个虚拟的 Python 环境
- Django REST with React: 构建一个 Django 的 application
- Django REST with React: 创建一个 Django Model(译者增 GitHub 托管代码)
- Django REST with React: 一丢丢测试
- Django REST with React: Django Rest serializers(序列化器)
- Django REST with React: 设置视图控制器
- Django REST with React: 设置urls 路由
- Django REST with React: (seeding)喂养数据库
- Django REST with React: Django 与 React 协同
- Django REST with React: 构建 React 与 webpack 
- Django REST with React: React 前端
- Django REST with React: 前端测试
- Django REST with React: 构建一个React 表单
- Django REST with React: 总结
- Django REST with React: 资源

### Django REST with React: 我们将会学到什么

在下面的教程中你将会学到:

- 如何构建一个简单的 Django REST API
- 如何组织构建一个 Django project 与 React

我们将要做个啥嘞？ 在这个项目里，我们将会构建一个简单的 API 来列出并存储领导。

### Django REST with React: requirements(环境依赖)

为了能继续跟进下面的教程，你应该拥有:

- 掌握 Python 与 Django 基础
- 掌握 JavaScript ES6 和 React 基础
- 在你的电脑系统中安装一个新版本的 Node.js

准备好了？让我们一起出发吧！ 

### Django REST with React: 为项目创建一个虚拟的Python环境

首要的是来确认你拥有一个虚拟的Python环境，你可以在Python3中使用 pipenv，pyenv，或者venv模块。 （译者推荐使用pipenv）

```
pip install pipenv
mkdir django-drf-react-quickstart && cd django-drf-react-quickstart
pipenv shell
```

然后安装依赖: 安装Django 和 Django REST framework 通过下面命令:

```
pipenv install django djangorestframework
```

当安装结束，你可以通过下面命令创建一个新的Django 项目:

```
django-admin startproject drf_react .
```

现在我们可以开始构建我们的第一个Django app 来 列出与存储leads

### Django REST with React: 构建一个Django的application

一个Django项目包含许多的 applications(应用)。理想状态下 每个应用应该只做一件事。

Django applications(应用) 是模块化的，可重用的。 例如： 我创建了一个lead 应用用来创建和列出领导。

如果其他项目需要一个相同的app，我可以通过 package manager(INSTALLED_APPS) 来安装leads。嗯，没错，这就是全部。

我建议去阅读 [How to write reusable apps](https://docs.djangoproject.com/zh-hans/2.1/intro/reusable-apps/),并观看 [DjangoCon 2008: Reusable Apps](https://youtu.be/A-S0tqpPga4) 来学习关于app的最佳实践。

在Django中创建一个新的application，你应该运行:

```
django-admin startapp app_name 
``` 

为了在项目的文件夹中创建leads app:

```
cd django-drf-react-quickstart
``` 

然后初始化我们的app:

```
django-admin startapp leads
```

注意: 我需要假设你位于 `/YOUR_CODE_DIR/django-drf-react-quickstart/`来运行的上述命令！ 以及这些命令是在pipenv shell启动的虚拟环境下执行的。

![mark](http://myphoto.mtianyan.cn/mdblog/20190107/a0myvSfwJkaO.png?imageslim)

你将看到一个新的名字为leads的文件夹，在你的/YOUR_CODE_DIR/django-drf-react-quickstart/目录中。

![mark](http://myphoto.mtianyan.cn/mdblog/20190107/ga5zAoyBmJTs.png?imageslim)

就像上图这样。

现在让我们告诉Django如何使用这个新的app

打开 ./drf_react/settings.py 并添加这个app在 INSTALLED_APPS中:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'leads', # 添加leads app
]
```

到目前为止一切顺利。

#### 译者附加教程(GitHub管理代码)

- GitHub 新建Repo

![mark](http://myphoto.mtianyan.cn/mdblog/20190107/uQ0FfG0BDh2i.png?imageslim)

- GitHub 夺命N连环

```
# 起手式
git init
# 添加当前文件进入式
git add .
# commit文件式
git commit -m "初始化项目目录，添加leads app"
# 设置要上哪个云端式
git remote add origin https://github.com/mtianyan/django-drf-react-quickstart.git
# 把网页新建拉下来式
git pull --rebase origin master
# push真的上云式
git push --set-upstream origin master
```

>首次使用才如上面N连环般麻烦。

- GitHub 夺命三全

```
# 第一全,文件全添加
git add .
# 第二全,commit信息要全
git commit -m "巴啦啦小魔仙balala"
# 第三全,全网速推上云
git push 
```

### Django REST with React: 创建一个Django Model

注意: 在进行下一步之前，请确保你仍然位于: `/YOUR_CODE_DIR/django-drf-react-quickstart/`

随着app的安装，我们是时候来创建我们的第一个model了，一个model 是一个对象用来表示你的表数据。几乎每个web 框架都拥有models这个概念。 Django 也不例外。

一个Django模型可以拥有一个或多个字段： 每个字段是你数据表中的一列，当进行下一步之前，让我们为我们的lead app 定义它的依赖。

首先我们需要一个 Lead model

由于我收集了leads的信息，我认为一个Lead model 应该拥有以下字段:

- 一个名字
- 一个邮件
- 一个信息

（感觉添加额外的字段也很轻松，例如我们想再加个手机号）

请不要忘记一个 时间戳 字段！ Django 不会默认的为你添加一个 created_at 列。

很好！

打开 `./leads/models.py`然后创建Lead model:

```python
from django.db import models
class Lead(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField()
    message = models.CharField(max_length=300)
    created_at = models.DateTimeField(auto_now_add=True)
```

这里有一个关于models的快速指南: 花时间看看 -> [Django fields documentation](https://docs.djangoproject.com/en/2.0/ref/models/fields/)

当计划一个model时，请尽力选择对于你的案例最适合的那些字段。

#### (译者)附加使用mysql及本地化时间等

```
pipenv install mysqlclient
```

新建一个mysql数据库

![mark](http://myphoto.mtianyan.cn/mdblog/20190107/xnkYUIRb7HAt.png?imageslim)

settings -> 修改为使用mysql数据库:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'drf_react',
        'USER': 'root',
        'PASSWORD': '密码',
        'HOST':'127.0.0.1'

    }
}
```
settings -> 设置本地化

```python
# 语言改为中文
LANGUAGE_CODE = 'zh-hans'

# 时区改为上海
TIME_ZONE = 'Asia/Shanghai'

USE_I18N = True

USE_L10N = True

# 数据库存储使用时间，True时间会被存为UTC的时间
USE_TZ = False
```


在model定义就位之后，让我们一起创建一个migration(迁移) 通过运行:

```
python manage.py makemigrations leads
```

并最终 migrate 到数据库使用下面命令:

```
python manage.py migrate
```

可以看到mysql中生成的表:

![mark](http://myphoto.mtianyan.cn/mdblog/20190107/va0FFF2zj71H.png?imageslim)

非常棒， 下一小节我们将讨论序列化器和试图，但是首先我们需要一点小小的测试。

### Django REST with React: 一丢丢测试

此时此刻，你或许在惊叹"Valentino(作者名)， 咋测试这个app"

不会让TDD(测试驱动开发) 教程来把你搞崩溃，我将给你一些小技巧(tips)

我见过大量的Django 教程里都是这样开始写的:

```python
class SomeModelModelTest(TestCase):
    def setUp(self):
        SomeModel.objects.create(
            name=fake.name(),
            email=fake.email(),
            phone=fake.phone_number(),
            message=fake.text(),
            source=fake.url()
        )
    def test_save_model(self):
        saved_models = SomeModel.objects.count()
        self.assertEqual(saved_models, 2)
```

不要这样做，这对于测试Django model 或 Django ORM 都是没踩对点的(没啥用的)。

对于Django里的测试，这里有一个好的出发点:

- 不要测试 Django 的内建代码(models views 等)
- 不要测试 Python 的内建函数

总的来说: **不要测试那些已经被测试过的!**

那我该测些啥呢？ 你是否在Django的model中添加了一个 **custom method(自定义的方法)**？ 测它！

你是否有一个**custom view**(自定义的视图)呢？ 测它！ 但是我怎么知道要去测试啥呢？

通过安装工具 coverage 来帮自己一点忙

```
pipenv install coverage
```

然后，每当你添加一些代码到你的应用时，运行coverage:

```
coverage run --source='.' manage.py test
```

然后会产生报告:

```
coverage html
```

用浏览器打开 `/YOUR_CODE_DIR/django-drf-react-quickstart/htmlcov/index.html`,你将看到你需要测试啥。

如果你更想在命令行里看到测试报告，你可以运行:

```
coverage report
```

等会！ 你坚持看到了这？ 我感动死了。

不要停！ 紧紧抱住我的大腿， 下一节我们将看看序列化器(serializers)! 

breakpoint: 夺命三全 -> push

### Django REST with React: Django Rest serializers(序列化器) 

注意: 开始下面之前，请确保你仍然在`/YOUR_CODE_DIR/django-drf-react-quickstart/` 目录。

什么是序列化？ 什么是[Django REST 序列器](http://www.django-rest-framework.org/api-guide/serializers/)？

序列化是转换一个对象到另一种数据格式的一种动作。在对象转换完成后，我们可以将它保存到文件，或者在互联网中发送它。

为什么序列化如此必要呢？

思考一个Django model: 它是一个Python的嘞， 你该如何在浏览器中渲染一个Python的类到JSON 呢?

使用Django REST 序列器！

一个序列器 也可以反向工作: 它转换 **JSON 到 对象**

这种方式你可以:

- 通过转换Django的models到JSON，来在浏览器中查看它们。
- 制作 一个JSON 负载的 CURD(增删改查) 的请求到API

简要概括: DjangoREST 序列器 是操作模型到API的主宰。


创建一个新文件: ./leads/serializers.py. 这个LeadSerializer 接收我们的Lead model 与一些字段

```python
from rest_framework import serializers
from leads.models import Lead
class LeadSerializer(serializers.ModelSerializer):
    class Meta:
        model = Lead
        fields = ('id', 'name', 'email', 'message')
```

就像上面你能看到的代码里那样，我们继承了ModelSerializer。

一个 [ModelSerializer](http://www.django-rest-framework.org/api-guide/serializers/#modelserializer) 在Django REST中就像一个 [ModelForm](https://docs.djangoproject.com/zh-hans/2.1/topics/forms/modelforms/)

它非常适合于，任何你想接近 映射一个Model 到 一个Serializer 中时。

除了明确的定义需要映射的字段，你也可以映射model中的所有字段。


```python
from rest_framework import serializers
from leads.models import Lead
class LeadSerializer(serializers.ModelSerializer):
    class Meta:
        model = Lead
        fields = '__all__'
```

保存并关闭文件，我们离完成app更近了一步。

下面的章节里，我们将看一下 views(视图) 和 urls。

### Django REST with React: 设置视图控制器

注意: 开始下面之前，请确保你仍然在`/YOUR_CODE_DIR/django-drf-react-quickstart/` 目录。

如果你从其他框架转到Django，你可能感到惊奇的是: Django 没有控制器！

控制器用来封装处理 request 请求的逻辑并返回response。传统的MVC 体系 包含 Model(模型)， View(视图), Controller(控制器)

这样的MVC框架有: Rails, Phoenix, Laravel。

Django 是一个 MVT framework，它有的三层是: Model(模型) - View (视图) - Template(模板)。其中View(视图) 关注着请求和响应的一整个生命周期。

Django中很多类型的views: [function views 函数视图](https://docs.djangoproject.com/zh-hans/2.1/topics/http/views/), [class based views 基于类的视图](https://docs.djangoproject.com/zh-hans/2.1/topics/class-based-views/), and [generic views 通用视图](https://docs.djangoproject.com/zh-hans/2.1/topics/class-based-views/generic-display/).


虽然很多开发者相对于class based views(基于类的视图)更喜欢 function views (函数视图),但我是后者(基于类)的一个大粉丝

当我选择Django，因为我的核心价值要求是: 开发速度，DRY(dont repeat yourself)，更少的代码。

当已经存在一组合理的默认值时，我认为继续手写Code是没有意义的。

这是我的经验法则：

仅当我们想要自定义通用视图所花费的时间超过手动编写视图所花费的时间时，才使用函数视图。

与简单的Django一样，在Django REST 框架中，有很多方法可以编写视图:

- function based views(基于函数的视图)
- class based views(基于类的视图)
- generic API views(通用的API 视图)

在这个教程的范围内，我将使用 generic API view，目的是为了写更少的代码。

我们的简单app应该有:

- 列出模型中的集合
- 创建一个新的对象到数据库中

通过阅读[generic API views documentation](http://www.django-rest-framework.org/api-guide/generic-views/#generic-views) 我们可以看到一件提供给我们一个可以用于列出和创建models的view。

它就是 [ListCreateAPIView](http://www.django-rest-framework.org/api-guide/generic-views/#listcreateapiview)

ListCreateAPIView 接收一个queryset 和 一个序列化类作为参数。

打开 `./leads/views.py` 创建视图:

```python
from leads.models import Lead
from leads.serializers import LeadSerializer
from rest_framework import generics
class LeadListCreate(generics.ListCreateAPIView):
    queryset = Lead.objects.all()
    serializer_class = LeadSerializer
```

通过三行代码我们创建了一个可以处理GET 和 POST请求的view。

现在还缺点啥呢？ URL 映射！ 换句话说，我们需要映射URLs 到 视图

咋整？ 敬请期待下一节

### Django REST with React: 设置urls 路由

注意: 开始下面之前，请确保你仍然在`/YOUR_CODE_DIR/django-drf-react-quickstart/` 目录。

来自Rails, Phoenix, or Laravel 的朋友们，你可能又感到惊讶了，Django竟然没有路由配置。

虽然DRF 提出了  [resourceful router 资源化的路由](http://www.django-rest-framework.org/api-guide/routers/)，但最简单的映射URl到视图的方式还是[URL mapping](https://docs.djangoproject.com/zh-hans/2.1/topics/http/urls/)。

我们的目标是联通 LeadListCreate 到 api/lead/

换句话讲，我们想要制作一个GET 和 POST请求到api/lead/ 来 列出和创建models。

在 `./drf_react/urls.py`配置URL mapping 包含app(leads)中的urls

```python
from django.urls import path, include
urlpatterns = [
    path('', include('leads.urls')),
]
```

下一步是创建一个新的文件: `./leads/urls.py`:

```python
from django.urls import path
from . import views
urlpatterns = [
    path('api/lead/', views.LeadListCreate.as_view() ),
]
```

最后: 我们要让 `rest_framework` 注册进 INSTALLED_APPS。

打开 `./drf_react/settings.py` 然后添加app 进入INSTALLED_APPS。

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'leads', 
    'rest_framework' # 加入 rest framework
]
```

此时你应该可以通过健全性检查:

```python
python manage.py runserver
```

访问: http://127.0.0.1:8000/api/lead/ 你将会看到 [browsable API 可浏览式api](https://www.django-rest-framework.org/topics/browsable-api/)

![mark](http://myphoto.mtianyan.cn/mdblog/20190107/ylRh1mr8Dcmx.png?imageslim)

此时你可以通过网页上内建的表单来创建一些数据

在下一节中，我们将学习如何在Django 中喂养数据库(-v-)

### Django REST with React: (seeding)喂养数据库

注意: 开始下面之前，请确保你仍然在`/YOUR_CODE_DIR/django-drf-react-quickstart/` 目录。

你可以使用 [Django fixtures](https://docs.djangoproject.com/zh-hans/2.1/howto/initial-data/) 来填充数据库。

当你想要给前端一些demo数据时，Fixtures时非常有用的。

创建一个新文件夹: `./leads/fixtures`

然后创建一个新的文件: `./leads/fixtures/leads.json` 内容为下面的JSON:

```json
[
    {
        "model": "leads.lead",
        "pk": 1,
        "fields": {
            "name": "mtianyan",
            "email": "1147727180@qq.com",
            "message": "I am a happy pythoner",
            "created_at": "2019-01-07 00:00:00"
        }
    },
    {
        "model": "leads.lead",
        "pk": 2,
        "fields": {
            "name": "Tom",
            "email": "tomsomething@gmail.com",
            "message": "I want to talk about a Python project",
            "created_at": "2018-01-14 00:00:00"
        }
    }
]
```

保存并关闭这个文件，然后使用下面命令加载fixture:

```
python manage.py loaddata leads
```

![mark](http://myphoto.mtianyan.cn/mdblog/20190107/EiscvzVMGa1C.png?imageslim)

这就是全部了，下面的部分，我们将实现一个简单的React 前端!

### 译者添加: xadmin 管理 & drf 在线文档

#### 安装
```python
pipenv install https://codeload.github.com/sshwsfc/xadmin/zip/django2
```

#### 注册进INSTALL_APP

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'leads',          # 添加leads app
    'rest_framework', # 添加rest framework
    'xadmin',         # 添加 xadmin
    'crispy_forms',   # 添加 xadmin
]
```

#### 配置URL(django-drf-react-quickstart\drf_react\urls.py):

```python
from django.urls import path, include
import xadmin

urlpatterns = [
    path('', include('leads.urls')),
    path('xadmin/', xadmin.site.urls),
]
```

添加了xadmin的import 和 xadmin path url mapping。

#### 新建adminx.py(django-drf-react-quickstart\leads\adaminx.py)

```python
import xadmin
from xadmin import views
from .models import Lead
class BaseSetting(object):
	"""xadmin的基础信息配置"""
	enable_themes = True  # 开启主题功能
	use_bootswatch = True 

class GlobalSettings(object):
	"""xadmin通用信息配置"""
	site_title = "Leads Xadmin"
	site_footer = "mtianyan@qq.com"

# 注册设置信息到View
xadmin.site.register(views.BaseAdminView, BaseSetting)
xadmin.site.register(views.CommAdminView, GlobalSettings)

class LeadAdmin(object):
	"""
	list_display: 后台展示哪些字段
	search_fields: 后台可搜索哪些字段
	list_filter: 后台过滤器可使用哪些字段
	"""
	list_display = ['name', 'email','message']
	search_fields = ['name', 'email','message']
	list_filter = ['name', 'email','message','created_at']

xadmin.site.register(Lead, LeadAdmin)
```

#### 迁移数据库:

```
python manage.py makemigrations
python manage.py migrate
```

#### 创建后台管理员

```
python manage.py createsuperuser
```

#### 登录后台

运行

```
python manage.py runserver
```

访问: http://127.0.0.1:8000/xadmin/

![mark](http://myphoto.mtianyan.cn/mdblog/20190107/lL1zYB27EyMg.png?imageslim)

#### 在线api交互文档

- 安装需要的包:

```
pipenv install coreapi
```

- 配置url(django-drf-react-quickstart\drf_react\urls.py)

```python
from django.urls import path, include
import xadmin
from rest_framework.documentation import include_docs_urls # new 

urlpatterns = [
    path('', include('leads.urls')),
    path('xadmin/', xadmin.site.urls),
    path('api/docs/', include_docs_urls(title='Lead 在线API文档')), # new
]
```

- 添加注释以便在线文档生成

models.py

```python
from django.db import models

# Create your models here.
class Lead(models.Model):
    name = models.CharField(max_length=100,verbose_name="lead名字", help_text="lead名字")
    email = models.EmailField(verbose_name="邮箱", help_text="邮箱")
    message = models.CharField(max_length=300, verbose_name="信息", help_text="信息")
    created_at = models.DateTimeField(auto_now_add=True,verbose_name="创建时间", help_text="创建时间")
```

添加verbose_name用于xadmin，help_text用于drf

views.py

```python
class LeadListCreate(generics.ListCreateAPIView):
	"""
    get:
    返回所有已存在的lead对象的列表

    post:
    创建一个新的lead实例
	"""
	queryset = Lead.objects.all()
	serializer_class = LeadSerializer
```

- 运行:

```
python manage.py runserver
```

访问: http://127.0.0.1:8000/api/docs/

![mark](http://myphoto.mtianyan.cn/mdblog/20190107/hxMfWjIhNFwS.png?imageslim)

大功告成: 我们有了一个既有后台，又提供api，又有在线API文档的Lead服务啦。

敬请期待下次更新React篇！
敬请期待下次更新React篇！
敬请期待下次更新React篇！
