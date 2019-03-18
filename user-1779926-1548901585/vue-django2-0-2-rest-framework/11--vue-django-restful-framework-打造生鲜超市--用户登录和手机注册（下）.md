## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

Vue+Django REST framework实战

>使用Python3.6与Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

项目支持支付宝支付(暂不支持微信支付),支持手机短信验证码注册， 支持第三方登录。集成了sentry错误监控系统。

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

## user serializer和validator验证(注册功能)

注册页面需要我们输入手机号码 验证码 和密码

django的form 和 model form 是用来验证用户提交的字段的合法性的。

restful api 中实际是对资源的操作。我们的注册对应的资源就是用户。

因为用户注册必定是会crate model操作的，所以继承CreateModelMixin

先为viewset准备一个配套的Serializers

users/serializers.py(继承modelSerializer，因为字段都是必填项，都有的，虽然相比较用户model多了一个code字段)

>通过一些小技巧，既能享受model Serializer带来的好处，又能突破它的限制

```python
class UserRegSerializer(serializers.ModelSerializer):
    code = serializers.CharField(required=True, write_only=True, max_length=4, min_length=4,error_messages={
                                     "blank": "请输入验证码",
                                     "required": "请输入验证码",
                                     "max_length": "验证码格式错误",
                                     "min_length": "验证码格式错误"
                                 },
                                 help_text="验证码")
```

>自定义我们每种验证的错误信息。

验证username是否存在:

```python
    username = serializers.CharField(label="用户名", help_text="用户名", required=True, allow_blank=False,
                                     validators=[UniqueValidator(queryset=User.objects.all(), message="用户已经存在")])
```

![mark](http://myphoto.mtianyan.cn/blog/180308/eCbC9f1cFf.png?imageslim)

>drf为我们提供的唯一性字段验证。together是联合字段验证，也就是比如收藏这种。
关系唯一。

UniqueValidator的参数:

- queryset required - This is the queryset against which uniqueness should be enforced.
- message - The error message that should be used when validation fails.
- lookup - The lookup used to find an existing instance with the value being validated. Defaults to 'exact'.

>lookup执行哪些操作。

```python
    username = serializers.CharField(label="用户名", help_text="用户名", required=True, allow_blank=False,
                                     validators=[UniqueValidator(queryset=User.objects.all(), message="用户已经存在")])
```

eclipse快捷键下，pycharm格式化快捷键为`ctrl+shift+f`

为mobile model添加可以为空的选项。

```python
    mobile = models.CharField(null=True, blank=True, max_length=11, verbose_name="电话")
```

因为前端只有手机号码这一个值。而这个值还存在username中。所以需要置空，保证验证成功。

在Serializer中添加code字段，这个code是多余字段不会被保存到数据库中

```python
 def validate_code(self, code):

        # get与filter的区别: get有两种异常，一个是有多个，一个是一个都没有。
        # try:
        #     verify_records = VerifyCode.objects.get(mobile=self.initial_data["username"], code=code)
        # except VerifyCode.DoesNotExist as e:
        #     pass
        # except VerifyCode.MultipleObjectsReturned as e:
        #     pass

        # 验证码在数据库中是否存在，用户从前端post过来的值都会放入initial_data里面，排序(最新一条)。
        verify_records = VerifyCode.objects.filter(mobile=self.initial_data["username"]).order_by("-add_time")
        if verify_records:
            # 获取到最新一条
            last_record = verify_records[0]

            # 有效期为五分钟。
            five_mintes_ago = datetime.now() - timedelta(hours=0, minutes=5, seconds=0)
            if five_mintes_ago > last_record.add_time:
                raise serializers.ValidationError("验证码过期")

            if last_record.code != code:
                raise serializers.ValidationError("验证码错误")

        else:
            raise serializers.ValidationError("验证码错误")
```

>进行验证码的验证，可能出现的错误有过期，验证码错误。或记录根本不存在。

验证完之后将code这个字段删除掉

```python
    # 不加字段名的验证器作用于所有字段之上。attrs是字段 validate之后返回的总的dict
    def validate(self, attrs):
        attrs["mobile"] = attrs["username"]
        del attrs["code"]
        return attrs
```

views中的用户viewset中实例化Serializer

```python
class UserViewset(CreateModelMixin, viewsets.GenericViewSet):
    """
    用户
    """
    serializer_class = UserRegSerializer
```

为该viewset配置路由

```python
# 配置users的url
router.register(r'users', UserViewset, base_name="users")
```

drf验证默认的返回格式:

```
HTTP 400 Bad Request
Allow: POST, OPTIONS
Content-Type: application/json
Vary: Accept

{
    "username": [
        "用户已经存在"
    ],
    "code": [
        "验证码错误"
    ]
}
```

- 单个字段出错: 字段 + 数组 
- 联合字段出错: `non_fields_error`

哪个出错哪个高亮。

一般我们的传统开发都会喜欢自定义消息

```
{
	"status": 0,
	"msg": 验证码出错
}
```

不好的地方:

>前端自己要去做解析知道哪种对应着失败。
想要做到单个字段的提示，消息要做成这种格式

```python
{
	"status": 0,
	"msg":
	{
		moblie: [""],
		code: [""]	
	}
}
```

status如果只是用来判定用户的状态正确或失败，那跟httpcode的区别就几乎没有了
rest api 的设计模式。

拉勾网: 请求过多page, 已经发生异常显示的不正常的页面，但是200状态。
影响seo。对于Google

基于http code进行开发，可以让大家对于错误正确的状态判断保持一致

## 完善用户注册，django信号量实现用户密码修改。

用户注册逻辑编码。

后台添加一条用户短信验证码数据之后进行验证。



```python
class UserViewset(CreateModelMixin, viewsets.GenericViewSet):
    """
    用户
    """
    serializer_class = UserRegSerializer
    queryset = User.objects.all()
```

http://www.django-rest-framework.org/api-guide/fields/

报错信息:

```
Got AttributeError when attempting to get a value for field `code` on serializer `UserRegSerializer`.
The serializer field might be named incorrectly and not match any attribute or key on the `UserProfile` instance.
Original exception text was: 'UserProfile' object has no attribute 'code'.
```

这是因为我们配置的

```
        fields = ("username", "code", "mobile", "password")
```

有四个字段，而我们在Serializer的处理验证过程中已经删除了其中的code。

解决方案（重要参数）:

>code字段添加write only=true。就不会将此字段进行序列化返回给前端。

上面的错误查看源码中CreateModelMixin的部分代码。可以看到它在验证了是否有效之后执行了save。这些都是不会有问题的，但是当它return Response时，它会return S rializer的data（会依照我们在fields中的配置）。这时候因为data中的字段已经和model中的不再一致。

如果是一个正常的Serializer会将我们post过去的数据序列化之后返回回来。

>password也被返回了回来。这是不合理的，为password也添加write only =True参数

密码会被明文存储。

```python
    def create(self, validated_data):
        user = super(UserRegSerializer, self).create(validated_data=validated_data)
        user.set_password(validated_data["password"])
        user.save()
        return user
```

重载Serializer的create方法。可以实现。


虽然重载代码量已经很少了，但是可能比较难理解，所以我们选择其他解决方案

附录:

label是如下图form中左侧字样，helptext是input框下面的。

### django信号量机制。

`django post_save()`方法

我们的model对象进行操作的时候，会发出全局的信号。捕捉之后做出我们自己的操作。

https://docs.djangoproject.com/en/2.0/ref/signals/

django的信号量如request_started和scrapy中的是一致的。

删除之前做一些事情，就可以接收pre_delete的信号

post_save 

http://www.django-rest-framework.org/api-guide/authentication/

Generating Tokens
By using signals
If you want every user to have an automatically generated Token, you can simply catch the User's post_save signal.

```python
from django.conf import settings
from django.db.models.signals import post_save
from django.dispatch import receiver
from rest_framework.authtoken.models import Token

@receiver(post_save, sender=settings.AUTH_USER_MODEL)
def create_auth_token(sender, instance=None, created=False, **kwargs):
    if created:
        Token.objects.create(user=instance)
```

新建文件users/signals.py

```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/3/9 0009 09:29'

from django.conf import settings
from django.db.models.signals import post_save
from django.dispatch import receiver
from rest_framework.authtoken.models import Token

from django.contrib.auth import get_user_model
User = get_user_model()


# 参数一接收哪种信号，参数二是接收哪个model的信号
@receiver(post_save, sender=User)
def create_auth_token(sender, instance=None, created=False, **kwargs):
    # 是否新建，因为update的时候也会进行post_save
    if created:
        password = instance.password
        instance.set_password(password)
        instance.save()
```

**做完刚才这些操作，还要重载一个配置**

否则会导致虽然没有任何报错信息，但是密码并没有被加密

apps.py中。

```python
    def ready(self):
        """
        Override this method in subclasses to run code when Django starts.
        """
```

这是AppConfig中我们可以在子类中自定义的函数，它将会在django启动时被运行。

```python
    def ready(self):
        import users.signals
```

我们修改的mobile的字段可为空，只在代码中修改是没有用的，还需要我们进行migrations

这样我们才可以在后台中不需要mobile字段直接添加用户。

![mark](http://myphoto.mtianyan.cn/blog/180309/HHa2aFAiIH.png?imageslim)

可以看到成功的添加了密码并且为我们进行加密。

## vue和注册功能联调

- 查看vue的项目结构，找到register.vue

```python
                    <input class="btn btn-green" id="jsMobileRegBtn" @click="isRegister" type="button" value="注册并登录">
```

点击注册并登录会调用`isRegister` 函数，

```python
isRegister(){
            var that = this;
            register({
                password:that.password,
                username:that.mobile ,
                code:that.code,
            }).then((response)=> {
              cookie.setCookie('name',response.data.username,7);
              cookie.setCookie('token',response.data.token,7)
              //存储在store
              // 更新store数据
              that.$store.dispatch('setInfo');
              //跳转到首页页面
              this.$router.push({ name: 'index'})

          })
```

调用了一个register的函数接口。

```python
export const register = parmas => { return axios.post(`${host}/users/`, parmas) }
```

实际上就是指向我们的user这个url。获取参数。将host 改为localhost进行联调

register传递的参数有password，username，code。这些都是前端页面post过来的值

常见的注册登录有注册完了你自己登录，以及注册完成帮你自动登录。

如果是让用户自己去登录，那么就将cookie.setcookie这两行注释掉。直接让它跳转到首页。

但是如果是自动登录，那么我们此时就没有给前台返回jwt 的token。

重载createmodelmixin里面的create函数。

框架原本实现代码:

```python
    def create(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)
        serializer.is_valid(raise_exception=True)
        self.perform_create(serializer)
        headers = self.get_success_headers(serializer.data)
        return Response(serializer.data, status=status.HTTP_201_CREATED, headers=headers)

    def perform_create(self, serializer):
        serializer.save()
```

这里的perform中的save是save了当前的model（user）。但是并没有返回该model。我们要想获取到user model 就必须重写让它返回model

然后在执行perform_create之后插入我们自己的逻辑。

分析jwt的源码实现，找到它哪部分才是生成token的。

- 从url入口。点进obtain_jwt_token

```python
    # jwt的token认证
    path('login/', obtain_jwt_token )
```

实现代码:

```python
obtain_jwt_token = ObtainJSONWebToken.as_view()
```

```python
class ObtainJSONWebToken(JSONWebTokenAPIView):
    """
    API View that receives a POST with a user's username and password.

    Returns a JSON Web Token that can be used for authenticated requests.
    """
    serializer_class = JSONWebTokenSerializer
```

此时我们就可以去查看继承的父类: JSONWebTokenAPIView

该类中用户在post数据过来之后。

```python
 def post(self, request, *args, **kwargs):
        serializer = self.get_serializer(data=request.data)

        if serializer.is_valid():
            user = serializer.object.get('user') or request.user
            token = serializer.object.get('token')
            response_data = jwt_response_payload_handler(token, user, request)
            response = Response(response_data)
            if api_settings.JWT_AUTH_COOKIE:
                expiration = (datetime.utcnow() +
                              api_settings.JWT_EXPIRATION_DELTA)
                response.set_cookie(api_settings.JWT_AUTH_COOKIE,
                                    token,
                                    expires=expiration,
                                    httponly=True)
            return response

        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

>关键在于token是直接从Serializer中获取的，那么token的生成应该是在Serializer中实现的。

```python
            token = serializer.object.get('token')
```

寻找我们的Serializer

```python
serializer = self.get_serializer(data=request.data)

    def get_serializer(self, *args, **kwargs):
        serializer_class = self.get_serializer_class()
       	return self.serializer_class
```


Serializer位于rest_framework_jwt/views.py的ObtainJSONWebToken类中


```python
    serializer_class = JSONWebTokenSerializer
```

该类中token的生成相关代码

```python
                payload = jwt_payload_handler(user)

                return {
                    'token': jwt_encode_handler(payload),
                    'user': user
                }
```

调用了jwt_encode_handler， 而该handler是api_setting中设置的

```python
jwt_payload_handler = api_settings.JWT_PAYLOAD_HANDLER
jwt_encode_handler = api_settings.JWT_ENCODE_HANDLER
```

rest_framework_jwt/settings.py

```python
DEFAULTS = {
    'JWT_ENCODE_HANDLER':
    'rest_framework_jwt.utils.jwt_encode_handler',
      'JWT_PAYLOAD_HANDLER':
    'rest_framework_jwt.utils.jwt_payload_handler',
    }
```

所以我们已经找到了生成token的两个重要步骤，一payload，二encode

实现代码:

```python
from rest_framework_jwt.serializers import jwt_payload_handler, jwt_encode_handler

        re_dict = serializer.data
        payload = jwt_payload_handler(user)
        re_dict["token"] = jwt_encode_handler(payload)
        re_dict["name"] = user.name if user.name else user.username

         headers = self.get_success_headers(serializer.data)
        return Response(re_dict, status=status.HTTP_201_CREATED, headers=headers)
```

其中的token要和前端保持一致。注意将原本返回的Serializer.data进行加工之后返回。

### 退出功能

退出功能就变得更好做了，因为jwt的token并不是保存在服务器端的。

src/views/head/shophead.vue

实现代码:

```python
                <a @click="loginOut">退出</a>
```

退出按钮调用的是loginout函数

实现代码:

```python
 loginOut(){
        cookie.delCookie('token');
        cookie.delCookie('name');
        //重新触发store
        //更新store数据
        this.$store.dispatch('setInfo');
        //跳转到登录
        this.$router.push({name: 'login'})
      },
```

清空token给axios发一个通知。跳转到登录页面。

注册页面测试，将codes与前端保持一致，以及修改localhost
