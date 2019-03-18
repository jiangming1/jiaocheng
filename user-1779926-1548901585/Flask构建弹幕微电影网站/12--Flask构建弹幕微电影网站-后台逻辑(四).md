## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

已上线演示地址: http://movie.mtianyan.cn
项目源码地址：https://github.com/mtianyan/movie_project

## 会员管理

1. 模型: USer
2. 表单: 无
3. 请求方法: GET POST
4. 访问控制: `@admin_login_req`

插入一些我们提前设定的用户数据

```
/*会员*/
insert into user(name,pwd,email,phone,info,face,uuid,addtime) values('鼠','1231','1231@123.com','13888888881','鼠','1f401.png','d32a72bdac524478b7e4f6dfc8394fc0',now());
insert into user(name,pwd,email,phone,info,face,uuid,addtime) values('牛','1232','1232@123.com','13888888882','牛','1f402.png','d32a72bdac524478b7e4f6dfc8394fc1',now());
insert into user(name,pwd,email,phone,info,face,uuid,addtime) values('虎','1233','1233@123.com','13888888883','虎','1f405.png','d32a72bdac524478b7e4f6dfc8394fc2',now());
insert into user(name,pwd,email,phone,info,face,uuid,addtime) values('兔','1234','1234@123.com','13888888884','兔','1f407.png','d32a72bdac524478b7e4f6dfc8394fc3',now());
insert into user(name,pwd,email,phone,info,face,uuid,addtime) values('龙','1235','1235@123.com','13888888885','龙','1f409.png','d32a72bdac524478b7e4f6dfc8394fc4',now());
insert into user(name,pwd,email,phone,info,face,uuid,addtime) values('蛇','1236','1236@123.com','13888888886','蛇','1f40d.png','d32a72bdac524478b7e4f6dfc8394fc5',now());
insert into user(name,pwd,email,phone,info,face,uuid,addtime) values('马','1237','1237@123.com','13888888887','马','1f434.png','d32a72bdac524478b7e4f6dfc8394fc6',now());
insert into user(name,pwd,email,phone,info,face,uuid,addtime) values('羊','1238','1238@123.com','13888888888','羊','1f411.png','d32a72bdac524478b7e4f6dfc8394fc7',now());
insert into user(name,pwd,email,phone,info,face,uuid,addtime) values('猴','1239','1239@123.com','13888888889','猴','1f412.png','d32a72bdac524478b7e4f6dfc8394fc8',now());
insert into user(name,pwd,email,phone,info,face,uuid,addtime) values('鸡','1240','1240@123.com','13888888891','鸡','1f413.png','d32a72bdac524478b7e4f6dfc8394fc9',now());
insert into user(name,pwd,email,phone,info,face,uuid,addtime) values('狗','1241','1241@123.com','13888888892','狗','1f415.png','d32a72bdac524478b7e4f6dfc8394fd0',now());
insert into user(name,pwd,email,phone,info,face,uuid,addtime) values('猪','1242','1242@123.com','13888888893','猪','1f416.png','d32a72bdac524478b7e4f6dfc8394fd1',now());
```
![mark](http://myphoto.mtianyan.cn/blog/180225/KmJIBJBkkA.png?imageslim)

将素材中的头像拷贝到uploads中

![mark](http://myphoto.mtianyan.cn/blog/180225/J6GDlK5jb2.png?imageslim)

views中进行会员列表的分页:

```python
@admin.route("/user/list/<int:page>/", methods=["GET"])
@admin_login_req
def user_list(page =None):
    """
    会员列表
    """
    if page is None:
        page = 1
    page_data = User.query.order_by(
        User.addtime.desc()
    ).paginate(page=page, per_page=1)
    return render_template("admin/user_list.html", page_data=page_data)
```

此时前往user_list中进行pagedata的填充以及引入pg 然后调用page方法

![mark](http://myphoto.mtianyan.cn/blog/180225/kELAlAg41g.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180225/EL2F6d4m2j.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180225/fIC63DgF2F.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180225/AJLi0Fcgbb.png?imageslim)

为查看添加id值。

```
@admin.route("/user/view/<int:id>/", methods=["GET"])
@admin_login_req
def user_view(id=None):
    """
    查看会员详情
    """
    return render_template("admin/user_view.html")
```

报错:

```
werkzeug.routing.BuildError
werkzeug.routing.BuildError: Could not build url for endpoint 'admin.user_list'. Did you forget to specify values ['page']?
```

啊啊啊啊啊。一看错误就是提示我们忘记了设置特别的value。前往grid中设置

![mark](http://myphoto.mtianyan.cn/blog/180225/02aLl63Fgj.png?imageslim)

### 定义查看和删除的功能

```python
@admin.route("/user/view/<int:id>/", methods=["GET"])
@admin_login_req
def user_view(id=None):
    """
    查看会员详情
    """
    from_page = request.args.get('fp')
    # 兼容不加参数的无来源页面访问。
    if not from_page:
        from_page = 1
    user = User.query.get_or_404(int(id))
    return render_template("admin/user_view.html", user=user, from_page=from_page)
```

通过这个frompage参数实现回到原来的page

为userlist中点进详情的按钮添加额外参数fp使其传递到user_view中:

![mark](http://myphoto.mtianyan.cn/blog/180225/4f8cbFGf78.png?imageslim)

userview中添加参数page回到列表页。

![mark](http://myphoto.mtianyan.cn/blog/180225/ikGGdHeII6.png?imageslim)

前往user_view中将字段改为我们当前user的值

![mark](http://myphoto.mtianyan.cn/blog/180225/455bICI2F9.png?imageslim)


>至此就可以实现返回刚才查看的页面

### 实现删除用户功能

```python
@admin.route("/user/del/<int:id>/", methods=["GET"])
@admin_login_req
def user_del(id=None):
    """
    删除会员
    """
    # 因为删除当前页。假如是最后一页，这一页已经不见了。回不到。
    from_page = int(request.args.get('fp')) -1
    # 此处考虑全删完了，没法前挪的情况，0被视为false
    if not from_page:
        from_page = 1
    user = User.query.get_or_404(int(id))
    db.session.delete(user)
    db.session.commit()
    flash("删除会员成功！", "ok")
    return redirect(url_for('admin.user_list', page=from_page))
```

将list中指向删除的url进行修改

![mark](http://myphoto.mtianyan.cn/blog/180225/2bk40DGgLJ.png?imageslim)

删除了某一个，不要返回第一页而是返回临近的页。

添加删除成功的提示信息

![mark](http://myphoto.mtianyan.cn/blog/180225/fFGlJCg53I.png?imageslim)

## 评论管理

1. 模型: comment
2. 表单: 无
3. 请求方法: GET
4. 访问控制: `@admin_login_req`

![mark](http://myphoto.mtianyan.cn/blog/180225/Cd5CaelBck.png?imageslim)

插入评论数据之前，先确认自己的电影表中有7,8号电影。以及用户表中有1-8号用户。
这样才不会插入失败。

```
/*评论*/
insert into comment(movie_id,user_id,content,addtime) values(7,1,"好看",now());
insert into comment(movie_id,user_id,content,addtime) values(7,2,"不错",now());
insert into comment(movie_id,user_id,content,addtime) values(7,3,"经典",now());
insert into comment(movie_id,user_id,content,addtime) values(7,4,"给力",now());
insert into comment(movie_id,user_id,content,addtime) values(8,5,"难看",now());
insert into comment(movie_id,user_id,content,addtime) values(8,6,"无聊",now());
insert into comment(movie_id,user_id,content,addtime) values(8,7,"乏味",now());
insert into comment(movie_id,user_id,content,addtime) values(8,8,"无感",now());
```

修改路由视图中的`comment_list`

```
@admin.route("/comment/list/<int:page>/", methods=["GET"])
@admin_login_req
def comment_list(page=None):
    """
    评论列表
    """
    if page is None:
        page = 1
    # 通过评论join查询其相关的movie，和相关的用户。
    # 然后过滤出其中电影id等于评论电影id的电影，和用户id等于评论用户id的用户
    page_data = Comment.query.join(
        Movie
    ).join(
        User
    ).filter(
        Movie.id == Comment.movie_id,
        User.id == Comment.user_id
    ).order_by(
        Comment.addtime.desc()
    ).paginate(page=page, per_page=1)
    return render_template("admin/comment_list.html", page_data=page_data)
```

在grid.html中修改指向评论列表的url 为其添加page=1

![mark](http://myphoto.mtianyan.cn/blog/180225/J0eGD9I839.png?imageslim)

前往comment_list中填充page_data 以及进行pg的import 与调用page方法

![mark](http://myphoto.mtianyan.cn/blog/180225/57ghad82Jh.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180225/D3mIJJ1IjD.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180225/EFmgKa7Da7.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180225/DgJ5bFmFJ5.png?imageslim)

>上图为添加flash展示位。

### 删除功能实现

```python
@admin.route("/comment/del/<int:id>/", methods=["GET"])
@admin_login_req
def comment_del(id=None):
    """
    删除评论
    """
    # 因为删除当前页。假如是最后一页，这一页已经不见了。回不到。
    from_page = int(request.args.get('fp')) - 1
    # 此处考虑全删完了，没法前挪的情况，0被视为false
    if not from_page:
        from_page = 1
    comment = Comment.query.get_or_404(int(id))
    db.session.delete(comment)
    db.session.commit()
    flash("删除评论成功！", "ok")
    return redirect(url_for('admin.comment_list', page=from_page))
```

删除评论的flash消息已经在上一节中得到了接收。

添加list中删除按钮跳转的url链接

![mark](http://myphoto.mtianyan.cn/blog/180225/12C85491HD.png?imageslim)
