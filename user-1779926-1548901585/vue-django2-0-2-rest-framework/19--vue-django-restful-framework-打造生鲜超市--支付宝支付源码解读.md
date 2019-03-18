## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节:支付宝支付源码解读

## 支付宝支付源码解读

init的时候有一些参数。

```python
    def __init__(self, appid, app_notify_url, app_private_key_path,
                 alipay_public_key_path, return_url, debug=False):
        # appid是我们的appid或沙箱环境的appid
        self.appid = appid
        # 
        self.app_notify_url = app_notify_url
        # 我们私钥文件的路径
        self.app_private_key_path = app_private_key_path
        # 我们是要读文件然后生成private_key的
        self.app_private_key = None
        # 
        self.return_url = return_url
        # 打开这个文件，读这个文件，读完之后
        # 调用RSA的import_key(from Crypto.PublicKey import RSA)
        with open(self.app_private_key_path) as fp:
            self.app_private_key = RSA.importKey(fp.read())

        # 支付宝的公钥在链接生成的过程中实际没什么用。
        # 但是在验证支付宝给我们返回的消息的时候，有用
        self.alipay_public_key_path = alipay_public_key_path
        with open(self.alipay_public_key_path) as fp:
            self.alipay_public_key = RSA.import_key(fp.read())


        # debug是true会调用沙箱的gateway
        if debug is True:
            self.__gateway = "https://openapi.alipaydev.com/gateway.do"
        else:
            self.__gateway = "https://openapi.alipay.com/gateway.do"
```

direct_pay方法

```
	# 传递一些参数进来。交易标题，我们的订单号，总金额。
    def direct_pay(self, subject, out_trade_no, total_amount, return_url=None, **kwargs):
        biz_content = {
            "subject": subject,
            "out_trade_no": out_trade_no,
            "total_amount": total_amount,
            "product_code": "FAST_INSTANT_TRADE_PAY",
            # "qr_pay_mode":4
        }

        biz_content.update(kwargs)
        data = self.build_body("alipay.trade.page.pay", biz_content, self.return_url)
        return self.sign_data(data)
```

biz_content是一个很重要的参数。与订单相关的四个必填字段。

通过python传入可变参数。如果后面还有其他的值，只需要传进来就行了。

当biz_content生成好了之后，会调用build_body方法

```python
    def build_body(self, method, biz_content, return_url=None):
    	# data中字段与公共请求的必填字段一致
        data = {
            "app_id": self.appid,
            "method": method,
            "charset": "utf-8",
            "sign_type": "RSA2",
            "timestamp": datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
            "version": "1.0",
            "biz_content": biz_content
        }

        if return_url is not None:
            data["notify_url"] = self.app_notify_url
            data["return_url"] = self.return_url

        return data
```

参数有两种: 一种是公共请求参数，一种是请求参数(业务)。

整个参数以公共请求参数为主，这里面嵌套了bizcontent

build_body是生成公共请求参数(包含biz_content)

>如果传递进来的参数有return_url 那么我们就将return_url也放入data中

buildbody是用来生成整个消息格式的。

- 获取到消息格式的data

对于这个消息格式进行签名

```
    def sign_data(self, data):
        data.pop("sign", None)
        # 排序后的字符串
        unsigned_items = self.ordered_data(data)
        unsigned_string = "&".join("{0}={1}".format(k, v) for k, v in unsigned_items)
        sign = self.sign(unsigned_string.encode("utf-8"))
        # ordered_items = self.ordered_data(data)
        quoted_string = "&".join("{0}={1}".format(k, quote_plus(v)) for k, v in unsigned_items)

        # 获得最终的订单信息字符串
        signed_string = quoted_string + "&sign=" + quote_plus(sign)
        return signed_string
```

签名非常的关键。在签名之前，将sign字段先pop掉。(如果有，现在当然是没有的)

data就是一个清除了不必要参数的data
对于data进行排序。参数传进来进行排序

```
    def ordered_data(self, data):
        complex_keys = []
        for key, value in data.items():
            if isinstance(value, dict):
                complex_keys.append(key)

        # 将字典类型的数据dump出来
        for key in complex_keys:
            data[key] = json.dumps(data[key], separators=(',', ':'))

        对于data进行排序。参数传进来进行排序return sorted([(k, v) for k, v in data.items()])

```

对于data进行排序，生成有一个tuple。这是一个有顺序的tuple

![mark](http://myphoto.mtianyan.cn/blog/180312/4lLB4C3c7k.png?imageslim)

unsigned_items已经是排过序的一个数组了。


```
app_id=沙箱appid&biz_content={"subject":"\u6d4b\u8bd5\u8ba2\u5355","out_trade_no":"20180312mtianyan001","total_amount":9999,"product_code":"FAST_INSTANT_TRADE_PAY"}&charset=utf-8&method=alipay.trade.page.pay&notify_url=http://127.0.0.1:8000/alipay/return/&return_url=http://127.0.0.1:8000/alipay/return/&sign_type=RSA2&timestamp=2018-03-12 18:59:09&version=1.0
```

unsigned_string是拼接而成的字符串。

这时我们就要拿着这个字符串进行签名了。

```
sign = self.sign(unsigned_string.encode("utf-8"))

    def sign(self, unsigned_string):
        # 开始计算签名
        key = self.app_private_key
        signer = PKCS1_v1_5.new(key)
        signature = signer.sign(SHA256.new(unsigned_string))
        # base64 编码，转换为unicode表示并移除回车
        sign = encodebytes(signature).decode("utf8").replace("\n", "")
        return sign
```


首先要拿到我们的私钥，然后使用PKCS1_v1_5进行签名。

```
from Crypto.Signature import PKCS1_v1_5
```

生成一个签名的对象signer，调用该对象的sign函数。使用SHA256算法生成一个签名

生成签名还没有完，文档中说明了签名还有最后一步。并进行base64编码

```
from base64 import decodebytes, encodebytes
```

我们这里用到的是base64的encodebytes编码。编码之后我们要使用decode将其转换为utf-8字符串。

签名字符串完成。

得到sign的值之后

```
quoted_string = "&".join("{0}={1}".format(k, quote_plus(v)) for k, v in unsigned_items)
```

quote_plus会将url做一定的处理。区别在于不会让参数签名带http://等多余字符。

也就是签名用的是原始字符串，而我们的url请求时是往quote后的字符串中添加sign参数

最终的返回值被url接收。拼接上我们的调用沙箱接口url形成支付页面链接

这个订单我们也是可以进行支付的

支付宝支付成功之后会跳回到我们自己的页面。这是因为我们没有给他传递我们的return_url

支付完成之后立即跳回去的页面。

已支付的订单号再去生成的话会提示订单已支付。

会变成我们传递过去的url并跟一系列的参数。- 这就是我们return url的作用

那么问题来了，notify url是什么意思呢？

>用户扫码但是没有支付，然后将支付页面关闭了。用户如果在手机里的账单里支付
不是来这个页面支付，那么这个return url就没有用了。

return url 是我们在支付成功之后支付宝会自动跳转的页面。

notify url就是用户一旦完成支付(不在当前支付页面，而是通过如手机账单等)
支付宝会给你发起一个异步的请求。


>异步的请求就是说比如我们把页面关了之后，支付宝发了一个通知告诉你该账单已经被用户支付了。需要去系统里更改订单的状态啊等一些后续的工作。
那时候的url已经和支付宝产生一个异步的交互了。是不可能给浏览器再返回页面的。

>所以我们也需要一个异步接收的接口。return是一个同步接收的接口。

## 支付宝通知接口验证

对于return url 和 notify url 进行更加详细的介绍

上一节课我们已经可以生成支付宝支付的页面。

这是一个支付宝给我们发回来的请求。我们需要验证支付宝给我们返回的数据是否有效。
因为这个数据有可能是被别人截获。它会修改里面的数据，让订单状态变为成功支付。

验证数据是否对。与生成的加密过程正好是一个反过程

```python
    def verify(self, data, signature):
        if "sign_type" in data:
            sign_type = data.pop("sign_type")
        # 排序后的字符串
        unsigned_items = self.ordered_data(data)
        message = "&".join(u"{}={}".format(k, v) for k, v in unsigned_items)
        return self._verify(message, signature)
```

![mark](http://myphoto.mtianyan.cn/blog/180312/92bBHakfbf.png?imageslim)

把里面的sign pop出去，然后对剩下的部分进行签名，将签名之后的字符串与原来的进行比对就知道是否合法了。


```
    return_url = 'http://127.0.0.1:8000/?total_amount=100.00&timestamp=2017-08-15+23%3A53%3A34&sign=e9E9UE0AxR84NK8TP1CicX6aZL8VQj68ylugWGHnM79zA7BKTIuxxkf%2FvhdDYz4XOLzNf9pTJxTDt8tTAAx%2FfUAJln4WAeZbacf1Gp4IzodcqU%2FsIc4z93xlfIZ7OLBoWW0kpKQ8AdOxrWBMXZck%2F1cffy4Ya2dWOYM6Pcdpd94CLNRPlH6kFsMCJCbhqvyJTflxdpVQ9kpH%2B%2Fhpqrqvm678vLwM%2B29LgqsLq0lojFWLe5ZGS1iFBdKiQI6wZiisBff%2BdAKT9Wcao3XeBUGigzUmVyEoVIcWJBH0Q8KTwz6IRC0S74FtfDWTafplUHlL%2Fnf6j%2FQd1y6Wcr2A5Kl6BQ%3D%3D&trade_no=2017081521001004340200204115&sign_type=RSA2&auth_app_id=2016080600180695&charset=utf-8&seller_id=2088102170208070&method=alipay.trade.page.pay.return&app_id=2016080600180695&out_trade_no=20170202185&version=1.0'
    o = urlparse(return_url)
    query = parse_qs(o.query)
    processed_query = {}
    ali_sign = query.pop("sign")[0]
```

一定要pop一下，将里面的sign删除掉。

```
    def _verify(self, raw_content, signature):
        # 开始计算签名
        key = self.alipay_public_key
        signer = PKCS1_v1_5.new(key)
        digest = SHA256.new()
        digest.update(raw_content.encode("utf8"))
        if signer.verify(digest, decodebytes(signature.encode("utf8"))):
            return True
        return False
```

字符串，与支付宝给我们返回的sign进行比对。

有了验证之后我们就要去修改支付宝支付的订单的状态等的接口实现

写一个接口Alipayviewset

## django集成支付宝notify_url和return_url接口-1

不管是同步还是异步的支付，都会向我们发起notify url的post请求。

>return url 是一个非必填字段，只会在支付宝页面告诉我们支付成功。

支付结果异步通知。

https://docs.open.alipay.com/270/105902/

>对于PC网站支付的交易，在用户支付完成之后，支付宝会根据API中商户传入的notify_url，通过POST请求的形式将支付结果作为参数通知到商户系统。

页面回跳参数。

return_url 是一种同步返回的机制：会发起向我们服务器的get请求

写一个view，既可以处理post，又可以处理get。只需要配置一个url，就可以完成对于两种形式返回的适配

因为我们跟支付宝相关的这部分没有model。所以我们可以使用最底层的api view来完成

```
from rest_framework.views import APIView
class AlipayView(APIView):
```

- 定义get 和 post 函数，分别用于处理return url 和 notify url

```
    def get(self, request):
        """
        处理支付宝的return_url返回
        """
    def post(self, request):
        """
        处理支付宝的notify_url
        """
```

前往url中配置一个接口地址

```
from trade.views import AlipayView

# 支付宝支付相关接口
path('alipay/return/', AlipayView.as_view())
```

先处理异步请求。

先验证支付宝post请求是否进来。

将我们的utils 中的Alipay.py中的return url 和 notifyurl 都改为线上地址

如:

```
http://115.159.122.64:8000/alipay/return/
```

重要问题: 现在的修改都是本地的修改。

我们的解释器是线上的地址，如果在本地修改了什么都不做。不upload，还是会用服务器上的旧版代码进行调试。

- 注意要选定整个项目目录文件夹进行上传。

修改一下订单号。

- 这次我们只修改了Alipay.py我们可以只单独上传。

用手机扫码一下，它跳转到了post方法。

我们观察参数如下:

![mark](http://myphoto.mtianyan.cn/blog/180314/iJ9kK3A4G1.png?imageslim)

数据都存放在request.POST中，里面有sign等字段，我们拿着sign就可以去验签。
保证这个是支付宝给我们发过来的。

异步通知的参数:

- 签名: 
- 业务参数: trade_no是支付宝返回的交易凭证号
- out_trade_no: 我们自己平台的订单号
- Trade_status: 支付宝的交易状态

TRADE_FINISHED	交易完成	false（不触发通知）

TRADE_SUCCESS	支付成功	true（触发通知）

WAIT_BUYER_PAY	交易创建	false（不触发通知）

TRADE_CLOSED	交易关闭	false（不触发通知）


我们就可以把这些定义到我们的model中。

```
    ORDER_STATUS = (
        ("TRADE_SUCCESS", "成功"),
        ("TRADE_CLOSED", "超时关闭"),
        ("WAIT_BUYER_PAY", "交易创建"),
        ("TRADE_FINISHED", "交易结束"),
        ("paying", "待支付"),
    )
```

配置支付宝相关key的路径在setttings.py中

```
# 支付宝相关的key路径
private_key_path = os.path.join(BASE_DIR, 'apps/trade/keys/private_2048.txt')
ali_pub_key_path = os.path.join(BASE_DIR, 'apps/trade/keys/alipay_key_2048.txt')
```

编程post方法

```python
from utils.alipay import AliPay
from VueDjangoFrameWorkShop.settings import ali_pub_key_path, private_key_path

    def post(self, request):
        """
        处理支付宝的notify_url
        """
        # 1. 先将sign剔除掉
        processed_dict = {}
        for key, value in request.POST.items():
            processed_dict[key] = value

        sign = processed_dict.pop("sign", None)

        # 2. 生成一个Alipay对象
        alipay = AliPay(
            appid="",
            app_notify_url="http://115.159.122.64:8000/alipay/return/",
            app_private_key_path=private_key_path,
            alipay_public_key_path=ali_pub_key_path,  # 支付宝的公钥，验证支付宝回传消息使用，不是你自己的公钥,
            debug=True,  # 默认False,
            return_url="http://115.159.122.64:8000/alipay/return/"
        )

        # 3. 进行验签，确保这是支付宝给我们的
        verify_re = alipay.verify(processed_dict, sign)
```

![mark](http://myphoto.mtianyan.cn/blog/180314/9iAIJAEdg0.png?imageslim)

可以看到我们的processed_dict

```
from rest_framework.response import Response

        # 如果验签成功
        if verify_re is True:
            order_sn = processed_dict.get('out_trade_no', None)
            trade_no = processed_dict.get('trade_no', None)
            trade_status = processed_dict.get('trade_status', None)

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

                # 更新订单状态，填充支付宝给的交易凭证号。
                existed_order.pay_status = trade_status
                existed_order.trade_no = trade_no
                existed_order.pay_time = datetime.now()
                existed_order.save()
            # 将success返回给支付宝，支付宝就不会一直不停的继续发消息了。
            return Response("success")
```

如果验签失败，就不返回信息了。

其实return的逻辑和notify的差不多，只不过是从get中请求到数据罢了。

如果支付后希望可以调回到个人订单页面，这时候return url的作用就出来了。

```
    def get(self, request):
        """
        处理支付宝的return_url返回
        """
        processed_dict = {}
        # 1. 获取GET中参数
        for key, value in request.GET.items():
            processed_dict[key] = value
        # 2. 取出sign
        sign = processed_dict.pop("sign", None)

        # 3. 生成ALipay对象
        alipay = AliPay(
            appid="2016091200490210",
            app_notify_url="http://115.159.122.64:8000/alipay/return/",
            app_private_key_path=private_key_path,
            alipay_public_key_path=ali_pub_key_path,  # 支付宝的公钥，验证支付宝回传消息使用，不是你自己的公钥,
            debug=True,  # 默认False,
            return_url="http://115.159.122.64:8000/alipay/return/"
        )

        verify_re = alipay.verify(processed_dict, sign)

        # 这里可以不做操作。因为不管发不发return url。notify url都会修改订单状态。
        if verify_re is True:
            order_sn = processed_dict.get('out_trade_no', None)
            trade_no = processed_dict.get('trade_no', None)
            trade_status = processed_dict.get('trade_status', None)

            existed_orders = OrderInfo.objects.filter(order_sn=order_sn)
            for existed_order in existed_orders:
                existed_order.pay_status = trade_status
                existed_order.trade_no = trade_no
                existed_order.pay_time = datetime.now()
                existed_order.save()
```


## 支付宝和vue联调

流程: 退出之后重新登录，将localhost改为线上地址

- 在创建订单的过程中，会将购物车清空，创建order_goods,创建order对象
现在我们要涉及支付了，还要有支付的页面。如何生成支付的url呢

- 我们可以重写createModelMixin的Create方法。把支付宝支付的url也放进来。

### 不重载create，而是Serializer的另一个功能

Serializer fields 里面的SerializerMethodField

```
from django.contrib.auth.models import User
from django.utils.timezone import now
from rest_framework import serializers

class UserSerializer(serializers.ModelSerializer):
    days_since_joined = serializers.SerializerMethodField()

    class Meta:
        model = User

    def get_days_since_joined(self, obj):
        return (now() - obj.date_joined).days
```

可以让我们自己写函数，在函数里面写逻辑。这样就会变的灵活，不再依赖于数据表里的某一个字段了。

在我们的OrderSerializer中

在用户提交了订单的时候，我们可以在Serializer中加一个字段alipay_url 专门返回
支付宝的支付链接。不能让用户自己提交，是服务器端生成返回给用户的。

字段前面加上get就是用来跟它做对应的函数

```python
    alipay_url = serializers.SerializerMethodField(read_only=True)

    def get_alipay_url(self, obj):
        alipay = AliPay(
            appid="2016091200490210",
            app_notify_url="http://115.159.122.64:8000/alipay/return/",
            app_private_key_path=private_key_path,
            alipay_public_key_path=ali_pub_key_path,  # 支付宝的公钥，验证支付宝回传消息使用，不是你自己的公钥,
            debug=True,  # 默认False,
            return_url="http://115.159.122.64:8000/alipay/return/"
        )

```

>上线之后记得改为debug为false，参数改完之后就可以调用之前的逻辑了。


```python
        url = alipay.direct_pay(
            subject=obj.order_sn,
            out_trade_no=obj.order_sn,
            total_amount=obj.order_mount,
        )
        re_url = "https://openapi.alipaydev.com/gateway.do?{data}".format(data=url)

        return re_url
```

因为订单里会有多个商品，所以标题改为我们自己商户的订单号。
return_url不需要再传，

```
data = self.build_body("alipay.trade.page.pay", biz_content, self.return_url)

```

因为在Alipay中build_body的时候已经传进去过了。

别忘了服务器上传代码。

我们还要将这个逻辑拷贝到OrderDetailSerializer中

```python
alipay_url = serializers.SerializerMethodField(read_only=True)

    def get_alipay_url(self, obj):
        alipay = AliPay(
            appid="2016091200490210",
            app_notify_url="http://115.159.122.64:8000/alipay/return/",
            app_private_key_path=private_key_path,
            alipay_public_key_path=ali_pub_key_path,  # 支付宝的公钥，验证支付宝回传消息使用，不是你自己的公钥,
            debug=True,  # 默认False,
            return_url="http://115.159.122.64:8000/alipay/return/"
        )

        url = alipay.direct_pay(
            subject=obj.order_sn,
            out_trade_no=obj.order_sn,
            total_amount=obj.order_mount,
        )
        re_url = "https://openapi.alipaydev.com/gateway.do?{data}".format(data=url)

        return re_url
```

因为订单详情中未支付订单的立即使用支付宝支付也是需要跳转到支付宝的。

可以将appid，return_url等都配置到setting中去。可配置性会高一点

### 查看vue中是怎么做的。

src/views/cart/cart.vue中:

```
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
            }).
```

订单创建成功会跳转到接口返回数据中的alipay_url。

这里的留言是必填的，如果想改为可选。修改model。

![mark](http://myphoto.mtianyan.cn/blog/180314/lJhg5HhEBh.png?imageslim)

我们希望支付完成之后，跳转回我们的订单列表页面。

- 解决方案1: 支付宝返回的不是一个页面而是这个图片，让vue去显示图片。自己做页面内部的跳转。

它的return_url实际是服务器的一个url。服务器时无法通知我们的vue做更新的。

- 解决方案2

简单暴力的做法。让django来返回这个页面。不让node.js来代理这个页面。

就类似于一种template的机制了。这样就不存在跨域的问题了

### 将vue纳入django项目中

这关乎于部署方式。

vue有两种开发模式，一种是dev模式，一种是build模式。

```
npm run build
```

build是用来生成html 及静态文件的。

![mark](http://myphoto.mtianyan.cn/blog/180314/hl834e8EJH.png?imageslim)

首先将index.html拷贝出来放到template目录里。

![mark](http://myphoto.mtianyan.cn/blog/180314/2Id602GHeh.png?imageslim)

- 然后新建static目录

![mark](http://myphoto.mtianyan.cn/blog/180314/K61F2HE9dH.png?imageslim)

设置我们的setting文件中的MEDIA_URL

```
STATIC_URL = '/static/'

STATICFILES_DIRS = [
    os.path.join(BASE_DIR, "static"),
]
```

修改index.html中的引用

![mark](http://myphoto.mtianyan.cn/blog/180314/C9bkEjbjhc.png?imageslim)

```
    # 首页
    path('index/', TemplateView.as_view(template_name='index.html'))
```

静态文件出问题404:

```
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'static')

re_path('static/(?P<path>.*)', serve, {"document_root": STATIC_ROOT}),
```

此时我们可以通过http://远端ip地址:8000/index/访问到我们的首页

![mark](http://myphoto.mtianyan.cn/blog/180314/leh10HLa6g.png?imageslim)

为首页路径添加name

```
    path('index/', TemplateView.as_view(template_name='index.html'),name='index'),

```
trade/views.py中return的时候:

```python
from django.shortcuts import render, redirect

            response = redirect("index")
            # 希望跳转到vue项目的时候直接帮我们跳转到支付的页面
            response.set_cookie("nextPath","pay", max_age=3)
            return response
        else:
            response = redirect("index")
            return response
```

实际上在vue中写过一个逻辑。

src/router/index.js

```
//进行路由判断
router.beforeEach((to, from, next) => {
  var nextPath = cookie.getCookie('nextPath')
  console.log(nextPath)
  if(nextPath=="pay"){
    next({
      path: '/app/home/member/order',
    });
  }
```

如果cookie有nextpath直接跳转到订单。max_age尽量设置短一点，让它取一次就可以过期。

将order.vue中的success改为与我们model中一致的TRADE_SUCCESS

![mark](http://myphoto.mtianyan.cn/blog/180314/i39c77d5eD.png?imageslim)

src/views/member/orderDetail.vue

做同样的处理

还有一种模式是支付宝给我们一张图片。让vue嵌入图片。

参数: qr_pay_model设置为4.订单码，生成一张图片

>第十章完结

### 老师埋的几个坑。

支付宝接口会先发起post请求，再响应get请求。而returnurl的返回参数中并不包含我们的支付状态，所以会导致原本被post请求写入的支付状态被get请求获取不到的默认值none覆盖为空。

- 与vue联调时，会报错

```
Uncaught RangeError: Maximum call stack size exceeded
```

前端不熟，所以我将老师的setcookie，前端取值，如果有该值就跳转订单页面改为了后端实现

```
            response = redirect("/index/#/app/home/member/order")
            # response.set_cookie("nextPath","pay", max_age=3)
```

这都是我自己摸索debug出来的成果。如果对你有所帮助，还请打赏。
