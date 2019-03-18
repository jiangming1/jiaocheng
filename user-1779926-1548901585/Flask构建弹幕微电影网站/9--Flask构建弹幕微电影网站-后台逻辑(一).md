## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

已上线演示地址: http://movie.mtianyan.cn
项目源码地址：https://github.com/mtianyan/movie_project

后台业务逻辑

## 管理员登录

1. `app/__init__.py` 中创建db对象
2. `app/models.py` 中导入db对象
3. `app/admin/forms.py` 定义表单验证
4. `app/templates/admin/login.html` 中使用表单字段，信息验证，消息闪现
5. `app/admin/views.py` 中处理登录请求，保存会话
6. `app/admin/views.py` 定义登录装饰器，访问控制

- 模型: Admin
- 表单: LoginForm
- 请求方法: GET POST
- 访问控制: 无

对于代码进行调整，将原本models中涉及app的代码，全部转移到init.py中
models中只保留datetime的导入，init中重复的包的引入删除掉。
将models中的Create_all先注释掉。

开始定义表单

- `pip install flask-wtf`

```python
from flask_wtf import FlaskForm
from wtforms import StringField,PasswordField,SubmitField
from wtforms.validators import DataRequired
```

>点进源码可以查看有哪些字段可以供我们使用

管理员登录的表单

```
class LoginForm(FlaskForm):
    """
    管理员登录表单
    """
    account = StringField(
        label="账号",
        validators=[
            DataRequired("账号不能为空")
        ],
        description="账号",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入账号！",
            "required": "required"
        }
    )
    pwd = PasswordField(
        label="密码",
        validators=[
            DataRequired("密码不能为空")
        ],
        description="密码",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入密码！",
            "required": "required"
        }
    )
    submit = SubmitField(
        '登录',
        render_kw={
            "class": "btn btn-primary btn-block btn-flat",
        }
    )
```

>validators 数据校验，render_kw 因为forms会为我们直接生成html代码，
通过该参数加上各种class等。

如何将表单传递到我们的前端html中呢，这里需要用到views，在return的时候附加参数。

views.py中的login方法。

```python
from flask import render_template, redirect, url_for
from app.admin.forms import LoginForm

@admin.route("/login/")
def login():
    """
    后台登录
    """
    form = LoginForm()
    return render_template("admin/login.html", form=form)
```

去模板中进行展示: 前往login.html修改

![mark](http://myphoto.mtianyan.cn/blog/180221/Fg4eBh8jl7.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180221/i7clc247EC.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180221/g7KjJBhdbA.png?imageslim)

```
builtins.KeyError
KeyError: 'A secret key is required to use CSRF.'
```

所有的form表单的提交submit之前都要添加csrf

只添加这个还不行，还需要添加一个secret key。

```
app.config['SECRET_KEY'] = 'mtianyan_movie'
```

![mark](http://myphoto.mtianyan.cn/blog/180222/k3ak8CFlBl.png?imageslim)

**进行视图的处理**



```python
    if form.validate_on_submit():
            data = form.data
```

将错误信息显示到模板中。

![mark](http://myphoto.mtianyan.cn/blog/180222/k0K3l6kBGG.png?imageslim)

将提交时的action去掉，只需要通过method=post进行提交。

```
@admin.route("/login/", methods=["GET", "POST"])

```
为login的路由添加get post方法

此时前往/admin/login/访问

![mark](http://myphoto.mtianyan.cn/blog/180222/HA6g9k29dd.png?imageslim)

点击登录，此处效果为forms.py中"required": "required" 实现

我们将其注释掉可以显示出来我们自己的forms的报错信息

![mark](http://myphoto.mtianyan.cn/blog/180222/H3J8hLiD4h.png?imageslim)

### 验证账号和密码

在forms中自定义一个对于账号的验证

```python
    def validate_account(self, field):
        account = field.data
        admin = Admin.query.filter_by(name=account).count()
        if admin == 0:
            raise ValidationError("账号不存在! ")

```

>注意此处自定义验证方法的命名规范validate_+字段名

可能的报错:

```
ImportError: cannot import name db
```

![mark](http://myphoto.mtianyan.cn/blog/180222/bC1gJkEf1e.png?imageslim)


>注意导入的顺序问题。
>原因有点复杂，有人可以解释清楚还请在评论中跟我也说说


进行密码的验证:

前往模型的admin类中定义方法；

```python
    def check_pwd(self, pwd):
        from werkzeug.security import check_password_hash
        return check_password_hash(self.pwd, pwd)
```

views中账号密码的处理。

```python
def login():
    """
    后台登录
    """
    form = LoginForm()
    if form.validate_on_submit():
        data = form.data
        admin = Admin.query.filter_by(name=data["account"]).first()
        # 密码错误时，check_pwd返回false,则此时not check_pwd(data["pwd"])为真。
        if not admin.check_pwd(data["pwd"]):
            flash("密码错误!")
            return redirect(url_for("admin.login"))
        # 如果是正确的，就要定义session的会话进行保存。
        session["admin"] = data["account"]
        return redirect(request.args.get("next") or url_for("admin.index"))
    return render_template("admin/login.html", form=form)
```

在前端网页中显示错误信息的flash闪现。

![mark](http://myphoto.mtianyan.cn/blog/180223/K8LE1fG4fD.png?imageslim)

报错:

```
sqlalchemy.exc.InvalidRequestError: Table 'comment' is already defined for this MetaData instance.  Specify 'extend_existing=True' to redefine options and columns on an existing Table object.
```

>解决方案: 为所有的models添加`__table_args__ = {"useexisting": True}`

>sqlalchemy.exc.InvalidRequestError
sqlalchemy.exc.InvalidRequestError: Multiple classes found for path "Userlog" in the registry of this declarative base. Please use a fully module-qualified path.

这是因为我们在引入models中的模型时没有采用全量路径，而是采用了很短的路径

解决方案1：

```
from models import Admin

# 改为

from app.models import Admin
```

方法2在django中遇到这种问题，一般可以采用将app路径加入根目录当中，但是在flask中尝试失败:

```python
import sys
import os
# BASE_DIR = os.path.dirname(os.path.abspath(__file__))
# sys.path.insert(0, os.path.join(BASE_DIR, 'movie_project\\app'))
# sys.path.insert(0, os.path.join(BASE_DIR, 'movie_project\\app\\admin'))
# sys.path.insert(0, os.path.join(BASE_DIR, 'movie_project\\app\\home'))
```

在没有登录的状态下也能点击登录后才可以点击的操作，因为我们没有进行一个访问的控制。

- 注销时的处理

```python
@admin.route("/logout/")
def logout():
    """
    后台注销登录
    """
    session.pop("admin", None)
    return redirect(url_for("admin.login"))
```

- 装饰器的访问控制

```python
from functools import wraps

def admin_login_req(f):
    """
    登录装饰器
    """
    @wraps(f)
    def decorated_function(*args, **kwargs):
        if "admin" not in session:
            return redirect(url_for("admin.login", next=request.url))
        return f(*args, **kwargs)

    return decorated_function
```

在我们的所有需要登录状态才能访问的后台功能上添加装饰器
然后进行测试。

可能报错:

装饰器的访问限制不起作用:

```
@admin.route("/pwd/")
@admin_login_req
```

>访问控制装饰器一定要写在路由装饰器之后，否则会导致没有效果。
