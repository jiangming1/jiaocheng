## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

Vue+Django REST framework实战

>使用Python3.6与Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

项目支持支付宝支付(暂不支持微信支付),支持手机短信验证码注册， 支持第三方登录。集成了sentry错误监控系统。

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

## drf的token登录和原理

用户的下单，个人中心等功能都是需要用户登录之后才能进行的。

用户的登录在前后端分离的开发中和我们之前基于模板template进行开发的是有一定区别的。

用户登录的session和cookie等在模板中是有用的。

我们drf的页面中右上角的log in为什么可以实现登录，是因为我们配置了

```
    # 调试登录
    path('api-auth/', include('rest_framework.urls'))
```

它里面有login 和 loginout。里面的login调用了loginview，这个view是django自带的view

```
    @method_decorator(csrf_protect)
```

会验证我们的csrf。我们打开登录的界面f12可以看到一个隐藏的csrf

![mark](http://myphoto.mtianyan.cn/blog/180304/eEA1B6AigC.png?imageslim)

使用csrf进行表单的验证。防止跨站攻击。

前后端分离的系统，不需要做crsf的验证。app和网站服务端本来就是跨站了。

api guide 中的auth

```
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
    )
}
```

所有drf中的全局变量设置都是在上图中的位置设置。

SessionAuthentication实际上是使用了django里面的sessionMiddleware

```
    'django.contrib.sessions.middleware.SessionMiddleware',
	'django.contrib.auth.middleware.AuthenticationMiddleware',
```

每当一个request进来的时候，这两个meddleware就会将我们的cookie里的session_id转换成request.user

rest_framework/authentication.py源码解读:

```
class SessionAuthentication(BaseAuthentication):
    """
    Use Django's session framework for authentication.
    """

    def authenticate(self, request):
        """
        Returns a `User` if the request session currently has a logged in user.
        Otherwise returns `None`.
        """

        # Get the session-based user from the underlying HttpRequest object
        user = getattr(request._request, 'user', None)

        # Unauthenticated, CSRF validation not required
        if not user or not user.is_active:
            return None

        self.enforce_csrf(request)

        # CSRF passed with authenticated user
        return (user, None)
```

![mark](http://myphoto.mtianyan.cn/blog/180304/40eCB41m2h.png?imageslim)

>其中的authenticate就是从我们的request中取出user。实际上还依赖的是我们django自带的session机制。

即使我们不填写，也会默认为这两个默认的。

![mark](http://myphoto.mtianyan.cn/blog/180304/B1cf0IGDml.png?imageslim)

drf为我们提供了三种不同的auth

sessionauth在浏览器中比较常见，它会自动设置cookie，并将session等带到我们的服务器。

前后端分离的系统中这种sessionauth比较少见。

### TokenAuth

```
INSTALLED_APPS = (
    ...
    'rest_framework.authtoken'
)
```

tokenauth会为我们建一张表的，凡是会有表产生的。都必须放到install app中
否则会造成makemigrate报错。

```
makemigrations
migrate
```

![mark](http://myphoto.mtianyan.cn/blog/180304/mlfGi6GFlh.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180304/2C454KefBa.png?imageslim)

这里的user_id实际是一个外键指向user。

步骤2： 为你的用户创建一个token



一个用户在创建的时候就应该拥有一个token，但是现在我们还并没有拥有token。

测试:

```
createsuperuser
```

>并没有为我们创建token

官方代码:

```
from rest_framework.authtoken.models import Token

token = Token.objects.create(user=...)
print token.key
```

实际在用户注册的时候我们就可以调用上面的代码。让它为我们生成一个token



我们必须为我们的token生成配置相关的url

官方文档代码:

```
from rest_framework.authtoken import views
urlpatterns += [
    url(r'^api-token-auth/', views.obtain_auth_token)
]
```

django2.0代码

```python
from rest_framework.authtoken import views
urlpatterns += [
    path('api-token-auth/', views.obtain_auth_token)
]
```

我们获取token只需要向这个接口post数据。

使用postman

![mark](http://myphoto.mtianyan.cn/blog/180304/2cb583Ae4L.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180304/8I2EFjm4D5.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180304/iddl8hB34g.png?imageslim)

可以看到我们的数据库中已经有了一个token.外键指向的用户是1

- 如何使用我们拿到的token

对于客户端进行身份验证，令牌密钥应该包含在AuthorizationHTTP头中。关键字应以字符串文字“Token”为前缀，用空格分隔两个字符串。例如：

```
Authorization: Token 9944b09199c62bcf9418ad846dd0e4bbdfc6ee4b
```

key 与 value

注意：如果您想在标题中使用不同的关键字，比如`Bearer`简单地创建子类TokenAuthentication并设置keyword类变量。

如果成功通过身份验证，则TokenAuthentication提供以下凭据。

request.user将是一个Django User实例。
request.auth将是一个rest_framework.authtoken.models.Token实例。

![mark](http://myphoto.mtianyan.cn/blog/180304/2IbBHF367L.png?imageslim)

拿着数据库里刚刚发给我们的auth token, 请求goods接口。

在goodsviewset继承的ListModelMixin中打上断点。

我的postman不支持与django调试的配合。因此我采用print大法

![mark](http://myphoto.mtianyan.cn/blog/180304/4h0jigjCJL.png?imageslim)

可以看到此时确实无法取到我们想要的用户。

默认我们的auth class中只有基础认证和session认证，这时我们想要使用token认证就要添加token认证


```
        'rest_framework.authentication.TokenAuthentication'
```

这些认证类和middlewire一样会优先调用类里面的auth方法

>这种方法会将user放入我们的request当中去。

这三种会逐一认证，只要通过就会放入request.user中

![mark](http://myphoto.mtianyan.cn/blog/180304/1f44lfBCE5.png?imageslim)

可以看到成功的打印出了当前用户。

request.data中存放着用户从前端post过来的数据。request只会将post和field中的东西放入我们的data中来。

head中设置的token会被放进request.auth中

想要知道用户传递过来的auth时只需要request.auth

django源码中的sessionMiddleware中有一个process_request方法
和一个process_response方法。

django/contrib/sessions/middleware.py:

```
 def process_request(self, request):
        session_key = request.COOKIES.get(settings.SESSION_COOKIE_NAME)
        request.session = self.SessionStore(session_key)

    def process_response(self, request, response):
```

setting中注册的middleware会将用户request的数据经过这些middlware中有process_request方法和process_response方法注册进入。

当用户的request进入view之前会将这些process_request通通调用一遍

如果用户post过来的是session_id那么我们的session middleware就会起作用。

会执行上面代码从request.cookies中获取到setting中设置的SESSION_COOKIE_NAME
这里仅仅是完成了把session放入request。

这里的middleware和scrapy中的middlware是一样的、

http://www.projectsedu.com/2016/10/17/django%E4%BB%8E%E8%AF%B7%E6%B1%82%E5%88%B0%E8%BF%94%E5%9B%9E%E9%83%BD%E7%BB%8F%E5%8E%86%E4%BA%86%E4%BB%80%E4%B9%88/

![mark](http://myphoto.mtianyan.cn/blog/180304/3kL3Cagim5.png?imageslim)

1. 浏览器发起一个http 的请求。http请求会通过python的handler序列化出一个httprequest对象

2. 经过request的middleware(这些middlware就是我们在django的setting中配置的middlware)

3. 只有重载了process_request才叫做，request middlware

这里面我们不一定只加工传过来的request。也可以直接返回。这种情况会直接返回，不会进入view中。

**全局拦截器，拦截用户的浏览器是否是Chrome浏览器**

process_request中直接return response

只要重载了process_response的middlware都会被调用,调用的顺序。

![mark](http://myphoto.mtianyan.cn/blog/180304/3kdGIi2Cg2.png?imageslim)

request的时候顺序是从上往下的，而response的时候是从下至上。

- 如果没有被提前返回。则会去调用url conf里面的配置

- 这个urlconf找到对应的之后，就会被我们的view middlware进行执行。

- viewmiddleware如果没有提前返回。就会调用我们自己view

AuthenticationMiddleware的process_request会判断我们的request中是否有session

就会设置我们的user

```
assert hasattr(request, 'session'),
request.user = SimpleLazyObject(lambda: get_user(request))
```

调用get_user方法，而get_user方法又会调用我们的auth.get_user

```python
def get_user(request):
    if not hasattr(request, '_cached_user'):
        request._cached_user = auth.get_user(request)
    return request._cached_user
```

auth.get_user就会通过session获取到user

```
        user_id = _get_user_session_key(request)
        backend_path = request.session[BACKEND_SESSION_KEY]
```

>session里实际上有很多东西。通过session获取到我们的backend

```
user = backend.get_user(user_id)
```

通过backend的get_user获取到我们的user对象。

```
        if backend_path in settings.AUTHENTICATION_BACKENDS:
```

这里是对于我们的授权backend的判断，说明我们可以在setting中设置我们自己的

drf在setting中设置的auth和我们前面django自带的middlware是不一样的。
django自带的是会对每一个request做一些处理。而drf的auth是来验证用户登录的。

rest_framework/authentication.py中的TokenAuthentication

```
def get_authorization_header(request):
    """
    Return request's 'Authorization:' header, as a bytestring.

    Hide some test client ickyness where the header can be unicode.
    """
    auth = request.META.get('HTTP_AUTHORIZATION', b'')
    if isinstance(auth, text_type):
        # Work around django test client oddness
        auth = auth.encode(HTTP_HEADER_ENCODING)
    return auth
```

会调用get_authorization_header获取到request中的HTTP_AUTHORIZATION取到的值就是后面的value

![mark](http://myphoto.mtianyan.cn/blog/180304/J8cfhkl1hJ.png?imageslim)

如果你后面的auth长度等于1或大于2都是不合法的，会抛出异常401
这是drf为我们处理的。

如果等于2

也就是此时的值为`Token 76c4705854086725ddfa1e83cbf5c2f0413b9458`

他就会通过

```
auth = get_authorization_header(request).split()
```

通过空格进行分割。形成列表。

token = auth[1].decode()

取到后面的值。也就是我们的token值

前面的Token的校验是通过转化为小写。和小写的keyword进行校验的。

```
 if not auth or auth[0].lower() != self.keyword.lower().encode():
```

![mark](http://myphoto.mtianyan.cn/blog/180304/EA1Acfjdah.png?imageslim)

可以经验证我们使用小写的也不会影响。而且这个关键字可以通过比如`Bearer`简单地创建子类TokenAuthentication并设置keyword类变量。覆盖该变量进行实现。

取到了token之后重点就在:

```
return self.authenticate_credentials(token)
```

```python
    def authenticate_credentials(self, key):
        model = self.get_model()
        try:
            token = model.objects.select_related('user').get(key=key)
        except model.DoesNotExist:
            raise exceptions.AuthenticationFailed(_('Invalid token.'))

        if not token.user.is_active:
            raise exceptions.AuthenticationFailed(_('User inactive or deleted.'))

        return (token.user, token)
```

>这个方法里会调用我们的model，

```
    def authenticate_credentials(self, key):
        model = self.get_model()
```

get_model方法会获取到我们的Token 这个model

```
    def get_model(self):
        if self.model is not None:
            return self.model
        from rest_framework.authtoken.models import Token
        return Token
```

Token这个model就是我们对应的数据库中的那张表

```
class Token(models.Model):
    """
    The default authorization token model.
    """
    key = models.CharField(_("Key"), max_length=40, primary_key=True)
    user = models.OneToOneField(
        settings.AUTH_USER_MODEL, related_name='auth_token',
        on_delete=models.CASCADE, verbose_name=_("User")
    )
    created = models.DateTimeField(_("Created"), auto_now_add=True)
```

这里我们就可以重写这个model及Token auth为我们定制出token的过期时间等。

```
    def authenticate_credentials(self, key):
        model = self.get_model()
        try:
            token = model.objects.select_related('user').get(key=key)
        except model.DoesNotExist:
            raise exceptions.AuthenticationFailed(_('Invalid token.'))

        if not token.user.is_active:
            raise exceptions.AuthenticationFailed(_('User inactive or deleted.'))

        return (token.user, token)
```

这里是在通过我们的key(token值)，取Token model中的关联user中获取token.user
获取token对象。

这里有两种异常情况:

```
不存在这个对应的token，或者这个token找到了但是对应的用户未激活。
```

### 那么问题来了，表里的token值是哪里来的呢？

```
    # token授权登录,获取token需要向该地址post数据
    path('api-token-auth/', views.obtain_auth_token)
```

```
obtain_auth_token = ObtainAuthToken.as_view()
```
ObtainAuthTokenview的post方法中的get_or_create会get。没有就create

```
    def post(self, request, *args, **kwargs):
        serializer = self.serializer_class(data=request.data,
                                           context={'request': request})
        serializer.is_valid(raise_exception=True)
        user = serializer.validated_data['user']
        token, created = Token.objects.get_or_create(user=user)
        return Response({'token': token.key})
```

>刚才我们登录的时候，它就自动创建了。

drf的token是保存在我们的服务器数据库当中，但是我们如果是一个分布式的系统，
两套系统想用一个token的话就会出现问题。分布式就得做用户同步。

- 第二个非常严重的问题，这个token没有过期时间。永久有效。

这两个问题我们下一节课就来结尾。JWT解决

### viewsets 配置认证类

我们在setting中配置了一个全局的token auth认证类。这个auth类会对token进行验证
如果验证失败会抛出上面源码中的两种异常

直接访问goods列表页。会返回401的错误。！！！这是一个可以公开的页面

用户登录失败或者token过期等各种情况也应该可以获取到的。

解决方案1: 访问goods列表页不带token。

很多前端写的时候是会全局加token的。

后端解决方案:

将token认证拿到view中来做。

>不配置全局的，配置某个具体的viewset的。

删除setting中的auth token配置。

并在列表页中添加单独的auth 认证

```
    # 设置列表页的单独auth认证
    authentication_classes = (TokenAuthentication,)
```

验证此时list之中能不能得到user

![mark](http://myphoto.mtianyan.cn/blog/180304/3fJm898bFm.png?imageslim)

可以看到依然可以拿到user

因为商品列表页是一个公开的。所以不能配置这个。务必注释掉
