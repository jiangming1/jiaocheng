## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

>本章内容: 基于角色的访问控制
已上线演示地址: http://movie.mtianyan.cn
项目源码地址：https://github.com/mtianyan/movie_project

# 基于角色的访问控制

角色的访问控制:

将职责和功能划分一个角色，比如电影管理员，预告管理员。

1. 模型: Auth
2. 表单: AuthForm
3. 请求方法: GET POST
4. 访问控制: `@admin_login_req`


首先定义authform

```python
class AuthForm(FlaskForm):
    name = StringField(
        label="权限名称",
        validators=[
            DataRequired("权限名称不能为空！")
        ],
        description="权限名称",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入权限名称！"
        }
    )
    url = StringField(
        label="权限地址",
        validators=[
            DataRequired("权限地址不能为空！")
        ],
        description="权限地址",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入权限地址！"
        }
    )
    submit = SubmitField(
        '编辑',
        render_kw={
            "class": "btn btn-primary",
        }
    )
```


views中定义auth_add

权限添加

```python
@admin.route("/auth/add/", methods=["GET", "POST"])
@admin_login_req
def auth_add():
    """
    添加权限
    """
    form = AuthForm()
    if form.validate_on_submit():
        data = form.data
        auth = Auth(
            name=data["name"],
            url=data["url"]
        )
        db.session.add(auth)
        db.session.commit()
        flash("添加权限成功！", "ok")
    return render_template("admin/auth_add.html",form=form)
```

将form传递到表单中，然后开始在模板中替换字段

![mark](http://myphoto.mtianyan.cn/blog/180227/2I67l868jF.png?imageslim)

>记得自行加上表单令牌

为字段添加错误信息:

![mark](http://myphoto.mtianyan.cn/blog/180227/DiCmDLBlC6.png?imageslim)

添加form表单的methods

![mark](http://myphoto.mtianyan.cn/blog/180227/kjc5ck111D.png?imageslim)

添加flash消息闪现的显示

![mark](http://myphoto.mtianyan.cn/blog/180227/e05JCfaLG6.png?imageslim)

添加添加标签的权限

![mark](http://myphoto.mtianyan.cn/blog/180227/9f0A4KH98L.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180227/h2ecglbB15.png?imageslim)

#### 权限列表

views中

```python
@admin.route("/auth/list/<int:page>/", methods=["GET"])
@admin_login_req
def auth_list(page=None):
    """
    权限列表
    """
    if page is None:
        page = 1
    page_data = Auth.query.order_by(
        Auth.addtime.desc()
    ).paginate(page=page, per_page=2)
    return render_template("admin/auth_list.html", page_data=page_data)
```

>取出所有的数据然后进行时间排序。

pagedata传递到了模板，进行字段填充，import pg 然后调用page方法。

![mark](http://myphoto.mtianyan.cn/blog/180227/CEDCDBgdLa.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180227/JFiLm522D1.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180227/L7jGGhlhEj.png?imageslim)

grid中为跳转list的url添加page参数。

![mark](http://myphoto.mtianyan.cn/blog/180227/k5lI6Ie3El.png?imageslim)

### 编辑与删除功能实现

```python
@admin.route("/auth/del/<int:id>/", methods=["GET"])
@admin_login_req
def auth_del(id=None):
    """
    权限删除
    """
    auth = Auth.query.filter_by(id=id).first_or_404()
    db.session.delete(auth)
    db.session.commit()
    flash("删除权限成功！", "ok")
    return redirect(url_for('admin.auth_list', page=1))


@admin.route("/auth/edit/<int:id>/", methods=["GET", "POST"])
@admin_login_req
def auth_edit(id=None):
    """
    编辑权限
    """
    form = AuthForm()
    auth = Auth.query.get_or_404(id)
    if form.validate_on_submit():
        data = form.data
        auth.url = data["url"]
        auth.name = data["name"]
        db.session.add(auth)
        db.session.commit()
        flash("修改权限成功！", "ok")
        redirect(url_for('admin.auth_edit', id=id))
    return render_template("admin/auth_edit.html", form=form, auth=auth)
```

为编辑和删除按钮添加url

![mark](http://myphoto.mtianyan.cn/blog/180227/ekHiiIa05I.png?imageslim)

新建auth_edit.html复制auth_add的代码修改其中汉字部分以及填充值。

![mark](http://myphoto.mtianyan.cn/blog/180227/b3EFJiDIfd.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180227/K2I56E0Hd7.png?imageslim)

权限列表中添加展示消息闪现的部分。

![mark](http://myphoto.mtianyan.cn/blog/180227/Fa9JbfdAGB.png?imageslim)

## 角色管理

1. 模型: Role
2. 表单: RoleForm
3. 请求方法: GET POST
4. 访问控制: `@admin_login_req`

角色权限列表中存入id的字符串。

首先创建一个form Roleform

```python
class RoleForm(FlaskForm):
    name = StringField(
        label="角色名称",
        validators=[
            DataRequired("角色名称不能为空！")
        ],
        description="角色名称",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入角色名称！"
        }
    )
    # 多选框
    auths = SelectMultipleField(
        label="权限列表",
        validators=[
            DataRequired("权限列表不能为空！")
        ],
        # 动态数据填充选择栏：列表生成器
        coerce=int,
        choices=[(v.id, v.name) for v in Auth.query.all()],
        description="权限列表",
        render_kw={
            "class": "form-control",
        }
    )
    submit = SubmitField(
        '编辑',
        render_kw={
            "class": "btn btn-primary",
        }
    )
```

在views中进行对于表单的校验以及传递到模板中。

```python
@admin.route("/role/add/", methods=["GET", "POST"])
@admin_login_req
def role_add():
    """
    角色添加
    """
    form = RoleForm()
    if form.validate_on_submit():
        data = form.data
        role = Role(
            name=data["name"],
            auths=",".join(map(lambda v: str(v), data["auths"]))
        )
        db.session.add(role)
        db.session.commit()
        flash("添加角色成功！", "ok")
    return render_template("admin/role_add.html", form=form)
```

此时前往form中进行字段的填充。为模板中form添加post方法

![mark](http://myphoto.mtianyan.cn/blog/180227/LKedghKF6I.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180227/IfkBKhAjjc.png?imageslim)

添加提示的flash信息块。

![mark](http://myphoto.mtianyan.cn/blog/180227/0K3AkBIGB8.png?imageslim)

添加字段校验:

![mark](http://myphoto.mtianyan.cn/blog/180227/G7iC9HKGd9.png?imageslim)

>备用: 制作多选框checkbox 用widgets.ListWidget 包裹 widgets.CheckboxInput()自己做一个多选.然后choices=[(v.id, v.name) for v in Auth.query.all(). 这样传回来的也是[1,2,3]

### 角色列表的展示

```python
@admin.route("/role/list/<int:page>/", methods=["GET"])
@admin_login_req
def role_list(page=None):
    """
    角色列表
    """
    if page is None:
        page = 1
    page_data = Role.query.order_by(
        Role.addtime.desc()
    ).paginate(page=page, per_page=2)
    return render_template("admin/role_list.html", page_data=page_data)
```

views中定义角色列表，然后将pagedata进行填充，以及import pg与调用page方法进行分页。

![mark](http://myphoto.mtianyan.cn/blog/180227/kLHeJ3BiG8.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180227/EbgKeI9E7b.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180227/D182A57546.png?imageslim)

grid中的url添加page参数

![mark](http://myphoto.mtianyan.cn/blog/180227/GhCfJkIB14.png?imageslim)

因为会有删除功能添加flash信息显示

![mark](http://myphoto.mtianyan.cn/blog/180227/H86kjLJidh.png?imageslim)

### 删除角色功能

```python
@admin.route("/role/del/<int:id>/", methods=["GET"])
@admin_login_req
def role_del(id=None):
    """
    删除角色
    """
    role = Role.query.filter_by(id=id).first_or_404()
    db.session.delete(role)
    db.session.commit()
    flash("删除角色成功！", "ok")
    return redirect(url_for('admin.role_list', page=1))
```

为list中的删除按钮添加对应的url

![mark](http://myphoto.mtianyan.cn/blog/180227/kKj65j3j4c.png?imageslim)

### 编辑角色功能实现

views中对于edit的处理

```python
@admin.route("/role/edit/<int:id>/", methods=["GET", "POST"])
@admin_login_req
def role_edit(id=None):
    """
     编辑角色
    """
    form = RoleForm()
    role = Role.query.get_or_404(id)
    # get时进行赋值。应对无法模板中赋初值
    if request.method == "GET":
        auths = role.auths
        form.auths.data = list(map(lambda v: int(v), auths.split(",")))
    if form.validate_on_submit():
        data = form.data
        role.name = data["name"]
        role.auths = ",".join(map(lambda v: str(v), data["auths"]))
        db.session.add(role)
        db.session.commit()
        flash("修改角色成功！", "ok")
    return render_template("admin/role_edit.html", form=form, role=role)
```

新建一个role_edit.html 把add中的内容粘贴过来，修改汉字。以及填充值

![mark](http://myphoto.mtianyan.cn/blog/180227/bHdFK5I245.png?imageslim)

在list中修改指向编辑的链接。

## 管理员管理

1. 模型: Admin
2. 表单: AdminForm
3. 请求方法: GET POST
4. 访问控制: `@admin_login_req`

定义管理员的表单模型

```python
class AdminForm(FlaskForm):
    name = StringField(
        label="管理员名称",
        validators=[
            DataRequired("管理员名称不能为空！")
        ],
        description="管理员名称",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入管理员名称！",
        }
    )
    pwd = PasswordField(
        label="管理员密码",
        validators=[
            DataRequired("管理员密码不能为空！")
        ],
        description="管理员密码",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入管理员密码！",
        }
    )
    repwd = PasswordField(
        label="管理员重复密码",
        validators=[
            DataRequired("管理员重复密码不能为空！"),
            EqualTo('pwd', message="两次密码不一致！")
        ],
        description="管理员重复密码",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入管理员重复密码！",
        }
    )
    role_id = SelectField(
        label="所属角色",
        coerce=int,
        choices=[(v.id, v.name) for v in Role.query.all()],
        render_kw={
            "class": "form-control",
        }
    )
    submit = SubmitField(
        '编辑',
        render_kw={
            "class": "btn btn-primary",
        }
    )
```

views中实例化form 并将form 传递到前台的模板中
将数据存入数据库

```python
@admin.route("/admin/add/", methods=["GET", "POST"])
@admin_login_req
def admin_add():
    """
    添加管理员
    """
    form = AdminForm()
    from werkzeug.security import generate_password_hash
    if form.validate_on_submit():
        data = form.data
        admin = Admin(
            name=data["name"],
            pwd=generate_password_hash(data["pwd"]),
            role_id=data["role_id"],
            is_super=1
        )
        db.session.add(admin)
        db.session.commit()
        flash("添加管理员成功！", "ok")
    return render_template("admin/admin_add.html", form=form)
```

![mark](http://myphoto.mtianyan.cn/blog/180227/ehAmFiFGG9.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180227/hHA1klE0B0.png?imageslim)

添加flash消息显示和错误提示。(自行)注意为模板中form添加post方法。

### 管理员列表显示

views中添加管理员列表显示

```python
@admin.route("/admin/list/<int:page>/", methods=["GET"])
@admin_login_req
def admin_list(page=None):
    """
    管理员列表
    """
    if page is None:
        page = 1
    page_data = Admin.query.join(
        Role
    ).filter(
        Role.id == Admin.role_id
    ).order_by(
        Admin.addtime.desc()
    ).paginate(page=page, per_page=1)
    return render_template("admin/admin_list.html", page_data=page_data)
```


前往admin_list中填充pagedata 以及pg的import以及调用page方法进行分页。

![mark](http://myphoto.mtianyan.cn/blog/180227/I5cHBcmELm.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180227/0j9Bhk06kJ.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180227/4kEb9Fgdl7.png?imageslim)

grid中修改调转到list的url参数，添加上page=1

![mark](http://myphoto.mtianyan.cn/blog/180227/dGK67b8d1c.png?imageslim)

## 访问权限控制

```
def admin_auth(f):
	@wraps(f)
	def decorate_function(*args, **kwargs):
		# 权限查询
		abort(404)
		return f(*args, **kwargs)

	return decorate_function
```

```
# 调用权限装饰器
@admin_auth
```

>通过一个权限装饰器进行访问的控制，在装饰器里面进行一个查询，拿出session中保存的管理员的id，查询出对应的角色。同过角色查询出角色所对应的列表。列表是一个id的列表，id列表中去权限表中查询出我们能够访问的路由规则。根据路由规则进行匹配，得出我们能不能访问该页面。

```
print(request.script_root);print(request.url_rule)
from flask import abort
```

```python
def admin_auth(f):
    """
    权限控制装饰器
    """
    @wraps(f)
    def decorated_function(*args, **kwargs):
        admin = Admin.query.join(
            Role
        ).filter(
            Role.id == Admin.role_id,
            Admin.id == session["admin_id"]
        ).first()
        auths = admin.role.auths
        # 将原本存储的权限字符串转换为列表
        auths = list(map(lambda v: int(v), auths.split(",")))
        auth_list = Auth.query.all()
        urls = [v.url for v in auth_list for val in auths if val == v.id]
        rule = request.url_rule
        if str(rule) not in urls:
            abort(404)
        return f(*args, **kwargs)

    return decorated_function
```

接下来为除过登录和后台首页都加上。然后测试是否可用。

登录我们的标签管理员。

```
进阶: 后台菜单动态显示，这个可以设计一个菜单数据表，分配可访问的菜单到用户权限中即可。
```
