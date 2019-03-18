## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

## 3-2 配置表单页面

>上节教程(来学习本节前置条件):

- 对应第一次commit: 留言板仓库初始化。内容截止3-1一章结束。

**github仓库地址：https://github.com/mtianyan/DjangoGetStarted**

> 本节我们学习将表单页面配置进django项目中

### 必要的该说的，该了解的

**前置条件：**

>你已经学习了前面教程。将项目的文件夹目录结构，setting配置等修改完毕与我保持一致。

本节通过Django快速的配置一个**留言板页面**来学习

Django从请求到响应的整个完整流程。为我们开发在线教育平台打下基础。

![mark](http://upload-images.jianshu.io/upload_images/1779926-3d3bdb964307d03e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图便是本节教程所要用到的静态页面: 前往Github下载：`form.html`

>具体的业务：填写信息 -> 然后点击提交 ->数据被存储到数据库。

这个`html`是一个单文件，里面已经包含了`css` `js`内容。

### 将html文件整合进项目操作步骤

#### 将`html`文件直接复制进`templates`目录.

![mark](http://upload-images.jianshu.io/upload_images/1779926-d616eeeecbd35c8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 创建`static目录下的css文件夹` 和 `static/js`

-  在css中再新建一个`style.css`

![mark](http://upload-images.jianshu.io/upload_images/1779926-e13272527408e87a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

-  `form.html`中点击`<style>`标签左侧减号。将style内容收成一行。然后把这一行内容**剪切粘贴到**`style.css`

![mark](http://upload-images.jianshu.io/upload_images/1779926-c384afa4b721b75c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 粘贴进去之后，**将首尾两个`<style>`删除**,`shift + tab`可以将css格式化更整齐。

- 在`form.html`新建`<link>`来引入css。(文件里其实已经先加上了，学一种操作而已)

```html
<link rel="stylesheet" href="/static/css/style.css">
```
### 配置Django连接Mysql数据库

在`setting.py` 大概80行找到:

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'),
    }
}
```

这是Django默认与自己项目根目录下的`db.sqlite3`连接的设置。

![mark](http://upload-images.jianshu.io/upload_images/1779926-96af509256e1480f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们的项目是与`mysql`连接，所以我们要改成如下：

```
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'testdjango',
        'USER': 'root',
        'PASSWORD': '密码',
        'HOST': "127.0.0.1"
    }
}
```

其中的`name`应设置为我们中在Navicat中新建的数据库名字。**名字一定要保持一致**

这时要将我们之前建的表提前**全部删除**掉。

![mark](http://upload-images.jianshu.io/upload_images/1779926-93bff1ee45f39980.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 配置mysql驱动和seeting文件。
点击`Tools 菜单下 Run manage.py Task`我们会发现报错了：

```
    raise ImproperlyConfigured("Error loading MySQLdb module: %s" % e)
django.core.exceptions.ImproperlyConfigured: Error loading MySQLdb module: No module named MySQLdb
```

>由错误信息我们可以看出是因为没有安装数据库驱动模块`MySQLdb`

![mark](http://upload-images.jianshu.io/upload_images/1779926-6f85fb071df9cc13.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`cmd`下`workon`进我们的虚拟环境目录。`pip install mysql-python`
然后会发现报错：

![mark](http://upload-images.jianshu.io/upload_images/1779926-66c951bb0f17c24d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
_mysql.c(42) : fatal error C1083: Cannot open include file: 'config-win.h': 
No such file or directory error: command 
'"C:\Users\mtian\AppData\Local\Programs\Common\Microsoft\Visual C ++ 
for Python\9.0\VC\Bin\amd64\cl.exe"' failed with exit status 2
```
前往地址 https://www.lfd.uci.edu/~gohlke/pythonlibs/#mysql-python

下载`MySQL_python_1.2.5_cp27_none_win_amd64.whl`到本地,放到桌面。
然后使用下面命令进行安装：注意是在虚拟环境下。

```
cd Desktop
pip install MySQL_python-1.2.5-cp27-none-win_amd64.whl
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-3973b1d60a7762eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个时候我们再次点击`Tools 菜单下 Run manage.py Task `会看到已经没有刚才的错误。
![mark](http://upload-images.jianshu.io/upload_images/1779926-236e765f970744de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
但是会有红框里的警告，**面向强迫症解决方案是**在`setting.py` 新增`STATIC_ROOT = '/static/'`

但其实现在还没有用到这个参数。后面用到我们再配置。(推荐自行克服强迫症)

输入下面命令来生成表:
```
makemigrations
migrate
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-a7bf4b7ecb20cfc6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这时我们去Navicat查看会发现为我们生成了很多表。

![mark](http://upload-images.jianshu.io/upload_images/1779926-7d05cad1e40f6cf1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这些都是Django系统默认的**内置**数据表。

做完这些操作我们可以点击`Run`来运行项目，
然后到`http://127.0.0.1:8000/`来访问看是否运行成功。成功页面(It worked)

### 配置form页面展示出来：

`DjangoGetStarted/urls.py`修改如下:

```python
urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^form/$', getform) #这行是新增加的.
]
```

新增加`url(r'^form/$', getform)`,`^`是代表以`form`为开头，`$`代表以`/`结尾的地址。
这里`getform` 是对于这个`url`的相应处理的`view`。我们先去创建一个.

`message/views.py`添加如下代码：

```python
def getform(request):
    return render(request, 'message_form.html')
```
`request` 参数是一个django的`http request`对象。(基础)
这里我们可以按住`ctrl` + `左键` 跟踪到我们的`render`函数里面。
`Alt + 左箭头` 回来。

```python
def render(request, template_name,...):
```
源代码解读：可以看到我们的`render`需要一个`request对象`和`template_name`参数

**注意：记性好的还记得我们提供的源文件是form.html**

>知识点：django内置了很多html页面，form会先从内置中寻找。所以我们得改。

因此我们需要右键如下图`Refactor`修改`from.html` 为`message_form`

![mark](http://upload-images.jianshu.io/upload_images/1779926-8c11378b46744d7d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果我们的项目在运行，`ctrl + s`会自动重启我们的项目。

这时我们有了view，我们可以去配置完整的url了(前面已经配完整的检查一遍)：

`DjangoGetStarted/urls.py`
```
from message.views import getform

urlpatterns = [
    url(r'^admin/', admin.site.urls),
    url(r'^form/$', getform)
]
```
**这里我们不能加括号**否则会变成方法的调用。

按住`ctrl` + `render` 跟踪到我们的`url`函数里面查看源码如下:可以看到它除过一组正则表达式，还需要接收一个view对象。

```
def url(regex, view,...):
```
如果`getform`加上括号会报错：

```
TypeError: getform() takes exactly 1 argument (0 given)
```

访问`http://127.0.0.1:8000/` 正常结果：Page not found

```
Using the URLconf defined in DjangoGetStarted.urls, Django tried these URL patterns, in this order:

^admin/
^form/$
```
是因为我们在url中加入了个人的配置`^form/$`,它就不会采用默认配置了。

原因：(源码探究标记点)

这时访问：http://127.0.0.1:8000/form/

旧版本pycharm会报：`TemplateDoesNotExist`错误。我的新版本pycharm并没有出现。

重要代码在`DjangoGetStarted/settings.py` 60行左右

```
# 指明我们的templates目录路径
'DIRS': [os.path.join(BASE_DIR, 'templates')]
# 老版本pycharm创建django项目该值为空。
```
现在再次访问 http://127.0.0.1:8000/form/

**页面出来了但是样式没有。static目录下的css文件提示没有找到。**

![mark](http://upload-images.jianshu.io/upload_images/1779926-10ada3a639773c76.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Setting中静态文件的配置，这是因为我们setting中的

```
STATIC_URL = '/static/'
```
只说明了目录的名称。并没有指明查找的根路径。添加下面代码：

```
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static')
]
```

**过程中看似不停的出错**，其实是为了让大家更好记住该记住的。

### 项目配置流程图

我们刚才是以倒序：

1. 把html文件放进来
2. 通过简单的url配置来访问html。
3. 发现找不到页面，所以我们设置setting中`DIRS`
4. 文件找到了又说找不到静态文件，我们设置了`STATICFILES_DIRS`

![mark](http://upload-images.jianshu.io/upload_images/1779926-85d3e6c3bbded37a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这是我们的整体流程图，推荐新建一个项目再按照正向流程图来一遍。

后面我们的工作会围绕从`migration生成数据表往下的内容`展开。

GitHub地址：https://github.com/mtianyan/DjangoGetStarted

>本节结束对应于commit：
