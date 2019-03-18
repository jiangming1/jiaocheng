## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

>使用Python3.6与Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

项目支持支付宝支付(暂不支持微信支付),支持手机短信验证码注册， 支持第三方登录。集成了sentry错误监控系统。

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节内容: Django原生以及使用drf 完成 商品列表页


## django的view实现商品列表页

**本章节很重要。**

通过Django的fbv cbv (class base view)都可以实现。

更建议通过基于class的view编码，面向对象。

实现json返回。通过商品列表页学习大多数drf知识点

1. 配置url

```
商品列表页
path('goods/', GoodsListView.as_view(),name="goods-list"),
```

2. goods中新建一个view_base 来实现一个只通过Django实现的json返回。

>查看Django的开发文档可以看到提供了很多view来减少我们的代码量

```
class GoodsListView(View):
    def get(self, request):
        """
        通过django的view实现商品列表页
        """
        json_list = []
        goods = Goods.objects.all()[:10]

        from django.forms.models import model_to_dict
        for good in goods:
            json_dict = model_to_dict(good)
            json_list.append(json_dict)

        import json
        from django.core import serializers
        json_data = serializers.serialize('json', goods)
        json_data = json.loads(json_data)
        from django.http import HttpResponse, JsonResponse
        return JsonResponse(json_data, safe=False)
```

>model_to_dict 将model转换为字典，不用一个字段一个字段提取。
- images field和 datetime直接dumps会出错
- serializers 专门用于序列化，有了这个序列化，其实上面的model_to_dict都不用做了

```
moudle not callable
```

>说明这是一个moudle我们不能直接调用，而应该进一步写明调用其中哪个方法。

![mark](http://upload-images.jianshu.io/upload_images/1779926-30cfcad5af26967f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 最基本方法问题，字段被一个一个的序列化。字段很多会很麻烦

```
  # for good in goods:
        #     json_dict = {}
        #     json_dict["name"] = good.name
        #     json_dict["category"] = good.category.name
        #     json_dict["market_price"] = good.market_price
        #     json_dict["add_time"] = good.add_time
        #     json_list.append(json_dict)
```



2. 如果将addtime加入会报错,json.dumps无法自己完成。

```
TypeError: Object of type 'add_time' is not JSON serializable
```

推荐阅读:

https://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/00138683221577998e407bb309542d9b6a68d9276bc3dbe000 

Django本身的实现其实已经蛮简单了，那我们为啥要用drf

敲重点！！！

images这个图片保存的是一个相对路径，而我们需要加上前面的前缀。

这个工作drf可以完成补前缀工作。

字段序列化方式被定死了，重组麻烦。

文档生成，输入检测(你是放在request body还表单过来的)

## 使用drf完成商品列表页

商品列表页过基础知识

http://www.django-rest-framework.org/

强大的，灵活的，api

- Web browsable API 
- Authentication policies 认证
- Serialization that supports both ORM and non-ORM data sources.

>序列化ORM 和 非ORM的数据源

 regular function-based views 我们是用的cbv

 ![mark](http://upload-images.jianshu.io/upload_images/1779926-605a87acbf065906.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 安装

```
coreapi (1.32.0+) - Schema generation support.
django-guardian (1.1.1+) - Object level permissions support.
```

对象级别的权限支持，coreapi支持文档

安装时报出utf-8 decode错误

修改虚拟环境中的

```
D:\CodeSpace\PythonEnvs\mxshop36\Lib\site-packages\pip\compat
```

大约75行

![mark](http://upload-images.jianshu.io/upload_images/1779926-65da33ac75078c0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果出现错误，把这个地方改为gbk

引入我们的文档。

```
    # 自动化文档,1.11版本中注意此处前往不要加$符号
    path('docs/', include_docs_urls(title='mtianyan生鲜超市文档'))
```

配置好了只要运行不报错验证成功。

Add 'rest_framework' to your INSTALLED_APPS setting.

```
INSTALLED_APPS = (
    ...
    'rest_framework',
)
```

```
path('api-auth/', include('rest_framework.urls'))
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-b4d7ee11b1a05db4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

http://www.django-rest-framework.org/tutorial/3-class-based-views/

这里面官方给出了一个例子如何简单的写一个class view

```
class SnippetList(APIView):
    """
    List all snippets, or create a new snippet.
    """
    def get(self, request, format=None):
        snippets = Snippet.objects.all()
        serializer = SnippetSerializer(snippets, many=True)
        return Response(serializer.data)
```

将官方示例中的snippets用我们的Goods代替

![mark](http://upload-images.jianshu.io/upload_images/1779926-4f846d4a8689a18e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到api view也是继承的view在它之上做了很多事。

```
from .models import Goods
```
写明引入当前目录下models中的Goos不容易重名失败。

可以自定义序列化的类。SnippetSerializer

modelform 和 form。modelform可以将字段直接转成html

现在drf里的Serializer是用来取代form开发的。modelfrom是针对html的，
Serializer是针对json的。

和之前的form一样。新建一个文件`serializers.py`

http://www.django-rest-framework.org/tutorial/1-serialization/

```
from rest_framework import serializers


class GoodsSerializer(serializers.Serializer):
    name = serializers.CharField(required=True,max_length=100)
    click_num = serializers.IntegerField(default=0)
```

字段太多了。我们先讲两个。

![mark](http://upload-images.jianshu.io/upload_images/1779926-b5e44c22304a12db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因为我们是在用浏览器请求所以Drf会帮你渲染成网页格式

![mark](http://upload-images.jianshu.io/upload_images/1779926-80bcb0d913fe57e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这就是drf的`Web browsable API`

使用浏览器请求会返回html。

![mark](http://upload-images.jianshu.io/upload_images/1779926-8a205de960842f66.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

get的时候指明json

![mark](http://upload-images.jianshu.io/upload_images/1779926-a7c7881d7c7804b6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-382bd9f12cd860ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

接口的描述我们可以自行定义。

post过去的数据他能解析的格式。

因为我们是序列化的Goods，所以我们的Serializer要和goods model中保持一致

我们在Serializer中加上front image

![mark](http://upload-images.jianshu.io/upload_images/1779926-57d88bf0184e52b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到我们返回的json中，image字段全部加上了media前缀

前缀是通过我们setting的MEDIA_URL 来自动添加的。

## drf的modelSerializer实现商品列表页功能

在drf的登录系统中user nonetype
注意检查这里需要返回的是username

```
    def __str__(self):
        return self.username
```

之所以可以登录退出，
是因为这里配置了一个url

```
    # 调试登录
    path('api-auth/', include('rest_framework.urls'))
```

goods/views.py添加:

```
  def post(self, request, format=None):
        serializer = GoodsSerializer(data=request.data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data, status=status.HTTP_201_CREATED)
        return Response(serializer.errors, status=status.HTTP_400_BAD_REQUEST)
```

接收前端request数据，交给GoodsSerializer进行验证，验证通过进行保存。

goods/serializers.py添加：

```
  def create(self, validated_data):
        """
        Create and return a new `Goods` instance, given the validated data.
        """
        return Goods.objects.create(**validated_data)
```

有了drf之后，他会把不管是用户GET post Body过来的数据
都会取到，放到data中。不需要对于get post 等做单独的处理。

save 会去调用Serializer里的create方法。

Django有form和modelform。那么Serializer是不是也有他的model

>bingo

```
class GoodsSerializer(serializers.ModelSerializer):
    class Meta:
        model = Goods
        # fields = ('category', 'goods_sn', 'name', 'click_num', 'sold_num', 'market_price')
        fields = "__all__"
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-a98cfefc0635c7ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

外键会序列化成id。那我们通过id拿到对应的category

>进行Serializer的嵌套使用。覆盖外键字段

```
class CategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = GoodsCategory
        fields = "__all__"


class GoodsSerializer(serializers.ModelSerializer):
    category = CategorySerializer()
    
    class Meta:
        model = Goods
        # fields = ('category', 'goods_sn', 'name', 'click_num', 'sold_num', 'market_price')
        fields = "__all__"
```

## GenericView 方式实现商品列表页和分页功能

使用更加上层的view写起来更简单。

使用Using mixins 和 generic view

GenericAPIView是在apiview的基础上加了filter 分页等一堆东西

>我们不用添加createModelMixin是因为我们的商品数据是后台添加的，前台不会提交。


```
class GoodsListView(mixins.ListModelMixin, generics.GenericAPIView):
    """
    商品列表页
    """
    queryset = Goods.objects.all()
    serializer_class = GoodsSerializer

    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)
```

list函数是在mixin中的，做了分页以及序列化。

不去重写定义get等http请求的方法默认你不接收这种方法

虽然上面已经很简单了，但是我们能不能更简单呢。

查看源码路径: D:/CodeSpace/PythonEnvs/mxshop36/Lib/site-packages/rest_framework

D:/CodeSpace/PythonEnvs/mxshop36/Lib/site-packages/rest_framework/generics.py

![mark](http://upload-images.jianshu.io/upload_images/1779926-2fc77d51a2d0abe7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>这些view都是官方提供给我们的view

- ListAPIview (获取列表)
- CreateAPiView (创建一个)
- Retrieve (获取某一条)

```
class GoodsListView(ListAPIView):
    """
    商品列表页
    """
    queryset = Goods.objects.all()
    serializer_class = GoodsSerializer
```

列表页通常都是要分页的，我们如何只通过setting的一个配置完成我们的分页。

所有关于restframework的配置要写在变量里

D:/CodeSpace/PythonEnvs/mxshop36/Lib/site-packages/rest_framework/settings.py

>源码中的setting配置

![mark](http://upload-images.jianshu.io/upload_images/1779926-28c3cc39bbf53e0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到已经不提供默认的分页类了。我们需要自己指明

```
REST_FRAMEWORK = {
    'DEFAULT_PAGINATION_CLASS': 'rest_framework.pagination.LimitOffsetPagination',
    'PAGE_SIZE': 10,
}
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-2db70ff94081b08d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-7496f999588aa542.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

返回的数据有了变化。

有了count next页的url，将之前的数据放到了results中

![mark](http://upload-images.jianshu.io/upload_images/1779926-0022674b3863b5f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

很神奇的是图片连域名都加上了。

>问题来了: 这个域名怎么加上的，待探究。

将整个url提供实际是我们restful api的一个标准

```python
class GoodsPagination(PageNumberPagination):
    page_size = 12
    page_size_query_param = 'page_size'
    page_query_param = "page"
    max_page_size = 100
```

最多100个。

goodslistview中添加

```
    pagination_class = GoodsPagination
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-c3654fd0fecf2020.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


第几页，每一页取多少条都变成了可配置的。

## Viewsets和router完成商品列表页

```
from rest_framework import viewsets
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-8df6d3527c80516b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到它里面包含的

```
class GenericViewSet(ViewSetMixin, generics.GenericAPIView):
```

它的内部之间pass，只是多继承了一个ViewSetMixin

ViewSetMixin中重写了as_view方法可以让我们的注册url变得更加简单

![mark](http://upload-images.jianshu.io/upload_images/1779926-00b1dd7fb69f407a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在view的基础上设置了很多动作。动态设置Serializer

```
class GoodsListView(mixins.ListModelMixin, generics.GenericAPIView):
```

GenericViewSet只继承了api view,那么原来`ListAPIView`中的get方法的实现就没有了

```
class GoodsListView(mixins.ListModelMixin, viewsets.GenericViewSet):
```

>ViewSets和Routers配套使用。

1. 为了让我们更好看清我们使用的是viewset的实现.

goods/views.py

```python
class GoodsListViewSet(mixins.ListModelMixin, viewsets.GenericViewSet):
```

>改名字

配置我们的url

```python
goods_list = GoodsListViewSet.as_view({
    'get': 'list',
})
```

将get请求绑定到list之上，类似于之前的

```python
def get(self, request, *args, **kwargs):
	return self.list(request, *args, **kwargs)
```

这样我们就不用自己再去绑定了。

配置url时就不用再加as_view()了

```python
    # 商品列表页
    path('goods/', goods_list,name="goods-list"),
```

**但是我们可以更厉害一点，直接不用进行这个get 与list的绑定，它自动完成**

>我们要介绍的router就是做这个的。

```python
from rest_framework.routers import DefaultRouter

router = DefaultRouter()

# 配置goods的url 
router.register(r'goods', GoodsListViewSet)
```

url中配置

```python
    # router的path路径
    re_path('^', include(router.urls)),
```

>router自动帮我们配置了get 和list，create 和 post的绑定

## 对于api view, GenericView viewset 和 router 的原理分析

理清我们的这些view他们之间的关系，以及listmodelMixin。
以及这些关系的组合使用，这样才能让我们更清楚什么时候使用哪种。

1. genericViewSet 是最高的一层

```
GenericViewSet(ViewSet) -drf
	GenericAPIView      -drf
		APIView 		-drf
			View 		-django
```

这些view之间的差异就引出了drf中另一个核心点mixin

![mark](http://upload-images.jianshu.io/upload_images/1779926-9cd33f59e1f4a69c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到源码中的mixin一共有五种

而各种view的差异，mixin就扮演着重要的角色

以ListModelMixin为例做区别，如果我们不去继承这个mixin它里面的这些方法的话。
就无法将get 和 list连接起来。无法连接，那么list中所作的所有功能都不能完成。

比如其中的

```
  queryset = self.filter_queryset(self.get_queryset())
  page = self.paginate_queryset(queryset)
        if page is not None:
            serializer = self.get_serializer(page, many=True)
            return self.get_paginated_response(serializer.data)

        serializer = self.get_serializer(queryset, many=True)
        return Response(serializer.data)
```

过滤，分页都将享受不到。

RetrieveModelMixin对于具体的商品信息进行了获取，序列化。这个在后面的商品详情页会介绍到。

UpdateModelMixin中对于部分更新还是全部更新进行了判断。

DestroyModelMixin用来连接我们的delete方法，在我们delete时有一些必要的操作，如设置返回状态204等。

>上述这些功能都是mixin做的，而generic view并没有做。所以drf是通过两者的结合来实现。

GenericAPIView继承于views.APIView

> Base class for all other generic views.是所有通用视图的基类

```python
filter_backends = api_settings.DEFAULT_FILTER_BACKENDS
pagination_class = api_settings.DEFAULT_PAGINATION_CLASS
```

>在原本的apiview基础上添加了过滤，分页。如果不用genericapiview而用api view这些事情都要我们自己实现。

配合着genericview 加上mixin就能组合出更加强大的:

![mark](http://upload-images.jianshu.io/upload_images/1779926-79c5e7941f6d40d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图这些分别为某个单独操作设置的apiview

```
class RetrieveAPIView(mixins.RetrieveModelMixin,
                      GenericAPIView):
    """
    Concrete view for retrieving a model instance.
    具体的视图 对于 检索一个model实例
    """
    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)
```

get响应的是浏览器发过来的请求，浏览器发过来的请求只有http协议中规定的几种。
我们将get请求绑定上retrieve方法，就能享受到retrieve方法给我们的好处

我们可以自己写一个view来继承mixins.RetrieveModelMixin,GenericAPIView 但是我们
一定要把get 和 retrieve的绑定写上。

一般我们都会优先考虑下面组合好的这些apiview。还是满足不了要求，那就自己组合，满足这种配置方式就可以了。

Viewset有哪些好处？

generic下的各种变种view也是继承了genericAPIView，配合各种mixin进行组合工作。

它将具体的单个modelmixin(如:mixins.RetrieveModelMixin,)换成了ViewSetMixin

>之前我们需要写函数将get等与retrieve等进行绑定。

ViewSetMixin的好处就是: 不需要我们通过方法来绑定。但是我们还是需要绑定关系的。

>在url配置的时候进行绑定。

```
def as_view(cls, actions=None, **initkwargs)
```

他重写了as_view，接受参数，传递到对应的method 与 action进行绑定

```python
goods_list = GoodsListViewSet.as_view({
    'get': 'list',
})
```

将本来在代码中的绑定移到url中进行一个绑定的配置。

我们可以用router进行一个默认的绑定，router中的绑定其实和generic中的差不多

viewSetmixin还有一个很大的功能:

```python
def initialize_request(self, request, *args, **kwargs):
	   """
        Set the `.action` attribute on the view,
        depending on the request method.
        """
        request = super(ViewSetMixin, self).initialize_request(request, *args, **kwargs)
        method = request.method.lower()
        if method == 'options':
            # This is a special case as we always provide handling for the
            # options method in the base `View` class.
            # Unlike the other explicitly defined actions, 'metadata' is implicit.
            self.action = 'metadata'
        else:
            self.action = self.action_map.get(method)
        return request
```

>为视图绑定动作，依赖于具体的request请求方法。这些action在我们后期进行动态Serializer时有很大好处。

![mark](http://upload-images.jianshu.io/upload_images/1779926-d8556208b5d5f08c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
