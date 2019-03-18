## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

已上线演示地址: http://movie.mtianyan.cn
项目源码地址：https://github.com/mtianyan/movie_project

# 项目优化与模型设计

## 前后台项目目录分析：

微电影网站：

- 前台模块（home）
- 后台模块（admin）

前台模块：

- 数据模型：models.py
- 表单处理： home/forms.py
- 模板目录： templates/home
- 静态目录：static

后台

- 数据模型：models.py
- 表单处理： admin/forms.py
- 模板目录： templates/admin
- 静态目录：static

前台后台表单处理，模板目录独立。数据模型共有。

**前后台目录分析：**

![项目目录分析](http://upload-images.jianshu.io/upload_images/1779926-1fa97903a121d937.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

pythonpackage：

- app
- app::admin
- app::home

python file：

- manage.py(入口脚本文件)
- app::models.py
- app::home::views.py
- app::home::forms.py
- app::admin::views.py
- app::admin::forms.py

directory:

- app::static
- templates
- templates::admin
- templates::home

最终项目目录如下图：

![目录结构](http://upload-images.jianshu.io/upload_images/1779926-2e569d294f608656.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 蓝图Blueprint规划项目结构

1. 什么是蓝图？

> 一个应用中或跨应用制作应用组件和支持通用的模式

为了有效的将前台和后台的模块进行区分与关联使用蓝图进行统一的规划。

2. 蓝图的作用：

- 将不同的功能模块化
- 构建大型应用
- 优化项目结构
- 增强可读性，易于维护

**将项目合理规划，易于扩展，维护。**

- 定义蓝图(`app/admin/__init__.py`)

```python
from flask import Blueprint
# 传入两个参数，一个是蓝图名称，一个是name值
admin = Blueprint("admin",__name__)
import views
```

- 注册蓝图(`app/__init__.py`)

```python
# 从前台，后台模块中导入我们的蓝图对象
from admin import admin as admin_blueprint

# 使用app对象，调用register_blueprint函数进行蓝图的注册
# 第一个参数是蓝图，第二个参数是url地址的前缀。通过地址前缀划分前后台的路由
app.register_blueprint(admin_blueprint,url_prefix="admin")

``` 
- 调用蓝图(`app/admin/views.py`)

```python
# 从模块的初始化文件中导入蓝图。
from . import admin

# 路由定义使用装饰器进行定义
@admin.route("/")
```

### 实际编码：

home/init.py:

```python
from flask import Blueprint

home = Blueprint("home",__name__)

import app.home.views
```

admin/init.py:

```python
from flask import Blueprint

admin = Blueprint("admin",__name__)

import app.admin.views
```

- 第一步：定义出了两个蓝图对象。
- 第二步：去views.py内调用蓝图对象：

home/views.py:

```python
from . import home

@home.route("/")
def index():
    return "<h1 style='color:green'>this is home</h1>"
```

admin/views.py:

```python
from . import admin

@admin.route("/")
def index():
    return "<h1 style='color:red'>this is admin</h1>"
```

- 第三步：注册蓝图对象

app/init.py:

```python
# _*_ coding: utf-8 _*_
__author__ = 'mtianyan'
__date__ = '2017/8/26 17:05'

from flask import Flask

app = Flask(__name__)
app.debug = True

from app.home import home as home_blueprint
from app.admin import admin as admin_blueprint

app.register_blueprint(home_blueprint)
app.register_blueprint(admin_blueprint, url_prefix="/admin")
```

>前台不需要加前缀,并为admin设置了url前缀"/admin"

- 最后一步：定义入口脚本manage.py。启动app。

manage.py:

```python
# _*_ coding: utf-8 _*_
__author__ = 'mtianyan'
__date__ = '2017/8/26 17:05'

from app import app

if __name__ == "__main__":
    app.run()
```

成果：

>点击run之后可以在浏览器里看到：
前台绿色，后台(/admin)红色

## 数据模型关系分析

- 前台模型
- 后台模型

![mark](http://upload-images.jianshu.io/upload_images/1779926-d09437686e918943.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-7f1d6c6a6f4a889b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-67a13494d9d69ba3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 会员及会员登录日志数据模型设计

使用flaskSQLAlchemy定义和业务需求相关的数据库模型

- 安装数据库连接依赖包
`pip install flask-sqlalchemy`

>sqlalchemy(企业级的orm对象关系映射框架。面向对象的思想操作数据库)

- 定义mysql数据库连接：

```python
from flask_sqlalchemy import SQLAlchemy
from flask import Flask

app = from flask import Flask 
app = Flask(__name__)
app.config['SQLALCHEMY_DATABASE_URL'] = "mysql://root:ty158917@localhost/movie"
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] =True
db = SQLAlchemy(app)
```
- 定义会员数据模型：

```python
class User(db.Model):
    # 数据库中数据表的名称
	__tablename__ = "user"
	id = db.Column(db.Integer,primary_key=True)
    name = db.Column(db.String)
    pwd = db.Column(db.String)
    email = db.Column(db.String)
    phone = db.Column(db.String)
    info = db.Column(db.Text)
    face = db.Column(db.String)
    addtime = db.Column(db.DateTime,index=True,default=datetime.now)
    uuid = db.Column(db.String)
```

![会员数据模型字段说明](http://upload-images.jianshu.io/upload_images/1779926-7a5cde1c59c62dab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 会员登录日志数据模型：
	- id:编号
	- user_id：所属会员编号
	- ip：最近登录ip地址
	- addtime：最近登录时间

### 实际编码：

- 控制台下使用：`mysql -uroot -p密码`进入mysql控制台
- mysql>`create database movie;`来创建数据库 

models.py:

http://www.pythondoc.com/flask-sqlalchemy/config.html#id2

Flask-SQLAlchemy 配置键参数查询地址

```python
# _*_ coding: utf-8 _*_
import datetime

__author__ = 'mtianyan'
__date__ = '2017/8/26 17:05'

from flask import Flask
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)

# 用于连接数据的数据库。
app.config['SQLALCHEMY_DATABASE_URL'] = "mysql://root:root@127.0.0.1:3306/movie"
# 如果设置成 True (默认情况)，Flask-SQLAlchemy 将会追踪对象的修改并且发送信号。
app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] =True

db = SQLAlchemy(app)

# 会员数据模型
class User(db.Model):
    __tablename__ = "user"
    id = db.Column(db.Integer, primary_key=True)  # 编号
    name = db.Column(db.String(100), unique=True)  # 昵称
    pwd = db.Column(db.String(100))  # 密码
    email = db.Column(db.String(100), unique=True)  # 邮箱
    phone = db.Column(db.String(11), unique=True)  # 手机号
    info = db.Column(db.Text)  # 个性简介
    face = db.Column(db.String(255), unique=True)  # 头像
    addtime = db.Column(db.DateTime, index=True, default=datetime.now)  # 注册时间
    uuid = db.Column(db.String(255), unique=True)  # 唯一标识符
    # （下面是设置外键的第二步,指向Userlog模型，进行一个互相关系的绑定）
    userlogs = db.relationship('Userlog', backref='user')  # 会员日志外键关系关联
    comments = db.relationship('Comment', backref='user')  # 评论外键关系关联
    moviecols = db.relationship('Moviecol', backref='user')  # 收藏外键关系关联

    def __repr__(self):
        return '<User %r>' % self.name


# 会员登录日志
class Userlog(db.Model):
    __tablename__ = "userlog"
    id = db.Column(db.Integer, primary_key=True)  # 编号
    # （下面是设置外键的第一步）:指向user表的id字段
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'))  # 所属会员
    ip = db.Column(db.String(100))  # ip地址
    addtime = db.Column(db.DateTime, index=True, default=datetime.now)  # 登录时间

    def __repr__(self):
        return '<User %r>' % self.id

```

**注意**设置外键分为两步进行。

## 标签、电影、上映预告数据模型设计
定义标签数据模型：

	- id：编号
	- name：标题
	- movies：电影外键关联
	- addtime：创建时间

- 定义电影数据模型：

![电影数据模型](http://upload-images.jianshu.io/upload_images/1779926-b776ab8897ccce85.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 定义上映预告数据模型
	- id：编号
	- title：上映预告标题
	- logo：上映预告封面
	- addtime：创建时间

```python
# 标签
class Tag(db.Model):
    __tablename__ = "tag"
    id = db.Column(db.Integer, primary_key=True)  # 编号
    name = db.Column(db.String(100), unique=True)  # 标题
    addtime = db.Column(db.DateTime, index=True, default=datetime.now)  # 添加电影时间
    # （设置外键的第二步）关联模型，相互关系
    movies = db.relationship("Movie", backref='tag')  # 电影外键关系关联

    def __repr__(self):
        return "<Tag %r>" % self.name


# 电影
class Movie(db.Model):
    __tablename__ = "movie"
    id = db.Column(db.Integer, primary_key=True)  # 编号
    title = db.Column(db.String(255), unique=True)  # 标题
    url = db.Column(db.String(255), unique=True)  # 地址
    info = db.Column(db.Text)  # 电影简介
    logo = db.Column(db.String(255), unique=True)  # 封面
    star = db.Column(db.SmallInteger)  # 星级
    playnum = db.Column(db.BigInteger)  # 播放量
    commentnum = db.Column(db.BigInteger)  # 评论量
    #  （设置外键第一步）
    tag_id = db.Column(db.Integer, db.ForeignKey('tag.id'))  # 所属标签
    area = db.Column(db.String(255))  # 上映地区
    release_time = db.Column(db.Date)  # 上映时间
    length = db.Column(db.String(100))  # 播放时间
    addtime = db.Column(db.DateTime, index=True, default=datetime.now)  # 添加时间
    comments = db.relationship("Comment", backref='movie')  # 评论外键关系关联
    moviecols = db.relationship("Moviecol", backref='movie')  # 收藏外键关系关联

    def __repr__(self):
        return "<Movie %r>" % self.title


# 上映预告
class Preview(db.Model):
    __tablename__ = "preview"
    id = db.Column(db.Integer, primary_key=True)  # 编号
    title = db.Column(db.String(255), unique=True)  # 标题
    logo = db.Column(db.String(255), unique=True)  # 封面
    addtime = db.Column(db.DateTime, index=True, default=datetime.now)  # 添加时间

    def __repr__(self):
        return "<Preview %r>" % self.title
```

## 评论及收藏电影数据模型设计

定义评论数据模型：

- id：编号
- content：评论内容
- movie_id: 所属电影
- user_id: 所属用户
- addtime：最近登录时间

定义收藏电影数据模型；

![mark](http://upload-images.jianshu.io/upload_images/1779926-07ccdf7de214e3c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```python
# 评论
class Comment(db.Model):
    __tablename__ = "comment"
    id = db.Column(db.Integer, primary_key=True)  # 编号
    content = db.Column(db.Text)  # 评论内容
    # 关联外键第一步，还要去user表和movie表进行第二步
    movie_id = db.Column(db.Integer, db.ForeignKey('movie.id'))  # 所属电影
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'))  # 所属用户
    addtime = db.Column(db.DateTime, index=True, default=datetime.now)  # 添加时间

    def __repr__(self):
        return "<Comment %r>" % self.id


# 电影收藏
class Moviecol(db.Model):
    __tablename__ = "moviecol"
    id = db.Column(db.Integer, primary_key=True)  # 编号
    # 关联外键第一步，还要去user表和movie表进行第二步
    movie_id = db.Column(db.Integer, db.ForeignKey('movie.id'))  # 所属电影
    user_id = db.Column(db.Integer, db.ForeignKey('user.id'))  # 所属用户
    addtime = db.Column(db.DateTime, index=True, default=datetime.now)  # 添加时间

    def __repr__(self):
        return "<Moviecol %r>" % self.id
```

## 权限及角色数据模型设计

定义权限数据模型：

- id：编号
- name：名称
- url：地址（路由地址）
- addtime：创建时间

>如果路由地址和权限地址匹配说明我们的管理员有这个权限。

定义角色模型：

- id：编号
- name：名称
- auths：权限列表（多个权限以分隔符分开组成的字符串）
- addtime：创建时间
- admins：管理员外键关联

```python
# 权限
class Auth(db.Model):
    __tablename__ = "auth"
    id = db.Column(db.Integer, primary_key=True)  # 编号
    name = db.Column(db.String(100), unique=True)  # 名称
    url = db.Column(db.String(255), unique=True)  # 地址
    addtime = db.Column(db.DateTime, index=True, default=datetime.now)  # 添加时间

    def __repr__(self):
        return "<Auth %r>" % self.name


# 角色
class Role(db.Model):
    __tablename__ = "role"
    id = db.Column(db.Integer, primary_key=True)  # 编号
    name = db.Column(db.String(100), unique=True)  # 名称
    auths = db.Column(db.String(600))  # 角色权限列表
    addtime = db.Column(db.DateTime, index=True, default=datetime.now)  # 添加时间
    admins = db.relationship("Admin", backref='role')  # 管理员外键关系关联

    def __repr__(self):
        return "<Role %r>" % self.name
```
## 管理员.登陆日志。操作日志数据模型设计

![管理员数据模型](http://upload-images.jianshu.io/upload_images/1779926-2d8955e6fc9c1c65.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![管理员登录日志数据模型](http://upload-images.jianshu.io/upload_images/1779926-aab8cebb9a69495b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![操作日志数据模型](http://upload-images.jianshu.io/upload_images/1779926-fc22332db13ecb9f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
# 管理员
class Admin(db.Model):
    __tablename__ = "admin"
    id = db.Column(db.Integer, primary_key=True)  # 编号
    name = db.Column(db.String(100), unique=True)  # 管理员账号
    pwd = db.Column(db.String(100))  # 管理员密码
    is_super = db.Column(db.SmallInteger)  # 是否为超级管理员，0为超级管理员
    role_id = db.Column(db.Integer, db.ForeignKey('role.id'))  # 所属角色
    addtime = db.Column(db.DateTime, index=True, default=datetime.now)  # 添加时间
    adminlogs = db.relationship("Adminlog", backref='admin')  # 管理员登录日志外键关系关联
    oplogs = db.relationship("Oplog", backref='admin')  # 管理员操作日志外键关系关联

    def __repr__(self):
        return "<Admin %r>" % self.name

    def check_pwd(self, pwd):
        from werkzeug.security import check_password_hash
        return check_password_hash(self.pwd, pwd)


# 管理员登录日志
class Adminlog(db.Model):
    __tablename__ = "adminlog"
    id = db.Column(db.Integer, primary_key=True)  # 编号
    admin_id = db.Column(db.Integer, db.ForeignKey('admin.id'))  # 所属管理员
    ip = db.Column(db.String(100))  # 登录IP
    addtime = db.Column(db.DateTime, index=True, default=datetime.now)  # 登录时间

    def __repr__(self):
        return "<Adminlog %r>" % self.id


# 操作日志
class Oplog(db.Model):
    __tablename__ = "oplog"
    id = db.Column(db.Integer, primary_key=True)  # 编号
    admin_id = db.Column(db.Integer, db.ForeignKey('admin.id'))  # 所属管理员
    ip = db.Column(db.String(100))  # 操作IP
    reason = db.Column(db.String(600))  # 操作原因
    addtime = db.Column(db.DateTime, index=True, default=datetime.now)  # 登录时间

    def __repr__(self):
        return "<Oplog %r>" % self.id
```

### 结合mysql数据库生成数据表

1. 安装`pymysql`然后点击run运行。

**故障提示：**

SQLAlchemy插入数据产生警告

```
cursors.py:166: Warning: (1366, "Incorrect string value: '\\xD6\\xD0\\xB9\\xFA\\xB1\\xEA...' for column 'VARIABLE_VALUE' at row 479")result = self._query(query)
```

>windows系统默认使用的是gbk的字符集，而我在windows系统上使用的数据库也是在本机windows系统上，而报错是因为字符集的问题，但是我指定了数据库的字符集是utf8的，然每次连接依然有这个错误，个人分析应该是系统字符串造成的。所以我改到使用linux系统里的mysql建立一个utf8的数据库后，使用未出现上述问题，所以分析应该是系统字符串导致的。

此处我使用了远程连接linux服务器上的数据库，消除了警告。但是即使你没有linux上的数据库也不影响，因为这只是一个警告，并不是错误。

完整models.py代码地址：

https://github.com/mtianyan/movie_project/blob/master/app/models.py

最终映射成功后的数据表：

![运行成功](http://upload-images.jianshu.io/upload_images/1779926-09b97bcdcea40dd7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![映射出的数据库表](http://upload-images.jianshu.io/upload_images/1779926-5e459f49616d3df1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

附录：
mysql命令：
- `use movie;`
- `show tables;`
- `desc admin;`

测试插入一条数据

```
if __name__ == "__main__":
    db.create_all()

    role = Role(
        name="超级管理员",
        auths=""
    )
    db.session.add(role)
    db.session.commit()

```

![mark](http://upload-images.jianshu.io/upload_images/1779926-9e2d60d8f6d98aa6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
  from werkzeug.security import generate_password_hash

    admin = Admin(
        name="mtianyanmovie",
        pwd=generate_password_hash("mtianyanmovie"),
        is_super=0,
        role_id=10
    )
    db.session.add(admin)
    db.session.commit()
```

>注意这里的roleid 应该是你数据库里存在的role id 否则会产生外键错误。
