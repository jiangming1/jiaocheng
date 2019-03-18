## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节:首页商品分类显示功能

## 首页商品分类显示功能-2

如果你还没有添加xadmin的管理类绑定。

```python
class IndexAdAdmin(object):
    list_display = ["category", "goods"]

xadmin.site.register(IndexAd, IndexAdAdmin)
```

填充brand数据。

![mark](http://myphoto.mtianyan.cn/blog/180314/fJ29DgK4A6.png?imageslim)

可以看到我们在选择商品类目的时候，只能选择到一级类目。


goods/adminx.py中对于GoodsBrandAdmin重载get_context方法

```python
    def get_context(self):
        context = super(GoodsBrandAdmin, self).get_context()
        if 'form' in context:
            context['form'].fields['category'].queryset = GoodsCategory.objects.filter(category_type=1)
        return context
```

>调用它父类的get_context方法，也就是父类本身该做的也全部做了。

if 'form' in context:这是一个固定写法

取到fields中的category。这里的名称和我们model中的名称是一样的。(model中的外键)
它的queryset我们做出限制。只取category等于1的数据。

>这样可以在后台只取出字段中一部分数据。进阶开发想当有用！！！

自己手动在后台为生鲜食品大类和酒水饮料大类各添加三个品牌logo。

### vue中进行联调

src/views/index/series-list.vue

```
    created(){
        this.getList();
    }
```

组件在创建的时候调用了getList()

```
getList(){
              queryCategorygoods()
                .then((response)=> {
                   //跳转到首页页response.body面
                  console.log(response)
                    this.list = response.data
                })
                .catch(function (error) {
                  console.log(error);
                });
            }
```

>getList()调用了queryCategorygoods的接口

```
//获取商品类别信息
export const queryCategorygoods = params => { return axios.get(`${local_host}/indexgoods/`) }
```

并将该接口的数据赋值给list

对于list数据进行遍历。

前面关于goods的images等都会自动为我们加上域名，而这一次没有加上域名是为什么呢？

>如果我们自己在Serializer中调用Serializer的话。

```
    def get_ad_goods(self, obj):
        goods_json = {}
        ad_goods = IndexAd.objects.filter(category_id=obj.id, )
        if ad_goods:
            good_ins = ad_goods[0].goods
            goods_json = GoodsSerializer(good_ins, many=False).data
        return goods_json
```

这时候就不会自动为我们加上域名。想要让它加上域名就得加上一个参数

```
context={'request': self.context['request']}
```

image在序列化时源码中会判断是否存在上下文的request。如果存在就会加上域名。

在view中我们调用Serializer的时候，上下文会自动的给我们传进来。但是我们自己调用Serializer嵌套Serializer时不会被传进来。

## 商品点击数.收藏数修改

商品的GoodsListViewSet继承的 mixins.RetrieveModelMixin是获取商品的详情的。

我们可以重载其中的retrieve方法加入自己的逻辑.

原本的retrieve方法
```python
    def retrieve(self, request, *args, **kwargs):
        instance = self.get_object()
        serializer = self.get_serializer(instance)
        return Response(serializer.data)
```

添加我们逻辑的retrieve方法

```
    def retrieve(self, request, *args, **kwargs):
        instance = self.get_object()
        instance.click_num += 1
        instance.save()
        serializer = self.get_serializer(instance)
        return Response(serializer.data)
```

### 收藏数

收藏的viewset中UserFavViewset

点进继承的CreateModelMixin，找到perform_create

在原基础上添加我们自己的功能。

```

    # 收藏数+1
    def perform_create(self, serializer):
        instance = serializer.save()
        # 通过这个instance Userfav找到goods
        goods = instance.goods
        goods.fav_num +=1
        goods.save()

```

![mark](http://myphoto.mtianyan.cn/blog/180314/7Eh2B5c05C.png?imageslim)

可以看到我们的收藏数+1了。

我们只有这一种写法吗？django的信号量也可以解决这个问题。

goods中新建一个signals.py

```
from django.conf import settings
from django.db.models.signals import post_save
from django.dispatch import receiver

from user_operation.models import UserFav


# 参数一接收哪种信号，参数二是接收哪个model的信号
@receiver(post_save, sender=UserFav)
def create_user(sender, instance=None, created=False, **kwargs):
    # 是否新建，因为update的时候也会进行post_save
    if created:
        goods = instance.goods
        goods.fav_num +=1
        goods.save()
```

在apps中做操作。

```
    def ready(self):
        import user_operation.signals
```

将原来的实现perform_create注释掉

save delete都会有信号量发出的。

在用户取消收藏的时候也就是发出post_delete信号时减一

```python
# 参数一接收哪种信号，参数二是接收哪个model的信号
@receiver(post_delete, sender=UserFav)
def del_user_fav(sender, instance=None, created=False, **kwargs):
    goods = instance.goods
    goods.fav_num -=1
    goods.save()

```

这时减一。

信号量可能遇到的问题xadmin创建用户密码不对.被加密两次

```
很简单， 你找到xadmin源码中的 UserCreationForm
 里面有save_models方法 你修改一下这个方法中user.set_password(self.cleaned_data["password1"])
 这个逻辑 把这一行删除就行了
```

当然删除收藏数的操作也可以在perform_destory中操作

## 商品库存和销量的修改

目前我们最简单粗暴的三种场景

1. 新增商品到购物车
2. 修改购物车数量
3. 删除购物车记录

新增商品是一个create操作

直接在ShoppingCartViewset通过perform_xx实现

```python
	# 库存数-1
    def perform_create(self, serializer):
        shop_cart = serializer.save()
        goods = shop_cart.goods
        goods.goods_num -= shop_cart.nums
        goods.save()

    # 库存数+1
    def perform_destroy(self, instance):
        goods = instance.goods
        goods.goods_num += instance.nums
        goods.save()
        # 取goods在del之前取之后就被删掉了
        instance.delete()

    # 更新库存
    def perform_update(self, serializer):
        existed_record = ShoppingCart.objects.get(id=serializer.instance.id)
        existed_nums = existed_record.nums
        # 先保存之前的数据existed_nums
        saved_record = serializer.save()
        # 变化的数量
        nums = saved_record.nums-existed_nums
        goods = saved_record.goods
        goods.goods_num -= nums
        goods.save()
```

### 支付成功的销量加一

```python
            # 查询数据库中存在的订单
            existed_orders = OrderInfo.objects.filter(order_sn=order_sn)
            for existed_order in existed_orders:
                # 订单商品项
                order_goods = existed_order.goods.all()
                # 商品销量增加订单中数值
                for order_good in order_goods:
                    goods = order_good.goods
                    goods.sold_num += order_good.goods_num
                    goods.save()
```

反向取值，通过我们在model中设置的relate_name来取值。

## drf的缓存设置

加速网站的访问。

django的缓存机制。

动态网站的基本特点是它是一个动态的，每次用户请求页面它会重新计算。
有些变动比较小的数据: 如商品类别数据。可以有一定的延迟。

流量比较大时添加缓存机制。

django支持多种backend机制。redis memorycache

setting中设置一下就好了。

### drf的缓存

https://github.com/chibisov/drf-extensions

这是drf的一个扩展，不止增强了缓存还有其他的。

```
pip install drf-extensions
```

缓存viewset中的retrieve和list 方法是很常见的。这就是为什么CacheResponseMixin存在。

获取数据的才会用到cache

```python
from rest_framework_extensions.cache.mixins import CacheResponseMixin
```

把这个CacheResponseMixin，放在list之前。尽量放在第一个继承的类

```python
class GoodsListViewSet(CacheResponseMixin,mixins.ListModelMixin, mixins.RetrieveModelMixin, viewsets.GenericViewSet):
```

### 设置过期时间

```
REST_FRAMEWORK_EXTENSIONS = {
    'DEFAULT_CACHE_RESPONSE_TIMEOUT': 5
}
```

根据自己需求加缓存。目前这个缓存使用的是内存。每次系统重启会丢失

## drf的redis缓存设置

如何将redis作为backend

redis提供了一个client，我们可以进行观察key值比较透明。

redis中是保存html，还是保存json格式数据都是需要我们考虑的。

drf是兼容这两种模式的。

即使是商品列表页，加上过滤器参数之后，不同的人加上了不同的过滤器，肯定是不能用gods_list这个什么参数都不加的访问结果的。

所以缓存还应该与请求的参数挂钩，参数是什么，对应的返回结果是什么

### django-redis的第三方库


http://django-redis-chs.readthedocs.io/zh_CN/latest/

```
pip install django-redis
```

向setting中加入

```
CACHES = {
    "default": {
        "BACKEND": "django_redis.cache.RedisCache",
        "LOCATION": "redis://127.0.0.1:6379",
        "OPTIONS": {
            "CLIENT_CLASS": "django_redis.client.DefaultClient",
        }
    }
}
```

>前提是请自行安装好redis

配置密码

```
直接密码@127
```

后面的1指数据库。连接池解释器等。

![mark](http://myphoto.mtianyan.cn/blog/180314/ekLKjAHDd7.png?imageslim)

可以看到redis缓存成功。只有goods页面拥有缓存

key对于url参数进行了生成。json和html内容生成不同的key。

同样规则的人取出同样的数据，不同规则取不同数据。

## drf的throttle设置api的访问速率

>防止爬虫。无节制的爬取数据。服务器的压力。

对于某些关键数据进行限速访问。人为点击速度没有那么大。

api数据工整。

drf的自带功能。

http://www.django-rest-framework.org/api-guide/throttling/

### throttle配置到setting中

```python
    'DEFAULT_THROTTLE_CLASSES': (
        'rest_framework.throttling.AnonRateThrottle',
        'rest_framework.throttling.UserRateThrottle'
    ),
    'DEFAULT_THROTTLE_RATES': {
        'anon': '100/day',
        'user': '1000/day'
    }
```

限速规则与限速的类。未登录情况下限速，通过ip地址。登录情况下通过session或token来判断。

模拟登录。对于用户登录之后也做限制。

>The rate descriptions used in DEFAULT_THROTTLE_RATES may include second, minute, hour or day as the throttle period.

设置到我们的接口

```python
from rest_framework.throttling import UserRateThrottle,AnonRateThrottle

throttle_classes = (UserRateThrottle, AnonRateThrottle)
```

![mark](http://myphoto.mtianyan.cn/blog/180314/36lCK4jja9.png?imageslim)

parse_rate进行解析我们的规则

allow_request中使用django的cache进行缓存。将每个ip的访问次数设置到缓存中

get_ident会通过request.meta.get('remote_addr')取出ip

已登录用户的限制是通过

```
request.user.pk
```
