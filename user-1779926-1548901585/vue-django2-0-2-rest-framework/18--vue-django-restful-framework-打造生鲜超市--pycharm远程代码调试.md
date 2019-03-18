## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节:pycharm远程代码调试

## pycharm远程代码调试

- 第三方支付和第三方登录都有一个回调的URl。一般指向服务器的ip地址。

要完成能够通过pycharm去调试远端的服务器。回调时就可以调试代码

### 如何将代码上传至远端服务器

![mark](http://myphoto.mtianyan.cn/blog/180312/cjBc1cBHA3.png?imageslim)

点击tools下的deploy点击config。

然后新建一个远程ftp。sftp

![mark](http://myphoto.mtianyan.cn/blog/180312/4iE8D79f5g.png?imageslim)

为了防止连接过一段时间自动退出来

```
yum install tmux
```

输入tumx，时间过长也不会关闭掉

![mark](http://myphoto.mtianyan.cn/blog/180312/H6jAD4bgbk.png?imageslim)

将本地地址与服务器上目录地址进行绑定映射。

代码同步过去了，不代表我们可以调试远程服务器的代码了，还必须在远程服务器建立一个虚拟环境。

以下内容来自老师博客: http://www.projectsedu.com/

1. 安装python3.6

- 获取

```
wget https://www.python.org/ftp/python/3.6.2/Python-3.6.2.tgz
tar -xzvf Python-3.6.2.tgz -C  /tmp
cd  /tmp/Python-3.6.2/

```
- 把Python3.6安装到 /usr/local 目录

```
./configure --prefix=/usr/local
make
make altinstall
```

- 更改/usr/bin/python链接

```
ln -s /usr/local/bin/python3.6 /usr/bin/python3
```

注意: 阿里云端口限制

安全组规则中新建规则

连接上之后新建数据库。将本地数据库进行同步。数据传输，选择服务器和它的数据库。


如果数据传输失败的话。工具里面有个服务器监控，里面有个变量

![mark](http://myphoto.mtianyan.cn/blog/180312/4jLkefikd4.png?imageslim)

修改max_allowed_packet大小

新建虚拟环境。centos下需要多运行一条

```
yum install python-setuptools python-devel
```

不同操作系统下文件的位置可能不同。

```
sudo find / -name virtualenvwrapper.sh
```

mysqlclient安装出错。

```
pip install -r requirement.txt
```

项目中setting.py里关于mysql的配置。

host应该写我们远程服务器的ip地址

在mysql的设置权限中设置了%就不会包括localhost

要点击项目的根目录再点击上传。

```
python manage.py runserver 0.0.0.0:8000
```

部署到外部服务器时需要加上allowed_HOSTS,这样才能执行外部ip的访问。

解释器配置服务器端的解释器。

ssh验证

- ip地址 
- 用户名
- 密码

要将python环境选择到我们新建的虚拟环境的python。会将服务器端的全部拷贝到本地。

debug。设置ip0.0.0.0然后启动代码。打断点进行调试。

## 支付宝公钥，私钥和沙箱环境的配置

对接支付宝支付功能我们需要干什么？

https://open.alipay.com/platform/home.htm

登陆之后，进入管理中心

![mark](http://myphoto.mtianyan.cn/blog/180312/GJbdaFaEmg.png?imageslim)

创建一个应用

我们可以拿到应用的appid。在真正上线的时候需要提交这些信息进行审核的。

微信支付和支付宝支付都是要求企业认证才可以完成的。个人开发者是不可以的。

沙箱环境，可以让我们不具备这些应用或者说应用审核还没通过的时候先开发

https://openhome.alipay.com/platform/appDaily.htm?tab=info

我们的重点是电脑网站支付。

https://docs.open.alipay.com/270

关于电脑支付的api一共有这么几种

![mark](http://myphoto.mtianyan.cn/blog/180312/93klj9cDE6.png?imageslim)

alipay.trade.page.pay	统一收单下单并支付页面接口

https://docs.open.alipay.com/270/alipay.trade.page.pay

文档中告诉了我们这些接口需要传递的参数。正式环境的地址，而我们实际要用到的是沙箱环境。将地址改为沙箱环境的。

请求参数

appid 也就是我们沙箱环境提供的appid。(正式的是我们自己的)

return url 参数实际是我们自己的url

charset	String: utf-8

sign_type: 商户生成签名字符串所使用的签名算法类型: 推荐使用RSA2

sign非常核心的一个参数，关系到能否成功。

https://docs.open.alipay.com/291/105974

windows平台的rsa密钥生成。

https://docs.open.alipay.com/291/105971/

点击下载工具。

打开工具中的说明

1. 双击脚本文件 “RSA签名验签工具.bat” 即运行RSA签名验签工具。
2. RSA2签名方式使用算法：SHA256withRSA。
   RSA签名方式使用算法：SHA1withRSA。
3. 签名验签工具不要放在文件路径名存在空格的文件夹下。

![mark](http://myphoto.mtianyan.cn/blog/180312/kLfI0gJmBC.png?imageslim)

因为我们使用的是python。所以是非python版本，长度选择2048

- 点击生成后，可以在路径下查看到

![mark](http://myphoto.mtianyan.cn/blog/180312/JehdIbD12k.png?imageslim)

- 前往沙箱环境上传公钥

![mark](http://myphoto.mtianyan.cn/blog/180312/4d2KIhEHmD.png?imageslim)

将我们刚才生成的公钥粘贴进去

![mark](http://myphoto.mtianyan.cn/blog/180312/mgbh67L3aJ.png?imageslim)

点击验证公钥正确性。点击下载使用签名工具

这个签名工具只支持java适用的pkcs8，所以我们不做验证了.

我们的私钥文件一定要妥善保管。将中文名改为英文名。

![mark](http://myphoto.mtianyan.cn/blog/180312/f9mJjlc7e7.png?imageslim)

- 重要步骤:

在公钥和私钥的字符串前后加上

```
-----BEGIN PRIVATE KEY-----
-----END PRIVATE KEY-----
```

此时要将沙箱环境中支付宝提供给我们的公钥也要取下来。

这个公钥在我们后面做订单查询状态的时候有用。

![mark](http://myphoto.mtianyan.cn/blog/180312/hIGAbbbfeE.png?imageslim)

新建alipay_key_2048.txt，然后将公钥拷入。

这里面最重要的是支付宝的公钥和我们自己的私钥

我们在请求支付宝的时候拿着我们的密钥进行加密，支付宝拿着我们给他上传的公钥去进行解密验证。


## 支付宝开发文档解读

将解释器设置为本地运行环境。

支付宝官方文档

- 电脑网站支付接口: 统一下单支付接口。

请求的地址以及参数。appid(沙箱或自己)

sign的生成很关键。

- timestamp 时间戳

- version 固定值

- biz_content 这个值很关键

sign & biz_content

业务请求参数的集合，最大长度不限，除公共参数外所有请求参数都必须放在这个参数中传递，具体参照各产品快速接入文档

>其他的字段都没有表明我们要支付多少钱等。所以只有通过该字段支付宝才能知道我们需要支付多少钱。产品名称。

请求参数:

- out_trade_no: 商户自己平台的订单号

在我们的trade中models中已经有该字段就是Order_sn

>这个订单号需要传递过去。因为它会将该订单号传递回来。

- product_code: 目前只支持FAST_INSTANT_TRADE_PAY。值固定，不用细究

- total_amount：实际就是订单的交易金额(单位为元，精确到小数点后两位)

- subject: 订单的标题

- body: 订单描述

后面的字段都是非必填字段。

### singn商户请求参数的签名串，详见签名

https://docs.open.alipay.com/291/105974

技术同学把1.APPID，2.应用私钥，3.支付宝公钥，配置在代码中，对请求内容进行签名，并对支付宝返回的内容进行验签。

sdk是没有python版本的。没有多大的参考价值

https://docs.open.alipay.com/291/106118

### 如何签名

1.筛选并排序

>获取所有请求参数(appid method等)，不包括字节类型参数，如文件、字节流，剔除sign字段，剔除值为空的参数，并按照第一个字符的键值ASCII码递增排序（字母升序排序），如果遇到相同字符则按照第二个字符的键值ASCII码递增排序，以此类推。

比如a是排在m之前的，要对所有参数进行排序

2.拼接

>将排序后的参数与其对应值，组合成“参数=参数值”的格式，并且把这些参数用&字符连接起来，此时生成的字符串为待签名字符串。

```
REQUEST URL: https://openapi.alipay.com/gateway.do
REQUEST METHOD: POST
CONTENT:
app_id=2014072300007148
method=alipay.mobile.public.menu.add
charset=GBK
sign_type=RSA2
timestamp=2014-07-24 03:07:50
biz_content={"button":[{"actionParam":"ZFB_HFCZ","actionType":"out","name":"话费充值"},{"name":"查询","subButton":[{"actionParam":"ZFB_YECX","actionType":"out","name":"余额查询"},{"actionParam":"ZFB_LLCX","actionType":"out","name":"流量查询"},{"actionParam":"ZFB_HFCX","actionType":"out","name":"话费查询"}]},{"actionParam":"http://m.alipay.com","actionType":"link","name":"最新优惠"}]}
sign=e9zEAe4TTQ4LPLQvETPoLGXTiURcxiAKfMVQ6Hrrsx2hmyIEGvSfAQzbLxHrhyZ48wOJXTsD4FPnt+YGdK57+fP1BCbf9rIVycfjhYCqlFhbTu9pFnZgT55W+xbAFb9y7vL0MyAxwXUXvZtQVqEwW7pURtKilbcBTEW7TAxzgro=
version=1.0
```

>appid 等于这个值，拼出来效果`app_id=2014072300007148`


拼完之后，用&符号连接起来。此时生成的字符串为待签名字符串

>前面的这些工作都只是为了生成一个字符串，这个字符串我们还要对他进行签名。
才能够最终生成我们的sign值。

使用各自语言对应的SHA256WithRSA(对应sign_type为RSA2)或SHA1WithRSA(对应sign_type为RSA)签名函数利用商户私钥(我们之前生成的那个)对待签名字符串进行签名，并进行Base64编码。

>不仅要签名，签名之后还要使用base64编码

4. 把生成的签名赋值给sign参数，拼接到请求参数中。

可以参考开放平台SDK源码中AlipaySignature.rsaSign方法。

### 开始编码

```
pip install pycryptodome
```

1. utils中新建Alipay.py

```
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/3/12 0012 17:07'

from datetime import datetime
from Crypto.PublicKey import RSA
from Crypto.Signature import PKCS1_v1_5
from Crypto.Hash import SHA256
from base64 import b64encode, b64decode
from urllib.parse import quote_plus
from urllib.parse import urlparse, parse_qs
from urllib.request import urlopen
from base64 import decodebytes, encodebytes

import json


class AliPay(object):
    """
    支付宝支付接口
    """
    def __init__(self, appid, app_notify_url, app_private_key_path,
                 alipay_public_key_path, return_url, debug=False):
        self.appid = appid
        self.app_notify_url = app_notify_url
        self.app_private_key_path = app_private_key_path
        self.app_private_key = None
        self.return_url = return_url
        with open(self.app_private_key_path) as fp:
            self.app_private_key = RSA.importKey(fp.read())

        self.alipay_public_key_path = alipay_public_key_path
        with open(self.alipay_public_key_path) as fp:
            self.alipay_public_key = RSA.import_key(fp.read())


        if debug is True:
            self.__gateway = "https://openapi.alipaydev.com/gateway.do"
        else:
            self.__gateway = "https://openapi.alipay.com/gateway.do"

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

    def build_body(self, method, biz_content, return_url=None):
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

    def ordered_data(self, data):
        complex_keys = []
        for key, value in data.items():
            if isinstance(value, dict):
                complex_keys.append(key)

        # 将字典类型的数据dump出来
        for key in complex_keys:
            data[key] = json.dumps(data[key], separators=(',', ':'))

        return sorted([(k, v) for k, v in data.items()])

    def sign(self, unsigned_string):
        # 开始计算签名
        key = self.app_private_key
        signer = PKCS1_v1_5.new(key)
        signature = signer.sign(SHA256.new(unsigned_string))
        # base64 编码，转换为unicode表示并移除回车
        sign = encodebytes(signature).decode("utf8").replace("\n", "")
        return sign

    def _verify(self, raw_content, signature):
        # 开始计算签名
        key = self.alipay_public_key
        signer = PKCS1_v1_5.new(key)
        digest = SHA256.new()
        digest.update(raw_content.encode("utf8"))
        if signer.verify(digest, decodebytes(signature.encode("utf8"))):
            return True
        return False

    def verify(self, data, signature):
        if "sign_type" in data:
            sign_type = data.pop("sign_type")
        # 排序后的字符串
        unsigned_items = self.ordered_data(data)
        message = "&".join(u"{}={}".format(k, v) for k, v in unsigned_items)
        return self._verify(message, signature)


if __name__ == "__main__":
    return_url = 'http://127.0.0.1:8000/?total_amount=100.00&timestamp=2017-08-15+23%3A53%3A34&sign=e9E9UE0AxR84NK8TP1CicX6aZL8VQj68ylugWGHnM79zA7BKTIuxxkf%2FvhdDYz4XOLzNf9pTJxTDt8tTAAx%2FfUAJln4WAeZbacf1Gp4IzodcqU%2FsIc4z93xlfIZ7OLBoWW0kpKQ8AdOxrWBMXZck%2F1cffy4Ya2dWOYM6Pcdpd94CLNRPlH6kFsMCJCbhqvyJTflxdpVQ9kpH%2B%2Fhpqrqvm678vLwM%2B29LgqsLq0lojFWLe5ZGS1iFBdKiQI6wZiisBff%2BdAKT9Wcao3XeBUGigzUmVyEoVIcWJBH0Q8KTwz6IRC0S74FtfDWTafplUHlL%2Fnf6j%2FQd1y6Wcr2A5Kl6BQ%3D%3D&trade_no=2017081521001004340200204115&sign_type=RSA2&auth_app_id=2016080600180695&charset=utf-8&seller_id=2088102170208070&method=alipay.trade.page.pay.return&app_id=2016080600180695&out_trade_no=20170202185&version=1.0'
    o = urlparse(return_url)
    query = parse_qs(o.query)
    processed_query = {}
    ali_sign = query.pop("sign")[0]


# 测试用例
    alipay = AliPay(
        # appid在沙箱环境中就可以找到
        appid="2016091200490210",
        # 这个值先不管，在与vue的联调中介绍
        app_notify_url="http://127.0.0.1:8000/alipay/return/",
        # 我们自己商户的密钥
        app_private_key_path="../trade/keys/private_2048.txt",
        # 支付宝的公钥
        alipay_public_key_path="../trade/keys/alipay_key_2048.txt",  # 支付宝的公钥，验证支付宝回传消息使用，不是你自己的公钥,
        # debug为true时使用沙箱的url。如果不是用正式环境的url
        debug=True,  # 默认False,

        # 先不用管，后面vue解释
        return_url="http://127.0.0.1:8000/alipay/return/"
    )

    for key, value in query.items():
        processed_query[key] = value[0]
    print (alipay.verify(processed_query, ali_sign))

    # 直接支付:生成请求的字符串。
    url = alipay.direct_pay(
        # 订单标题
        subject="测试订单2",
        # 我们商户自行生成的订单号
        out_trade_no="20180312mtianyan",
        # 订单金额
        total_amount=100,
        # return_url="http://127.0.0.1:8000/alipay/return/"
    )
    # 将生成的请求字符串拿到我们的url中进行拼接
    re_url = "https://openapi.alipaydev.com/gateway.do?{data}".format(data=url)

    print(re_url)
```

将打印出的url拿到浏览器中进行访问。这是沙箱环境为我们生成的url。可以直接进行支付。


支付必须要用沙箱环境为我们提供的买家账号，以及沙箱app

![mark](http://myphoto.mtianyan.cn/blog/180312/dCJib1AE1f.png?imageslim)

下载沙箱支付宝app
