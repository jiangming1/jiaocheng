## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

# Vue+Django REST framework实战

>使用Python3.6与Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

项目支持支付宝支付(暂不支持微信支付),支持手机短信验证码注册， 支持第三方登录。集成了sentry错误监控系统。

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节内容: drf的权限验证

## drf的权限验证

看起来已经完成了用户添加收藏，删除收藏的功能。但是正常的业务逻辑应该是用户只能删除自己的收藏。


http://www.django-rest-framework.org/api-guide/permissions/

auth 和 permission是两种东西。auth是用来做用户验证的，permission是用来做权限判断的。

AllowAny:不管有没有权限都可以访问。

IsAuthenticated: 判断是否已经登录

IsAdminUser：判断用户是否是一个管理员。user.is_staff


第一步判断用户是否登录了。

实现代码:

```python
from rest_framework.permissions import IsAuthenticated
permission_classes = (IsAuthenticated,)
```

用户未登录访问userfav的list会给我们抛出401的错误。

![mark](http://myphoto.mtianyan.cn/blog/180310/5m10bBGjKB.png?imageslim)

官方例子，ip是否在白名单中

实现代码:

```python
from rest_framework import permissions

class BlacklistPermission(permissions.BasePermission):
    """
    Global permission check for blacklisted IPs.
    """

    def has_permission(self, request, view):
        ip_addr = request.META['REMOTE_ADDR']
        blacklisted = Blacklist.objects.filter(ip_addr=ip_addr).exists()
        return not blacklisted
```

拿着model做一下过滤实现

- 官方例子：是否是所有者，否则仅仅可读

实现代码:

```python
class IsOwnerOrReadOnly(permissions.BasePermission):
    """
    Object-level permission to only allow owners of an object to edit it.
    Assumes the model instance has an `owner` attribute.
    """

    def has_object_permission(self, request, view, obj):
        # Read permissions are allowed to any request,
        # so we'll always allow GET, HEAD or OPTIONS requests.
        if request.method in permissions.SAFE_METHODS:
            return True

        # Instance must have an attribute named `owner`.
        return obj.owner == request.user
```

在utils中新建permissions，这是我们自定义的permissions，然后粘贴上面的IsOwnerOrReadOnly

这个自定义的permission类继承了我们的BasePermission。它有一个方法叫做has_object_permission，是否有对象权限。

会检测我们从数据库中拿出来的obj的owner是否等于request.user

这个obj是我们数据库中的表，所以这里的owner应该改为我们数据库中的外键user

安全的方法也就是不会对数据库进行变动的方法，总是可以让大家都有权限访问到。

views中添加该自定义的权限认证类

实现代码:

```python
from utils.permissions import IsOwnerOrReadOnly

permission_classes = (IsAuthenticated, IsOwnerOrReadOnly)
```

这样在做删除的时候就会验证权限。

不能让所有的收藏关系数据都被获取到。因此我们要重载get_queryset方法

实现代码:

```python
    def get_queryset(self):
        return UserFav.objects.filter(user=self.request.user)
```

重载之后queryset的参数配置就可以注释掉了

在model设计中。我们的str方法返回值为name。这个name是有可能为null的。

所以报出错误

```python
__str__ return non-string(type NoneType)
```

我们在return 的时候。将name改为username

此时我们前往数据库将收藏关系的userid 改为一个非当前登录用户，会发现确实只能查看自己的收藏。

![mark](http://myphoto.mtianyan.cn/blog/180310/lmFfkadmHc.png?imageslim)

删除一条不属于它自己的收藏记录时会提示不存在。

在使用其他工具时输入用户名密码也可以进行登录，是因为我们配置了多种auth类。

token的认证最好是配置到view里面去，而不是配置到全局中。

前端的每一个request请求都加入我们的token的话，token过期了，当用户访问goods列表页等不需要token认证的页面也会拿不到数据。

将setting中的`'rest_framework.authentication.SessionAuthentication',`删除掉。

然后在具体的view中来import以及进行配置

user_operation/views.py中实现代码:

```python
from rest_framework_jwt.authentication import JSONWebTokenAuthentication

authentication_classes = (JSONWebTokenAuthentication, )
```

此时在我们的api控制台以及无法使用登录进入userfav了，是因为我们的类内auth并不包含session auth 

实现代码:

```python
from rest_framework.authentication import SessionAuthentication

authentication_classes = (JSONWebTokenAuthentication, SessionAuthentication)
```

## 用户收藏功能和vue联调

vue和后端联调的一些问题:

未登录状态，商品详情页商品的收藏状态应该是未收藏，而登录状态我们就需要通过设置好的接口，动态的显示。

/userfavs/5/获取某一个收藏的详情，这里的id是数据表里的这条收藏关系的id

实际上我们并不知道这件商品收藏到数据库里面的id是什么，既想使用现有接口，又不想传这个id。前端提出需求，可不可以不传这个id而是传当前商品的goods_id

后端通过传过来的goods_id和当前的user找到是否被收藏过。

当我们添加`RetrieveModelMixin`之后，我们可以通过

http://127.0.0.1:8000/userfavs/5/ 获取到某一条关系的详情。

### 了解retrieve的原理

实现代码:

```python
    def retrieve(self, request, *args, **kwargs):
        instance = self.get_object()
        serializer = self.get_serializer(instance)
        return Response(serializer.data)
```

调用get_object函数，这个函数在rest_framework/generics.py中的GenericAPIView中

实现代码:

```python
    def get_object(self):
        """
        Returns the object the view is displaying.

        You may want to override this if you need to provide non-standard
        queryset lookups.  Eg if objects are referenced using multiple
        keyword arguments in the url conf.
        """
        queryset = self.filter_queryset(self.get_queryset())

        # Perform the lookup filtering.
        lookup_url_kwarg = self.lookup_url_kwarg or self.lookup_field

        assert lookup_url_kwarg in self.kwargs, (
            'Expected view %s to be called with a URL keyword argument '
            'named "%s". Fix your URL conf, or set the `.lookup_field` '
            'attribute on the view correctly.' %
            (self.__class__.__name__, lookup_url_kwarg)
        )

        filter_kwargs = {self.lookup_field: self.kwargs[lookup_url_kwarg]}
        obj = get_object_or_404(queryset, **filter_kwargs)

        # May raise a permission denied
        self.check_object_permissions(self.request, obj)

        return obj
```


会根据我们传入的id默认搜索主键也就是id这个字段。我们希望它不要搜索id这个字段，而是搜索goods这个字段。lookup_field接收哪些字段，这个实际上是可以配置的。

在http://www.django-rest-framework.org/api-guide/generic-views/#genericapiview
中有这个参数。

```python
    lookup_field = 'pk'
```

在apiview中这个字段默认值是pk，我们可以将它改为goods_id。

![mark](http://myphoto.mtianyan.cn/blog/180310/70d6Bhdcj5.png?imageslim)

因为在数据库表中goods这个外键实际存储的字段名是goods_id

```python
created () {
        this.productId = this.$route.params.productId;
        var productId = this.productId
        if(cookie.getCookie('token')){
          getFav(productId).then((response)=> {
            this.hasFav = true
          }).catch(function (error) {
            console.log(error);
          });
        }
        this.getDetails();
    },
```

在src/views/productDetail/productDetail.vue中create时会首先获取到产品的id
会在cookie中找token，如果有token，那么将会通过产品id执行getFav

```
//判断是否收藏
export const getFav = goodsId => { return axios.get(`${host}/userfavs/`+goodsId+'/') }
```

如果response的状态是200，那么我们就可以将hasfav赋值true

如果hasfav为true的情况下。

```
  <a v-if="hasFav" id="fav-btn" class="graybtn" @click="deleteCollect">
                                        <i class="iconfont">&#xe613;</i>已收藏</a>
                                  <a v-else class="graybtn" @click="addCollect">
                                    <i class="iconfont">&#xe613;</i>收藏</a>
```

如果是true，v-if显示收藏，收藏状态下点击会执行删除收藏。
未收藏时点击就会执行收藏。

```
deleteCollect () {
            //删除收藏
          delFav(this.productId).then((response)=> {
            console.log(response.data);
            this.hasFav = false
          }).catch(function (error) {
            console.log(error);
          });
        },
```

删除收藏调用的是delfav接口。

```
//取消收藏
export const delFav = goodsId => { return axios.delete(`${host}/userfavs/`+goodsId+'/') }

//收藏
export const addFav = params => { return axios.post(`${host}/userfavs/`, params) }
```

添加收藏调用的是addFav接口。

将添加收藏，取消收藏和获取某一条收藏关系的host改为localhost。而getalllfav会在个人中心才会进行实现

login的url需要改为localhost(我前面已经改过了)，需要修改的原因是本地登录加密
解密的密钥是setting中的`SECRET_KEY`线上加密线下解密会导致失败

每次生成项目时这个密钥都是不一样的。

lookup_fields为goods会不会把其他用户收藏的该商品的收藏关系查询出来。

实际上lookupfield的操作时在get_queryset的结果中来做的。已经做过过滤了。

个人中心会大量的用到auth 和 permission
