## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

已上线演示地址: http://movie.mtianyan.cn
项目源码地址：https://github.com/mtianyan/movie_project

## 实战小项目介绍

1. 项目介绍
2. 项目演示
3. 开发思路

>实现用户注册，用户登录，退出登录，添加文章
文章分页列表，修改文章，删除文章等功能

开发思路:

![mark](http://upload-images.jianshu.io/upload_images/1779926-a7b12ff97e2fe8bc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 创建路由，视图，模板，静态文件

路由(route):

```python
@app.route("/login/", methods=["GET","POST"]) # 用户登录
@app.route("/logout/", methods=["GET"]) # 用户退出
@app.route("/register/", methods=["GET","POST"]) # 用户注册
@app.route("/art/add", methods=["GET","POST"]) # 发布文章
@app.route("/art/edit/<int:id>/", methods=["GET","POST"]) # 编辑文章
@app.route("/art/list/", methods=["GET"]) # 文章列表
@app.route("/art/del/<int:id>/", methods=["GET"]) # 删除文章
```

>路由就是我们访问网站时需要在浏览器中输入的一串地址。

视图(views):

```
login # 用户登录
logout # 用户退出
register # 用户注册
art_add # 发布文章
art_edit # 编辑文章
art_list # 文章列表
art_del # 删除文章
```

>通过后端与前端的结合，通过视图书写一些后端的逻辑。

模板(templates):

```
login.html # 用户登录
register.html # 用户注册
art_add.html # 发布文章
art_edit.html # 编辑文章
art_list.html # 文章列表
```

>html展示页面

静态文件(static):

```
css # 层叠样式表
js # javascript脚本
ue # 百度ueditor富文本编辑器
```

为项目创建自己的虚拟环境

```
mkvirtualenv -p=D:\softEnvDown\Anaconda2\envs\py36\python.exe flaskgetstarted
pip install flask
```

创建一个纯python项目，选择我们刚才的虚拟环境。

![mark](http://upload-images.jianshu.io/upload_images/1779926-90c675a296471a0c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将项目创建为如上图所示。

views.py:

```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/2/12 0012 00:34'

from flask import  Flask

app = Flask(__name__)

# login # 用户登录
# logout # 用户退出
# register # 用户注册
# art_add # 发布文章
# art_edit # 编辑文章
# art_list # 文章列表
# art_del # 删除文章
```

下面我们开始定义这些视图

```
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/2/12 0012 00:34'

from flask import Flask, render_template, redirect, url_for

app = Flask(__name__)


# login 用户登录
@app.route("/login/", methods=["GET", "POST"])  # 用户登录
def login():
    # 返回渲染模板
    return render_template("login.html")


# logout 用户退出(302跳转到登录页面)
@app.route("/logout/", methods=["GET"])  # 用户退出
def logout():
    # 重定向到指定的视图对应url，蓝图中才可以使用
    # return redirect(url_for("app.login"))

    # 直接跳转路径
    return  redirect("/login/")


# register 用户注册
@app.route("/register/", methods=["GET", "POST"])  # 用户注册
def register():
    return render_template("register.html")


# art_add 发布文章
@app.route("/art/add/", methods=["GET", "POST"])  # 发布文章\
def art_add():
    return render_template("art_add.html")


# art_edit 编辑文章
# 传入整型id参数
@app.route("/art/edit/<int:id>/", methods=["GET", "POST"])  # 编辑文章
def art_edit(id):
    return render_template("art_edit.html")


# art_list 文章列表
@app.route("/art/list/", methods=["GET"])  # 文章列表
def art_list():
    return render_template("art_list.html")


# art_del 删除文章
@app.route("/art/del/<int:id>/", methods=["GET"]) # 删除文章
def art_del(id):
    return redirect("/art/list")


if __name__ == "__main__":
    app.run(debug=True, host="127.0.0.1", port=8080)
```

可能遇到的访问错误:

```
builtins.TypeError
TypeError: art_del() got an unexpected keyword argument 'id'
```

你的方法没有将url中的参数接收进来。

定义视图使用函数方法，定义路由使用装饰器方法。
`render_template()` 与 `redirect()`302跳转

- url中包含可变参数，并在视图函数中接收
- 如何启动：name main app.run

![mark](http://upload-images.jianshu.io/upload_images/1779926-3e0e0b8ca0f53284.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## Jinja2模板语法

1. 继承

```
{% extends "父模板路径" %}
```

2. 数据块(页面块)

```
{% block 块名 %} ... {% endblock %}
```
3. 路由生成

```
{{ url_for("模块名.视图名") }}
```

4. 静态文件加载

静态文件目录

```
{{ url_for('static', filename='静态文件路径')}}
```
5. 循环语句:

```
{% for 条件 %} ... {% endfor %}
```

6. 条件语句

显示什么不显示什么

```
{% if 条件 %} ... {% endif %}
```

## 编写用户注册登录界面(Bootstrap)

Bootstrap

快速入门。

https://getbootstrap.com/docs/4.0/getting-started/download/

https://github.com/wangfupeng1988/wangEditor/releases

下载富文本编辑器将其中的release拷贝进目录并改名字

![mark](http://upload-images.jianshu.io/upload_images/1779926-3d3efc4d04d490fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-0fcb7a473dbf75fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```html
<!doctype html>
<html lang="zh-cn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>登录页面</title>
</head>
<body>

</body>
</html>
```

>自己补全doctype html zh-cn

自行下载jquery.slim.min.js
自行下载 popper.min.js

>百度在各自官网即可下载

```
<link rel="stylesheet" type="text/css" href="{{ url_for('static',filename='css/bootstrap.min.css') }}">


<script src="{{ url_for('static',filename='js/jquery-3.3.1.slim.min.js') }}"></script>
<script src="{{ url_for('static',filename='js/popper.min.js') }}"></script>
<script src="{{ url_for('static',filename='js/bootstrap.min.js') }}"></script>
```

>注意后面三个js的加载顺序。以及css(head中) js(body中)

```
<div class="container" style="margin-top: 100px">
    <div class="row">
        <div class="col-md-12">
            <h3 class="text-center">文章管理系统</h3>
        </div>
    </div>
</div>
```

定义容器，内含行，定义栅格系统占12个位置。
h3标签使之变大，text-center居中，style margintop 距离上面100px

```
        <div class="col-md-6 offset-md-3">
            <div class="card-header">登录</div>
            <div class="card-body">
                <form>
                    <div class="form-group">
                        <label>账号</label>
                        <input type="text" class="form-control" placeholder="请输入账号">
                    </div>
                    <div class="form-group">
                        <label>密码</label>
                        <input type="password" class="form-control" placeholder="请输入密码">
                    </div>
                </form>
            </div>
        </div>
```

使用6个栅栏位置，并使用(12-6)/2进行居中操作。

- 使用card-header card-body定义卡片头与内容
- form group 中包含 label 和 input

定义登录

```
<a href="/register/">没有账号?前往注册</a>
<br>
 <a class="btn btn-primary" href="/art/list">登录</a>
```

新建user_base.html

将除了表单的都拷过来

![mark](http://upload-images.jianshu.io/upload_images/1779926-83e641ddfb5b23ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

把 login中的除form删除掉

![mark](http://upload-images.jianshu.io/upload_images/1779926-c83203d3f439b7c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时访问登录的字不见了。因为title没有值传递过来。

```
return render_template("login.html", title ="登录")
```
### 注册页面

![mark](http://upload-images.jianshu.io/upload_images/1779926-2229a8a1f51fe92d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注册我们要引入自定义的js。

所以在base中在定义一个数据块，然后复写它。

![mark](http://upload-images.jianshu.io/upload_images/1779926-2f9771d77e94fabf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

http://www.bootcdn.cn/holder/

```
<script src="https://cdn.bootcss.com/holder/2.9.4/holder.min.js"></script>
```

引入holder.min.js图片占位。

传递注册title

```
<form>
                    <div class="form-group">
                        <label>账号</label>
                        <input type="text" class="form-control" placeholder="请输入账号">
                    </div>
                    <div class="form-group">
                        <label>密码</label>
                        <input type="password" class="form-control" placeholder="请输入密码">
                    </div>
                    <div class="form-group">
                        <label>确认密码</label>
                        <input type="password" class="form-control" placeholder="请确认密码">
                    </div>
                    <div class="form-group">
                        <label>验证码</label>
                        <input type="text" class="form-control" placeholder="请输入验证码">
                        <img data-src="holder.js/180x50" style="margin-top: 6px">
                    </div>
                    <a href="/login/">已有账号!前往登录</a>
                    <br>
                    <a class="btn btn-primary" href="/login/">注册</a></form>
```

## 编写文章编辑列表页面(百度ueditor)

头部和菜单是公共的部分:
```
<!doctype html>
<html lang="zh-cn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>文章管理系统</title>
    <link rel="stylesheet" type="text/css" href="{{ url_for('static',filename='css/bootstrap.min.css') }}">

{% block css %} {% endblock %}

</head>
<body>

{% block js %} {% endblock %}
<script src="{{ url_for('static',filename='js/jquery-3.3.1.slim.min.js') }}"></script>
<script src="{{ url_for('static',filename='js/popper.min.js') }}"></script>
<script src="{{ url_for('static',filename='js/bootstrap.min.js') }}"></script>
<script src="https://cdn.bootcss.com/holder/2.9.4/holder.min.js"></script>
</body>
</html>
```

首先将文件都引入。

### 定义导航。

使用bootstrap的nav标签

![mark](http://upload-images.jianshu.io/upload_images/1779926-958f5ba3d2ce4757.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
<nav class="navbar navbar-light" style="background-color: #062c33">
    <div class="container">
        <a class="navbar-brand" style="color: #ffffff"> 文章管理系统</a>
        <a class="btn btn-outline-warning" href="/login/">账号名称 -> 退出</a>
    </div>
</nav>
```

定义左侧菜单：

```
<div class="container" style="margin-top: 6px">
    <div class="row">
        <div class="col-md-3">
            <div class="list-group">
                <a href="/art/add/" class="list-group-item list-group-item-action">发布文章</a>
                <a href="/art/list/" class="list-group-item list-group-item-action">文章列表</a>
            </div>
        </div>
        <div class="col-md-9"></div>
    </div>
</div>
```

其中左侧菜单占3个栅栏，剩下的9个归文章所有。

```
        <div class="col-md-9">
            <div class="card">
                <div class="card-header">文章列表</div>
                 <div class="card-body">
                     <table class="table table-bordered">
                         <thead>
                         <tr>
                             <th>标题</th>
                             <th>分类</th>
                             <th>封面</th>
                             <th>作者</th>
                             <th>发布时间</th>
                             <th>管理操作</th>
                         </tr>
                         </thead>
                         {% for v in range(1,11) %}
                     <tr>
                         <td>mtianyan编程之旅</td>
                         <td>python</td>
                         <td><img data-src="holder.js/75x40"></td>
                         <td>mtianyan</td>
                         <td>2018-02-12 12:00:00</td>
                         <td>
                             <button class="btn btn-sm btn-outline-success">编辑</button>
                             <button class="btn btn-sm btn-outline-danger">删除</button>
                         </td>
                     </tr>
                     {% endfor %}
                     </table>
                 </div>
            </div>
        </div>
```

成型效果：

![mark](http://upload-images.jianshu.io/upload_images/1779926-119f2c0e421138a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

分页:

```
 <nav>
                         <ul class="pagination">
                             <li class="page-item"><a class="page-link" href="#">上一页</a></li>
                             <li class="page-item"><a class="page-link" href="#">1</a></li>
                             <li class="page-item"><a class="page-link" href="#">2</a></li>
                             <li class="page-item"><a class="page-link" href="#">3</a></li>
                             <li class="page-item"><a class="page-link" href="#">4</a></li>
                             <li class="page-item"><a class="page-link" href="#">下一页</a></li>
                         </ul>
                     </nav>
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-95673ca6b185ba1c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

创建art_base页面作为爸爸页面

将刚才的art_list页面内容全部拷贝。然后把可变部分9栏删除。用block content代替

![mark](http://upload-images.jianshu.io/upload_images/1779926-305b758f07d3afae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

原本list文件中只保留可变部分9栏，继承art_base

![mark](http://upload-images.jianshu.io/upload_images/1779926-3eb736ac1d216273.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-616b9061a0570d77.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到如上图内容没有居中显示。我们可以右键检查打开查看元素

```
.table td,
.table th {
    padding: .75rem;
    vertical-align: top;
    border-top: 1px solid #dee2e6;
}
```

改为垂直居中

```
    .table td,
    .table th{
        vertical-align: middle;
    }
```

list中重写css block

![mark](http://upload-images.jianshu.io/upload_images/1779926-402755aa13d1ae04.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 发布文章

- 首先继承art_base

![mark](http://upload-images.jianshu.io/upload_images/1779926-2dcb45c4918b2688.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

菜单分离出来使用**include**引入。

![mark](http://upload-images.jianshu.io/upload_images/1779926-aa16d5b2852ee259.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-58ea80a122c1f3a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 实现active

添加block js代码

```
<script>
    $(document).ready(function () {
        $("#m2").addClass("active");
    });
</script>
```

列表页为m2 添加页为m1

**可能遇到的不生效的错误**

document是使用jQuery实现的，所以我们的blockjs一定要在jQuery引入之后

![mark](http://upload-images.jianshu.io/upload_images/1779926-54c7554f3405e30e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

views中传入标题: 

```
def art_list():
    return render_template("art_list.html",title="文章列表")
```

art_add 同理可得

然后在art_list和art_add中使用参数。

![mark](http://upload-images.jianshu.io/upload_images/1779926-af3cec901bba4298.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

开始编写art_add 的content

```
<form>
    <div class="form-group">
        <label>标题</label>
        <input type="text" class="form-control" placeholder="请输入标题！">
    </div>
    <div class="form-group">
        <label>分类</label>
        <select class="form-control">
            <option value="1">科技</option>
            <option value="2">搞笑</option>
            <option value="3">军事</option>
        </select>
    </div>
    <div class="form-group">
        <label>封面</label>
        <input type="file" class="form-control-file"style="margin-left: 2px;">
        <img data-src="holder.js/300x160" style="margin-top: 6px;">
        <a class="btn btn-primary" style="margin-top: 6px;">上传封面</a>
    </div>
    <div class="form-group">
         <label>内容</label>
        <textarea class="form-control"></textarea>
    </div>
</form>
```
此时我们的内容还并不是那个富文本，因此我们这时候需要将富文本集成。

前往官网下载php版本拷入项目目录

![mark](http://upload-images.jianshu.io/upload_images/1779926-142b99fdbf3e0688.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

加载ue需要的js
![mark](http://upload-images.jianshu.io/upload_images/1779926-c9253a61f3f40424.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

给我们的内容一个id=content

```
    <div class="form-group">
         <label>内容</label>
        <textarea class="form-control" id="content"></textarea>
    </div>
```

然后在js中

```
    var ue = UE.getEditor("content");
```

将原来的textera的class去掉。修复样式

```
    <div class="form-group">
         <label>内容</label>
        <textarea id="content" style="height: 300px;"></textarea>
    </div>
    <button type="button" class="btn btn-primary">发布文章</button>
```

## 使用sqlalchemy定义数据表

操作mysql

```
ssh root@192.168.0.7
mysql -uroot -p
\s
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-79c30749b9b56be2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>可以看到当前的状态。

```
vim /etc/my.cnf
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-aa3bc2886b2ce8f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-cdabc048ea34a3ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

修改mysql编码。

![mark](http://upload-images.jianshu.io/upload_images/1779926-ca444803cf6352e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

然后重启服务`service mysql restart`。再次查看。

![mark](http://upload-images.jianshu.io/upload_images/1779926-f1e24e0d2dddc2ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

四个utf8表示设置成功。

centos: 编辑`vim /ect/my.cnf`

创建数据库 artcms

原生方法与model ORM方式对比

创建artcms.sql并思考我们需要的字段

```
/*
用户表
0. id编号
1. 账号
2. 密码
3. 注册时间
 */

CREATE TABLE if NOT EXISTS user(
  id int unsigned not null auto_increment key comment "主键ID",
  account varchar(20) not null comment "账号",
  pwd  varchar(100) not null comment "密码",
  addtime datetime not null comment "注册时间"
)engine=InnoDB DEFAULT charset=utf8 comment "用户";

/*
文章表
0. id编号
1. 标题
2. 分类
3. 作者
4. 封面
5. 内容
6. 发布时间
 */
CREATE TABLE if NOT EXISTS article(
  id int unsigned not null auto_increment key comment "主键ID",
  title varchar(100) not null comment "标题",
  category  tinyint unsigned not null comment "分类",
  user_id int unsigned not null comment "作者",
  logo varchar(100) not null comment "封面",
  content mediumtext not null comment "文章",
  addtime datetime not null comment "发表时间"
)engine=InnoDB DEFAULT charset=utf8 comment "文章";
```

操作数据库需要使用原生语句。以后不用mysql 数据库迁移存在问题。

安装mysql client

```
lsof -i:3306
pip install Flask-SQLAlchemy
```

```
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/2/12 0012 00:35'
from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
app.config["SQLALCHEMY_DATABASE_URI"] = "mysql://root:tp158917@127.0.0.1:3306/artcms"
# 如果设置成 True (默认情况)，Flask-SQLAlchemy 将会追踪对象的修改并且发送信号。
app.config["SQLALCHEMY_TRACK_MODIFICATIONS"] = True

# 绑定app至SQLAlchemy
db = SQLAlchemy(app)

"""
用户模型
0. id编号
1. 账号
2. 密码
3. 注册时间
"""


class User(db.Model):
    __tablename__ = "user"
    id = db.Column(db.Integer, primary_key=True)  # 编号id
    account = db.Column(db.String(20), nullable=False)  # 账号非空
    pwd = db.Column(db.String(100), nullable=False)  # 密码非空
    add_time = db.Column(db.DateTime, nullable=False)  # 注册时间

    # 查询时的返回
    def __repr__(self):
        return "<User %r>" % self.account


"""
文章模型
0. id编号
1. 标题
2. 分类
3. 作者
4. 封面
5. 内容
6. 发布时间
"""


class Article(db.Model):
    __tablename__ = "article"
    id = db.Column(db.Integer, primary_key=True)  # 编号id
    title = db.Column(db.String(100), nullable=False)  # 标题非空
    category = db.Column(db.Integer, nullable=False)  # 编号id
    user_id = db.Column(db.Integer, nullable=False)  # 作者
    logo = db.Column(db.String(100), nullable=False)  # 封面
    content = db.Column(db.Text, nullable=False)  # 内容
    add_time = db.Column(db.DateTime, nullable=False)  # 发布时间

    # 查询时的返回
    def __repr__(self):
        return "<Article %r>" % self.title
```

```
# 执行创建表语句
if __name__ == "__main__":
    db.create_all()
```

执行时警告

```
 Warning: (1366, "Incorrect string value: '\\xD6\\xD0\\xB9\\xFA\\xB1\\xEA...' for column 'VARIABLE_VALUE' at row 480")
```

>windows下暂无解决方案。把mysql所有编码都设为utf8也不行。连接一个linux下数据库没有该警告。

## 使用wtforms定义表单

输入框的集合被我们统称为表单，对于表单进行统一的管理，对于表单数据进行统一的验证。

集中化的管理表单: wtforms

安装`Flask-WTF`，并定义登录的表单

```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/2/12 0012 00:34'
from flask_wtf import FlaskForm
from wtforms import StringField, PasswordField, SubmitField

"""
登录表单:
1. 账号
2. 密码
3. 登录按钮
"""


class LoginForm(FlaskForm):
    account = StringField(
        label=u"账号",
        validators=[],
        description=u"账号",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入账号!"
        }
    )
    pwd = PasswordField(
        label=u"密码",
        validators=[],
        description=u"密码",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入密码!"
        }
    )
    submit = SubmitField(
        u"登录",
        render_kw={
            "class": "btn btn-primary"
        }
    )
```

在views中使用loginform

```
from forms import LoginForm

# login 用户登录
@app.route("/login/", methods=["GET", "POST"])  # 用户登录
def login():
    form = LoginForm()
    # 返回渲染模板
    return render_template("login.html", title="登录", form=form)

```
前往login页面进行改造

![mark](http://upload-images.jianshu.io/upload_images/1779926-d0cdf60e56eaa4a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>直接去掉图中的代码改为

![mark](http://upload-images.jianshu.io/upload_images/1779926-8637de4dc87d11ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时点击退出登录

```
builtins.KeyError
KeyError: 'A secret key is required to use CSRF.'
```

views中定义:

```
app.config["SECRET_KEY"] = "12345678"
```

然后可以正确执行。

### 定义注册表单并替换。

```
"""
注册表单:
1. 账号
2. 密码
3. 确认密码
4. 验证码
5. 注册按钮
"""


class RegisterForm(FlaskForm):
    account = StringField(
        validators=[],
        description=u"账号",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入账号"
        }
    )
    pwd = PasswordField(
        validators=[],
        description=u"密码",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入密码"
        }
    )
    re_pwd = PasswordField(
        validators=[],
        description=u"确认密码",
        render_kw={
            "class": "form-control",
            "placeholder": "请确认密码"
        }
    )
    captcha = StringField(
        validators=[],
        description=u"验证码",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入验证码"
        }
    )
    submit = SubmitField(
        u"注册",
        render_kw={
            "class": "btn btn-primary"
        }
    )
```

```
# register 用户注册
@app.route("/register/", methods=["GET", "POST"])  # 用户注册
def register():
    form = RegisterForm()
    return render_template("register.html", title ="注册", form=form)
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-0dfbf38a27151d97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 发布文章的表单

```
"""
发布文章表单:
1. 标题
2. 分类
3. 封面
4. 内容
5. 发布文章按钮
"""


class ArticleAddForm(FlaskForm):
    title = StringField(
        validators=[],
        description=u"标题",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入标题"
        }
    )
    # 强制类型为整型
    category = SelectField(
        validators=[],
        description=u"分类",
        choices=[(1, u"科技"), (2, u"搞笑"), (3, u"军事")],
        default=3,
        coerce=int,
        render_kw={
            "class": "form-control"
        }
    )
    logo = FileField(
        validators=[],
        description=u"封面",
        render_kw={
            "class": "form-control-file",
        }
    )
    content = TextAreaField(
        validators=[],
        description=u"内容",
        render_kw={
            "style": "height:300px;",
            "id": "content"
        }
    )
    submit = SubmitField(
        u"发布文章",
        render_kw={
            "class": "btn btn-primary"
        }
    )
```

view中实例化并传到html中去。


```
# art_add 发布文章
@app.route("/art/add/", methods=["GET", "POST"])  # 发布文章\
def art_add():
    form = ArticleAddForm()
    return render_template("art_add.html", title="发布文章", form=form)
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-85e573cc85f4fd9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 实现用户注册功能

1. 进行数据验证。

定义验证规则。

forms.py 中:

```
from wtforms.validators import DataRequired, EqualTo, ValidationError
```
>分别是字段必须存在，密码相等，自定义错误信息。

```
        validators=[
            DataRequired(u"账号不能为空")
        ],
```

>此处照猫画虎环节:自行为其他非空字段也填上验证和信息。

![mark](http://upload-images.jianshu.io/upload_images/1779926-38f9474a8c93b291.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
    re_pwd = PasswordField(
        validators=[
            DataRequired(u"确认密码不能为空"),
            EqualTo('pwd', message=u"两次输入密码不一致")
        ],
        )
```

去视图中进行调用

```
from forms import LoginForm, RegisterForm, ArticleAddForm
```

然后进行验证逻辑:

```
def register():
    form = RegisterForm()
    if form.validate_on_submit():
        data = form.data
```

在form标签中加入crsf token验证

![mark](http://upload-images.jianshu.io/upload_images/1779926-464fc37eec2bed45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-43126b53a4c49fc3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>设置提交方式为post，定义错误信息的显示。

![mark](http://upload-images.jianshu.io/upload_images/1779926-0e58a5764051d80e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

其他也同理设置。

保存数据；

```
def register():
    form = RegisterForm()
    if form.validate_on_submit():
        data = form.data
        # 保存数据
        user = User(
            account=data["account"],
            # 对于pwd进行加密
            pwd=generate_password_hash(data["pwd"]),
            add_time=datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
        )
        db.session.add(user)
        db.session.commit()
        # 定义一个会话的闪现
        flash("注册成功, 请登录", "ok")
        return redirect("/login/")
    return render_template("register.html", title="注册", form=form)
```

遇到的错误:

post正常，一直无法通过validate_on_submit()，却没有error

>忘记填写crsf_token 或crsf token填写错误

![mark](http://upload-images.jianshu.io/upload_images/1779926-176cdb70847e27d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

报错`keyerror: 'sqlalchemy_track_modifications'`

运气不错，看了几个什么回退版本之类。从17年就出现的问题不应该18年了还在的bug啊。
果然找到完美的解决方案。

>当我们的应用中有不止一个app时会出现这种错误。全局保留一个app，其他的impor
已有的app

view中import，而不是新实例化

```
from models import app
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-fb3bcc7aa64ec698.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将成功的喜悦flash到登录页面

确保注册的账号的唯一性。

```
from models import User

    # 自定义字段验证规则: validate 下划线 字段名
    def validate_account(self, field):
        account = field.data
        user = User.query.filter_by(account=account).count()
        if user > 0:
            raise ValidationError(u"账号已存在")
```

## 实现验证码功能

定义一个验证码处理的文件:

安装pillow库

```
# encoding: utf-8
import os
import uuid
__author__ = 'mtianyan'
__date__ = '2018/2/17 0017 02:10'
import random
from PIL import Image, ImageDraw, ImageFont, ImageFilter


class Captcha:
    """
    验证码功能类
    """
    # 随机一个字母或者数字

    def random_char(self):
        num = random.randint(1, 3)
        if num == 1:
            # 随机一个0-9之间数字: ascii码
            char = random.randint(48, 57)
        elif num == 2:
            # 随机一个a-z之间字母
            char = random.randint(97, 122)
        else:
            # 随机一个A-Z之间字母
            char = random.randint(65, 90)
        # chr将数字转换为对应ASCII码数字字母
        return chr(char)

    # 随机一个干扰字符
    def random_diss(self):
        arr = ["^", "_", "-", ".", "~"]
        return arr[random.randint(0, len(arr) - 1)]

    # 定义干扰字符颜色,干扰字符与字符颜色在不同区间
    def random_char_color(self):
        return (
            random.randint(
                65, 255), random.randint(
                65, 255), random.randint(
                65, 255))

    # 定义字符颜色, 三原色 RGB
    def random_diss_color(self):
        return (
            random.randint(
                32, 127), random.randint(
                32, 127), random.randint(
                32, 127))

    # 生成验证码:
    def create_captcha(self):
        width = 240  # 240px
        height = 60  # 60px

        # 创建一个图片
        image = Image.new("RGB", (width, height), (192, 192, 192))

        # 创建font对象，定义字体和大小
        font_name = random.randint(1, 3)
        font_file = os.path.join(
            os.path.dirname(__file__),
            "static/fonts") + "/%d.ttf" % font_name
        font = ImageFont.truetype(font_file, 40)

        # 创建draw画布使图片可编辑，填充像素点
        draw = ImageDraw.Draw(image)
        for x in range(0, width, 5):
            for y in range(0, height, 5):
                draw.point((x, y), fill=self.random_diss_color())

        # 填充干扰字符
        for v in range(0, width, 30):
            dis = self.random_diss()
            w = 5 + v
            # 距离图片上边距最多15个像素， 最低五个像素
            h = random.randint(5, 15)
            draw.text((w, h), dis, font=font, fill=self.random_diss_color())
        # 填充字符
        chars = ""
        for v in range(4):
            c = self.random_char()
            chars += str(c)
            # 距离图片上边距最多15个像素， 最低五个像素
            h = random.randint(5, 15)
            # 占图片宽度1/4， 10px间距, 顺序平移
            w = width / 4 * v + 10
            draw.text((w, h), c, font=font, fill=self.random_char_color())
        # 模糊效果:
        image.filter(ImageFilter.BLUR)
        image_name = "%s.jpg" % uuid.uuid4().hex
        save_dir = os.path.join(
            os.path.dirname(__file__),
            "static/captcha")
        if not os.path.exists(save_dir):
            os.makedirs(save_dir)
        image.save(save_dir + '/' + image_name, "jpeg")
        return dict(
            image_name=image_name,
            captcha=chars
        )
        image.show()


if __name__ == "__main__":
    c = Captcha()
    print(c.random_char())
    print(c.random_diss())
    print(c.random_char_color())
    print(c.random_diss_color())
    c.create_captcha()

```

验证码有了我们该如何调用呢

引入

```
from flask import session, Response
# 验证码
@app.route("/captcha/", methods=["GET"])
def captcha():
    from captcha import Captcha
    c = Captcha()
    info = c.create_captcha()
    image = os.path.join(os.path.dirname(__file__), "static/captcha") + "/" + info["image_name"]
    with open(image, 'rb') as f:
        image = f.read()
    return Response(image, mimetype="jpeg")
```

>一个用于会话一个用于响应。

flask自带code，与我们命名的code会冲突，还好我一直命名captcha

报错:

```
UnicodeDecodeError: 'gbk' codec can't decode byte 0xff in position 0: illegal multibyte sequence
```

>解决方案: 打开图片应该以二进制方式打开。

验证码实现显示:

![mark](http://upload-images.jianshu.io/upload_images/1779926-a4bb0f1a664ce6ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 点击图片实现验证码刷新

```
<img  title="点击切换" src="/captcha/" onclick="this.src='/captcha/?'+Math.random()" style="width: 180px; height: 50px;margin-top: 6px;">
```
1. 进行会话session中值的保存；

```
    session['captcha'] = info["captcha"]
    print(session['captcha'])
```

2. form中自定义验证码验证功能:

```
     # 自定义验证码规则: validate 下划线 字段名
    def validate_captcha(self, field):
        captcha = field.data
        if not form_session["captcha"]:
            raise ValidationError(u"非法操作")
        if form_session["captcha"].lower() != captcha.lower():
            raise ValidationError(u"验证码不正确")
```

## 实现用户登录功能

```
 form = LoginForm()
    if form.validate_on_submit():
        data = form.data
        session["account"] = data["account"]
        flash("登录成功", "ok")
        return redirect("/art/list/")
```

models中编写验证的方法。

```
    # 检查密码是否正确
    def check_pwd(self, pwd):
        return check_password_hash(self.pwd, pwd)
```

forms中重写

```
    def validate_pwd(self, field):
        pwd = field.data
        user = User.query.filter_by(name=self.account.data).first()
        if not user.check_pwd(pwd):
            raise ValidationError(u"密码不正确")
```

login页面中，添加method 等于post

submit之前添加csrf token

添加账号，密码的报错信息。

![mark](http://upload-images.jianshu.io/upload_images/1779926-5801da2617157cad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 实现用户退出功能

```
    # 调用session的pop功能将user变为None
    session.pop("user", None)
```

登录成功后会跳转列表页面。但是列表页面的用户并没有动态显示。

如果出现编码问题

```
import sys   #引用sys模块进来，并不是进行sys的第一次加载  
reload(sys)  #重新加载sys  
sys.setdefaultencoding('utf8')  ##调用setdefaultencoding函数
```

将登陆成功中的闪现传递到art list中

![mark](http://upload-images.jianshu.io/upload_images/1779926-fbb09b35f27fd8e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

刷新等操作闪现就会消失

![mark](http://upload-images.jianshu.io/upload_images/1779926-cbdf06c865414ea1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 用户登录权限控制(使用装饰器进行权限控制)

限制用户在未登录状态不能访问list等需要登录的页面。

```
# 登录装饰器
def user_login_req(f):
    @wraps(f)
    def login_req(*args,**kwargs):
        if "user" not in session:
            return redirect(url_for('login', next=request.url ))
        return f(*args,**kwargs)
    return login_req
```

>该装饰器传入一个函数，判断包装过后的函数对象。

为我们的退出，发布文章，编辑文章，删除文章，文章列表加上装饰器

```
@user_login_req
```

## 实现添加文章功能

views中添加文章。

```
def art_add():
    form = ArticleAddForm()
    if form.validate_on_submit():
        data = form.data
```

forms中进行字段的验证

```
        validators=[
            DataRequired(u"内容不能为空")
        ],
```

>照猫画虎，自行完成环节。

添加错误信息三部曲:

```
<form method="post" >
<form method="post" enctype="multipart/form-data">
```

支持文件上传功能。

![mark](http://upload-images.jianshu.io/upload_images/1779926-12acd261c34cf184.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>照猫画虎为各个字段添加错误信息提示。

第三步:加上csrf令牌

![mark](http://upload-images.jianshu.io/upload_images/1779926-13ce5f2a05126ec7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 定义数据的保存以及图片的上传

```
from werkzeug.utils import secure_filename

# 设置上传封面图路径
app.config["uploads"] = os.path.join(os.path.dirname(__file__), "static/uploads")

# 修改文件名称
def change_name(name):
    info = os.path.splitext(name)
    # 文件名: 时间格式字符串+唯一字符串+后缀名
    name = datetime.now().strftime("%Y%m%d%H%M%S")+str(uuid.uuid4().hex)+info[-1],
    return name
```

>握了根草，不小心多打了一个逗号。竟然不报错。而是报错我猜是它把info加上逗号当成了一个单元素tuple

```
builtins.TypeError
TypeError: must be str, not tuple
```

如果你跟着老师数据库都定义错了，这时候图片字段会保存的不是我们预期的值

![mark](http://upload-images.jianshu.io/upload_images/1779926-b4c664e4868b1755.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
alter table article modify logo varchar(100) not null;
```

将model中的同步改为

```
logo = db.Column(db.String(100), nullable=False)  # 封面
```

```
def art_add():
    form = ArticleAddForm()
    if form.validate_on_submit():
        data = form.data

        # 上传logo
        file = secure_filename(form.logo.data.filename)
        logo = change_name(file)
        if not os.path.exists(app.config["uploads"]):
            os.makedirs(app.config["uploads"])
        # 保存文件
        form.logo.data.save(app.config["uploads"] + "/" + logo)
        # 获取用户ID
        user = User.query.filter_by(account=session["user"]).first()
        user_id = user.id
        # 保存数据，Article
        article = Article(
            title=data["title"],
            category=data["category"],
            user_id=user_id,
            logo=logo,
            content=data["content"],
            add_time=datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
        )
        db.session.add(article)
        db.session.commit()
        flash(u"发布文章成功", "ok")
    return render_template("art_add.html", title="发布文章", form=form)
```

发布文章。多发布几篇测试数据



## 实现文章列表功能

```
# art_list 文章列表
@app.route("/art/list/<int:page>/", methods=["GET"])  # 文章列表
@user_login_req
def art_list(page):
    if page is None:
        page = 1
    # 只展示当前用户才能看到的内容
    user = User.query.filter_by(account=session["user"]).first()
    user_id = user.id
    page_data = Article.query.filter_by(
        user_id=user_id
    ).order_by(
        Article.add_time.desc()
    ).paginate(page=page, per_page=1)
    return render_template("art_list.html", title="文章列表", page_data=page_data)
```

>文章列表页取出当前用户的文章，以时间排序。将分页后的数据返回到前端html

### 前端html中展示数据

![mark](http://upload-images.jianshu.io/upload_images/1779926-eae83c790ffb55d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时访问art/list出错404.因为我们已经必须要接受参数了。、

所以在art_menu中设置默认第一页

![mark](http://upload-images.jianshu.io/upload_images/1779926-265db63025c13402.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

登录的时候，让它默认跳到第一页。

```
return redirect("/art/list/1/")
```

此时登录可以看到我们的列表页已经显示了出来。

![mark](http://upload-images.jianshu.io/upload_images/1779926-72103c6909a8e42b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 分类实现

```
 category = [(1, u"科技"), (2, u"搞笑"), (3, u"军事")]
    return render_template("art_list.html", title="文章列表", page_data=page_data, category=category)
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-dfee7547285e78e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>使用数据库中保存的category数字在categorylist中取到对应的索引位置的元组

再通过索引1取到汉字

图片上传的展示

![mark](http://upload-images.jianshu.io/upload_images/1779926-0ce9ff69b839760f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 分页功能

创建一个专门的分页的html。page.html

将原本list中用于分页的部分剪切到新的

![mark](http://upload-images.jianshu.io/upload_images/1779926-51dea8185a2a11ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-352bb52f5902f52c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如何调用这个分页page。

![mark](http://upload-images.jianshu.io/upload_images/1779926-a78c8bc7d4a49049.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-435715dbfc66bf38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>传入我们的数据和视图

## 实现编辑文章功能

文章列表中点击编辑文章，跳转到编辑文章

1. 设计文章编辑的表单

![mark](http://upload-images.jianshu.io/upload_images/1779926-5f2143f82c98a3ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

比文章添加只多了一个字段id

```
jinja2.exceptions.UndefinedError
jinja2.exceptions.UndefinedError: 'form' is undefined
```

因为我们只定义了form没有在edit视图中实例化，查询出数据

```
def art_edit(id):
    form = ArticleEditForm()
    article = Article.query.get_or_404(int(id))
    return render_template("art_edit.html", form=form, title="编辑文章", article=article)
```

此时进行html中填充值

![mark](http://upload-images.jianshu.io/upload_images/1779926-b2845dd3abde58db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-159340352a0a449b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于内容这里取值无效。所以我们在后端直接给form动态赋值。

```
    form.content.data = article.content
    form.category.data = article.category
    form.logo.data = article.logo
```

图片显示

![mark](http://upload-images.jianshu.io/upload_images/1779926-ecf113a8791d5d23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

完整代码；

```
def art_edit(id):
    form = ArticleEditForm()
    article = Article.query.get_or_404(int(id))
    if request.method == "GET":
        form.content.data = article.content
        form.category.data = article.category
    # 莫名其妙赋初值:不赋初值表单提交时会提示封面为空
    # 放在这里修复显示请选择封面的错误
    form.logo.data = article.logo
    if form.validate_on_submit():
        data = form.data
        # 上传logo
        file = secure_filename(form.logo.data.filename)
        logo = change_name(file)
        if not os.path.exists(app.config["uploads"]):
            os.makedirs(app.config["uploads"])
        # 保存文件
        form.logo.data.save(app.config["uploads"] + "/" + logo)
        article.logo = logo
        article.title = data['title']
        article.content = data['content']
        article.category = data['category']
        db.session.add(article)
        db.session.commit()
        flash(u"编辑文章成功", "ok")
    return render_template("art_edit.html", form=form, title="编辑文章", article=article)
```

其中注意的几个点。get请求时才赋初值，无论get post 都为logo赋初值。

## 实现删除文章功能

在art list中添加删除的链接。、

![mark](http://upload-images.jianshu.io/upload_images/1779926-840b845920a7a585.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

视图中查询到对应的文章并删除。

```python
# art_del 删除文章
@app.route("/art/del/<int:id>/", methods=["GET"])  # 删除文章
@user_login_req
def art_del(id):
    article = Article.query.get_or_404(int(id))
    db.session.delete(article)
    db.session.commit()
    flash("删除《%s》成功!" % article.title, "ok" )
    return redirect("/art/list/1")
```

## 本章总结

1. bootstrap语法
2. flask视图 路由 模板 静态文件创建
3. jinja2模板语法
4. 使用sqlachemy操作msyql
5. 使用wtforms定义表单
