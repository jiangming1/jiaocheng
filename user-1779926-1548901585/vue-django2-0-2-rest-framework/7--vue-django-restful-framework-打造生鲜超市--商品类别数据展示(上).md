## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

Vue+Django REST framework实战

>使用Python3.6与Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

项目支持支付宝支付(暂不支持微信支付),支持手机短信验证码注册， 支持第三方登录。集成了sentry错误监控系统。

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节内容: 商品类别数据展示(上)

## 商品类别数据展示(上)

并没有将列表页的数据json 与前端的页面展示结合起来

讲解如果将drf 返回的数据映射到vue的界面中

首先解决商品的分类

目录会有；两个接口，

一个是全部数据的接口。用于首页的全部分类。一级-二级-三级

![mark](http://myphoto.mtianyan.cn/blog/180303/3IljlKaf38.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180303/266ibEIecg.png?imageslim)

另一个获取某一类的分类。以及商品数量

![mark](http://myphoto.mtianyan.cn/blog/180303/bAl4Gbi1be.png?imageslim)

为商品通过大类进行过滤条件的过滤。价格，销量的排序。分页。

价格区间功能。

![mark](http://myphoto.mtianyan.cn/blog/180303/9815c5Lddc.png?imageslim)

通过vue的插件我们可以查看到我们的具体的组件和页面结构。

![mark](http://myphoto.mtianyan.cn/blog/180303/6eAkI2J29I.png?imageslim)

产品列表中是一堆router link 点击会跳转详情页，详情页实际是vue router路由。

### 开始开发类别数据接口

Serializer是需要我们为其准备一个Serializer的。

```python
class CategorySerializer(serializers.ModelSerializer):
    """
    商品类别序列化
    """
    class Meta:
        model = GoodsCategory
        fields = "__all__"
```

对于分类进行筛选。但是不需要进行分页。分页用于数据量很大的情况。

```python
class CategoryViewset(mixins.ListModelMixin, mixins.RetrieveModelMixin, viewsets.GenericViewSet):
    """
    list:
        商品分类列表数据
    retrieve:
        获取商品分类详情
    """
    queryset = GoodsCategory.objects.all()
    serializer_class = CategorySerializer
```

>这里的注释要如上图所示写清楚。才能让后面生成文档更方便。

- 配置到我们的url中

```
# 配置goods的url,这个basename是干啥的
router.register(r'goods', GoodsListViewSet, base_name="goods")

# 配置Category的url
router.register(r'categories', CategoryViewset, base_name="categories")
```

http://127.0.0.1:8000/categories/

这里将我们所有的目录数据全部拿了出来。而我们期望的是有一个顺序的层次结构。

```python
    queryset = GoodsCategory.objects.filter(category_type=1)
```

>将查询所有数据改为只获取category类型为1的数据。

![mark](http://myphoto.mtianyan.cn/blog/180304/mF9aIH9a98.png?imageslim)

问题来了: 

>如何让一类数据包括二类数据。

Serializer中添加三级分类的Serializer

```python
class CategorySerializer3(serializers.ModelSerializer):
    class Meta:
        model = GoodsCategory
        fields = "__all__"


class CategorySerializer2(serializers.ModelSerializer):
    sub_cat = CategorySerializer3(many=True)
    class Meta:
        model = GoodsCategory
        fields = "__all__"


class CategorySerializer(serializers.ModelSerializer):
    """
    商品一级类别序列化
    """
    sub_cat = CategorySerializer2(many=True)
    class Meta:
        model = GoodsCategory
        fields = "__all__"
```

这里的sub_cat 是因为我们在自身的继承关系中将这种关系进行了命名

models.py:

```python
    parent_category = models.ForeignKey("self", on_delete=models.CASCADE, null=True, blank=True, verbose_name="父类目级别", help_text="父目录",
                                        related_name="sub_cat")
```

如何通过一级分类(parents)拿到二级分类，一类对象直接`father.subcat`
此时的sub_cat就变成了拿到的二类数据，但是对于此时拿到的二类数据，我们依然希望它进行序列化。

>因为我们此时通过一类拿到的二类有很多，所以必须加上`many = True`的参数

 如果不加这个参数会产生报错:

```
 AttributeError at /categories/
Got AttributeError when attempting to get a value for field `sub_cat` on serializer `CategorySerializer2`.
The serializer field might be named incorrectly and not match any attribute or key on the `RelatedManager` instance.
Original exception text was: 'RelatedManager' object has no attribute 'sub_cat'.
```

问题又来了: 现在我们获取的是所有的category，如何获取具体某一个下面的。

点击生鲜食品，左侧出现的是生鲜食品下面的具体分类。

**目标: 获取列表页里面的详情页**

让我们的viewset 多继承一个mixins.retrievemodelmixin

```python
class CategoryViewset(mixins.ListModelMixin, mixins.RetrieveModelMixin, viewsets.GenericViewSet):
```

这里我们需要遵循restful api 对于某一个商品详情获取的推荐

```python
GET /zoos/ID：获取某个指定动物园的信息
```

在获取某个商品详情的时候会把id加进来

但是其实drf 已经帮我们做好了（做好到我们甚至可以不用配置这个url）。之前的课程我们如何配置url来满足id

```python
 # 激活用户url
    re_path('active/(?P<active_code>.*)/', ActiveUserView.as_view(), name= "user_active"),
```

通过?p将正则获取到的参数放入active_code里面

但是其实这个工作viewset已经帮我们做了。一但我们进行了register的注册。

只要我们继承了RetrieveModelMixin就可以直接通过id进行获取。

http://127.0.0.1:8000/categories/1/

此时返回的就是一个dict，而不是一个数组了。

## Vue展示商品分类数据

将category的数据和vue进行联调。

首先需要解决的问题是跨域的问题。

>跨域问题是前后端分离的项目中非常常见的问题。

我们先找一下category是在哪里编码又是如何将数据映射进来的呢

category也是在head里面

src/views/head/head.vue

![mark](http://myphoto.mtianyan.cn/blog/180304/i5AIAm8d9l.png?imageslim)

getMenu函数调用了getCategory函数，getcatgory函数在哪里呢。

```
        getMenu(){//获取菜单
          getCategory({
            params:{}
          }).then((response)=> {
                    console.log(response)
                    this.allMenuLabel = response.data
                })
                .catch(function (error) {
                  console.log(error);
                });
        },
```

可以看到我们的import

```
import { getHotSearch, getCategory ,deleteShopCart } from  '../../api/api'
```

我们的get category方法位于src/api/api.js中

```
//获取商品类别信息
export const getCategory = params => {
  if('id' in params){
    return axios.get(`${host}/categorys/`+params.id+'/');
  }
  else {
    return axios.get(`${host}/categorys/`, params);
  }
};
```

里面有getCategory 函数，这种写法是es6的写法。

es6中的箭头函数`params =>`传递一个params，如果params中有id那么，通过axios获取接口

```
axios.get(`${host}/categorys/`+params.id+'/');
```

- 如果没有id值，请求接口

```
axios.get(`${host}/categorys/`, params);
```

`${host}`是取值我们的域名

```
let host = 'http://shop.projectsedu.com';
```

这个函数是一个共有函数，`export`估计是暴露导出供大家使用。
解决了详情或所有的通用性问题

目前host指向的老师线上的环境

如何调试接口。很多地方我们都用到了host。

我们新建一个自己的host然后慢慢替换。

```
let local_host = 'http://127.0.0.1:8000'
```

然后将localhost 放入我们要调试的部分替换host

```
  if('id' in params){
    return axios.get(`${local_host}/categories/`+params.id+'/');
  }
  else {
    return axios.get(`${local_host}/categories/`, params);
  }
```

```
Failed to load resource: net::ERR_CONNECTION_REFUSED
```

后端的服务没有启动。获取不到。启动之后

```
Failed to load http://127.0.0.1:8000/categories/: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://127.0.0.1:3000' is therefore not allowed access.
```

不允许跨域访问的请求

前端npm也是可以完成跨域的，一般通过proxy代理。

### 设置服务器完成跨域

django-cors-headers

>Django app for handling the server headers required for Cross-Origin Resource Sharing (CORS)

```
pip install django-cors-headers
```

```
INSTALLED_APPS = (
    ...
    'corsheaders',
    ...
)
```

添加进安装的app中

![mark](http://myphoto.mtianyan.cn/blog/180304/dDg7l9a749.png?imageslim)

然后添加我们的middleware

要放的尽可能靠前，在csrfview之前。

添加参数为true及设置白名单

```
CORS_ORIGIN_ALLOW_ALL = True
CORS_ORIGIN_WHITELIST = (
    '127.0.0.1:3000'
)
```

数据是如何填充进来的，简单介绍一下

```
        getMenu(){//获取菜单
          getCategory({
            params:{}
          }).then((response)=> {
                    console.log(response)
                    this.allMenuLabel = response.data
                })
                .catch(function (error) {
                  console.log(error);
                });
        },
```

将response的数据，放入了allMenuLabel

在head.vue中，使用v-for进行遍历

```
 <li class="first" v-for="(item,index) in allMenuLabel" @mouseover="overChildrenmenu(index)" @mouseout="outChildrenmenu(index)">

 <router-link :to="'/app/home/list/'+item.id">{{item.name}}</router-link> </h3>
```

router-link 是指向前端的vue_router路由。并不是我们后台的路径。

将数据的展示和路由交给了前端。后端只需要提供数据。

![mark](http://myphoto.mtianyan.cn/blog/180304/1GldJaH2Aa.png?imageslim)

tag页面为空。是因为我们导入数据时默认全部都不是tag

我们可以前往xadmin后台修改为进行导航。

![mark](http://myphoto.mtianyan.cn/blog/180304/m8LEE9E3gj.png?imageslim)

做了对于数据的遍历。

![mark](http://myphoto.mtianyan.cn/blog/180304/m8LEE9E3gj.png?imageslim)

也是同样进行遍历，不过当它是tag的时候才显示。
