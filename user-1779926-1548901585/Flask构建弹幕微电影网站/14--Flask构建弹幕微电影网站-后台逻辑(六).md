## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

>本章内容: 日志管理实现
已上线演示地址: http://movie.mtianyan.cn
项目源码地址：https://github.com/mtianyan/movie_project

1. 模型: Oplog Userlog Adminlog
2. 表单: 无
3. 请求方法: GET
4. 访问控制: `@admin_login_req`

引入上下文应用处理器的概念。封装全局变量把全局变量展现到模板里面

```
@admin.context_processor
def tpl_extra():
    """
    上下应用处理器
    """
    data = dict(
        online_time=datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    )
    return data
```

如何将online_time体现在模板中

![mark](http://myphoto.mtianyan.cn/blog/180226/a704iI32bi.png?imageslim)

### 将增删改查的记录记录下来

```python
session["admin_id"] = admin.id
session.pop("admin_id", None)
```

登录进来将id保存到session。退出时清除。

在tag_add中添加

```python
        oplog = Oplog(
            admin_id=session["admin_id"],
            ip=request.remote_addr,
            reason="添加标签%s" % data["name"]
        )
        db.session.add(oplog)
        db.session.commit()
```

flask中获取ip地址使用`request.remote_addr`

上一节中的g只活在当前请求的上下文，岂不是每个都得添加。所以在上下文应用处理器中添加字段: logo,但是全局上下文就包括了那些没有session['admin']存在的页面，如登录页面所以加上try捕获。

![mark](http://myphoto.mtianyan.cn/blog/180226/K2DDg51hgG.png?imageslim)

### 实现操作日志列表

```python
@admin.route("/oplog/list/<int:page>/", methods=["GET"])
@admin_login_req
def oplog_list(page=None):
    """
    操作日志管理
    """
    if page is None:
        page = 1
    page_data = Oplog.query.join(
        Admin
    ).filter(
        Admin.id == Oplog.admin_id,
    ).order_by(
        Oplog.addtime.desc()
    ).paginate(page=page, per_page=1)
    return render_template("admin/oplog_list.html", page_data=page_data)
```

设置了列表的处理view。前往grid中为跳转的url添加page字段。

![mark](http://myphoto.mtianyan.cn/blog/180226/50cB2fA69C.png?imageslim)

使用pagedata填充页面字段值。

![mark](http://myphoto.mtianyan.cn/blog/180226/d55ielhc9f.png?imageslim)

然后import pg 并调用page方法进行分页操作

![mark](http://myphoto.mtianyan.cn/blog/180226/JLIb6GIIJ2.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180226/DEB3B2AC36.png?imageslim)

### 管理员登录日志

在登录的view视图时存入数据

```python
        adminlog = Adminlog(
            admin_id=admin.id,
            ip=request.remote_addr,
        )
        db.session.add(adminlog)
        db.session.commit()
```

查看列表

```python
@admin.route("/adminloginlog/list/<int:page>/", methods=["GET"])
@admin_login_req
def adminloginlog_list(page=None):
    if page is None:
        page = 1
    page_data = Adminlog.query.join(
        Admin
    ).filter(
        Admin.id == Adminlog.admin_id,
    ).order_by(
        Adminlog.addtime.desc()
    ).paginate(page=page, per_page=1)
    return render_template("admin/adminloginlog_list.html", page_data=page_data)
```

将page_data填充到模板中，import pg 然后调用page方法。

![mark](http://myphoto.mtianyan.cn/blog/180226/Fk61BA4bIc.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180226/Iledkhe9Jg.png?imageslim)

前往grid中添加跳转过来的url

![mark](http://myphoto.mtianyan.cn/blog/180226/I6aLeC3884.png?imageslim)


### 会员登录日志列表

添加测试数据

```
insert into userlog(user_id,ip,addtime) values(1,"192.168.4.1",now());
insert into userlog(user_id,ip,addtime) values(2,"192.168.4.2",now());
insert into userlog(user_id,ip,addtime) values(3,"192.168.4.3",now());
insert into userlog(user_id,ip,addtime) values(4,"192.168.4.4",now());
insert into userlog(user_id,ip,addtime) values(5,"192.168.4.5",now());
insert into userlog(user_id,ip,addtime) values(6,"192.168.4.6",now());
insert into userlog(user_id,ip,addtime) values(7,"192.168.4.7",now());
insert into userlog(user_id,ip,addtime) values(8,"192.168.4.8",now());
```

views.py中

```python
@admin.route("/userloginlog/list/<int:page>/", methods=["GET"])
@admin_login_req
def userloginlog_list(page=None):
    """
    会员登录日志列表
    """
    if page is None:
        page = 1
    page_data = Userlog.query.join(
        User
    ).filter(
        User.id == Userlog.user_id,
    ).order_by(
        Userlog.addtime.desc()
    ).paginate(page=page, per_page=2)
    return render_template("admin/userloginlog_list.html", page_data=page_data)
```

然后先使用pagedata填充数据，再使用import pg 以及page方法进行分页

![mark](http://myphoto.mtianyan.cn/blog/180226/9E0L2Ebigl.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180226/AC9AD58e17.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180226/cLa9l5iGfI.png?imageslim)

gird中为跳转到日志管理的url添加page参数

>第七章结束，撒花庆祝。
