## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

>本章内容: 会员模块实现
已上线演示地址: http://movie.mtianyan.cn
项目源码地址：https://github.com/mtianyan/movie_project

# 会员模块实现

1. 模型: User
2. 表单: RegisterForm
3. 请求方法: GET POST
4. 访问控制: 无

在home/forms.py中添加一个RegisterForm

```python
class RegistForm(FlaskForm):
    name = StringField(
        label="昵称",
        validators=[
            DataRequired("昵称不能为空！")
        ],
        description="昵称",
        render_kw={
            "class": "form-control input-lg",
            "placeholder": "请输入昵称！",
        }
    )
    email = StringField(
        label="邮箱",
        validators=[
            DataRequired("邮箱不能为空！"),
            Email("邮箱格式不正确！")
        ],
        description="邮箱",
        render_kw={
            "class": "form-control input-lg",
            "placeholder": "请输入邮箱！",
        }
    )
    phone = StringField(
        label="手机",
        validators=[
            DataRequired("手机号不能为空！"),
            Regexp("1[3458]\\d{9}", message="手机格式不正确！")
        ],
        description="手机",
        render_kw={
            "class": "form-control input-lg",
            "placeholder": "请输入手机！",
        }
    )
    pwd = PasswordField(
        label="密码",
        validators=[
            DataRequired("密码不能为空！")
        ],
        description="密码",
        render_kw={
            "class": "form-control input-lg",
            "placeholder": "请输入密码！",
        }
    )
    repwd = PasswordField(
        label="确认密码",
        validators=[
            DataRequired("请输入确认密码！"),
            EqualTo('pwd', message="两次密码不一致！")
        ],
        description="确认密码",
        render_kw={
            "class": "form-control input-lg",
            "placeholder": "请输入确认密码！",
        }
    )
    submit = SubmitField(
        '注册',
        render_kw={
            "class": "btn btn-lg btn-success btn-block",
        }
    )

     def validate_name(self, field):
        name = field.data
        user = User.query.filter_by(name=name).count()
        if user == 1:
            raise ValidationError("昵称已经存在！")

    def validate_email(self, field):
        email = field.data
        user = User.query.filter_by(email=email).count()
        if user == 1:
            raise ValidationError("邮箱已经存在！")

    def validate_phone(self, field):
        phone = field.data
        user = User.query.filter_by(phone=phone).count()
        if user == 1:
            raise ValidationError("手机号码已经存在！")
```

定义好form之后我们开始处理视图views中的逻辑处理以及将form传入模板填充

```python
@home.route("/register/", methods=["GET", "POST"])
def register():
    """
    会员注册
    """
    form = RegistForm()
    if form.validate_on_submit():
        data = form.data
        user = User(
            name=data["name"],
            email=data["email"],
            phone=data["phone"],
            pwd=generate_password_hash(data["pwd"]),
            uuid=uuid.uuid4().hex
        )
        db.session.add(user)
        db.session.commit()
        flash("注册成功！", "ok")
    return render_template("home/register.html", form=form)
```

前往register html 中进行form的填充

![mark](http://myphoto.mtianyan.cn/blog/180227/ADBKdBGj0C.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180227/Hl86l3bd74.png?imageslim)

- 添加错误提示

![mark](http://myphoto.mtianyan.cn/blog/180227/2G0DI2b97d.png?imageslim)

- 添加消息的闪现

![mark](http://myphoto.mtianyan.cn/blog/180227/I68BGJ3DlB.png?imageslim)

## 会员登录

1. 模型: User
2. 表单: LoginForm
3. 请求方法: GET POST
4. 访问控制: 无

定义loginform

```python
class LoginForm(FlaskForm):
    name = StringField(
        label="账号",
        validators=[
            DataRequired("账号不能为空！")
        ],
        description="账号",
        render_kw={
            "class": "form-control input-lg",
            "placeholder": "请输入账号！",
        }
    )
    pwd = PasswordField(
        label="密码",
        validators=[
            DataRequired("密码不能为空！")
        ],
        description="密码",
        render_kw={
            "class": "form-control input-lg",
            "placeholder": "请输入密码！",
        }
    )
    submit = SubmitField(
        '登录',
        render_kw={
            "class": "btn btn-lg btn-primary btn-block",
        }
    )
```

models user中添加登录设置校验密码函数

```python
 def check_pwd(self, pwd):
        from werkzeug.security import check_password_hash
        return check_password_hash(self.pwd, pwd)
```

定义登录的views

```python
@home.route("/login/", methods=["GET", "POST"])
def login():
    """
    登录
    """
    form = LoginForm()
    if form.validate_on_submit():
        data = form.data
        user = User.query.filter_by(name=data["name"]).first()
        if not user.check_pwd(data["pwd"]):
            flash("密码错误！", "err")
            return redirect(url_for("home.login"))
        session["user"] = user.name
        session["user_id"] = user.id
        userlog = Userlog(
            user_id=user.id,
            ip=request.remote_addr
        )
        db.session.add(userlog)
        db.session.commit()
        return redirect(url_for("home.user"))
    return render_template("home/login.html", form=form)
```

前往login.html中修改form填充字段

![mark](http://myphoto.mtianyan.cn/blog/180227/Cd6h4dg25E.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180227/j46F10ikj3.png?imageslim)

>别忘了csrf token值以及form 表单的method

- 添加错误信息

![mark](http://myphoto.mtianyan.cn/blog/180227/1G0dj6m0B8.png?imageslim)

- 自行添加消息闪现

### 退出功能实现

```python
@home.route("/logout/")
def logout():
    """
    退出登录
    """
    # 重定向到home模块下的登录。
    session.pop("user", None)
    session.pop("user_id", None)
    return redirect(url_for('home.login'))
```

### 已登录装饰器

```python
def user_login_req(f):
    """
    登录装饰器
    """
    @wraps(f)
    def decorated_function(*args, **kwargs):
        if "user" not in session:
            return redirect(url_for("home.login", next=request.url))
        return f(*args, **kwargs)
    return decorated_function
```

已登录就可以访问，否则重定向至登录。next页面为当前请求的url

为需要登录才可访问的页面添加登录装饰器。

![mark](http://myphoto.mtianyan.cn/blog/180227/FKH6Gf92j9.png?imageslim)

## 修改会员资料

1. 模型: User
2. 表单: UserdetailForm
3. 请求方法: GET POST
4. 访问控制: `@user_login_req`

首先定义表单

```python
class UserdetailForm(FlaskForm):
    name = StringField(
        label="账号",
        validators=[
            DataRequired("账号不能为空！")
        ],
        description="账号",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入账号！",
        }
    )
    email = StringField(
        label="邮箱",
        validators=[
            DataRequired("邮箱不能为空！"),
            Email("邮箱格式不正确！")
        ],
        description="邮箱",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入邮箱！",
        }
    )
    phone = StringField(
        label="手机",
        validators=[
            DataRequired("手机号不能为空！"),
            Regexp("1[3458]\\d{9}", message="手机格式不正确！")
        ],
        description="手机",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入手机！",
        }
    )
    face = FileField(
        label="头像",
        validators=[
            DataRequired("请上传头像！")
        ],
        description="头像",
    )
    info = TextAreaField(
        label="简介",
        validators=[
            DataRequired("简介不能为空！")
        ],
        description="简介",
        render_kw={
            "class": "form-control",
            "rows": 10
        }
    )
    submit = SubmitField(
        '保存修改',
        render_kw={
            "class": "btn btn-success",
        }
    )
```

在home/views中定义我们的修改个人信息的业务逻辑

```python
@home.route("/user/", methods=["GET", "POST"])
@user_login_req
def user():
    """
    用户中心个人资料
    """
    form = UserdetailForm()
    user = User.query.get(int(session["user_id"]))
    form.face.validators = []
    if request.method == "GET":
        form.name.data = user.name
        form.email.data = user.email
        form.phone.data = user.phone
        form.info.data = user.info
    if form.validate_on_submit():
        data = form.data
        file_face = secure_filename(form.face.data.filename)
        if not os.path.exists(app.config["FC_DIR"]):
            os.makedirs(app.config["FC_DIR"])
            os.chmod(app.config["FC_DIR"], "rw")
        user.face = change_filename(file_face)
        form.face.data.save(app.config["FC_DIR"] + user.face)

        name_count = User.query.filter_by(name=data["name"]).count()
        if data["name"] != user.name and name_count == 1:
            flash("昵称已经存在！", "err")
            return redirect(url_for("home.user"))

        email_count = User.query.filter_by(email=data["email"]).count()
        if data["email"] != user.email and email_count == 1:
            flash("邮箱已经存在！", "err")
            return redirect(url_for("home.user"))

        phone_count = User.query.filter_by(phone=data["phone"]).count()
        if data["phone"] != user.phone and phone_count == 1:
            flash("手机号码已经存在！", "err")
            return redirect(url_for("home.user"))

        user.name = data["name"]
        user.email = data["email"]
        user.phone = data["phone"]
        user.info = data["info"]
        db.session.add(user)
        db.session.commit()
        flash("修改成功！", "ok")
        return redirect(url_for("home.user"))
    return render_template("home/user.html", form=form, user=user)
```

然后前往模板中为user.html进行字段的替换填充(已有信息初始化赋值已在views的get方法中完成)，flash信息显示。报错提示。修改模板中form的method为post

![mark](http://myphoto.mtianyan.cn/blog/180227/7e9G7ifg19.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180227/jA0J90e4j2.png?imageslim)


啊啊啊啊啊。忘记图片上传的form要添加multipart formdata了

```
builtins.AttributeError
AttributeError: 'str' object has no attribute 'filename'
```

![mark](http://myphoto.mtianyan.cn/blog/180228/IHL2Ck37CG.png?imageslim)

敲重点，一定要加这个。

然后就是在什么都没修改点击报错。

添加一句

```
        if form.face.data != "":
```

## 修改密码

1. 模型: User
2. 表单: PwdForm
3. 请求方法: GET POST
4. 访问控制: `@user_login_req`

forms中定义

```
class PwdForm(FlaskForm):
    old_pwd = PasswordField(
        label="旧密码",
        validators=[
            DataRequired("旧密码不能为空！")
        ],
        description="旧密码",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入旧密码！",
        }
    )
    new_pwd = PasswordField(
        label="新密码",
        validators=[
            DataRequired("新密码不能为空！"),
        ],
        description="新密码",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入新密码！",
        }
    )
    submit = SubmitField(
        '修改密码',
        render_kw={
            "class": "btn btn-success",
        }
    )
```

然后前往views中编写修改密码的业务逻辑

```python
@home.route("/pwd/", methods=["GET", "POST"])
@user_login_req
def pwd():
    """
    修改密码
    """
    form = PwdForm()
    if form.validate_on_submit():
        data = form.data
        user = User.query.filter_by(name=session["user"]).first()
        if not user.check_pwd(data["old_pwd"]):
            flash("旧密码错误！", "err")
            return redirect(url_for('home.pwd'))
        user.pwd = generate_password_hash(data["new_pwd"])
        db.session.add(user)
        db.session.commit()
        flash("修改密码成功，请重新登录！", "ok")
        return redirect(url_for('home.logout'))
    return render_template("home/pwd.html", form=form)
```

前往pwd.html中修改form的字段填充，添加报错信息，添加flash信息。

![mark](http://myphoto.mtianyan.cn/blog/180228/hjemeibd2I.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180228/8I00GIhDeC.png?imageslim)

注意views中提前return掉。

## 会员登录日志

1. 模型: User
2. 表单: 无
3. 请求方法: GET
4. 访问控制: `@user_login_req`

编写views中会员登录的逻辑处理

```python
@home.route("/loginlog/<int:page>/", methods=["GET"])
@user_login_req
def loginlog(page=None):
    """
    会员登录日志
    """
    if page is None:
        page = 1
    page_data = Userlog.query.filter_by(
        user_id=int(session["user_id"])
    ).order_by(
        Userlog.addtime.desc()
    ).paginate(page=page, per_page=2)
    return render_template("home/loginlog.html", page_data=page_data)
```

然后前往loginlog中遍历pagedata。以及修改前台的menu页面中的url跳转

![mark](http://myphoto.mtianyan.cn/blog/180228/e0Li903mIg.png?imageslim)![mark](http://myphoto.mtianyan.cn/blog/180228/e0Li903mIg.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180228/6DFglLmBGc.png?imageslim)

在ui文件夹中创建homepage.html

将adminpage拷贝过来。

添加最外层的nav标签

![mark](http://myphoto.mtianyan.cn/blog/180228/HGL0dkhGBm.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180228/d86Ima381I.png?imageslim)

样式调整为前台的

![mark](http://myphoto.mtianyan.cn/blog/180228/7FhDlf0E59.png?imageslim)
