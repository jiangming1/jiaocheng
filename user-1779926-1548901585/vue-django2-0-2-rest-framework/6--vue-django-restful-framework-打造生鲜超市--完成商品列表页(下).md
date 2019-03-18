## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

Vue+Django REST framework实战

>使用Python3.6与Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

项目支持支付宝支付(暂不支持微信支付),支持手机短信验证码注册， 支持第三方登录。集成了sentry错误监控系统。

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节内容: Django原生以及使用drf 完成 商品列表页(下)

## drf中的request和response

drf对于django的request和response进行了一层封装。

浏览器请求的request会被drf进行一定的封装，扩展了标准的http request

```
REST framework's Request class extends the standard HttpRequest, adding support for REST framework's flexible request parsing and request authentication
```

在基本的httpRequest上进行了扩展。增加了对REST框架灵活的请求解析和请求认证的支持。

Request parsing 对于用户发过来的数据进行一定的解析


- data 将用户post过来的数据，以及files放到data里面
- 包括所有的解析内容，文件的，非文件的。
- 不仅解析post方法过来的内容。其他的如put patch都会帮我们进行解析

.query_params

里面放着get请求传递过来的参数?min=10


.parsers

用户传递过来的数据有很多种类型。比如file啊。post过来的json啊。字符串。

使用drf给我们提供的各种parsers

什么类型的数据都可以接收、调用parser指定对应的解析器


Authentication

认证中的.user就能获取到当前的用户。.auth返回一些额外的信息

### response根据你前台传过来的参数等给你返回

>比如前台要求返回json 就返回json。 要求返回浏览器可以解析的html就返回html

![mark](http://myphoto.mtianyan.cn/blog/180302/7GjI6mBA9d.png?imageslim)

response也是支持template_render的返回html

## drf的过滤器

通过drf为我们提供的过滤功能简单快速的完成过滤

GenericViewSet继承的GenericAPIView中有一个可以供我们重写的方法用于我们的过滤

```python
class GoodsListViewSet(mixins.ListModelMixin, viewsets.GenericViewSet):
    """
    商品列表页
    """
    # queryset是一个属性
    # good_viewset.queryset就可以访问到
    # 函数就必须调用good_viewset.get_queryset()函数
    # 如果有了下面的get_queryset。那么上面的这个就不需要了。
    # queryset = Goods.objects.all()
    serializer_class = GoodsSerializer
    pagination_class = GoodsPagination
    queryset = Goods.objects.all()

    def get_queryset(self):
        # 价格大于100的
        price_min = self.request.query_params.get('price_min', 0)
        if price_min:
            self.queryset = Goods.objects.filter(shop_price__gt=price_min)
        return self.queryset

```

>老师使用的drf中的这里将queryset 放在get 函数内部返回即可。
经我测试，新版本必须如上面代码所示

可能的报错：

```
    assert queryset is not None, '`base_name` argument not specified, and could ' \
AssertionError: `base_name` argument not specified, and could not automatically determine the name from the viewset, as it does not have a `.queryset` attribute.
```

>可以说报错信息写的很明显了。我们缺少一个queryset的属性。


可能的警告:

```
 UnorderedObjectListWarning: Pagination may yield inconsistent results with an unordered object_list: <class 'goods.models.Goods'> QuerySet.
  paginator = self.django_paginator_class(queryset, page_size)
```

>这个是因为我们没有为我们的返回数据添加一个指定的排序方式。

```python
self.queryset = Goods.objects.filter(shop_price__gt=price_min).order_by('-add_time')
```

添加指定的排序方式消除警告。

访问: http://127.0.0.1:8000/goods/?page=2&price_min=100

可以看到过滤效果

### 过滤的优化

即使这样我们还是觉得这个过滤实在是太麻烦了。

又要获取参数，又要判断参数是否存在，存在之后转化为int(我没转)

虽然现在看起来代码不多，但是如果有很多的过滤参数，那么过滤这件事将会变得较为复杂。

使用drf 的 filter

http://www.django-rest-framework.org/api-guide/filtering/

![mark](http://myphoto.mtianyan.cn/blog/180302/f64cI709F1.png?imageslim)

传统的filter 跟我们在xadmin后台中的过滤器差不多。

- searchFilter 它的行为是search行为。
- 排序的filter

列表页常见的三种过滤，就是列表页的传统字段精确过滤，搜索过滤，以及排序。

DjangoFilterBackend

```
pip install django-filter
```

- 安装之后加入已安装app的列表中。

```
django_filters,
```

- 添加设置。既然是默认设置一般都自动设置好了。

```
REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS': ('django_filters.rest_framework.DjangoFilterBackend',)
}
```

- 开始使用,首先import进来

goods/views.py

```python
from django_filters.rest_framework import DjangoFilterBackend
```

官方示例:

```
class UserListView(generics.ListAPIView):
    ...
    filter_backends = (DjangoFilterBackend,)
```

我们需要在我们的view中设置

![mark](http://myphoto.mtianyan.cn/blog/180302/fGm32h69HH.png?imageslim)

```python
filter_backends = (DjangoFilterBackend,)
```

我们既然决定使用djangofilter那么就可以不用get_queryset方法了。注释掉

- 设置我们需要过滤的字段

官方示例:

```
class ProductList(generics.ListAPIView):
    queryset = Product.objects.all()
    serializer_class = ProductSerializer
    filter_backends = (DjangoFilterBackend,)
    filter_fields = ('category', 'in_stock')
```

我们的实践:

```
    # 设置我们需要进行过滤的字段
    filter_fields = ('name', 'shop_price')
```

![mark](http://myphoto.mtianyan.cn/blog/180302/eeeageCJ05.png?imageslim)

可以看到我们页面的右上多了一个过滤器。这是一个精确地等于的过滤器

```
?name=&shop_price=100
```

### 我们想模糊搜索。或者搜索一个区间。

https://django-filter.readthedocs.io/en/master/

>他不但是一个满足drf的filter 也是用于

filter_class

我们可以自定义我们自己的filter类，进行自定义的filter类

在goods目录下新建filters.py

```python
# -*- coding:utf-8 _*-
__author__ = 'mtianyan'
__date__ = '2018/03/03 00:44'

from django_filters import rest_framework as filters
from goods.models import Goods


class GoodsFilter(filters.FilterSet):
    """
    商品的过滤类
    """
    # 指定字段以及字段上的行为，在shop_price上大于等于
    price_min = filters.NumberFilter(name="shop_price", lookup_expr='gte')
    price_max = filters.NumberFilter(name="shop_price", lookup_expr='lte')

    class Meta:
        model = Goods
        fields = ['price_min', 'price_max']
```

```python
    # 设置三大常用过滤器之DjangoFilterBackend
    filter_backends = (DjangoFilterBackend,)

    # 设置filter的类为我们自定义的类
    filter_class = GoodsFilter
```

>注释掉: filter_fields，这时候我们的filter_fields已经失效了

![mark](http://myphoto.mtianyan.cn/blog/180303/gJJkjCD9mE.png?imageslim)

>过滤器没有确定的按钮是因为继承错了。应该继承rf的`import rest_framework as filters`

## drf的搜索和排序

我们不一定在这个类里面使用它自带的过滤方法。还可以自定义我们的过滤方法，这个在后面会讲到。

https://code.ziqiangxuetang.com/django/django-queryset-api.html

Django QuerySetAPI

模糊查询

```
    # 行为: 名称中包含某字符，且字符不区分大小写
    name = filters.CharFilter(name="name", lookup_expr="icontains")

    class Meta:
        model = Goods
        fields = ['price_min', 'price_max', 'name']
```

配置drf里面的SearchFilter

http://www.django-rest-framework.org/api-guide/filtering/#searchfilter

```
from rest_framework import filters

    # 设置三大常用过滤器之DjangoFilterBackend, SearchFilter
    filter_backends = (DjangoFilterBackend, filters.SearchFilter)
```

>上面代码中的filter一定要注意不是django中的filter而是restframe中的。不然会报错。


```
    filter_backends = (DjangoFilterBackend, filters.SearchFilter)
AttributeError: module 'django_filters.rest_framework.filters' has no attribute 'SearchFilter'
```

配置了search_fields之后配置我们的search_fields

```
    # 设置我们的search字段
    search_fields = ('name', 'goods_brief', 'goods_desc')
```

search_fields.

- `^` Starts-with search.
- `=` Exact matches.
- `@` Full-text search. (Currently only supported Django's MySQL backend.)
- `$` Regex search.

以当前字段值开头的，等于类似于字段精确过滤。全文搜索。正则表达式搜索

```
search_fields = ('=username', '=email')
```

By default, the search parameter is named 'search', but this may be overridden with the SEARCH_PARAM setting.

默认的search参数为

![mark](http://myphoto.mtianyan.cn/blog/180303/A65i76ai7f.png?imageslim)

search。可以在设置中覆盖`SEARCH_PARAM` 进行自定义。

可以为多个字段配置不同精度的搜索。比如name是精确搜索，简介是模糊搜索这种。

OrderingFilter支持排序

```
    # 设置三大常用过滤器之DjangoFilterBackend, SearchFilter
    filter_backends = (DjangoFilterBackend, filters.SearchFilter, filters.OrderingFilter)
    # 设置排序
    ordering_fields = ('sold_num', 'add_time')
```

一般全文搜索，真正的模糊查询是使用elasticSearch来做的。

but this may by overridden with the `ORDERING_PARAM` setting.

重写order 参数。来实现url中指定参数值

不止数据的获取，数据的提交以及后面的login验证等，都可以在这个界面进行操作。

## 总结

1. view_base中通过django自身的原生view实现了商品列表页。
2. 自己序列化 - model_to_dict方法 - 使用Serializer - JSonresponse
3. api view generics - viewsets
