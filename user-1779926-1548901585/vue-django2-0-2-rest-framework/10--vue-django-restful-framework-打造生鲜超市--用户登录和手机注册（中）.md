## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

Vue+Django REST framework实战

>使用Python3.6与Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

项目支持支付宝支付(暂不支持微信支付),支持手机短信验证码注册， 支持第三方登录。集成了sentry错误监控系统。

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

## Json Web Token的原理

因为我们的drf 的token auth有它的缺点。所以最常用的还是JWT的方式

以下内容转载于: http://lion1ou.win/2017/01/18/

>http是一种无状态的协议，前后两次请求它会不知道这是从同一个人还是不同的人发的。

传统方式： 采用session和cookie结合的方式

前后端分离的传统: 用户信息生成token token 和对于的用户id保存到数据库或session中。我们的drf 的 token auth 就是这种。

接着把token传给用户，存入浏览器cookie。之后的请求带上这个cookie，后端根据这个cookie值查询用户，验证过期的逻辑需要表里多一个字段，以及后端的逻辑验证。

问题: xss漏洞: cookie可以被js读取。作为后端识别用户的标识，cookie的泄露意味着用户信息不再安全。特别是drf我们的token auth没有过期时间

设置cookie时两个更安全的选项: httpOnly以及secure项.

- httponly的不能被js读取，浏览器会自动加在请求header中
- secure就只通过https

httponly 问题。很容易被xsrf攻击，因为cookie会默认发出去。

如果将验证信息保存数据库。每次都要查询。保存session，加大了服务器端存储压力。

那我们可以不要服务器去查询呢？

>只要我们生成的token遵循一定的规律，比如使用对称加密算法来加密id 形成token。
服务端只需要解密token 就能知道id。

- 这样做的问题在于如果他知道你是怎么加密的。那么它可以通过这种加密方式伪造token

此时我们使用非对称加密算法。

对称加密，加密和解密使用的是同一个密钥。服务器把token传给用户，以及用户拿着token来服务器进行解密。加密解密都在服务器端。

JWT 是一个开放标准(RFC 7519)，它定义了一种用于简洁，自包含的用于通信双方之间以 JSON 对象的形式安全传递信息的方法。JWT 可以使用 HMAC 算法或者是 RSA 的公钥密钥对进行签名。它具备两个特点

- 简洁(Compact)

可以通过URL, POST 参数或者在 HTTP header 发送，因为数据量小，传输速度快

- 自包含(Self-contained)

负载中包含了所有用户所需要的信息，避免了多次查询数据库

![mark](http://myphoto.mtianyan.cn/blog/180304/1A01AaeiEF.png?imageslim)

头部包含两部分，token和加密算法。会进行Base64加密。但是会很容易反解出来

- Header 头部

>头部包含了两部分，token 类型和采用的加密算法

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

>它会使用 Base64 编码组成 JWT 结构的第一部分。

- Payload 负载

这部分就是我们存放信息的地方了，你可以把用户 ID 等信息放在这里，JWT 规范里面对这部分有进行了比较详细的介绍，常用的由 iss（签发者），exp（过期时间），sub（面向的用户），aud（接收方），iat（签发时间）。


```
{
    "iss": "lion1ou JWT",
    "iat": 1441593502,
    "exp": 1441594722,
    "aud": "www.example.com",
    "sub": "lion1ou@163.com"
}
```

>同样的，它会使用 Base64 编码组成 JWT 结构的第二部分

- Signature 签名

>前面两部分都是使用 Base64 进行编码的，即前端可以解开知道里面的信息。Signature 需要使用编码后的 header 和 payload 以及我们提供的一个密钥，然后使用 header 中指定的签名算法（HS256）进行签名。签名的作用是保证 JWT 没有被篡改过。

可以解开就是指前端可以通过token拿到用户的一部分非敏感信息。

JWT不会再保存数据了。a关注b，发邮件给b。b直接点击带回来串和action。就不需要登录了。

设计用户认证和授权系统(独立)，以及单点登录。


![mark](http://myphoto.mtianyan.cn/blog/180304/5J51kC51f6.png?imageslim)

单点登录，多个子域名通过一个统一的授权认证接口进行登录。

https://github.com/GetBlimp/django-rest-framework-jwt

首先要安装

```
pip install djangorestframework-jwt
```

使用:

需要将jsonWebAuth加入到drf 的default auth class中

```
        'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
```

对于用户post过来的token进行验证，将user取出来

- path中的配置

```
from rest_framework_jwt.views import obtain_jwt_token
#...

urlpatterns = [
    '',
    # ...

    url(r'^api-token-auth/', obtain_jwt_token),
]
```

配置path中的jwt

```
    # jwt的token认证
    path('jwt-auth/', obtain_jwt_token )
```

![mark](http://myphoto.mtianyan.cn/blog/180304/6k6blfb7F5.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180304/GLg276GK8A.png?imageslim)

```
curl -H "Authorization: JWT <your_token>" http://localhost:8000/protected-url/
```

![mark](http://myphoto.mtianyan.cn/blog/180304/mJd039JHcm.png?imageslim)

jwt的加密认证方式可以参照jwt的源码进行学习。

## vue和jwt接口调试

- vue中登录的逻辑

```
//登录
export const login = params => {
  return axios.post(`${host}/login/`, params)
}
```

vue中的登录post到login接口

要么改前端，要么改后端，毕竟我们现在在写后端。改后端url为login

```
    # jwt的token认证
    path('login/', obtain_jwt_token )
```

前往login.vue中查看登录的具体逻辑

```
 login({
          username:this.userName, //当前页码
          password:this.parseWord
      }).then((response)=> {
            console.log(response);
            //本地存储用户信息
            cookie.setCookie('name',this.userName,7);
            cookie.setCookie('token',response.data.token,7)
            //存储在store
            // 更新store数据
            that.$store.dispatch('setInfo');
            //跳转到首页页面
            this.$router.push({ name: 'index'})
          })
```

获取到当前的用户名和密码 这个用户名和密码来自当前的data()中

data中的值又通过v-model进行了与输入框中值的绑定(我猜的啊)

本地存储设置了cookie的名字和值，token和值。并设置了7天过期

![mark](http://myphoto.mtianyan.cn/blog/180304/K382A3KkJI.png?imageslim)

可以看到我们的vue数据中已经有了name 和 token

setInfo会进行实时数据同步更新的操作

```
 [types.SET_INFO] (state) {
        state.userInfo = {
            name:cookie.getCookie('name'),
            token:cookie.getCookie('token')
        }
        console.log(state.userInfo);
    },
```

我们的jwt 调用的是django自带的auth与userProfile中数据进行对比。而我们如果使用手机注册，就会导致验证失败。因为默认是用用户名和密码去查的。

### 自定义django用户认证函数

1. 首先在setting中设置变量:

```
# 设置邮箱和用户名和手机号均可登录
AUTHENTICATION_BACKENDS = (
    'users.views.CustomBackend',

)
```

2. 在user/view中

```
class CustomBackend(ModelBackend):
    """
    自定义用户验证规则
    """
    def authenticate(self, username=None, password=None, **kwargs):
        try:
            # 不希望用户存在两个，get只能有一个。两个是get失败的一种原因
            # 后期可以添加邮箱验证
            user = User.objects.get(
                Q(username=username) | Q(mobile=username))
            # django的后台中密码加密：所以不能password==password
            # UserProfile继承的AbstractUser中有def check_password(self,
            # raw_password):
            if user.check_password(password):
                return user
        except Exception as e:
            return None
```

![mark](http://myphoto.mtianyan.cn/blog/180305/dlKbmJgcmE.png?imageslim)

通过断点测试可以成功的进入了我们的这段逻辑。

### JWT的过期时间设置

```

# 与drf的jwt相关的设置
JWT_AUTH = {
    'JWT_EXPIRATION_DELTA': datetime.timedelta(seconds=20),
    'JWT_AUTH_HEADER_PREFIX': 'JWT',
}
```

这里的header前缀我们要保持与前端的一致。

src/axios/index.js:

```
config.headers.Authorization = `Bearer ${store.state.userInfo.token}`;
```

## 云片网发送短信验证码

注册会用到一些高级的Serializer。

写一个接口：发送短信

form的验证。

1. 云片网

>可以有很多子账号，每个子账号都会有一个api key 这个api key就会很重要。

发送短信验证码这个key是必须要用到的。

文本短信 & 语音短信

发送国内短信申请签名。短信模板。

- 新建签名(审核), 新建模板(可包含变量，审核)

api文档中使用说明。

国内短信api文档: https://www.yunpian.com/doc/zh_CN/domestic/list.html

>单条发送，批量发送(相同内容，不同内容)

https://www.yunpian.com/doc/zh_CN/domestic/single_send.html

参数。必填字段填过来。示例代码

### utils下yunpian.py

线上部署时一定要将自己服务器的ip加入ip白名单中。测试时搜索本机ip地址。

```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/3/8 0008 09:28'
import json
import requests


class YunPian(object):

    def __init__(self, api_key):
        self.api_key = api_key
        self.single_send_url = "https://sms.yunpian.com/v2/sms/single_send.json"

    def send_sms(self, code, mobile):
        parmas = {
            "apikey": self.api_key,
            "mobile": mobile,
            "text": "【慕学生鲜】您的验证码是{code}。如非本人操作，请忽略本短信".format(code=code)
        }

        response = requests.post(self.single_send_url, data=parmas)
        re_dict = json.loads(response.text)
        return re_dict


if __name__ == "__main__":
    yun_pian = YunPian("apikey的值")
    yun_pian.send_sms("2017", "手机号码")
```

注意text内容必须要与后台已申请过签名并审核通过的模板保持一致

## drf实现发送短信验证码接口

```
# 发送验证码是创建model中一条记录的操作
from rest_framework.mixins import CreateModelMixin
```

用户传过来的手机号码我们要进行两次验证:

- 是否有效
- 有没有被注册过

Serializer和django里的form modelform 是一样的，所以这个验证我们把它放到我们的Serializer里面来做。

- 为什么不像goods中一样使用`serializers.ModelSerializer`

>因为我们model中的code也是必填项，而我们拥有的只有手机号，所以会导致验证失败

setting.py中

```
# 手机号码正则表达式
REGEX_MOBILE = "^1[358]\d{9}$|^147\d{8}$|^176\d{8}$"
```

users/serializers.py:

```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/3/8 0008 09:41'
import re
from datetime import datetime, timedelta
from VueDjangoFrameWorkShop.settings import REGEX_MOBILE
from users.models import VerifyCode
from rest_framework import serializers
from django.contrib.auth import get_user_model
User = get_user_model()


class SmsSerializer(serializers.Serializer):
    mobile = serializers.CharField(max_length=11)

    def validate_mobile(self, mobile):
        """
        验证手机号码(函数名称必须为validate_ + 字段名)
        """
        # 手机是否注册
        if User.objects.filter(mobile=mobile).count():
            raise serializers.ValidationError("用户已经存在")

        # 验证手机号码是否合法
        if not re.match(REGEX_MOBILE, mobile):
            raise serializers.ValidationError("手机号码非法")

        # 验证码发送频率
        one_mintes_ago = datetime.now() - timedelta(hours=0, minutes=1, seconds=0)
        # 添加时间大于一分钟以前。也就是距离现在还不足一分钟
        if VerifyCode.objects.filter(add_time__gt=one_mintes_ago, mobile=mobile).count():
            raise serializers.ValidationError("距离上一次发送未超过60s")

        return mobile
```

然后views中class SmsCodeViewset(CreateModelMixin, viewsets.GenericViewSet):

重写CreateModelMixin中的create方法

原本的create方法

```python
 def create(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        self.perform_create(serializer)
        headers = self.get_success_headers(serializer.data)
        return Response(serializer.data, status=status.HTTP_201_CREATED, headers=headers)
```
`serializer.is_valid(raise_exception=True)`有效性验证失败会直接抛异常。
被drf捕捉到返回400状态码。



其中的APIKEY需要我们添加到setting.py中

```
# 云片网设置
APIKEY = 'apikey值'
```

生成四位数的验证码值

```python
    def generate_code(self):
        """
        生成四位数字的验证码
        """
        seeds = "1234567890"
        random_str = []
        for i in range(4):
            random_str.append(choice(seeds))

        return "".join(random_str)
```

改写后的自定义方法:

```python
    def create(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)

        mobile = serializer.validated_data["mobile"]

        yun_pian = YunPian(APIKEY)

        code = self.generate_code()

        sms_status = yun_pian.send_sms(code=code, mobile=mobile)

        if sms_status["code"] != 0:
            return Response({
                "mobile":sms_status["msg"]
            }, status=status.HTTP_400_BAD_REQUEST)
        else:
            code_record = VerifyCode(code=code, mobile=mobile)
            code_record.save()
            return Response({
                "mobile":mobile
            }, status=status.HTTP_201_CREATED)
```

![mark](http://myphoto.mtianyan.cn/blog/180308/g3ddFbJLLa.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180308/bLF082c8bJ.png?imageslim)

将返回的json在yunpian中loads成dict

然后取出dict中的code和msg进行判断与返回。我们不需要向前端返回status。而是遵循restful api的规范。http状态码即可区分成功或失败。消息并不代表。

发送成功之后再保存验证码

## 调试是否正确

调试之前配置好对应的url

```
from users.views import SmsCodeViewset
# 配置codes的url
router.register(r'codes', SmsCodeViewset, base_name="codes")
```

http://127.0.0.1:8000/codes/

![mark](http://myphoto.mtianyan.cn/blog/180308/Bj3i5mljEl.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180308/0J6JckCdhf.png?imageslim)

返回中既设置了400的http code 又有和form类似的字段信息错误

字段名称 : 数组(告诉你该字段的错误)

发送成功(201)

![mark](http://myphoto.mtianyan.cn/blog/180308/b7gKieeIml.png?imageslim)
