## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

## 上映预告

1. 模型: Preview
2. 表单: 无
3. 请求方法: GET
4. 访问控制: 无

views中进行业务逻辑的实现

```python
@home.route("/animation/")
def animation():
    """
    首页轮播动画
    """
    data = Preview.query.all()
    return render_template("home/animation.html", data=data)
```

## 标签筛选 - 电影分页

1. 模型: Tag Movie
2. 表单: 无
3. 请求方法: GET
4. 访问控制: 无

编写首页的views

```python
@home.route("/<int:page>/", methods=["GET"])
@home.route("/", methods=["GET"])
def index(page=None):
    """
    首页电影列表
    """
    tags = Tag.query.all()
    page_data = Movie.query
    # 标签
    tid = request.args.get("tid", 0)
    if int(tid) != 0:
        page_data = page_data.filter_by(tag_id=int(tid))
    # 星级
    star = request.args.get("star", 0)
    if int(star) != 0:
        page_data = page_data.filter_by(star=int(star))
    # 时间
    time = request.args.get("time", 0)
    if int(time) != 0:
        if int(time) == 1:
            page_data = page_data.order_by(
                Movie.addtime.desc()
            )
        else:
            page_data = page_data.order_by(
                Movie.addtime.asc()
            )
    # 播放量
    pm = request.args.get("pm", 0)
    if int(pm) != 0:
        if int(pm) == 1:
            page_data = page_data.order_by(
                Movie.playnum.desc()
            )
        else:
            page_data = page_data.order_by(
                Movie.playnum.asc()
            )
    # 评论量
    cm = request.args.get("cm", 0)
    if int(cm) != 0:
        if int(cm) == 1:
            page_data = page_data.order_by(
                Movie.commentnum.desc()
            )
        else:
            page_data = page_data.order_by(
                Movie.commentnum.asc()
            )
    if page is None:
        page = 1
    page_data = page_data.paginate(page=page, per_page=8)
    p = dict(
        tid=tid,
        star=star,
        time=time,
        pm=pm,
        cm=cm,
    )
    return render_template(
        "home/index.html",
        tags=tags,
        p=p,
        page_data=page_data)
```

>电影标签，电影星级，上映时间，播放数量，评论数量

- `tid = request.args.get("tid", 0)` 获取到问号传参中的`tid`没有获取到时置零
- 同理获取star`star = request.args.get("star", 0)`
- `time = request.args.get("time", 0)`同理获取到time
- 其他同理pm cm 这里的数量排序通过前台传过来是0还是1决定数量正序或倒序

>碎碎念杂谈，将用户已选择的分类细节展示给用户，可以点击x去除。

![mark](http://myphoto.mtianyan.cn/blog/180301/lHEIDbdFj6.png?imageslim)

>可以使用jquery实现，清除组合的时候移除dom节点，显示的时候添加dom节点，然后在添加和清除的时候注意参数的添加和删除，组装成url，通过触发事件触发搜素！

查询出所有的tag，然后经模板渲染出菜单。

```python
tags = Tag.query.all()
```

传递到模板进行填充。

![mark](http://myphoto.mtianyan.cn/blog/180301/5H3d6hJL1j.png?imageslim)

```
p = dict(
        tid=tid,
        star=star,
        time=time,
        pm=pm,
        cm=cm,
    )
```

构建一个参数的字典。

![mark](http://myphoto.mtianyan.cn/blog/180301/AA3a308D7J.png?imageslim)

处理标签的链接

```
 href="{{ url_for('home.index') }}?tid={{ v.id }}&star={{ p['star'] }}&time={{ p['time'] }}&pm={{ p['pm'] }}&cm={{ p['cm'] }}" 
```

上面为标签添加href值。其中的tid是取自当前循环

```
 href="{{ url_for('home.index') }}?tid={{ p['tid'] }}&star={{ v.star }}&time={{ p['time'] }}&pm={{ p['pm'] }}&cm={{ p['cm'] }}" 
```
上面为star添加href值。其中的star是取自当前循环

其他项同理可得，将当前值取实际值，其他值取p['']

### 分页电影

```
    if int(tid) != 0:
        page_data = page_data.filter_by(tag_id=int(tid))
```

其他字段做相同的过滤操作。

进行分页效果展示。

![mark](http://myphoto.mtianyan.cn/blog/180301/gaAgH8B6Fi.png?imageslim)

为所有的标签分类添加page=1

![mark](http://myphoto.mtianyan.cn/blog/180301/9C0Dfi4Lh2.png?imageslim)

## 电影搜索，搜索分页

1. 模型: Movie
2. 表单: 无
3. 请求方法: GET
4. 访问控制: 无

views中书写search相关的逻辑处理

```python
@home.route("/search/<int:page>/")
def search(page=None):
    """
    搜索
    """
    if page is None:
        page = 1
    key = request.args.get("key", "")
    movie_count = Movie.query.filter(
        Movie.title.ilike('%' + key + '%')
    ).count()
    page_data = Movie.query.filter(
        Movie.title.ilike('%' + key + '%')
    ).order_by(
        Movie.addtime.desc()
    ).paginate(page=page, per_page=10)
    page_data.key = key
    return render_template("home/search.html", movie_count=movie_count, key=key, page_data=page_data)
```

![mark](http://myphoto.mtianyan.cn/blog/180301/KDF49ebDC4.png?imageslim)

为layout中的搜索框添加id

添加id = do_search

![mark](http://myphoto.mtianyan.cn/blog/180301/JmLHbCff8I.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180301/724H6fgdfI.png?imageslim)

home页面中script标签中使用jQuery获取搜索框内key值。然后location跳转到search路由，并携带page key 参数

将这段代码再粘贴到其他需要搜索的页面

- layout home 都需要修改id

`ilike`进行模糊匹配

在search页面进行最终结果的填充与分页。

又是这种重复的填充操作，自己瞎做吧。跟以前没什么两样。

新建一个s_page.html，把home_page复制过去就行。

然后依然万年不变import 调用page方法。

碎碎念积累,回车直接搜索:

```
$("...").keydown(function(e){
if(e.keyCode==13){
    //处理事件
}
});
```

## 电影详情 电影播放 电影评论 电影收藏

1. 模型: Movie
2. 表单: 无
3. 请求方法: GET
4. 访问控制: 无

评论统计:

1. 模型: Movie User Comment
2. 表单: CommentForm
3. 请求方法: GET POST
4. 访问控制: 需登录

![mark](http://myphoto.mtianyan.cn/blog/180301/JmELB89fej.png?imageslim)

如果当前未登录则显示请先登录才可以提交。

![mark](http://myphoto.mtianyan.cn/blog/180301/Kce1ehKcA7.png?imageslim)

只有用户登录状态才会显示评论框。

forms中添加commentsform

```python
class CommentForm(FlaskForm):
    content = TextAreaField(
        label="内容",
        validators=[
            DataRequired("请输入内容！"),
        ],
        description="内容",
        render_kw={
            "id": "input_content"
        }
    )
    submit = SubmitField(
        '提交评论',
        render_kw={
            "class": "btn btn-success",
            "id": "btn-sub"
        }
    )
```

>注意内容的id input_content

views中处理播放页面的数据业务逻辑，以及评论的业务逻辑

```python
@home.route("/play/<int:id>/<int:page>/", methods=["GET", "POST"])
def play(id=None, page=None):
    """
    播放电影
    """
    # 查询出相关联的标签
    movie = Movie.query.join(Tag).filter(
        Tag.id == Movie.tag_id,
        Movie.id == int(id)
    ).first_or_404()

    if page is None:
        page = 1
    page_data = Comment.query.join(
        Movie
    ).join(
        User
    ).filter(
        Movie.id == movie.id,
        User.id == Comment.user_id
    ).order_by(
        Comment.addtime.desc()
    ).paginate(page=page, per_page=10)

    movie.playnum = movie.playnum + 1
    form = CommentForm()
    # 必须登录
    if "user" in session and form.validate_on_submit():
        data = form.data
        comment = Comment(
            content=data["content"],
            movie_id=movie.id,
            user_id=session["user_id"]
        )
        db.session.add(comment)
        db.session.commit()
        movie.commentnum = movie.commentnum + 1
        db.session.add(movie)
        db.session.commit()
        flash("添加评论成功！", "ok")
        return redirect(url_for('home.play', id=movie.id, page=1))
    db.session.add(movie)
    db.session.commit()
    return render_template("home/play.html", movie=movie, form=form, page_data=page_data)
```

index以及search页面中的播放按钮，都添加上v.id 以及page

前往play中填充movie的具体数据。使用form填充评论框，使用pagedata填充已有评论

- 当然flash的位置，报错信息。submit之前的csrf都不要忘记，还有form的method

![mark](http://myphoto.mtianyan.cn/blog/180301/LlckAd5Cc6.png?imageslim)

>修改js中需要修改的player file title 等

为评论分页新建一个html `ui/comment_page.html`复制s_page的内容

>为commentspage添加id参数。url中也添加id

然后又是重复的引入和page方法调用。pagedata填充

为了安全，默认不会显示html的内容，添加safe

![mark](http://myphoto.mtianyan.cn/blog/180301/ff56Ec7jJF.png?imageslim)

### 查询出自己的评论记录显示在自己个人中心

```python
@home.route("/comments/<int:page>/")
@user_login_req
def comments(page=None):
    """
    个人中心评论记录
    """
    if page is None:
        page = 1
    page_data = Comment.query.join(
        Movie
    ).join(
        User
    ).filter(
        Movie.id == Comment.movie_id,
        User.id == session["user_id"]
    ).order_by(
        Comment.addtime.desc()
    ).paginate(page=page, per_page=10)
    return render_template("home/comments.html", page_data=page_data)
```

将comments.html进行pagedata填充以及分页处理。

menu中评论记录url添加page=1

![mark](http://myphoto.mtianyan.cn/blog/180301/e5GCmheC6A.png?imageslim)

### 收藏电影

1. 模型: Movie User Moviecol
2. 表单: 无
3. 请求方法: GET
4. 访问控制: 需登录

通过ajax异步方法添加电影收藏。

![mark](http://myphoto.mtianyan.cn/blog/180301/H53dIglLAk.png?imageslim)

ajax的提示信息无处安放，添加上图id 为show_col_msg

play页面中有一个按钮是收藏电影，id为btn-col

![mark](http://myphoto.mtianyan.cn/blog/180301/g5mI27CEfG.png?imageslim)

>为btn-col 设置点击事件，获取当前的movieid，获取当前的用户。
使用ajax，提交的地址。type:get,提交的数据，返回的数据类型json。
成功之后的处理函数res接收返回的json对象


```python
@home.route("/moviecol/add/", methods=["GET"])
@user_login_req
def moviecol_add():
    """
    添加电影收藏
    """
    uid = request.args.get("uid", "")
    mid = request.args.get("mid", "")
    moviecol = Moviecol.query.filter_by(
        user_id=int(uid),
        movie_id=int(mid)
    ).count()
    # 已收藏
    if moviecol == 1:
        data = dict(ok=0)
    # 未收藏进行收藏
    if moviecol == 0:
        moviecol = Moviecol(
            user_id=int(uid),
            movie_id=int(mid)
        )
        db.session.add(moviecol)
        db.session.commit()
        data = dict(ok=1)
    import json
    return json.dumps(data)
```



处理个人中心中电影收藏的view

```python
@home.route("/moviecol/<int:page>/")
@user_login_req
def moviecol(page=None):
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
        User.id == session["user_id"]
    ).order_by(
        Moviecol.addtime.desc()
    ).paginate(page=page, per_page=10)
    return render_template("home/moviecol.html", page_data=page_data)
```

>page_data进行填充，进行分页

menus中将收藏电影

![mark](http://myphoto.mtianyan.cn/blog/180301/6I7LbE33c4.png?imageslim)


![mark](http://myphoto.mtianyan.cn/blog/180301/7liHm11cJK.png?imageslim)

修改这里配置菜单显示哪些按钮。

- 换不存在的账号登录出现报错

```
builtins.AttributeError
AttributeError: 'NoneType' object has no attribute 'check_pwd'
```

解决方案:

```python
        user = User.query.filter_by(name=data["name"]).first()
        if user:
            if not user.check_pwd(data["pwd"]):
                flash("密码错误！", "err")
                return redirect(url_for("home.login"))
        else:
            flash("账户不存在！", "err")
            return redirect(url_for("home.login"))
```

## FLask 结合Redis消息队列实现电影弹幕

1. 模型: Movie
2. 表单: 无
3. 请求方法: GET POST
4. 访问控制: 无
5. 消息队列: Redis
6. Flask第三方扩展: Flask-Redis
7. 弹幕播放器插件: dplayer.js(开源)

redis自行前往官网下载最新稳定版本

```
pip install flask-redis
```

前面的版本使用的是jwplayer

```
yum -y install gcc gcc-c++
tar zxf redis-4.0.1.tar.gz
cd redis-4.0.1/
```

>可以看到当前目录有Makefile文件，安装。

```
make && make install
```

启动服务

```
./utils/install_server.sh
vim /etc/redis/6379.conf
```

修改bind的地址

redis-cli -h 192.x.x.x

弹幕播放器views实现

```python
@home.route("/video/<int:id>/<int:page>/", methods=["GET", "POST"])
def video(id=None, page=None):
    """
    弹幕播放器
    """
    movie = Movie.query.join(Tag).filter(
        Tag.id == Movie.tag_id,
        Movie.id == int(id)
    ).first_or_404()

    if page is None:
        page = 1
    page_data = Comment.query.join(
        Movie
    ).join(
        User
    ).filter(
        Movie.id == movie.id,
        User.id == Comment.user_id
    ).order_by(
        Comment.addtime.desc()
    ).paginate(page=page, per_page=10)

    movie.playnum = movie.playnum + 1
    form = CommentForm()
    if "user" in session and form.validate_on_submit():
        data = form.data
        comment = Comment(
            content=data["content"],
            movie_id=movie.id,
            user_id=session["user_id"]
        )
        db.session.add(comment)
        db.session.commit()
        movie.commentnum = movie.commentnum + 1
        db.session.add(movie)
        db.session.commit()
        flash("添加评论成功！", "ok")
        return redirect(url_for('home.video', id=movie.id, page=1))
    db.session.add(movie)
    db.session.commit()
    return render_template("home/video.html", movie=movie, form=form, page_data=page_data)
```

templates目录创建一个video.html把原本play中的代码粘贴过来

将原本的jwplayer部分的script可以直接全部去掉了

![mark](http://myphoto.mtianyan.cn/blog/180301/B7L928gD5c.png?imageslim)

将评论这里的分页中视图改为video

将jwplayer的css去掉

进入播放需要的静态资源

```
<link rel="stylesheet" href="{{ url_for('static',filename='dplayer/dist/DPlayer.min.css') }}">
<script src="{{ url_for('static',filename='dplayer/plugin/flv.min.js') }}"></script>
<script src="{{ url_for('static',filename='dplayer/plugin/hls.min.js') }}"></script>
<script src="{{ url_for('static',filename='dplayer/dist/DPlayer.min.js') }}"></script>
```

修改样式

```
    .dplayer-comment-setting-type>label{
    display: inline;
```

定义播放器的样式

```
        <div id="dplayer1" style="height:500px;width: 774px;"></div>
```

播放页面

```
<script>
    var dp1 = new DPlayer({
        element: document.getElementById('dplayer1'),
        video: {
            url: "{{ url_for('static',filename='uploads/'+movie.url) }}",
        },
        danmaku: {
            id: '{{ movie.id }}',
            api: "/tm/"
        }
    });
</script>
```

在官方网站的demo上开发者工具xhr可以看到弹幕发送及加载数据的接口。

发送的格式是json的字符串。

定义弹幕相关的处理方法

init文件中

```
from flask_redis import FlaskRedis
```

定义我们redis的url格式

```
app.config["REDIS_URL"] = "redis://127.0.0.1:6379/0"
rd = FlaskRedis(app)
```

views.py中引入

```
@home.route("/tm/", methods=["GET", "POST"])
def tm():
    """
    弹幕消息处理
    """
    import json
    if request.method == "GET":
        # 获取弹幕消息队列
        id = request.args.get('id')
        # 存放在redis队列中的键值
        key = "movie" + str(id)
        if rd.llen(key):
            msgs = rd.lrange(key, 0, 2999)
            res = {
                "code": 1,
                "danmaku": [json.loads(v) for v in msgs]
            }
        else:
            res = {
                "code": 1,
                "danmaku": []
            }
        resp = json.dumps(res)
    if request.method == "POST":
        # 添加弹幕
        data = json.loads(request.get_data())
        msg = {
            "__v": 0,
            "author": data["author"],
            "time": data["time"],
            "text": data["text"],
            "color": data["color"],
            "type": data['type'],
            "ip": request.remote_addr,
            "_id": datetime.datetime.now().strftime("%Y%m%d%H%M%S") + uuid.uuid4().hex,
            "player": [
                data["player"]
            ]
        }
        res = {
            "code": 1,
            "data": msg
        }
        resp = json.dumps(res)
        # 将添加的弹幕推入redis的队列中
        rd.lpush("movie" + str(data["player"]), json.dumps(msg))
    return Response(resp, mimetype='application/json')
```

原理:

![mark](http://myphoto.mtianyan.cn/blog/180301/c52KDL255k.png?imageslim)


## 代码优化及bug处理

1. 头像判断
2. 关键字搜索分页
3. 电影右侧播放页面滚动条

```
builtins.TypeError
TypeError: must be str, not NoneType
```

![mark](http://myphoto.mtianyan.cn/blog/180301/g4Da9gjGBA.png?imageslim)

如果有头像，显示头像，没有头像就显示holder的空头像。

评论记录中的头像部分做相同处理

后台的会员列表也做同样的处理

![mark](http://myphoto.mtianyan.cn/blog/180301/C2c9eh4dA9.png?imageslim)

后台的评论列表也做同样的处理

![mark](http://myphoto.mtianyan.cn/blog/180301/C2HcedDm77.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180301/GCJlk6AB6C.png?imageslim)

会员详情中的会员头像也要判断templates/admin/user_view.html

![mark](http://myphoto.mtianyan.cn/blog/180301/i3ldDJ01BJ.png?imageslim)

### 关键字搜索分页

搜索分页中当前key会丢失掉

![mark](http://myphoto.mtianyan.cn/blog/180301/AA2ll90Kj3.png?imageslim)

spage中的key要传递进去。

### 当影片内容过长设置滚动条

![mark](http://myphoto.mtianyan.cn/blog/180301/3HlGkE2JCf.png?imageslim)

play 和 video中都做同样处理
