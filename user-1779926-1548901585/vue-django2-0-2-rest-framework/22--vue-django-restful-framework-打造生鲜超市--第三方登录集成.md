## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节: 第三方登录开发集成

## 第三方登录开发模式以及Oauth2.0简介

我们要接入微博登录。我们的页面肯定 要跳转到微博登录页面。

都要跳转到对应平台的页面，涉及一个页面的回调。跟支付宝的支付模式有点像

![mark](http://myphoto.mtianyan.cn/blog/180314/47k6ii9aAj.png?imageslim)

理解第三方登录的流程:

- 用户向本地应用商城发起请求，我要用微博进行登录。
- 我们的商城凑一个url让用户跳转到第三方应用的url(微博的登录页面)
- 用户在该界面点击输入用户名密码之后，点击授权。
- 微博有个回调url指向我们的应用。
- 我们的应用就可以获取到用户的基本信息等。

oauth2.0认证。

微信开放平台 & 微博开放平台 & qq开放平台

>微信开放平台而不是微信公众平台

- http://open.weibo.com/wp/intro

点击新建网页应用。上线之前必须审核才能让第三方用户登录

系统没开发完，没法审核。如何登录。测试模式。

![mark](http://myphoto.mtianyan.cn/blog/180314/JableF3FEG.png?imageslim)


应用信息里的高级信息里面有回调地址(回调地址我们自己设置)

可以添加一些测试用户，也就是即使应用没上线，这些用户也是可以登录的。

![mark](http://myphoto.mtianyan.cn/blog/180314/8F2e65AaF4.png?imageslim)

高级信息里面的回调必须我们自己来设置。要在应用里面设置好。

app的这两个值比较重要。

```
App Key：
App Secret：
```

参考文档中的认证模式

http://open.weibo.com/wiki/%E6%8E%88%E6%9D%83%E6%9C%BA%E5%88%B6%E8%AF%B4%E6%98%8E

![mark](http://myphoto.mtianyan.cn/blog/180314/g7g258b2lF.png?imageslim)

- 我们的商城第一步会发起auth request。(resource owner)用户会向微博的服务器请求一个url(auth grant)

- 进入微博登录的页面，在这里面输入用户名和密码。完成auth grant

- 微博会向我们的商城服务器发一个url。会带一些参数，我们的会拿着去auth server进行请求。拿到access token基本已经完成用户授权 

- 后面的是获取用户的个人信息才会用到的；

接口文档。

这两个比较重要:

- OAuth2/authorize	请求用户授权Token
- OAuth2/access_token	获取授权过的Access Token

## oauth2.0获取微博的access_token

http://open.weibo.com/wiki/Oauth2/authorize

这个接口是我们凑出一个url来提供给用户进行访问。

utils中新建一个weibo_login.py

http://open.weibo.com/wiki/Oauth2/authorize

![mark](http://myphoto.mtianyan.cn/blog/180315/58LmFek1a8.png?imageslim)

```
http://115.159.122.64:8000/complete/weibo/?code=160ceb0ea32daba321c49b7eb30c1427
```

在我们的回调url后面会添加一个code。

http://open.weibo.com/wiki/Oauth2/access_token

这个api才是我们真正获取到登录令牌的api

我们要拿着这个code再请求一次，才能获取到我们的access_token

![mark](http://myphoto.mtianyan.cn/blog/180315/6GDJ4i2diG.png?imageslim)

可以看到我们获取的access_token

http://open.weibo.com/wiki/2/users/show

>我们试着拿着access_token获取用户基本信息。

![mark](http://myphoto.mtianyan.cn/blog/180315/F1l3K6g8G8.png?imageslim)

可以看到可以成功获取到

```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/3/15 0015 14:46'


def get_auth_url():
    """
    client_id	必填	string	申请应用时分配的AppKey。
    redirect_uri	必填	string	授权回调地址，站外应用需与设置的回调地址一致。
    """
    weibo_auth_url = "https://api.weibo.com/oauth2/authorize"
    redirect_uri = "http://remote_ip:8000/complete/weibo/"
    client_id = ""
    auth_url = weibo_auth_url+"?client_id={client_id}&redirect_uri={re_url}".format(client_id=client_id,re_url=redirect_uri)

    print(auth_url)


def get_access_token(code):
    access_token_url = "https://api.weibo.com/oauth2/access_token"
    import requests
    re_dict = requests.post(access_token_url, data={
        "client_id": "",
        "client_secret": "",
        "grant_type": "authorization_code",
        "code": code,
        "redirect_uri": "http://remote_ip:8000/complete/weibo/",

    })
    pass

def get_user_info(access_token):
    user_url = "https://api.weibo.com/2/users/show.json"
    uid = ""
    get_url = user_url+"?access_token={at}&uid={uid}".format(at=access_token,uid=uid)
    print(get_url)

if __name__ == "__main__":
    # get_auth_url()
    # get_access_token("")
    get_user_info("")
```

## social_django 集成第三方登录

最终对应我们系统中的用户，新建用户以及绑定用户。

我们要用的第三方登录。 

https://github.com/python-social-auth/social-app-django

首先安装

```
pip install social-auth-app-django
```

文档中

http://python-social-auth.readthedocs.io/en/latest/configuration/django.html

install app 中加入

```
 'social_django',
```

生成数据表

只需要做migrate，因为migration的文件已经生成好了

```
migrate
```

出现warning不需要在意。记得mysql要使用innodb。

auth_backend加入。

```python
# 设置邮箱和用户名和手机号均可登录
AUTHENTICATION_BACKENDS = (
    'users.views.CustomBackend',
    'django.contrib.auth.backends.ModelBackend',
)

```

剩下的backend根据需要添加

mxshop36/Lib/site-packages/social_core

可以在social_core中看到已经支持的平台。

weibo weixin qq

```python
# 设置邮箱和用户名和手机号均可登录
AUTHENTICATION_BACKENDS = (
    'users.views.CustomBackend',
    'social_core.backends.weibo.WeiboOAuth2',
    'social_core.backends.qq.QQOAuth2',
    'social_core.backends.weixin.WeixinOAuth2',
    'django.contrib.auth.backends.ModelBackend',
)
```

微信有两种:oauth2 和 oauth2app，我们使用auth2

添加url支持

```
    # 第三方登录
    path('', include('social_django.urls', namespace='social'))
```

这个url里面有两个一个login 一个complete

![mark](http://myphoto.mtianyan.cn/blog/180314/Ffc39iCe6E.png?imageslim)

这里面的backend和我们的auth设置几乎是一样的，如果是微博就填weibo

转发到这个函数来处理。

login和我们的get_auth_url干的是同一件事

点击成功之后会帮我们跳转到complete的地址。`get_access_token`类似

>还会自动完成后续工作登录用户自动绑定，没有用户就新建用户。

对于template中的用法

向context_processors中加入

```
                'social_django.context_processors.backends',
                'social_django.context_processors.login_redirect',
```
You can add (or remove) several features on the social auth pipeline.

http://python-social-auth.readthedocs.io/en/latest/configuration/django.html

按pipeline的顺序执行。定制我们的pipeline，下次自动识别默认登录的用户。

>pipeline是用户拿到token之后会依次干什么。

微博的这个回调时浏览器重定向来完成的。支付宝post请求是支付宝向我们的服务器发起请求。

### 一些注意事项

- 2.0版本中不会出现这种问题，但是在1.x版本中我们应该将

```
    # jwt的token认证
    path('login/', obtain_jwt_token),
```

后面加上$符号，表示结尾。

否则会将后面的被它抢先匹配到。

```
url(r'^login/(?P<backend>[^/]+){0}$'.format(extra), views.auth,
```

settings中配置我们的key secert

```
# 第三方登录相关
SOCIAL_AUTH_WEIBO_KEY = 'foobar'
SOCIAL_AUTH_WEIBO_SECRET = 'bazqux'

SOCIAL_AUTH_QQ_KEY = 'foobar'
SOCIAL_AUTH_QQ_SECRET = 'bazqux'

SOCIAL_AUTH_WEIXIN_KEY = 'foobar'
SOCIAL_AUTH_WEIXIN_SECRET = 'bazqux'
```

https://open.weixin.qq.com/

配置用户授权之后重定向跳转的url

```
SOCIAL_AUTH_LOGIN_REDIRECT_URL = '/index/'
```

![mark](http://myphoto.mtianyan.cn/blog/180315/FG5A9e7HE6.png?imageslim)

## 修改social_core的源码使得它可以setcookie。

social_core/actions.py:

```python
return backend.strategy.redirect(url)
```

这是会在完成之后跳转到我们的url(index)的语句。
在这里我们加入一些自己的手脚。

```
    # 修改源码适配drf
    response = backend.strategy.redirect(url)
    payload = jwt_payload_handler(user)
    response.set_cookie("name",user.name if user.name else user.username, max_age=24*3600)
    response.set_cookie("token", jwt_encode_handler(payload), max_age=24*3600)
    return response
```

不要忘记return,否则会报错。

```
'NoneType' object has no attribute 'has_header'
```
