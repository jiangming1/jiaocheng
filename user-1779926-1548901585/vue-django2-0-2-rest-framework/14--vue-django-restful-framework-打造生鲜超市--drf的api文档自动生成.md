## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

# Vue+Django REST framework实战

>使用Python3.6与Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

项目支持支付宝支付(暂不支持微信支付),支持手机短信验证码注册， 支持第三方登录。集成了sentry错误监控系统。

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节内容: drf的api文档自动生成

## drf的api文档自动生成

文档的功能其实我们之前已经配置过了。

文档功能的url配置后面是不能加`$`符号结尾的。

实现代码:

```python
    # 自动化文档,1.11版本中注意此处前往不要加$符号
    path('docs/', include_docs_urls(title='mtianyan超市文档')),
```

优点: 全自动生成，文档里面做测试与交互。生成js shell代码段

左侧下方有个source code 可以选择shell JavaScript python

每一个接口会写好示例代码，把需要传递的参数说明清楚。

会将username和password这种参数写好。

根据我们在urls中配置的url自动生成文档。

![mark](http://myphoto.mtianyan.cn/blog/180310/bF45Fg5gkh.png?imageslim)

实现代码:

```python
    """
    list:
        商品分类列表数据
    retrieve:
        获取商品分类详情
    """
```

如上面代码的注释，list操作对应的说明

![mark](http://myphoto.mtianyan.cn/blog/180310/i69BmA2die.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180310/D5i1cBkD5E.png?imageslim)

如何为字段添加Description描述呢？

方法1：

在model中为goods添加help_text

方法2:

在Serializer的字段中添加help_text

方法3:

在filter中也可以添加help_text

点击Interact可以进行交互

而我们如果与一些需要auth的页面进行交互，会提示身份未认证。

如何进行交互呢？左下角的auth中有token basic session等

![mark](http://myphoto.mtianyan.cn/blog/180310/4E7804A7g4.png?imageslim)

文档和接口api控制台结合使用。

## 动态设置Serializer和permission获取用户信息

首先完成的功能，用户个人信息的修改。手机号码是不可编辑的，我们在注册的时候对于手机号码进行了验证。

点击用户资料，首先要将已有资料进行显示，所以要有一个接口请求用户的个人资料

UserViewset 接收post方法时用于用户注册，这时候我们只需要重载retrieveModelMixin

实现代码:

```python
from rest_framework import mixins

class UserViewset(CreateModelMixin, mixins.RetrieveModelMixin, viewsets.GenericViewSet):
```

使用默认的retrieve，获取用户详情就是users/id

这里面的id，用户在进入个人中心的时候是不知道的。

注册的时候，我们并没有为用户返回id。

- 解决方案1：返回id
- 解决方案2：重写get_object

重写get_obj实际上是控制retrieve。del也会用到get_object

users/views.py中实现代码:

```python
    # 重写该方法，不管传什么id，都只返回当前用户
    def get_object(self):
        return self.request.user
```

这时候用户随意传递参数进来，都只会返回当前用户

restful api 是对资源的操作，用户注册的时候post是对用户资源的一个操作

获取用户信息是getdetail 修改个人信息是update

直接使用UserViewset进行重用。

既然要获取到当前的用户，必须是一种登录的状态。

实现代码:

```python
permission_classes = (permissions.IsAuthenticated, )
```
访问viewset的方法时，都必须用户登录才可以。

但是create方法用户注册时不可能放在permission验证通过之后的。

这样我们就希望这个permission能在不同的http请求方式面前进行动态的选择

- 用户注册的时候没有权限，用户在get时有权限验证。

### 为了解决问题研究源码

rest_framework/views.py

实现代码:

```python
    def get_permissions(self):
        """
        Instantiates and returns the list of permissions that this view requires.
        """
        return [permission() for permission in self.permission_classes]
```

在我们之前配置的permission class里面去遍历，返回permission class的一个实例也就是对象

实际上我们就可以重载这个函数进行返回。问题有来了，我们的post get请求对应的action是什么？

>与函数名称是保持一致的。action放在self中，只有viewset是这样的，如果使用的是api view就不会这样了。

实现代码:

```python
    def get_permissions(self):
        if self.action == "retrieve":
            return [permissions.IsAuthenticated()]
        elif self.action == "create":
            return []

        return []
```

retrieve和create进行单独的处理。其他情况均返回空

既然要用户认证，此时我们的auth class被全局删除了之后，在用户注册中还没有配置

```python
    authentication_classes = (JSONWebTokenAuthentication, authentication.SessionAuthentication )
```

用户认证弹出框的模式是basic auth的模式，现在配置的这两种模式实际上是不需要用户输入用户名和密码的。

浏览器中添加session或head中添加token的。

刚才弹出是因为我们在setting中做过的设置

```
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework.authentication.BasicAuthentication',
        'rest_framework.authentication.SessionAuthentication',
    )
```

只返回了用户名和mobile，是因为我们之前设置了Serializer

```python
        fields = ("username", "code", "mobile", "password")
```

四个字段只有其中的username 和 mobile会显示，因为其他两个字段是writeonly

我们希望获取用户详情的时候，使用一个另外的Serializer

实现代码:

```python
class UserDetailSerializer(serializers.ModelSerializer):
    """
    用户详情序列化
    """
    class Meta:
        model = User
        fields = ("username", "gender", "birthday", "email","mobile")
```

问题来了，注册时候如果使用userdetailSerializer会导致那些没有的字段验证失败。
我们如何跟permission一样，动态的使用Serializer呢？

Serializer class 位于genericAPIView中

Serializer_class 可以通过直接配置，也可以通过get_Serializer_class函数获取到

所以我们需要动态的选择Serializer就可以重载这个函数就可以了

users/views.py中实现代码:

```python

    def get_serializer_class(self):
        if self.action == "retrieve":
            return UserDetailSerializer
        elif self.action == "create":
            return UserRegSerializer

        return UserDetailSerializer
```
