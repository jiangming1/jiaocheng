## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

已上线演示地址: http://movie.mtianyan.cn
项目源码地址：https://github.com/mtianyan/movie_project

flask打造一个微电影弹幕网站。
第六节: 后台页面搭建

# 后台页面搭建

![mark](http://upload-images.jianshu.io/upload_images/1779926-bf9979de22bc644b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 管理员登录页面搭建

app/admin/views.py

```
@admin.route("/login/")
def login():
    """
    后台登录
    """
    return render_template("admin/login.html")


@admin.route("/logout/")
def logout():
    """
    后台注销登录
    """
    return redirect(url_for("admin.login"))
```

创建login.html,将tpl admin/login内容拷贝过来并修改所有的静态文件。

## 后台布局搭建

![mark](http://upload-images.jianshu.io/upload_images/1779926-5fda39c24212f0c0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

template admin中创建后台的admin.html将tpl目录下的admin.html拷贝过来

菜单是大家都共用的部分，将这个菜单剪切走。

![mark](http://upload-images.jianshu.io/upload_images/1779926-87c2886d6f2c0161.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将admin中下图的菜单部分剪切走。创建grid.html粘贴进去

![mark](http://upload-images.jianshu.io/upload_images/1779926-269278aa4895c0b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-edb1f7bb1524a829.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在admin的内容注释部分添加大家可以重写的内容部分数据块。

同样操作，添加js的数据块

![mark](http://upload-images.jianshu.io/upload_images/1779926-04f0d6bd04b5f1cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

同理css数据块添加到head中，无图

>自行替换静态资源环节
将我们已经有的url地址如admin.logout替换进去。

![mark](http://upload-images.jianshu.io/upload_images/1779926-f4ffdf345cfb858d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

实际访问

新建admin: index.html

![mark](http://upload-images.jianshu.io/upload_images/1779926-ac91d773d5f40770.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
@admin.route("/")
def index():
    return render_template("admin/index.html")
```

此时访问后台可以访问到:

![mark](http://upload-images.jianshu.io/upload_images/1779926-006cbcb05bad746b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 修改密码界面搭建

```
@admin.route("/pwd/")
def pwd():
    """
    后台密码修改
    """
    return render_template("admin/pwd.html")
```

创建pwd.html,找到tpl中pwd的html

![mark](http://upload-images.jianshu.io/upload_images/1779926-6e6b22db96c5bc39.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将内容注释块中的拿过来

此时验证访问可以成功。但是右上角的修改密码没有链接过来，自己前往admin.html添加

![mark](http://upload-images.jianshu.io/upload_images/1779926-48946284c1c5fa65.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 控制面板搭建

>修改指向首页的view函数

```
@admin.route("/")
def index():
    """
    首页系统管理
    """
    return render_template("admin/index.html")
```

将后台中后台首页部分替换成tpl/admin/index.html中的内容部分

![mark](http://upload-images.jianshu.io/upload_images/1779926-e90b645ab92a50d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

修改grid.html中的首页控制面板链接。

内存的使用率没有显示。

所以要在index.html中将echarts引入进来

![mark](http://upload-images.jianshu.io/upload_images/1779926-43a3f0d67718c251.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击首页并没有激活该项。

前往 grid.html给菜单和菜单项都给上id值

![mark](http://upload-images.jianshu.io/upload_images/1779926-79e4a080ee1fcfde.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

前往index中添加jQuery为id项添加class

![mark](http://upload-images.jianshu.io/upload_images/1779926-21543bc039ec11c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 标签管理页面搭建

```python
@admin.route("/tag/add/")
def tag_add():
    """
    标签添加与编辑
    """
    return render_template("admin/tag_add.html")


@admin.route("/tag/list/")
def tag_list():
    """
    标签列表
    """
    return render_template("admin/tag_list.html")
```

在grid中给上id

1. treeview 后面的菜单id
2. li 标签后的菜单项id

>为了以后不用一个一个修改，这里按照上面这个规则一次性修改完

然后修改grid中标签相关的链接

![mark](http://upload-images.jianshu.io/upload_images/1779926-42339dcb91498fd0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

创建taglist和tagadd的html文件

![mark](http://upload-images.jianshu.io/upload_images/1779926-9354f17f84c1852d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>重复了太多遍的工作: 继承admin 然后把tpl下内容部分搞过来。

![mark](http://upload-images.jianshu.io/upload_images/1779926-66543ade63063fac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将jQuery添加class的代码分别复制到add list 修改为g-2-1 | 2

标签列表中为了效果，添加for循环。

![mark](http://upload-images.jianshu.io/upload_images/1779926-78cb1fbbedf34cdf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 电影管理页面搭建

```python
@admin.route("/movie/add/")
def movie_add():
    """
    编辑电影页面
    """
    return render_template("admin/movie_add.html")


@admin.route("/movie/list/")
def movie_list():
    """
    电影列表页面
    """
    return render_template("admin/movie_list.html")
```

- 修改grid中跳转过来的url
- 新建movie_add movie_list html

>继承admin 然后content替换为对应html中content，Script标签中修改jquery操作的id
为对应id。

movie_add中的播放器支持的js需要拷贝过去。

修改static静态文件目录

## 上映预告管理页面搭建

```python
@admin.route("/preview/add/")
def preview_add():
    """
    上映预告添加
    """
    return render_template("admin/preview_add.html")


@admin.route("/preview/list/")
def preview_list():
    """
    上映预告列表
    """
    return render_template("admin/preview_list.html")
```

新建preview_add.html & preview_list.html

然后继续继承admin 复制content过来。修改g-4-1 | 2

- 修改grid 中跳转过来的链接

## 会员管理页面搭建

```
@admin.route("/user/list/")
def user_list():
    """
    会员列表
    """
    return render_template("admin/user_list.html")


@admin.route("/user/view/")
def user_view():
    """
    查看会员
    """
    return render_template("admin/user_view.html")
```

新建user_list.html & user_view.html

然后继续继承admin 复制content过来。修改g-5-1

- 修改grid 中跳转到user_list的链接

将user中的查看链接修改为跳转至user_view

![mark](http://upload-images.jianshu.io/upload_images/1779926-9355f5dccb280a82.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-b57e07693a6ce822.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>将user_view独有的css拿过来。

## 评论管理页面搭建

```
@admin.route("/comment/list/")
def comment_list():
    """
    评论列表
    """
    return render_template("admin/comment_list.html")
```

新建comment_list.html

然后继续继承admin 复制content过来。修改g-6-1

- 修改grid 中跳转过来的链接

修改static文件路径并把css数据块中内容加上。

## 收藏管理页面搭建

```
@admin.route("/moviecol/list/")
def moviecol_list():
    """
    电影收藏
    """
    return render_template("admin/moviecol_list.html")
```

新建moviecol_list.html

然后继续继承admin 复制content过来。修改g-7-1

- 修改grid 中跳转过来的链接

修改static文件路径并把css数据块中内容加上。

## 日志管理页面搭建

```
@admin.route("/oplog/list/")
def oplog_list():
    """
    操作日志管理
    """
    return render_template("admin/oplog_list.html")


@admin.route("/adminloginlog/list/")
def adminloginlog_list():
    """
    管理员日志列表
    """
    return render_template("admin/adminloginlog_list.html")


@admin.route("/userloginlog/list/")
def userloginlog_list():
    """
    会员日志列表
    """
    return render_template("admin/userloginlog_list.html")
```
新建oplog_list.html adminloginlog_list.html userloginlog_list.html

然后继续继承admin 复制content过来。修改g-8-1 g-8-2 g-8-3

- 修改grid 中跳转过来的链接

修改static文件路径并把css数据块中内容加上(本小节没有需要修改的static文件路径)。

## 权限管理页面搭建

```python
@admin.route("/auth/add/")
def auth_add():
    """
    添加权限
    """
    return render_template("admin/auth_add.html")


@admin.route("/auth/list/")
def auth_list():
    """
    权限列表
    """
    return render_template("admin/auth_list.html")
```

新建auth_add.html auth_list.html

然后继续继承admin 复制content过来。修改g-9-1 g-9-2

- 修改grid 中跳转过来的链接

修改static文件路径并把css数据块中内容加上。

## 角色管理

```python
@admin.route("/role/add/")
def role_add():
    """
    添加角色
    """
    return render_template("admin/role_add.html")


@admin.route("/role/list/")
def role_list():
    """
    角色列表
    """
    return render_template("admin/role_list.html")
```

新建role_add.html role_list.html

然后继续继承admin 复制content过来。修改g-10-1 g-10-2

- 修改grid 中跳转过来的链接

修改static文件路径并把css数据块中内容加上。

## 管理员管理页面的搭建

```python
@admin.route("/admin/add/")
def admin_add():
    """
    添加管理员
    """
    return render_template("admin/admin_add.html")


@admin.route("/admin/list/")
def admin_list():
    """
    管理员列表
    """
    return render_template("admin/admin_list.html")
```

新建admin_add.html admin_list.html

然后继续继承admin 复制content过来。修改g-11-1 g-11-2

- 修改grid 中跳转过来的链接

修改static文件路径并把css数据块中内容加上。


>第六章完结 后台页面搭建完成。
