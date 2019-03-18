## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

#Flask 构建微电影视频网站   

已上线演示地址: http://movie.mtianyan.cn
项目源码地址：https://github.com/mtianyan/movie_project

>上一节教程地址：http://www.jianshu.com/p/387360c9c4b1
本节教程对应github : commit：首页搭建-0

持续更新教程与代码commit。欢迎大家一起学习，star。

##前端搭建

###实现前台后台html布局页面搭建

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

####前台布局搭建：

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
 <script src="{{ url_for('static',filename='base/js/jquery.min.js') }}"></script>
<script src="{{ url_for('static',filename='base/js/bootstrap.min.js') }}"></script>
<script src="{{ url_for('static',filename='base/js/jquery.singlePageNav.min.js') }}"></script>
<script src="{{ url_for('static',filename='base/js/wow.min.js') }}"></script>
<script src="{{ url_for('static',filename='lazyload/jquery.lazyload.min.js') }}"></script>
```
>将原本的引用外部的css/图片/js的部分。改写为static静态目录下文件名。
filename 应该是staic之后的相对地址。

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
