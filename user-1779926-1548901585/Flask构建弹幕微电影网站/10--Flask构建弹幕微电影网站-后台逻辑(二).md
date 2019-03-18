## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

已上线演示地址: http://movie.mtianyan.cn
项目源码地址：https://github.com/mtianyan/movie_project

## 标签管理

1. 模型：Tag
2. 表单: TagForm
3. 请求方法: GET POST
4. 访问控制: `@admin_login_req`

前往forms定义TagForm

```python
class TagForm(FlaskForm):
    name = StringField(
        label="名称",
        validators=[
            DataRequired("标签名不能为空")
        ],
        description="标签",
        render_kw={
            "class": "form-control",
            "id": "input_name",
            "placeholder": "请输入标签名称！"
        }
    )
    submit = SubmitField(
        '添加',
        render_kw={
            "class": "btn btn-primary",
        }
    )
```

admin/views.py:

```python
    form = TagForm()
    return render_template("admin/tag_add.html", form=form)
```

实例化出一个Tagform，然后将form传递到前台去。
下面在html中使用form

![mark](http://myphoto.mtianyan.cn/blog/180223/419ALdA7e5.png?imageslim)

在提交前加上csrf token

![mark](http://myphoto.mtianyan.cn/blog/180223/6ajEhLF0ke.png?imageslim)

数据库入库操作之前:html中定义展示错误信息

![mark](http://myphoto.mtianyan.cn/blog/180223/1II0J07aGC.png?imageslim)

此时验证没有标红的提示信息。是因为没有定义我们的method方法。

![mark](http://myphoto.mtianyan.cn/blog/180223/Am6Dk3fF9A.png?imageslim)

```
Method Not Allowed
The method is not allowed for the requested URL.
```

这是因为虽然在form中定义了method，但是在views中没有接受。

```python
@admin.route("/tag/add/", methods=["GET", "POST"])
```
标签不能重名

```python
        data = form.data
        tag = Tag.query.filter_by(name=data["name"]).count()
        # 说明已经有这个标签了
        if tag == 1:
            flash("标签已存在", "err")
            return redirect(url_for("admin.tag_add"))
        tag = Tag(
            name= data["name"]
        )
        db.session.add(tag)
        db.session.commit()
        flash("标签添加成功", "ok")
```

为flash进行分类，第二个参数指定flash信息的分类

![mark](http://myphoto.mtianyan.cn/blog/180223/fee6CAfBlb.png?imageslim)

>添加弹窗alert

![mark](http://myphoto.mtianyan.cn/blog/180223/e8E3bC1Imb.png?imageslim)

### 标签的分页展示

前往views中的标签列表进行查询以及分页。

```python
@admin.route("/tag/list/<int:page>/", methods= ["GET"])
@admin_login_req
def tag_list(page=None):
    """
    标签列表
    """
    if page is None:
        page = 1
    page_data = Tag.query.order_by(
        Tag.addtime.desc()
    ).paginate(page=page, per_page=1)
    return render_template("admin/tag_list.html", page_data=page_data)
```

html中

![mark](http://myphoto.mtianyan.cn/blog/180223/7b1d37F28D.png?imageslim)

修改gird中标签列表的url，为其添加参数page =1

![mark](http://myphoto.mtianyan.cn/blog/180223/0k2LEFJKJ5.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180223/JbfGhD9G2j.png?imageslim)

将页码信息拷贝走。

![mark](http://myphoto.mtianyan.cn/blog/180223/I2H9Eb4flK.png?imageslim)

并使用macro进行页码的动态显示

然后在要使用分页的html文件中import之后调用pg.page方法。传入分页后的数据。以及名称

![mark](http://myphoto.mtianyan.cn/blog/180223/HK4Llm8if3.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180223/99IFbbijGj.png?imageslim)

### 标签的删除

```python
@admin.route("/tag/del/<int:id>/", methods= ["GET"])
@admin_login_req
def tag_del(id=None):
    """
    标签删除
    """
    # filter_by在查不到或多个的时候并不会报错，get会报错。
    tag = Tag.query.filter_by(id=id).first_or_404()
    db.session.delete(tag)
    db.session.commit()
    flash("标签<<{0}>>删除成功".format(tag.name), "ok")
    return redirect(url_for("admin.tag_list", page=1))
```

将tag add中的alert弹框拷贝一份粘贴到tag list中接收闪现。

![mark](http://myphoto.mtianyan.cn/blog/180224/iHh9mlf4eL.png?imageslim)

>for msg in 和endfor忘了复制过来记得自己加。 

为删除字符添加对应的处理view函数

![mark](http://myphoto.mtianyan.cn/blog/180224/ADf5BgJ8Dk.png?imageslim)

### 编辑标签

```python
@admin.route("/tag/edit/<int:id>", methods=["GET", "POST"])
@admin_login_req
def tag_edit(id=None):
    """
    标签编辑
    """
    form = TagForm()
    form.submit.label.text = "编辑"
    tag = Tag.query.get_or_404(id)
    if form.validate_on_submit():
        data = form.data
        tag_count = Tag.query.filter_by(name=data["name"]).count()
        # 说明已经有这个标签了,此时向添加一个与其他标签重名的标签。
        if tag.name != data["name"] and tag_count == 1:
            flash("标签已存在", "err")
            return redirect(url_for("admin.tag_edit", id=tag.id))
        tag.name = data["name"]
        db.session.add(tag)
        db.session.commit()
        flash("标签修改成功", "ok")
        redirect(url_for("admin.tag_edit", id=tag.id))
    return render_template("admin/tag_edit.html", form=form, tag=tag)
```

>创建一个tag edit的html将tag add的内容拷贝进来

- 修改汉字将添加标签修改为编辑标签。

![mark](http://myphoto.mtianyan.cn/blog/180224/IBjBjK44hD.png?imageslim)

然后为from.name添加上value值。

在taglist中修改指向tag edit的路由

此处对于submit字段的text修改，必须放在redirect之前。

![mark](http://myphoto.mtianyan.cn/blog/180224/Cf3f9HihI5.png?imageslim)

## 电影管理

1. 模型: Movie
2. 表单: MovieForm
3. 请求方法: GET POST
4. 访问控制: @admin_login_req

### 创建添加电影的表单

```python
class MovieForm(FlaskForm):
    title = StringField(
        label="片名",
        validators=[
            DataRequired("片名不能为空！")
        ],
        description="片名",
        render_kw={
            "class": "form-control",
            "id": "input_title",
            "placeholder": "请输入片名！"
        }
    )
    url = FileField(
        label="文件",
        validators=[
            DataRequired("请上传文件！")
        ],
        description="文件",
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
    logo = FileField(
        label="封面",
        validators=[
            DataRequired("请上传封面！")
        ],
        description="封面",
    )
    star = SelectField(
        label="星级",
        validators=[
            DataRequired("请选择星级！")
        ],
        # star的数据类型
        coerce=int,
        choices=[(1, "1星"), (2, "2星"), (3, "3星"), (4, "4星"), (5, "5星")],
        description="星级",
        render_kw={
            "class": "form-control",
        }
    )
    # 标签要在数据库中查询已存在的标签
    tag_id = SelectField(
        label="标签",
        validators=[
            DataRequired("请选择标签！")
        ],
        coerce=int,
        # 通过列表生成器生成列表
        choices=[(v.id, v.name) for v in Tag.query.all()],
        description="标签",
        render_kw={
            "class": "form-control",
        }
    )
    area = StringField(
        label="地区",
        validators=[
            DataRequired("请输入地区！")
        ],
        description="地区",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入地区！"
        }
    )
    length = StringField(
        label="片长",
        validators=[
            DataRequired("片长不能为空！")
        ],
        description="片长",
        render_kw={
            "class": "form-control",
            "placeholder": "请输入片长！"
        }
    )
    release_time = StringField(
        label="上映时间",
        validators=[
            DataRequired("上映时间不能为空！")
        ],
        description="上映时间",
        render_kw={
            "class": "form-control",
            "placeholder": "请选择上映时间！",
            "id": "input_release_time"
        }
    )
    submit = SubmitField(
        '添加',
        render_kw={
            "class": "btn btn-primary",
        }
    )
```


### 将movie & movieforms 导入到views里面

```python
def movie_add():
    """
    添加电影页面
    """
    form = MovieForm()
    return render_template("admin/movie_add.html",form=form)
```

>下面展示在html中怎么用这个。

![mark](http://myphoto.mtianyan.cn/blog/180224/f4c5K0Gk93.png?imageslim)

找猫画虎同理可得的将这些修改完。

添加csrf_token,以及为form添加method = post

因为这里我们需要上传的是文件:

```python
enctype = "multipart/form-data"
```

### 书写逻辑验证

1. 添加错误信息:

![mark](http://myphoto.mtianyan.cn/blog/180224/iGkeCfDmJ6.png?imageslim)

>照猫画虎自行为所有字段添加错误信息

```
Method Not Allowed
The method is not allowed for the requested URL.
```

```python
@admin.route("/movie/add/", methods=["GET", "POST"])
```

2. 进行上传操作

首先打开init.py定义一个文件上传的路径。

```python
app.config["UP_DIR"] = os.path.join(os.path.abspath(os.path.dirname(__file__)), "static/uploads/")
```

获取上传的数据，

```
from app import db, app
from werkzeug.utils import secure_filename

# 修改文件名称
def change_filename(filename):
    fileinfo = os.path.splitext(filename)
    filename = datetime.now().strftime("%Y%m%d%H%M%S") + str(uuid.uuid4().hex) + fileinfo[-1]
    return filename

@admin.route("/movie/add/", methods=["GET", "POST"])
@admin_login_req
def movie_add():
    """
    添加电影页面
    """
    form = MovieForm()
    if form.validate_on_submit():
        data = form.data
        file_url = secure_filename(form.url.data.filename)
        file_logo = secure_filename(form.logo.data.filename)
        if not os.path.exists(app.config["UP_DIR"]):
            # 创建一个多级目录
            os.makedirs(app.config["UP_DIR"])
            os.chmod(app.config["UP_DIR"], "rw")
        url = change_filename(file_url)
        logo = change_filename(file_logo)
        # 保存
        form.url.data.save(app.config["UP_DIR"]+url)
        form.logo.data.save(app.config["UP_DIR"] + logo)
        # url,logo为上传视频,图片之后获取到的地址
        movie = Movie(
            title= data["title"],
            url = url,
            info = data["info"],
            logo = logo,
            star= data["star"],
            playnum=0,
            commentnum=0,
            tag_id=data["tag_id"],
            area=data["area"],
            release_time=data["release_time"],
            length=data["length"]
        )
        db.session.add(movie)
        db.session.commit()
        flash("添加电影成功！", "ok")
        return redirect(url_for('admin.movie_add'))
    return render_template("admin/movie_add.html", form=form)
```


添加消息闪现。

![mark](http://myphoto.mtianyan.cn/blog/180224/lamJjK0kKi.png?imageslim)

```
builtins.AttributeError
AttributeError: module 'app' has no attribute 'config'
```

说明引入的app不是我们需要的app

```python
import app

# 改为
from app import app
```

报错:

```
builtins.TypeError
TypeError: an integer is required (got type str)
```

```
star= int(data["star"]),
tag_id=int(data["tag_id"]),
```

检查是否为整型然后提交。

![mark](http://myphoto.mtianyan.cn/blog/180224/hka5ljbBGi.png?imageslim)
