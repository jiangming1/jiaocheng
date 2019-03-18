## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

已上线演示地址: http://movie.mtianyan.cn
项目源码地址：https://github.com/mtianyan/movie_project

## 收藏管理

1. 模型: Moviecol
2. 表单: 无
3. 请求方法: GET
4. 访问控制: `@admin_login_req`

插入我们的模拟数据

```
/*收藏*/
insert into moviecol(movie_id,user_id,addtime) values(7,1,now());
insert into moviecol(movie_id,user_id,addtime) values(7,2,now());
insert into moviecol(movie_id,user_id,addtime) values(7,3,now());
insert into moviecol(movie_id,user_id,addtime) values(7,4,now());
insert into moviecol(movie_id,user_id,addtime) values(8,5,now());
insert into moviecol(movie_id,user_id,addtime) values(8,6,now());
insert into moviecol(movie_id,user_id,addtime) values(8,7,now());
insert into moviecol(movie_id,user_id,addtime) values(8,8,now());

ALTER TABLE moviecol auto_increment=1;
```

利用关联查询完成电影收藏功能

views中编写收藏的功能实现

```python
@admin_login_req
@admin.route("/moviecol/list/<int:page>/", methods=["GET"])
def moviecol_list(page=None):
    """
    电影收藏
    """
    if page is None:
        page = 1
    page_data = Moviecol.query.join(
        Movie
    ).join(
        User
    ).filter(
        Movie.id == Moviecol.movie_id,
        User.id == Moviecol.user_id
    ).order_by(
        Moviecol.addtime.desc()
    ).paginate(page=page, per_page=1)
    return render_template("admin/moviecol_list.html", page_data=page_data)
```

为grid中跳转到movielist的url添加page参数

![mark](http://myphoto.mtianyan.cn/blog/180225/3ihdC602Jd.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180225/CJCBjkHbI7.png?imageslim)

通过pagedata进行数据的填充，以及pg的引入和调用page方法实现分页

![mark](http://myphoto.mtianyan.cn/blog/180225/80H45mI7dc.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180225/8g849H0Ehc.png?imageslim)

### 删除收藏

```python
@admin.route("/moviecol/del/<int:id>/", methods=["GET"])
@admin_login_req
def moviecol_del(id=None):
    """
    收藏删除
    """
    # 因为删除当前页。假如是最后一页，这一页已经不见了。回不到。
    from_page = int(request.args.get('fp')) - 1
    # 此处考虑全删完了，没法前挪的情况，0被视为false
    if not from_page:
        from_page = 1
    moviecol = Moviecol.query.get_or_404(int(id))
    db.session.delete(moviecol)
    db.session.commit()
    flash("删除收藏成功！", "ok")
    return redirect(url_for('admin.moviecol_list',page=from_page))
```

前往list中添加指向删除的url

![mark](http://myphoto.mtianyan.cn/blog/180226/lL36kLIbdI.png?imageslim)

添加删除的flash提示。

![mark](http://myphoto.mtianyan.cn/blog/180226/dg5cG94926.png?imageslim)

## 修改密码功能

1. 模型:Admin
2. 表单: PWdForm
3. 请求方法: GET POST
4. 访问控制: `@admin_login_req`

首先定义一个表单。

admin/forms.py:

```python
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
            DataRequired("新密码不能为空！")
        ],
        description="新密码",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入新密码！",
        }
    )
    submit = SubmitField(
        '编辑',
        render_kw={
            "class": "btn btn-primary",
        }
    )
```

在views中实例化该form并传递到模板中进行填充

```python
@admin.route("/pwd/", methods=["GET", "POST"])
@admin_login_req
def pwd():
    """
    后台密码修改
    """
    form = PwdForm()
    # 有下面这两句才有错误信息提示
    if form.validate_on_submit():
    data = form.data
    return render_template("admin/pwd.html",form=form)
```

![mark](http://myphoto.mtianyan.cn/blog/180226/ekAk5C2e1I.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180226/EjiC746aBd.png?imageslim)

### 进行表单的验证

添加错误信息的展示。

![mark](http://myphoto.mtianyan.cn/blog/180226/jgddEL2i4m.png?imageslim)

此时错误信息不展示是因为我们的form没有设置post方式提交。图中new字段自己改为new字段的错误提示

![mark](http://myphoto.mtianyan.cn/blog/180226/Fcde28dg90.png?imageslim)

### 实现验证旧密码

在forms中添加自定义的验证旧密码的验证方法

```python
    def validate_old_pwd(self, field):
        from flask import session
        pwd = field.data
        name = session["admin"]
        admin = Admin.query.filter_by(
            name=name
        ).first()
        if not admin.check_pwd(pwd):
            raise ValidationError("旧密码错误！")
```

>查询出与当前session中管理员账号相同的admin对象。调用该对象的check_pwd方法进行密码校验。

进行数据库中密码修改操作

```python
    if form.validate_on_submit():
        data = form.data
        admin = Admin.query.filter_by(name=session["admin"]).first()
        from werkzeug.security import generate_password_hash
        admin.pwd = generate_password_hash(data["new_pwd"])
        db.session.add(admin)
        db.session.commit()
        flash("修改密码成功，请重新登录！", "ok")
        return redirect(url_for('admin.logout'))
```

为login中的密码错误添加err分类

![mark](http://myphoto.mtianyan.cn/blog/180226/md1fB4EH9g.png?imageslim)

login.html进行修改:

![mark](http://myphoto.mtianyan.cn/blog/180226/FH4f1bK2Jc.png?imageslim)

ok的消息颜色设为绿色

注意事项:

>上面代码中的return是提前终止函数继续向下运行的语句。否则会造成跳不到logout的bug


### 修改右上个人中心信息

admin.html中修改所有的用户名地方为。

![mark](http://myphoto.mtianyan.cn/blog/180226/g07kL3a90C.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180226/hi8c05lG7d.png?imageslim)

博主个人: 用户头像这里可以牵扯出一个知识点: g

登录以及首页视图添加全局变量对象g的属性logo

```
# admin = Admin.query.filter_by(name=session["admin"]).first()
g.logo = "mtianyan.jpg"
```

注销时置空

```
g.logo = ""
```

因为目前我们的admin没有设置头像字段。所以如果以后添加可以使用上面方法。

- 插播: 模板中可以通过set 方法设置一个值等于一个函数的返回值等

因为flask不能在花括号里面再进行花括号取值。所以放在session中的情况因为这个原因取不出来。所以如下图语句无法实现。

![mark](http://myphoto.mtianyan.cn/blog/180226/cIla00k3hh.png?imageslim)

因此采用g不需要花括号取值，省一层括号。

![mark](http://myphoto.mtianyan.cn/blog/180226/29A1gbm2gH.png?imageslim)
