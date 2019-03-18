## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节: 购物车功能

## 购物车功能需求分析和加入到购物车实现

在商品详情点击加入购物车，右上角购物车会新增商品

这是从后台取出来的数据，点击去结算页面。上面是商品，有多少件以及总价。

配送地址显示过来。

点击增加一个商品，数量会变。

- 添加商品多次，是在商品的数量上加一

已经有记录，最常见做法加一。如果没有，就添加新纪录。

>点击加一，减一。是单独做一个接口，还是直接更新数量。

完成功能做法有很多，使用哪一种实现方式，关系到后台的复杂性。

获取列表页，创建，update，删除。

注意shoppingcart中的返回为

```
    def __str__(self):
        return "%s(%d)".format(self.goods.name, self.nums)
```

完成我们的viewset。

```python
from rest_framework import viewsets

class ShoppingCartViewset(viewsets.ModelViewSet):
    """
    购物车功能
    list:
        获取购物车详情
    create：
        加入购物车
    delete：
        删除购物记录
    """
    permission_classes = (IsAuthenticated, IsOwnerOrReadOnly)
    authentication_classes = (JSONWebTokenAuthentication, SessionAuthentication)
```

我们要为我们的viewset准备配套的Serializer，新建Serializers.py

继承Serializer而不是modelSerializer。因为Serializer灵活性高

Model中的unique_together在商品的收藏关系中也用到过。在收藏中会只允许收藏一次，而我们现在想要的是重复添加时更新数量。

```python
        unique_together = ("user", "goods")
```

如果我们继承的是modelSerializer。那么它在create方法会进行is_vaild的验证。这样就无法进入我们自己的重复加1操作。

```python
class ShopCartSerializer(serializers.Serializer):
    # 使用Serializer本身最好, 因为它是灵活性最高的。
    user = serializers.HiddenField(
        default=serializers.CurrentUserDefault()
    )
    nums = serializers.IntegerField(required=True, label="数量",min_value=1,
                                    error_messages={
                                        "min_value":"商品数量不能小于一",
                                        "required": "请选择购买数量"
                                    })
    goods = serializers.PrimaryKeyRelatedField(required=True, queryset=Goods.objects.all())
```

goods是Serializer中的外键字段。查询出goods的object中的所有值。

http://www.django-rest-framework.org/api-guide/fields/

可以看到Serializer为我们天然提供的这些字段

外键等这些关系型的被放入了

http://www.django-rest-framework.org/api-guide/relations/

文档中使用的是modelSerializer。所以不需要指明queryset，而我们使用的是Serializer
所以我们要指明queryset

Serializer是没有提供save功能的，所以我们要来重写create方法

create方法传入的validated_data是数据已经经过validate之后的数据。

而initial_data是未经validate处理过的原始值。需要我们自己进行类型转换等。

- 在view中是可以直接从request中取出用户的，但是在Serializer里面不能直接从request中取。

>要从`user = self.context["request"].user`

```python
 def create(self, validated_data):
        user = self.context["request"].user
        nums = validated_data["nums"]
        goods = validated_data["goods"]

        existed = ShoppingCart.objects.filter(user=user, goods=goods)

        if existed:
            existed = existed[0]
            existed.nums += nums
            existed.save()
        else:
            existed = ShoppingCart.objects.create(**validated_data)

        return existed
```

如果存在的话，将这个对象取出，然后num+1，并保存。

- 不存在的话，就根据validate数据创建出shoppingcart对象。然后返回给前端

viewset中配置Serializer

```
    serializer_class = ShopCartSerializer
    def get_queryset(self):
        return ShoppingCart.objects.filter(user=self.request.user)
```

购物车列表页只获取自己的购物车内容

配置相关的url

```
# 收货地址
router.register(r'shopcarts', ShoppingCartViewset, base_name="shopcarts")
```

## 修改购物车中商品数量

与收藏一样，希望传递goods_id过来而不是传递关系的id。

```
    lookup_field = "goods_id"
```

本来默认的lookup_field是这个model的id主键。这样就可以通过商品id拿到。

![mark](http://myphoto.mtianyan.cn/blog/180311/IfHeICL07C.png?imageslim)

更新数量为5

报错:

```
NotImplementedError at /shopcarts/1/
`update()` must be implemented.
```

个人信息修改的时候并没有实现update方法，也没有什么问题。现在为什么要实现update方法。

Serializer继承于baseSerializer。但是Serializer并没有去重写update方法。

```python
    def update(self, instance, validated_data):
        raise_errors_on_nested_writes('update', self, validated_data)
        info = model_meta.get_field_info(instance)

        # Simply set each attribute on the instance, and then save it.
        # Note that unlike `.create()` we don't need to treat many-to-many
        # relationships as being a special case. During updates we already
        # have an instance pk for the relationships to be associated with.
        for attr, value in validated_data.items():
            if attr in info.relations and info.relations[attr].to_many:
                field = getattr(instance, attr)
                field.set(value)
            else:
                setattr(instance, attr, value)
        instance.save()

        return instance
```

而如上面代码所示，我们的modelSerializer实现了update方法。所以我们可以模仿update方法来实现我们的Serializer中的update。

>所以这就是我们继承modelSerializer，不需要实现update的原因

```
    def update(self, instance, validated_data):
        # 修改商品数量
        instance.nums = validated_data["nums"]
        instance.save()
        return instance
```

## vue和购物车接口联调

在获取到商品的单价，数量等之前我们还是要获取到商品的详情的。

>比如商品的名称，商品的id(跳转详情用)。商品的图片。

我们现在的Serializer里面只有goods的主键id。需要动态的设置Serializer

这个Serializer是一个动态Serializer。

```python
class ShopCartDetailSerializer(serializers.ModelSerializer):
    # 一条购物车关系记录对应的只有一个goods。
    goods = GoodsSerializer(many=False, read_only=True)
    class Meta:
        model = ShoppingCart
        fields = ("goods", "nums")
```

然后在views中进行动态的Serializer的选择

```python

    def get_serializer_class(self):
        if self.action == 'list':
            return ShopCartDetailSerializer
        else:
            return ShopCartSerializer
```

前端项目中的api.js文件。

实现代码:

```python
//获取购物车商品
export const getShopCarts = params => { return axios.get(`${host}/shopcarts/`) }
// 添加商品到购物车
export const addShopCart = params => { return axios.post(`${host}/shopcarts/`, params) }
//更新购物车商品信息
export const updateShopCart = (goodsId, params) => { return axios.patch(`${host}/shopcarts/`+goodsId+'/', params) }
//删除某个商品的购物记录
export const deleteShopCart = goodsId => { return axios.delete(`${host}/shopcarts/`+goodsId+'/') }
```

将上面四个接口的host 改为localhost

组件src/views/productDetail/productDetail.vue中的addShoppingCart

```
addShoppingCart () { //加入购物车
            addShopCart({
                goods: this.productId, // 商品id
                nums: this.buyNum, // 购买数量
            }).then((response)=> {
                this.$refs.model.setShow();
                // 更新store数据
                this.$store.dispatch('setShopList');

            }).catch(function (error) {
                console.log(error);
            });

```

添加商品到后台数据接口

```
export const addShopCart = params => { return axios.post(`${local_host}/shopcarts/`, params) }
```

传递goods，也就是商品的id。和购买的数量。

如果成功的话，会更新我们的购物车。

```
export const setShopList = makeAction(types.SET_SHOPLIST);
```

head部分会取出数据:

```
    computed: {
        ...mapGetters({
          goods_list: 'goods_list',
          userInfo:'userInfo'
        })
    },
```

head里的购物车里就会拿着这个数据做显示。

![mark](http://myphoto.mtianyan.cn/blog/180311/il9HbGlEjG.png?imageslim)

for循环遍历将商品进行显示。使用v-link使得可以跳转商品详情。

![mark](http://myphoto.mtianyan.cn/blog/180311/G87hEEELe7.png?imageslim)

价格这些都是从vuex中取得数据，所以会进行动态的数据绑定与更新

结算的时候添加一个商品的数量是一个patch的返回操作

src/views/cart/cart.vue

```
    created () {
      // 请求购物车商品
      getShopCarts().then((response)=> {
        console.log(response.data)
        // 更新store数据
        //this.goods_list = response.data;
        var totalPrice = 0
        this.goods.goods_list = response.data;
        response.data.forEach(function(entry) {
          totalPrice += entry.goods.shop_price*entry.nums
          console.log(entry.goods.shop_price);
        });

        this.goods.totalPrice = totalPrice
        this.totalPrice = totalPrice
      }).catch(function (error) {
      });
      this.getAllAddr ()

    },
```

create的时候调用getShopCarts取获取购物车商品数据

计算总价格，总价格会被映射到页面中。会将后端传过来的数据放给goods。

for循环拿着goods_list做字段的显示。

```
 reduceCartNum(index, id) { //删除数量
        if(this.goods.goods_list[index].nums<=1){
          this.deleteGoods(index, id)
        }else{
          updateShopCart(id,{
            nums: this.goods.goods_list[index].nums-1
          }).then((response)=> {
            this.goods.goods_list[index].nums = this.goods.goods_list[index].nums - 1;
            // 更新store数据
            this.$store.dispatch('setShopList');
            //更新总价
            this.setTotalPrice();

          }).catch(function (error) {
            console.log(error);
          });
        }
```

不停的减少，如果小于1，那么调用删除goods将这个商品删除。

成功了的话就要去更新vuex。因为全局的head里面也用到了这个数据。只有通过修改全局的vuex1的数据，才能让大家的数据保持一致。

![mark](http://myphoto.mtianyan.cn/blog/180311/c0KacA9K4K.png?imageslim)

配送地址的填充来自addrInfo

```
getAllAddr () { //获得所有配送地址
        getAddress().then((response)=> {
          this.addrInfo = response.data;
        }).catch(function (error) {
          console.log(error);
        });
      },
```

获取getAddress()之前的配置。

选择地址，购物车商品加支付方式就可以完整的变成一个订单
