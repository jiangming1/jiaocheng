## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

已上线演示地址: http://movie.mtianyan.cn
项目源码地址：https://github.com/mtianyan/movie_project

## 电影管理列表

1. 找到grid，为电影列表，添加url。

![mark](http://myphoto.mtianyan.cn/blog/180224/D7ah8iD7D2.png?imageslim)

2. 参考标签列表

```python
@admin.route("/movie/list/<int:page>/", methods=["GET"])
@admin_login_req
def movie_list(page=None):
    """
    电影列表页面
    """
    if page is None:
        page = 1
    # 进行关联Tag的查询,单表查询使用filter_by 多表查询使用filter进行关联字段的声明
    page_data = Movie.query.join(Tag).filter(
        Tag.id == Movie.tag_id
    ).order_by(
        Movie.addtime.desc()
    ).paginate(page=page, per_page=1)
    return render_template("admin/movie_list.html", page_data=page_data)
```

![mark](http://myphoto.mtianyan.cn/blog/180224/cag0652I9l.png?imageslim)

### 分页实现

![mark](http://myphoto.mtianyan.cn/blog/180224/fA7Hifjf8g.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180224/9Jd7a9ckF4.png?imageslim)

导入之后调用page方法()传入page_data

>片名没有显示，是因为我们的片名在model中定义的不是name 而是title

这教育我们要对照着models中取值。

### 删除实现

views中

```python
@admin.route("/movie/del/<int:id>/", methods=["GET"])
@admin_login_req
def movie_del(id=None):
    """
    电影删除
    """
    movie = Movie.query.get_or_404(id)
    db.session.delete(movie)
    db.session.commit()
    flash("电影删除成功", "ok")
    return  redirect(url_for('admin.movie_list', page=1))
```

为movielist添加删除的提示。

![mark](http://myphoto.mtianyan.cn/blog/180224/3ChgIhBcG9.png?imageslim)

在movielist中为删除按钮添加url链接

![mark](http://myphoto.mtianyan.cn/blog/180224/LBc8AhDGh4.png?imageslim)


### 编辑功能实现

views中添加movie_edit的路由

```python
@admin.route("/movie/edit/<int:id>/", methods=["GET", "POST"])
@admin_login_req
def movie_edit(id=None):
    """
    编辑电影页面
    """
    form = MovieForm()
    movie = Movie.query.get_or_404(int(id))
    if form.validate_on_submit():
        data = form.data
        flash("修改电影成功！", "ok")
        return redirect(url_for('admin.movie_edit', id=movie.id))
    return render_template("admin/movie_edit.html", form=form)
```

创建movie_edit.html拷贝之前的movie add

将汉字添加变为修改。为字段赋初值。

### 赋初值

1. 在view中要将movie传进来。

```python
    return render_template("admin/movie_edit.html", form=form, movie=movie)
```
![mark](http://myphoto.mtianyan.cn/blog/180225/9H80394chi.png?imageslim)

同理修改url，info等

需要注意的是logo的修改

![mark](http://myphoto.mtianyan.cn/blog/180225/GKD660cHmd.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180225/1k0GBbKghk.png?imageslim)

>以及js代码中的这些部分的修改。


修改movie list中调向编辑的链接

![mark](http://myphoto.mtianyan.cn/blog/180225/7ALdfBill9.png?imageslim)

封面图不显示； 将data-src改为src。


然后对于其他几个量: info tag_id star赋初值

```python
    if request.method == "GET":
        form.info.data = movie.info
        form.tag_id.data = movie.tag_id
        form.star.data = movie.star
```

保存部分:

```python
    if form.validate_on_submit():
        data = form.data
        movie.star = data["star"]
        movie.tag_id = data["tag_id"]
        movie.info = data["info"]
        movie.title = data["title"]
        movie.area = data["area"]
        movie.length = data["length"]
        movie.release_time = data["release_time"]
        db.session.add(movie)
        db.session.commit()
        flash("修改电影成功！", "ok")
```

片名唯一的限制；

```
    form = MovieForm()
    # 因为是编辑，所以非空验证空
    form.url.validators = []
    form.logo.validators = []

     movie_count = Movie.query.filter_by(title=data["title"]).count()
        # 存在一步名字叫这个的电影，有可能是它自己，也有可能是同名。如果是现在的movie不等于要提交的数据中title。那么说明有两个。
        if movie_count == 1 and movie.title != data["title"]:
            flash("片名已经存在！", "err")
            return redirect(url_for('admin.movie_edit', id=id))
        # 创建目录
        if not os.path.exists(app.config["UP_DIR"]):
            os.makedirs(app.config["UP_DIR"])
            os.chmod(app.config["UP_DIR"], "rw")
        # 上传视频
        if form.url.data.filename != "":
            file_url = secure_filename(form.url.data.filename)
            movie.url = change_filename(file_url)
            form.url.data.save(app.config["UP_DIR"] + movie.url)
        # 上传图片
        if form.logo.data.filename != "":
            file_logo = secure_filename(form.logo.data.filename)
            movie.logo = change_filename(file_logo)
            form.logo.data.save(app.config["UP_DIR"] + movie.logo)
```


添加错误信息显示。

![mark](http://myphoto.mtianyan.cn/blog/180225/HDd17Ig4m8.png?imageslim)

```
builtins.AttributeError
AttributeError: 'str' object has no attribute 'filename'
```

将下面代码中 .filename 删除掉。

```
form.url.data.filename & form.logo.data.filename 
```

## 预告管理

1. 模型: Preview
2. 表单: PreviewForm
3. 请求方法: GET POST
4. 访问控制: `@admin_login_req`

### 创建预告forms

```python
class PreviewForm(FlaskForm):
    title = StringField(
        label="预告标题",
        validators=[
            DataRequired("预告标题不能为空！")
        ],
        description="预告标题",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入预告标题！"
        }
    )
    logo = FileField(
        label="预告封面",
        validators=[
            DataRequired("预告封面不能为空！")
        ],
        description="预告封面",
    )
    submit = SubmitField(
        '编辑',
        render_kw={
            "class": "btn btn-primary",
        }
    )
```

将该form实例化，然后通过view传送到html中

```python
def preview_add():
    """
    上映预告添加
    """
    form = PreviewForm()
    return render_template("admin/preview_add.html", form=form)
```

前往模板中进行form的填充。

![mark](http://myphoto.mtianyan.cn/blog/180225/fFHlEKihjL.png?imageslim)

添加字段的错误信息提示。

![mark](http://myphoto.mtianyan.cn/blog/180225/FgA2bgIgJb.png?imageslim)

添加操作正确的提示信息

**注意，为form添加method等于post 这个很重要。**

**当然，为html中的form添加了之后，对于视图中的也要添加**

```python
@admin.route("/preview/add/", methods=["GET", "POST"])
```

因为我们要上传图片所以，表单的enctype必须设置为multipart/form-data

想要显示错误信息:

```python
    if form.validate_on_submit():
        data = form.data
```

要添加对于form的验证。

### 实现业务逻辑

```
    if form.validate_on_submit():
        data = form.data
        file_logo = secure_filename(form.logo.data.filename)
        if not os.path.exists(app.config["UP_DIR"]):
            os.makedirs(app.config["UP_DIR"])
            os.chmod(app.config["UP_DIR"], "rw")
        logo = change_filename(file_logo)
        form.logo.data.save(app.config["UP_DIR"] + logo)
        preview = Preview(
            title=data["title"],
            logo=logo
        )
        db.session.add(preview)
        db.session.commit()
        flash("添加预告成功！", "ok")
        return redirect(url_for('admin.preview_add'))
```

上传logo文件，并重命名文件后保存在指定文件夹。保存title和logo字段。
显示消息闪现

### 实现预告列表

views中传递数据以及分页

```
@admin.route("/preview/list/<int:page>/", methods =["GET"])
@admin_login_req
def preview_list(page=None):
    """
    上映预告列表
    """
    if page is None:
        page = 1
    page_data = Preview.query.order_by(
        Preview.addtime.desc()
    ).paginate(page=page, per_page=1)
    return render_template("admin/preview_list.html", page_data=page_data)
```

此时可以想起来grid中的url还没有包含page参数。先去添加一个。

![mark](http://myphoto.mtianyan.cn/blog/180225/iKkf7HB4mI.png?imageslim)

进行list页面数据的填充

![mark](http://myphoto.mtianyan.cn/blog/180225/4b5ahIjB8h.png?imageslim)

添加之后我们该进入list页面import pg。调用page方法进行分页的实现

![mark](http://myphoto.mtianyan.cn/blog/180225/cdkehl7124.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180225/mhkc8jBl1I.png?imageslim)

此时访问可以发现图片过大。

![mark](http://myphoto.mtianyan.cn/blog/180225/Ek4f4i0lDk.png?imageslim)

### 删除与编辑实现

views中书写priviews的

```
@admin.route("/preview/del/<int:id>/", methods=["GET"])
@admin_login_req
def preview_del(id=None):
    """
    预告删除
    """
    preview = Preview.query.get_or_404(id)
    db.session.delete(preview)
    db.session.commit()
    flash("预告删除成功", "ok")
    return redirect(url_for('admin.preview_list', page=1))
```

前往list中为删除添加href地址

![mark](http://myphoto.mtianyan.cn/blog/180225/Jh4e44iedK.png?imageslim)

添加flash提示信息。

![mark](http://myphoto.mtianyan.cn/blog/180225/GlBjlh68Cb.png?imageslim)

>编辑功能

views中书写逻辑

```python
def preview_edit(id):
    """
    编辑预告
    """
    form = PreviewForm()
    # 下面这行代码禁用编辑时的提示:封面不能为空
    form.logo.validators = []
    preview = Preview.query.get_or_404(int(id))
    # get方法时，为title赋初值
    if request.method == "GET":
        form.title.data = preview.title
    if form.validate_on_submit():
        data = form.data
        if form.logo.data != "":
            file_logo = secure_filename(form.logo.data.filename)
            preview.logo = change_filename(file_logo)
            form.logo.data.save(app.config["UP_DIR"] + preview.logo)
        preview.title = data["title"]
        db.session.add(preview)
        db.session.commit()
        flash("修改预告成功！", "ok")
        return redirect(url_for('admin.preview_edit', id=id))
    return render_template("admin/preview_edit.html", form=form, preview=preview)
```

新建一个`preview_edit.html`，然后拷贝其中的`Preview_add.html`内容粘贴进去。

将其中的图片地址替换为我们当前预告的图片地址

将其中的添加预告改为修改预告。并且在`preview_list`中修改指向编辑的url

![mark](http://myphoto.mtianyan.cn/blog/180225/b5Fj1KG1m5.png?imageslim)
