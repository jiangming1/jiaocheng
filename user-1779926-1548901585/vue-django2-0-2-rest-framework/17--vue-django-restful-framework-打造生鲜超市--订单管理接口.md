## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节:订单管理接口

## 订单管理接口

首先理解一下购物车和订单之间的关系。

我们现在是做了一种最简单的实现就是把购物车中所有商品进行一起的结算
orderInfo model 里面有一个order_sn是不能为空的。

点击去结算之后为它生成一个订单。然后让用户去支付页面进行支付。如果用到viewset中国的create mixin。会对这些字段进行验证。但是用户是不可能post order_sn过来的。

那我们在model中设置该字段可以为空，这样验证字段时就没有问题了。

```
# unique订单号唯一
    order_sn = models.CharField(max_length=30, null=True, blank=True, unique=True, verbose_name="订单编号")
        # 以防用户支付到一半不支付了
    pay_status = models.CharField(choices=ORDER_STATUS, default="paying", max_length=30, verbose_name="订单状态")
```

trade_no是支付宝给我们返回的交易号码。

为什么不指向一个外键而是将值存起来呢？

因为用户有可能地址变动，将该地址删除了，但是地址相关联的订单作为历史数据不应该被改变。

orderInfo只有基本的信息，但是与订单相关的还有订单的商品信息。
所以我们设计了OrderGoods来保存订单的商品。

添加一个OrderViewSet来实现订单接口

```python
class OrderViewset(mixins.ListModelMixin, mixins.RetrieveModelMixin, mixins.CreateModelMixin, mixins.DestroyModelMixin,
                   viewsets.GenericViewSet):
```

为什么使用genericViewset，而不是ModelViewset。因为订单一般是不允许被修改的。
没有update这个mixin

list展示订单列表，create新建订单，DestroyModelMixin删除订单。

```
class OrderViewset(mixins.ListModelMixin, mixins.RetrieveModelMixin, mixins.CreateModelMixin, mixins.DestroyModelMixin,
                   viewsets.GenericViewSet):
    """
    订单管理
    list:
        获取个人订单
    delete:
        删除订单
    create：
        新增订单
    """
    permission_classes = (IsAuthenticated, IsOwnerOrReadOnly)
    authentication_classes = (JSONWebTokenAuthentication, SessionAuthentication)
    serializer_class = OrderSerializer

	def get_queryset(self):
        return OrderInfo.objects.filter(user=self.request.user)
```

在Serializers.py中添加一个OrderSerializer

```
class OrderSerializer(serializers.ModelSerializer):
    class Meta:
        model = OrderInfo
        fields = "__all__"
```

>提交订单的时候不会再提交每个商品，因为逻辑是一种比较简单的业务逻辑，直接清空购物车


- 将购物车中的数据添加到OrderGoods中。
- 清空购物车数据

它在create订单的时候还多了两步

我们可以在perform_create中写，实际默认调用的是Serializer的save方法

```
    def perform_create(self, serializer):
        order = serializer.save()
```

save之前订单号是必须要有的，在Serializer中。

```
    def generate_order_sn(self):
        # 当前时间+userid+随机数
        from random import Random
        random_ins = Random()
        order_sn = "{time_str}{userid}{ranstr}".format(time_str=time.strftime("%Y%m%d%H%M%S"),
                                                       userid=self.context["request"].user.id, ranstr=random_ins.randint(10, 99))
         return order_sn
```

>由时间串加用户id加随机字符串。10-99生成我们的订单编号.注意在Serializer中和view中取当前用户的不同方法。



```python
    def validate(self, attrs):
        attrs["order_sn"] = self.generate_order_sn()
        return attrs
```

在validate中做了之后，在view中就可以直接调用save了

```python
    def perform_create(self, serializer):
        order = serializer.save()
        # 获取到用户购物车里的商品
        shop_carts = ShoppingCart.objects.filter(user=self.request.user)
        for shop_cart in shop_carts:
            order_goods = OrderGoods()
            order_goods.goods = shop_cart.goods
            order_goods.goods_num = shop_cart.nums
            order_goods.order = order
            order_goods.save()

            shop_cart.delete()
        return order
```

现在我们就拿到order了。

获取到购物车里面的商品，然后进行for循环的遍历生成订单的商品项并保存。
清空购物车然后return order

然后配置一个url，用于我们的orderviewset

```python
# 订单相关url
router.register(r'orders', OrderViewset, base_name="orders")
```

![mark](http://myphoto.mtianyan.cn/blog/180311/Jda88eFdIa.png?imageslim)

此时用户被列出来了，而我们应该是选取当前默认用户并隐藏该选择框

OrderSerializer中

```python
    user = serializers.HiddenField(
        default=serializers.CurrentUserDefault()
    )
```

订单的状态不应该是用户可以提交的值。所以为订单状态添加readonly

```python
    pay_status = serializers.CharField(read_only=True)
    trade_no = serializers.CharField(read_only=True)
    order_sn = serializers.CharField(read_only=True)
    pay_time = serializers.DateTimeField(read_only=True)
```

同理可得的有交易号，订单号，支付时间等。

删除订单的时候，我们需要生成订单时返回的id。删除订单时我们希望的是将订单中的商品项一起删除掉。那么我们测试一下会一起删除么？

orderinfo 和 ordergoods一起被删掉了。

## vue个人中心订单接口调试

相关的接口位于

```
//获取订单
export const getOrders = () => { return axios.get(`${host}/orders/`) }
//删除订单
export const delOrder = orderId => { return axios.delete(`${host}/orders/`+orderId+'/') }
//添加订单
export const createOrder = params => {return axios.post(`${host}/orders/`, params)}
//获取订单详情
export const getOrderDetail = orderId => {return axios.get(`${host}/orders/`+orderId+'/')}
```

将这四个接口的host改为localhost。

首先是结算页面。订单的接口中没有商品的具体信息。

trade/views.py进行动态的Serializer的选择

```python
    def get_serializer_class(self):
        if self.action == "retrieve":
            return OrderDetailSerializer
        return OrderSerializer
```

trade/serializers.py新建OrderDetailSerializer类

```python
class OrderDetailSerializer(serializers.ModelSerializer):
    goods = OrderGoodsSerialzier(many=True)

    class Meta:
        model = OrderInfo
        fields = "__all__"
```

其中的goods字段嵌套一个OrderGoodsSerializer

trade/serializers.py添加OrderGoodsSerialzier:

```python
class OrderGoodsSerialzier(serializers.ModelSerializer):
    goods = GoodsSerializer(many=False)
    class Meta:
        model = OrderGoods
        fields = "__all__"
```

>orderGoodsSerializer用于goods详情的序列化。

```
class OrderGoods(models.Model):
    """
    订单内的商品详情
    """
    # 一个订单对应多个商品，所以添加外键
    order = models.ForeignKey(OrderInfo, on_delete=models.CASCADE, verbose_name="订单信息", related_name="goods")
```


订单与订单内的商品项是通过order进行关联的。

![mark](http://myphoto.mtianyan.cn/blog/180311/Ij0Fm83bk7.png?imageslim)

第一个goods实际上是外键关系，内层嵌套的goods才是我们的商品详情

![mark](http://myphoto.mtianyan.cn/blog/180311/DKglBmf976.png?imageslim)

订单状态是未支付的时候会显示支付宝支付的按钮。

### vue中订单逻辑

将数据映射到购物车，关键的一步是提交去结算

```
        <p class="sumup"><a class="btn" @click="balanceCount">去结算</a></p>
```

>点击去结算会调用balanceCount方法。

```
 balanceCount () { // 结算
          if(this.addrInfo.length==0){
              alert("请选择收货地址")
          }else{
            createOrder(
              {
                post_script:this.post_script,
                address:this.address,
                signer_name:this.signer_name,
                singer_mobile:this.signer_mobile,
                order_mount:this.totalPrice
              }
            ).then((response)=> {
              alert('订单创建成功')
              window.location.href=response.data.alipay_url;
            }).catch(function (error) {
              console.log(error);
            });
          }
      },
```

- 首先判断是否选择好了收货资质。

如果已经选择好了，那么创建订单。

```
//添加订单
export const createOrder = params => {return axios.post(`${local_host}/orders/`, params)}
```

向订单接口传递参数(post_script留言)，然后如果响应成功，弹出订单创建成功。并自动跳转到响应数据中提供的Alipay_url。


关于如何选择收货地址

```
selectAddr (id) { //选择配送地址
        this.addressActive = id;
        var cur_address = ''
        var cur_name = ''
        var cur_mobile = ''
        this.addrInfo.forEach(function(addrItem) {
          if(addrItem.id == id){
            cur_address = addrItem.province+addrItem.city+addrItem.district+addrItem.address
            cur_name = addrItem.signer_name
            cur_mobile = addrItem.signer_mobile
          }
        });
        this.address = cur_address
        this.signer_mobile = cur_mobile
        this.signer_name = cur_name
      },
```

选择收货地址后会更新状态值。

我的订单位于member里面的order.vue

实际上就是调用getorder()

```
        created () {
            this.getOrder();
        },

                    getOrder () {
                getOrders().then((response)=> {
                    this.orders = response.data;
                }).catch(function (error) {
                    console.log(error);
                });
            },
```

getorders接口就是

```
//获取订单
export const getOrders = () => { return axios.get(`${local_host}/orders/`) }
```

order detail的逻辑调用的是我们的getorderdetail，返回了订单的所有goods

```
        created () {
            this.orderId = this.$route.params.orderId;
            this.getOrderInfo();
            this.getReceiveByOrderId();
        },
```

```
getOrderInfo () { //获取订单信息
              getOrderDetail(this.orderId).then((response)=> {
                    this.orderInfo = response.data;
                    var totalPrice = 0
                    response.data.goods.forEach(function(entry) {
                      totalPrice += entry.goods_num*entry.goods.shop_price
                    });
                    this.totalPrice = totalPrice

                }).catch(function (error) {
                    console.log(error);
                });

            },
```

Orderinfo做一些数据的映射。
