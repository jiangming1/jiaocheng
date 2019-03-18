## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节:轮播图接口实现和vue调试

## 轮播图接口实现和vue调试

![mark](http://myphoto.mtianyan.cn/blog/180314/7Lm9amf8Bm.png?imageslim)

vue首页组件结构。

index是首页的内容区域，banner是轮播图，NewP是新品展示部分，SeriesList是系列产品

- 轮播图接口 & 新品接口 & 下面的系列产品接口

支付功能开发完了，我们需要进入本地调试环境

运行一下查看这个本地调试环境是否可以运行。将vue项目中的地址改为我们本地地址。

banner有一个外键指向我们的goods。

新建一个配套的BannerSerializer:

goods/serializers.py:

```
class BannerSerializer(serializers.ModelSerializer):
    class Meta:
        model = Banner
        fields = "__all__"
```

goods/views.py:

```
class BannerViewset(mixins.ListModelMixin, viewsets.GenericViewSet):
    """
    获取轮播图列表
    """
    queryset = Banner.objects.all().order_by("index")
    serializer_class = BannerSerializer
```

设置我们的url

```
# 首页banner轮播图url
router.register(r'banners', BannerViewset, base_name="banners")
```

![mark](http://myphoto.mtianyan.cn/blog/180314/k0ki8jKH18.png?imageslim)


没有报错但是也没有数据，我们直接来我们的后台管理系统中添加三张轮播图。


获取轮播图的接口为

```
//获取轮播图
export const bannerGoods = params => { return axios.get(`${host}/banners/`) }
```

改为localhost

src/views/index/index.vue:

首页的index组件中引用了三个组件

![mark](http://myphoto.mtianyan.cn/blog/180314/JE39fimmAB.png?imageslim)

```
    <div>
        <banner></banner>
        <newP></newP>
        <seriesList></seriesList>
    </div>
```

banner组件在created的时候调用了getBanner

```
    created(){
      this.getBanner();
    }
```

getBanner会调用bannerGoods

```
getBanner(){
        bannerGoods()
          .then((response)=> {
            console.log(response)
            //跳转到首页页response.body面
            this.banners = response.data
          })
          .catch(function (error) {
            console.log(error);
          });
      }
```

会将响应的数据填充到banners

![mark](http://myphoto.mtianyan.cn/blog/180314/d7bmLJKhFE.png?imageslim)

swipe组件对于数据进行渲染

```
  import { swiper, swiperSlide } from 'vue-awesome-swiper'
```

## 新品功能接口开发

新品是goods中有一个is_new的选项。

直接使用Goods的接口，进行一个过滤，过滤出其中是新品的。

goods/filters.py:

```
        fields = ['pricemin', 'pricemax', 'name', 'is_hot','is_new']
```

为goods的filter添加is_new字段

![mark](http://myphoto.mtianyan.cn/blog/180314/glIlHj50m9.png?imageslim)

使用过滤器可以看到url中 is_new =ture

这时返回200的正确状态码，但是值为空。因为我们并没有给它设置is_new的商品。

在xadmin后台添加两个新品。

然后前往vue查看组件是如何完成的。

src/views/index/new.vue

```
    created(){
        this.getOpro();
    }
```

getOpro调用了方法getGoods

```
getOpro(){
          getGoods({
            "is_new":"true"
          })
            .then((response)=> {
               //跳转到首页页response.body面
                this.newopro = response.data.results
            })
            .catch(function (error) {
              console.log(error);
            });
        }
```

调用接口

```
//获取商品列表
export const getGoods = params => { return axios.get(`${local_host}/goods/`, { params: params }) }
```

![mark](http://myphoto.mtianyan.cn/blog/180314/dJJKCD5ECc.png?imageslim)

使用响应的数据进行遍历填充商品信息值。

## 首页商品分类显示功能-1

大分类商品开发。

![mark](http://myphoto.mtianyan.cn/blog/180314/0K6LFDjKIm.png?imageslim)

这个div中元素data之间的关系会复杂一些。

- 一个大类会有很多brand商标。
- 大类又有小类，依然一对多
- 大类里面的商品。
- 加上它还有一张大图片。

首先来写一个用于这个系列商品展示的Serializer

```python
class IndexCategorySerializer(serializers.ModelSerializer):
    class Meta:
        model = GoodsCategory
        fields = "__all__"
```

找出其中的一对多关系。

- 首先要取到brand。GoodsCategoryBrand中有一个外键指向category，我们为它添加一个related_name。让它反向取的时候比较方便。

```python
category = models.ForeignKey(GoodsCategory, on_delete=models.CASCADE, related_name='brands', null=True, blank=True, verbose_name="商品类目")
```

我们就可以通过goods_category反向取到brand。

首先写一个BrandSerializer

```python
class BrandSerializer(serializers.ModelSerializer):
    class Meta:
        model = GoodsCategoryBrand
        fields = "__all__"
```

将brand放入IndexCategorySerializer

```
# 一个category会有多个brand。
brands = BrandSerializer(many=True)
```

为什么不能直接用goods的Serializer。

goods中确实有一个外键指向我们的category。为什么不能反向取。

>因为商品选择哪个类别的时候，往往选择的是最小的类别。(第三类)
而不是直接从属于第一类。

- 外键指向的第三类。而我们要拿到第一类别的数据。直接使用goods的Serializer取不出来。

```python
    goods = serializers.SerializerMethodField()

    def get_goods(self, obj):
    	# 将这个商品相关父类子类等都可以进行匹配
        all_goods = Goods.objects.filter(Q(category_id=obj.id)|Q(category__parent_category_id=obj.id)|Q(category__parent_category__parent_category_id=obj.id))
        goods_serializer = GoodsSerializer(all_goods, many=True, context={'request': self.context['request']})
        return goods_serializer.data
```

取到二级商品分类

![mark](http://myphoto.mtianyan.cn/blog/180314/bHeE8lib3l.png?imageslim)

```python

    sub_cat = CategorySerializer2(many=True)
```

首页广告商品大图，首先这是一个商品，属于一级类。

设计IndexAd model来存大类下的广告位。

```
class IndexAd(models.Model):
    """
    首页类别标签右边展示的七个商品广告
    """
    category = models.ForeignKey(GoodsCategory, on_delete=models.CASCADE, related_name='category',verbose_name="商品类目")
    goods =models.ForeignKey(Goods, on_delete=models.CASCADE, related_name='goods')

    class Meta:
        verbose_name = '首页广告'
        verbose_name_plural = verbose_name

    def __str__(self):
        return self.goods.name
```


```python
	ad_goods = serializers.SerializerMethodField()

    def get_ad_goods(self, obj):
        goods_json = {}
        ad_goods = IndexAd.objects.filter(category_id=obj.id, )
        if ad_goods:
            good_ins = ad_goods[0].goods
            goods_json = GoodsSerializer(good_ins, many=False, context={'request': self.context['request']}).data
        return goods_json
```

>ad_goods实际上我们只需要它的id和一张图片就够了，不需要使用返回所有商品信息的
GoodsSerializer，自己取里面的字段做返回。
或再去写一个Serializer

编写对应的viewset

```python
class IndexCategoryViewset(mixins.ListModelMixin, viewsets.GenericViewSet):
    """
    首页商品分类数据
    """
    # 获取已经被我们设置顶部导航的数据
    queryset = GoodsCategory.objects.filter(is_tab=True, name__in=["生鲜食品", "酒水饮料"])
    serializer_class = IndexCategorySerializer
```

>这里配置酒水饮料和生鲜食品是因为我们后面添加brand和ad_goods可以挑着这两类添加


为该接口配置相应的url

```python
# 首页系列商品展示url
router.register(r'indexgoods', IndexCategoryViewset, base_name="indexgoods")
```

![mark](http://myphoto.mtianyan.cn/blog/180314/3gGj09k7Jd.png?imageslim)

可以看到我们成功的访问了首页的商品分类数据
