## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

Vue+Django REST framework实战

>使用Python3.6与Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

项目支持支付宝支付(暂不支持微信支付),支持手机短信验证码注册， 支持第三方登录。集成了sentry错误监控系统。

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

商品详情页,商品收藏

## viewsets实现商品详情页接口

左侧商品轮播图 & 商品详情 描述，运费，库存量。

热门商品放在另外一个url

只需要在继承的里面添加这个mixins.RetrieveModelMixin就可以了。

实现代码:

```python
class GoodsListViewSet(mixins.ListModelMixin, mixins.RetrieveModelMixin,viewsets.GenericViewSet):
```

查看Serializer，将其中的外键指向的Goodsimage

如何执行外键型的字段Serializer呢？答案: 进行嵌套的使用。

实现代码:

```python
class GoodsImageSerializer(serializers.ModelSerializer):
    class Meta:
        model = GoodsImage
        fields = ("image", )
```

>如上面代码新建一个Serializer。然后下面进行字段的嵌套

实现代码:

GoodsSerializer中

```python
    images = GoodsImageSerializer(many=True)
```

这里的值叫什么名称与我们在models中自定义的related_name有关。
一个goods对应的GoodsImage是有多条的。一定要添加many=true

将商品详情页完成后就可以来分析我们vue的源码。组件是productDetail                                                                                  productDetail.vue

```
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

列表页跳转到某一个详情页的时候，vue router里面有一个productId
会通过router里面的productdetail传递进来

src/router/index.js实现代码:

```
 {
            path: 'productDetail/:productId',
            name: 'productDetail',
            component: productDetail,
            meta: {
              title: '商品详情',
              need_log: false
            }
          
```

src/api/api.js中商品详情接口配置，改为本地的api接口localhost

```python
//商品详情
export const getGoodsDetail = goodId => { return axios.get(`${host}/goods/${goodId}`+'/') }
```

## 热卖商品接口实现

商品goods中有一个字段是是否热销。获取热销产品，filter中加一个ishot

goods/filters.py:

实现代码:

```python
        fields = ['pricemin', 'pricemax', 'name', 'is_hot']
```

在后台管理系统之中添加三条热卖数据为true

![mark](http://myphoto.mtianyan.cn/blog/180310/9ClmbEm3j3.png?imageslim)

可以看到这其中的HotSales

src/views/productDetail/hotSales.vue：

它在create的时候调用了getHotSales

```
            getHotSales() { //请求热卖商品
              getGoods({
                is_hot:true
              })
                .then((response)=> {
                    console.log(response.data)
                    this.hotProduct = response.data.results;

                }).catch(function (error) {
                    console.log(error);
                });
            }
```

实际上还是调用了getGoods接口，只是设置了参数is_hot为true而已
返回的json值会被放入response的data里面，而我们的数据放在result里面

返回的json格式:

```
{
    "count": 3,
    "next": null,
    "previous": null,
    "results": [
        {
            "id": 102,
            "category": {
                "id": 55,
                "name": "方便速食",
                "code": "方便速食",
                "desc": "",
                "category_type": 2,
                "is_tab": false,
                "add_time": "2018-02-14T03:45:05.659267",
                "parent_category": 40
            },
        }
    }
```

![mark](http://myphoto.mtianyan.cn/blog/180310/aa6I6IFbhh.png?imageslim)

将数据进行过滤之后，vue进行v-for的遍历，遍历之后使用router-link做一个跳转

然后将值进行填充。

## 用户收藏接口实现

这属于用户操作的功能，所以我们将操作写在user_operation中

收藏的接口既要继承viewset，添加收藏create，删除收藏destroy

实现代码:

```python
class UserFavViewset(viewsets.GenericViewSet, mixins.CreateModelMixin, mixins.DestroyModelMixin):
```
新建一个配套的Serializers.py

实现代码:

```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/3/10 0010 09:54'
from rest_framework import serializers
from user_operation.models import UserFav


class UserFavSerializer(serializers.ModelSerializer):
    class Meta:
        model = UserFav
        fields = ("user", "goods")
```

此时在views中继续进行配置queryset等

```python
class UserFavViewset(viewsets.GenericViewSet, mixins.CreateModelMixin, mixins.DestroyModelMixin):
    """
    用户收藏功能
    """
    queryset = UserFav.objects.all()
    serializer_class = UserFavSerializer
```

配置相应的url等在urls.py中

```python
# 配置用户收藏的url
router.register(r'userfavs', UserFavViewset, base_name="userfavs")
```

![mark](http://myphoto.mtianyan.cn/blog/180310/IiifChmbgk.png?imageslim)

当前的实现是我们可以选择用户，选择商品进行post，但是实际的逻辑应该是获取到当前的用户，我们只需要post商品过去就可以了。

我们希望我们的UserFavSerializer获取的是当前登录用户的user。

如何让Serializer自动帮我们填充当前的user？

http://www.django-rest-framework.org/api-guide/validators/#currentuserdefault

官方文档中

REST框架包含一些在这种情况下可能有用的默认值。

CurrentUserDefault
可用于表示当前用户的默认类。为了使用它，在实例化序列化程序时，'request'必须作为上下文字典的一部分提供。

```
owner = serializers.HiddenField(
    default=serializers.CurrentUserDefault()
)
```

为我们的UserFavSerializer添加代码:

```
    user = serializers.HiddenField(
        default=serializers.CurrentUserDefault()
    )
```

如果对于一个Serializer我们想为它添加删除的功能，就要将该条记录的id也返回回来
取消收藏就会变得容易。

### 添加商品收藏列表功能

实现代码:

```python
class UserFavViewset(viewsets.GenericViewSet, mixins.ListModelMixin, mixins.CreateModelMixin, mixins.DestroyModelMixin):
```
目前这样的配置只会返回我们商品的id以及这条收藏关系记录的id

### 删除功能实现

restful api 删除时要用到的http里面的delete方法。

url格式为

```
userfavs/id
```

删除哪一个收藏记录。id为收藏关系对应的id。

![mark](http://myphoto.mtianyan.cn/blog/180310/3d0LClCK8j.png?imageslim)

204代表删除成功，2开头的一般都是操作成功的。

用户反复的对一件商品进行收藏，理论上不应该生成两条收藏关系的记录。

这时候需要django model中的设置参数。

```
        # 多个字段作为一个联合唯一索引
        unique_together = ("user", "goods")
```

如果我们重复了，数据库会给我们抛出异常来

清空原有的表数据，进行makemigration 和 migrate

![mark](http://myphoto.mtianyan.cn/blog/180310/8AiCGAaaAB.png?imageslim)

可以通过navicat查看到我们的唯一索引

```
{
    "non_field_errors": [
        "字段 user, goods 必须能构成唯一集合。"
    ]
}
```

实际上validator中也为我们提供了UniqueTogetherValidator

在Serializer的meta信息中你那个自己定义

```python
        # 使用validate方式实现唯一联合
        validators = [
            UniqueTogetherValidator(
                queryset=UserFav.objects.all(),
                fields=('user', 'goods'),
                message="已经收藏"
            )
        ]
```

我们的modelSerializer和modelform一样与model紧密联系，所以model中的设置它会读取并配置。

>这个validate是写在meta信息中的，是因为它不是作用于某一个字段之上了。

400错误，返回的错误消息存放在non_field_errors中。而具体的某一个字段出错，会指明字段的名称。

整体错误信息。
