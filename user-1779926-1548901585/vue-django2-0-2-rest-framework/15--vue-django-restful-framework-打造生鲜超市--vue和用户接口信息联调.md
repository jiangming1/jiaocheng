## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

# Vue+Django REST framework实战

>使用Python3.6与Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

项目支持支付宝支付(暂不支持微信支付),支持手机短信验证码注册， 支持第三方登录。集成了sentry错误监控系统。

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节内容: vue和用户接口信息联调

## vue和用户接口信息联调

vue的个人中心资料详情组件

src/views/member/userinfo.vue

数据加载肯定是在create方法中。

```
        created () {
            this.getUserInfo();
        },
```

调用了getUserInfo方法

```
 getUserInfo () { //请求用户信息
              getUserDetail().then((response)=> {
                    this.userInfo = response.data;
                }).catch(function (error) {
                    console.log(error);
                });
            },
```

调用getUserDeatil接口,然后将响应数据的data作为Userinfo进行填充。

```
//获取用户信息
export const getUserDetail = () => { return axios.get(`${host}/users/1/`) }
```

将其中的host改为localhost

## 用户个人信息修改

为UserViewset配置update mixins

这里面承载了更新和部分更新的操作。

它接受put和patch请求，put实际是一种更新的操作，patch是部分更新。

```python
class UserViewset(CreateModelMixin, mixins.UpdateModelMixin, mixins.RetrieveModelMixin, viewsets.GenericViewSet):
```

vue中逻辑

```
<button class="btn_blue_1" style="border:none;" @click="confirmModify">确认修改</button>
```

确认修改调用的是confirmModify方法。

```
            confirmModify () { // 确认修改
                updateUserInfo(this.userInfo).then((response)=> {
                    alert('修改成功');
                }).catch(function (error) {
                    console.log(error);
                });
            }
```

这其中调用的是接口updateUserInfo

```
//修改用户信息
export const updateUserInfo = params => { return axios.patch(`${host}/users/1/`, params) }
```

将host改为localhost

>已发现待解决问题，更新用户username为中文乱码。以及修改用户姓名，身份认证失效


## 用户收藏功能

我们之前在UserFavViewSet中的Listmodelmixin已经将goods的id 和 这条收藏关系的id返回了。但是我们需要的是商品的信息，所以在Serializer中

添加一个Serializers

```python
class UserFavDetailSerializer(serializers.ModelSerializer):

    # 通过goods_id拿到商品信息。就需要嵌套的Serializer
    goods = GoodsSerializer()
    class Meta:
        model = UserFav
        fields = ("goods", "id")
```

收藏提交的时候只需要传递goods的id过来，userfavdetail获取的时候想要获取goods的详情。

动态的Serializer。

```python
    # 设置动态的Serializer
    def get_serializer_class(self):
        if self.action == "list":
            return UserFavDetailSerializer
        elif self.action == "create":
            return UserFavSerializer

        return UserFavSerializer
```

>也可以写一个单独的Serializer，只返回收藏中的简要信息。

src/views/member/collection.vue:

中create时调用getCollection

```
        created () {
            this.getCollection();
        },
```

getcollection方法获取getAllFavs接口返回的数据然后进行填充

```
            getCollection () { //获取收藏列表
              getAllFavs().then((response)=> {
                    this.collections = response.data;
                }).catch(function (error) {
                    console.log(error);
                });
            },
```

修改getallfav为本地接口

```
export const getAllFavs = () => { return axios.get(`${local_host}/userfavs/`) }
```

删除操作:

```
<a class="f6" @click="deletePro(index, item.goods.id)">删除</a>
```

删除操作调用deletePro,并将index索引与goods.id作为参数传入

```
            deletePro (index, id) { //删除收藏商品
                alert('您确定要从收藏夹中删除选定的商品吗？');
                delFav(id).then((response)=> {
                    this.collections.splice(index,1);
                    alert('已删除商品');
                }).catch(function (error) {
                    console.log(error);
                });
            }
```

splice删除vue里面的数据，使前端页面少一条。

而delfav删除后端数据库数据。

```
//取消收藏
export const delFav = goodsId => { return axios.delete(`${local_host}/userfavs/`+goodsId+'/') }
```

## 用户留言功能

删除，获取留言，添加用户留言。留言中可以上传文件。

后台接口viewset和配套的Serializer

user_operation/serializers.py:

```python
class LeavingMessageSerializer(serializers.ModelSerializer):
    user = serializers.HiddenField(
        default=serializers.CurrentUserDefault()
    )
    add_time = serializers.DateTimeField(read_only=True, format='%Y-%m-%d %H:%M')
    class Meta:
        model = UserLeavingMessage
        fields = ("user", "message_type", "subject", "message", "file", "id" ,"add_time")
```

user直接获取当前的用户，add_time设置只可读取，格式化。

```python
class LeavingMessageViewset(mixins.ListModelMixin, mixins.DestroyModelMixin, mixins.CreateModelMixin,
                            viewsets.GenericViewSet):
    """
    list:
        获取用户留言
    create:
        添加留言
    delete:
        删除留言功能
    """

    permission_classes = (IsAuthenticated, IsOwnerOrReadOnly)
    authentication_classes = (JSONWebTokenAuthentication, SessionAuthentication)
    serializer_class = LeavingMessageSerializer

    # 只能看到自己的留言
    def get_queryset(self):
        return UserLeavingMessage.objects.filter(user=self.request.user)
```

仅返回当前用户的message


配置一个配套的url

实现代码:

```python
# 配置用户留言的url
router.register(r'messages', LeavingMessageViewset, base_name="messages")
```

我们在删除的时候需要服务器给我们返回的id。前后端分离的系统，服务器返回完整地址可以直接被前端查看。

addtime应该是只返回不提交。由获取的当前时间自动填充。

### vue源码接口

```
//获取留言
export const getMessages = () => {return axios.get(`${host}/messages/`)}

//添加留言
export const addMessage = params => {return axios.post(`${host}/messages/`, params, {headers:{ 'Content-Type': 'multipart/form-data' }})}

//删除留言
export const delMessages = messageId => {return axios.delete(`${host}/messages/`+messageId+'/')}
```

将全部host改为localhost

如何将时间中的T 换为空格符号呢? 为add_time添加参数(format)

数据填充映射是如何完成的。组件为src/views/member/message.vue

```
        created () {
            this.getMessage();
        },
```

组件创建之前调用getmessage()

```
            getMessage () { //获取留言
              getMessages().then((response)=> {
                    console.log(response.data);
                    this.messageAll = response.data;
                }).catch(function (error) {
                    console.log(error);
                });
            },
```

```
//获取留言
export const getMessages = () => {return axios.get(`${local_host}/messages/`)}
```

获取到所有的message然后将值赋给messageAll用于填充

![mark](http://myphoto.mtianyan.cn/blog/180311/dK7KG5KI8m.png?imageslim)

这里的if 条件映射要与我们model中的保持一致

删除会调用deleteMessage

```
<a @click="deleteMessage(index, item.id)">删除</a>

            deleteMessage (index, id) { // 删除留言
                delMessages(id).then((response)=> {
                  alert("删除成功")
                  this.messageAll.splice(index,1);
                }).catch(function (error) {
                    console.log(error);
                });
            },
```

```
//删除留言
export const delMessages = messageId => {return axios.delete(`${local_host}/messages/`+messageId+'/')}
```

上传文件(前端完成的工作)

```
            submitMessage () { //提交留言
                const formData = new FormData();
                formData.append('file',this.file);
                formData.append('subject',this.subject);
                formData.append('message',this.message);
                formData.append('message_type',this.message_type);
                addMessage(formData).then((response)=> {
                    this.getMessage();

                }).catch(function (error) {
                    console.log(error);
                });
            },
```

用到了es中的formdata。将file append进来。

```
//添加留言
export const addMessage = params => {return axios.post(`${local_host}/messages/`, params, {headers:{ 'Content-Type': 'multipart/form-data' }})}
```

addmessage的时候为它传递了head进来指明了它的类型`multipart/form-data`。

drf是如何将文件解析并保存到我们的数据库中的呢。

文档中的parse部分。

![mark](http://myphoto.mtianyan.cn/blog/180311/hLJEj9H492.png?imageslim)

可以看到drf为我们提供的多种parse

MultiPartParser

model中的file我们可以设置upload_to

```python
    file = models.FileField(upload_to="message/images/", verbose_name="上传的文件", help_text="上传的文件")
```

## 用户收货地址列表页接口开发

收货地址也是放在user_operation中的。获取到所有的收货地址，修改某一个收货地址。
删除某一个收货地址。

需要列表，添加，更新，删除:继承增删改查的mixin，但是如果都需要，那么有有一个viewset已经帮我们做到了。

```
class ModelViewSet(mixins.CreateModelMixin,
                   mixins.RetrieveModelMixin,
                   mixins.UpdateModelMixin,
                   mixins.DestroyModelMixin,
                   mixins.ListModelMixin,
                   GenericViewSet):
```

我们只需要继承于`ModelViewSet`就行了。

添加一个配套的Serializer。

```python
class AddressSerializer(serializers.ModelSerializer):
    user = serializers.HiddenField(
        default=serializers.CurrentUserDefault()
    )
    add_time = serializers.DateTimeField(read_only=True, format='%Y-%m-%d %H:%M')

    class Meta:
        model = UserAddress
        fields = ("id", "user", "province", "city", "district", "address", "signer_name", "add_time", "signer_mobile")
```

用户为当前用户。

地址回填的时候，如果是一个地址要将它的省市区全部回填就要拆分出来，这样比较麻烦。所以我们设置三个字段进行存储。省 市 区域

viewset中进行配置

```
class AddressViewset(viewsets.ModelViewSet):
    """
    收货地址管理
    list:
        获取收货地址
    create:
        添加收货地址
    update:
        更新收货地址
    delete:
        删除收货地址
    """
    permission_classes = (IsAuthenticated, IsOwnerOrReadOnly)
    authentication_classes = (JSONWebTokenAuthentication, SessionAuthentication)
    serializer_class = AddressSerializer

    def get_queryset(self):
        return UserAddress.objects.filter(user=self.request.user)
```

在url中进行配置:

```
# 收货地址
router.register(r'address', , base_name="address")
```

![mark](http://myphoto.mtianyan.cn/blog/180311/d88Ik781Fk.png?imageslim)

>可选作业: 添加字段的验证。如手机号码等。

## vue和收货地址接口联调

vue中的关于收货地址的接口

```
//添加收货地址
export const addAddress = params => {return axios.post(`${host}/address/`, params)}

//删除收货地址
export const delAddress = addressId => {return axios.delete(`${host}/address/`+addressId+'/')}

//修改收货地址
export const updateAddress = (addressId, params) => {return axios.patch(`${host}/address/`+addressId+'/', params)}

//获取收货地址
export const getAddress = () => {return axios.get(`${host}/address/`)}
```

将这四个的host都有改为localhost

src/views/member/receive.vue：这是我们收货地址的组件

组件被创建时

```
        created () {
            this.getReceiveInfo();
        },
```

getreceiveInfo()调用getaddress接口

```
getReceiveInfo() { //获取收件人信息
                getAddress().then((response)=> {
                    console.log(response.data);
                    this.receiveInfoArr = response.data;

                }).catch(function (error) {
                    console.log(error);
                });

            },
```

receiveInfoArr进行数据的遍历。

```
        <v-distpicker :province="newInfo.province" :city="newInfo.city" :area="newInfo.district" @province="getProvince" @city="getCity" @area="getArea"></v-distpicker>
```

已知bug，设置西安市长安区，区不显示。
