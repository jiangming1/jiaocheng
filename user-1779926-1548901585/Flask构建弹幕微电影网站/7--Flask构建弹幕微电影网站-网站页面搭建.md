## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

已上线演示地址: http://movie.mtianyan.cn
项目源码地址：https://github.com/mtianyan/movie_project

# 前端搭建

## 实现前台后台html布局页面搭建

**前台页面源码链接：
https://github.com/mtianyan/movie_project_html**

- 将整个前台页面源码放入我们的staic/tpl目录下。

- 打开tpl/2-movie/index.html.

点击右上角在浏览器里预览网页。可以看到首页。

![首页](http://upload-images.jianshu.io/upload_images/1779926-824c0feea0d6afff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

网页整体分为：

- 幻灯片banner展示
- 标签的筛选
- 卡片的电影展览
- 分页效果
- 顶部的导航
- 底部的版权信息。
- 搜索
- 搜索结果的分页。点击播放
- 播放界面：电影介绍，评论。评论列表分页
- 会员登录注册界面
- 会员中心：修改资料，密码。评论记录修改
- 收藏电影。

### 前台布局搭建：

- 静态文件的引入

```
{{ url_for('static',filename='文件路径')}}
```

- 定义路由：

```
{{ url_for('模块名.视图名',变量=参数)}}
```
- 定义数据块：
```
{%block 数据块名称%} .... {% endblock %}
```

1. 首先在templated/home目录下创建home.html
>网站共用的部分是顶部和底部。

tpl/2-movie/nav.html就是我们需要的导航和底部。

将10-13行link标签修改为静态文件

```html
 <link rel="shortcut icon" href="{{ url_for('static',filename='base/images/logo.png') }}">
    <link rel="stylesheet" href="{{ url_for('static',filename='base/css/bootstrap.min.css') }}">
    <link rel="stylesheet" href="{{ url_for('static',filename='base/css/bootstrap-movie.css') }}">
    <link rel="stylesheet" href="{{ url_for('static',filename='base/css/animate.css') }}">

<img src="{{ url_for('static',filename='base/images/logo.png') }}" style="height:30px;">

 <script src="{{ url_for('static',filename='base/js/jquery.min.js') }}"></script>
<script src="{{ url_for('static',filename='base/js/bootstrap.min.js') }}"></script>
<script src="{{ url_for('static',filename='base/js/jquery.singlePageNav.min.js') }}"></script>
<script src="{{ url_for('static',filename='base/js/wow.min.js') }}"></script>
<script src="{{ url_for('static',filename='lazyload/jquery.lazyload.min.js') }}"></script>
```
>将原本的引用外部的css/图片/js的部分。改写为static静态目录下文件名。
filename 应该是staic之后的相对地址。

在如下的内容部分定义content块。

```html
<!--内容-->
<div class="container" style="margin-top:76px">
    {% block content %}{% endblock %}
</div>
<!--内容-->
```

打开home模块的视图处理器（views.py）：

- 定义我们的路由。

home/view,py

```python
from . import home
from flask import render_template

@home.route("/")
def index():
    return render_template("home/index.html")
```

此时我们该去创建我们的index.html.（home/index.html）

```html
{% extends "home.html" %}

{% block %}
<h1>helloworld</h1>
{% endblock %}
```

继承home.html，然后写一个块。

打开入口脚本。运行manage.py

![报错](http://upload-images.jianshu.io/upload_images/1779926-7feb311137aef4ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

改为：继承home/重写content块

```html
{% extends "home.html" %}

{% block content %}
<h1>helloworld</h1>
{% endblock %}
```

报错：
>jinja2.exceptions.TemplateNotFound
jinja2.exceptions.TemplateNotFound: home.html

因为我们的路径有问题：

```html
{% extends "home/home.html" %}

{% block content %}
<h1>helloworld</h1>
{% endblock %}
```

将static/tpl/static/base剪切到static/

![项目目录](http://upload-images.jianshu.io/upload_images/1779926-8962192f3be382b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时再去运行manage.py 

访问我们可以看到首页的效果

![首页效果](http://upload-images.jianshu.io/upload_images/1779926-126e98009f650765.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 会员登录页面的搭建

需要掌握的知识点：

```python
#登录
@home.route("/login/")
def login():
	return render_template("home/login.html")

#退出
@home.route("/logout/")
def logout():
	return redirect(url_for('home.login'))
````
>上述登录和退出分别采用return渲染的模板以及直接重定向。

### 编码

app\home\views.py：
添加代码

```python
from flask import render_template,url_for,redirect
#登录
@home.route("/login/")
def login():
	return render_template("home/login.html")

#退出
@home.route("/logout/")
def logout():
	return redirect(url_for('home.login'))
#重定向到home模块下的登录。
```

- 在app\templates\home下新建login.html并将app/static/tpl/2-movie/login.html中内容注释的部分复制出来贴进去。

![login.html](http://upload-images.jianshu.io/upload_images/1779926-ca186cfb01d3e2ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 修改app/templates/home/home.html的登录退出按钮的href

```html
<a class="curlink" href="{{url_for('home.index')}}"><span class="glyphicon glyphicon-film"></span>&nbsp;电影</a>
<a class="curlink" href="{{ url_for('home.login')}}"><span class="glyphicon glyphicon-log-in"></span>&nbsp;登录</a>
<a class="curlink" href="{{ url_for('home.logout')}}"><span class="glyphicon glyphicon-log-out"></span>&nbsp;退出</a>
```

注意：传入的是一个`'home.login'`不要把单引号漏了

修改完成后进入manage.py点击run。访问

可以看到登录界面已经好了http://127.0.0.1:5000/login/

![登录界面](http://upload-images.jianshu.io/upload_images/1779926-e4c879ad73f5e3b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击登录和点击退出。也都能正确的跳转到登录。

## 会员注册页面的搭建

### 编码

定义home/views.py:

```python
# 会员注册
@home.route("/register/")
def regist():
    return render_template("home/register.html")
```

- 在app\templates\home下新建register.html并将app/static/tpl/2-movie/register.html中注释表明为内容的部分复制出来贴进去。

这里笔者的强迫症犯了，请自行将所有的regist 改为register写全。

>此时我们点击电影helloworld，点击登录，注册，分布展示对应界面。点击退出，会跳转到登录。

>此处为笔者上次前去考研中断项目的地址

>对应提交commit: 项目中断重启初始化: 首页路由，登录，注册已完成。

## 会员中心界面搭建

### 基础语句概要

```
# 会员中心
@home.route("/user/")
# 修改密码
@home.route("/pwd/")
# 评论记录
@home.route("/comments/")
# 登录日志
@home.route("/loginlog/")
# 收藏
@home.route("/moviecol/")
```

### 实际编码与模板创建

```python
@home.route("/user/")
def user():
    """
    用户中心
    """
    return render_template("home/user.html")


@home.route("/pwd/")
def pwd():
    """
    修改密码
    """
    return render_template("home/pwd.html")


@home.route("/comments/")
def comments():
    """
    评论记录
    """
    return render_template("home/comments.html")


@home.route("/loginlog/")
def loginlog():
    """
    登录日志
    """
    return render_template("home/loginlog.html")


@home.route("/moviecol/")
def moviecol():
    """
    收藏电影
    """
    return render_template("home/moviecol.html")
```

然后分别创建对应的template

![mark](http://upload-images.jianshu.io/upload_images/1779926-802394fd55a93bd5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

定义会员中心界面。

![mark](http://upload-images.jianshu.io/upload_images/1779926-46fa8c98c71feda9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到整个微电影用户中心的左侧是一个菜单。这个菜单是用户中心的这些功能共用的。

新建menu.html

将app/static/tpl/2-movie/user.html中的菜单拷贝过来。

![mark](http://upload-images.jianshu.io/upload_images/1779926-037d2ddf8abdbfd7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

修改我们的url链接

![mark](http://upload-images.jianshu.io/upload_images/1779926-f1e30a505f5e696a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**编写user.html**

![mark](http://upload-images.jianshu.io/upload_images/1779926-95b23fe82a8dfb55.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其中的contaier部分为app/static/tpl/2-movie/user.html中的内容部分

可能报错(已解决):

```
AssertionError: View function mapping is overwriting an existing endpoint function: home.pwd
```

说明视图中定义了两个重名的函数。

![mark](http://upload-images.jianshu.io/upload_images/1779926-f810f4b5af68cdee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

包含menu页面。

此时home.html中通往会员的url还没有打通，前往修改。

![mark](http://upload-images.jianshu.io/upload_images/1779926-eea8ea306a5b2715.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到user中还有和我们的home页面之余的自己独特的东西

![mark](http://upload-images.jianshu.io/upload_images/1779926-e050727f17d0fe88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将home中的style合并一下。

![mark](http://upload-images.jianshu.io/upload_images/1779926-d5c02253c2d024c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在home.html中定义一个css 数据块，用于其儿子们重写该数据块。

![mark](http://upload-images.jianshu.io/upload_images/1779926-0d54f03f12f8fc2f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

前往user.html中重写

将app/static/tpl/2-movie/user.html中的style部分拷贝过来

![mark](http://upload-images.jianshu.io/upload_images/1779926-180927d0abc0e0cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-724852aa7081e2d8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将user收缩至如图结构，覆盖到pwd comments loginlog moviecol

将其中的col-md-9 分别用/tpl/2-movie/中的这部分代替。

将每个页面内的style覆盖css数据块

>找猫画虎，自行完成环节。

### 实现菜单激活某一个具体项

为menu中的各项添加id

![mark](http://upload-images.jianshu.io/upload_images/1779926-bfe38886e4906e1d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在home.html中添加数据块block js，因为我们想在menu中使用jQuery动态添加class

![mark](http://upload-images.jianshu.io/upload_images/1779926-55a64656808e6c41.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

user.html中重新改block

![mark](http://upload-images.jianshu.io/upload_images/1779926-2d354a2512b2b48a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后左眼看着图中顺序，右手去其他文件中分别m2，m3

![mark](http://upload-images.jianshu.io/upload_images/1779926-83e30eb27d109591.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时并没有实现激活样式。控制台查看发现是lazyload没加载

将tpl中 lazyload拷贝到static

![mark](http://upload-images.jianshu.io/upload_images/1779926-985ed751fe6e101d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

出现两个激活，是因为忘记删除默认的user active 前往menu中删除即可。

点击登录注册暂时设置为跳转会员中心。

将user.html替换为

![mark](http://upload-images.jianshu.io/upload_images/1779926-3cf24e5b3fa4f892.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>提交commit：前台: 会员中心搭建

提交的代码有一些问题:

敲重点: jinja2 urlfor中括号里面必须单引号包裹。不能直接填home.user

```
jinja2.exceptions.UndefinedError
jinja2.exceptions.UndefinedError: 'home' is undefined
```

## 电影列表页面搭建

```python
# 列表
@home.route("/")
def index():
	return render_template("home/index.html")

# 动画
@home.route("/animation/")
def animation():
	return render_template("home/animation.html")

```

新建layout.html 复制home.html 到 layout

将内容部分。

![mark](http://upload-images.jianshu.io/upload_images/1779926-9d69445e0add94e5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>只保留content 

打开index.html继承layout

![mark](http://upload-images.jianshu.io/upload_images/1779926-dafc47d88e2db611.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将tpl/index.html中的电影列表部分粘贴到content当中。

将html收至如图形状

![mark](http://upload-images.jianshu.io/upload_images/1779926-12192d0bc44f3412.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

只保留前四个col-md-3，其余先删除了。

将热门电影也拷过来。可以看到热门电影指向一个iframe。animation

将tpl/animation的代码复制到home/animation(新建)中

将其配套的静态文件，在static目录下新建一个anim目录存放。

![mark](http://upload-images.jianshu.io/upload_images/1779926-661f39ee0cba3f65.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将animation.html中的静态文件自行进行修改。

![mark](http://upload-images.jianshu.io/upload_images/1779926-1d0f4055e6cd5792.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>配置首页指向animation的url

只保留一个col-md-3使用for循环创建12个。

![mark](http://upload-images.jianshu.io/upload_images/1779926-116873eb3a80f42d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这时运行项目，可能遇到的报错。

1. 静态文件的格式，单引号没有成对添加
2. 图片不显示，是忘记添加.jpg后缀

列表的holder.js没有显示

![mark](http://upload-images.jianshu.io/upload_images/1779926-b7f34ee51c4987d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时在layout中添加对于holder.js的支持。

![mark](http://upload-images.jianshu.io/upload_images/1779926-b4944f389e4f1290.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 电影搜索页面搭建

```python
@home.route("/search/")
def search():
    """
    搜索
    """
    return render_template("home/search.html")
```

创建search.html,内容如下

![mark](http://upload-images.jianshu.io/upload_images/1779926-45dd16f9aac70833.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因为home.html的content外围已经有一个container div了，所以将tpl/search.html中的row部分拷进来。

![mark](http://upload-images.jianshu.io/upload_images/1779926-73d89b1df04492a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-7638610310ef1315.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将div media 删除到剩下一个。使用for进行填充。

![mark](http://upload-images.jianshu.io/upload_images/1779926-d42194ff15e3323b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


前往home模板中为搜索添加url

![mark](http://upload-images.jianshu.io/upload_images/1779926-1fd55786fceaa2e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

同时修改layout和home两个html中的search url 链接

如果发现holder.js没有显示图片站位，前去添加图片占位

![mark](http://upload-images.jianshu.io/upload_images/1779926-f52ef17edbe812ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 电影详情页面搭建

```
@home.route("/play/")
def play():
    """
    播放
    """
    return render_template("home/play.html")
```

新建play.html

![mark](http://upload-images.jianshu.io/upload_images/1779926-c061905416838415.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

跟搜索差不多照猫画虎做出来。

将头部中的play页面独有的css内容拷贝过来，用css数据块包裹

![mark](http://upload-images.jianshu.io/upload_images/1779926-f6601ad8cc9d8db7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将带有播放页面的静态资源都分布放入css 和 js 块中。

将url路径改好为url_for格式，此时可以发现我们的那些static下并没有这些。
将tpl中静态资源复制过去。

报错:

```
jinja2.exceptions.TemplateSyntaxError
jinja2.exceptions.TemplateSyntaxError: expected token ',', got 'static'
```

>一般原因都是urlfor标签未正常

将home中的.html的跳转全部改成url for
将index中的播放同理修改

![mark](http://upload-images.jianshu.io/upload_images/1779926-53b7bf1cfd475ea9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

搜索中的电影播放与收藏中的电影播放。

收藏中电影项使用for range 大法处理

# 404页面搭建

```python
@app.errorhandler(404)
def page_not_found(error):
    """
    404
    """
    return render_template("home/404.html"), 404
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-3c7de02db831ff24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


新建404页面。将tpl下404复制过来

将static文件全部自行修改。

输入错误地址依然提示not found 是因为我们依然打开的是debug模式。

>以上是我在django中的理解。flask中debug模式下仍然可以404

修改404中 index的url链接。

>对应的commit提交: 

第五章完结
