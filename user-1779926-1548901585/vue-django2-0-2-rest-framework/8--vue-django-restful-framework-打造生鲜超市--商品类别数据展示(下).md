## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

Vue+Django REST framework实战

>使用Python3.6与Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

项目支持支付宝支付(暂不支持微信支付),支持手机短信验证码注册， 支持第三方登录。集成了sentry错误监控系统。

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

## vue展示商品列表页数据

1. 点击某一个导航的一级类，将数据显示出来。或点击搜索，搜索出结果

这两个功能要一起完成: 格局是一样的，左侧导航，右侧商品列表。

- 点击某一个导航分类会显示它底下的二级三级分类，一级它有多少商品。
- 搜索只会显示一级与二级分类。

- 商品列表页有他的分页和排序。

当我们点击生鲜食品和搜索都用的是同一个左侧nav组件。但是它又有所区别

点击搜索，它请求的是所有的category显示到二级目录，而点击生鲜食品。它只请求当前类别下的二级和三级目录。

如何才能在一个组件中区别出世导航栏过来的，还是搜索页面过来的。

在head里面就能找到这两者vue router的区别。

![mark](http://myphoto.mtianyan.cn/blog/180304/FFCIfCL5iB.png?imageslim)

可以看到不管是通过点击弹出的还是导航都是跳转到上图所示的vue router

![mark](http://myphoto.mtianyan.cn/blog/180304/fdklIm9JIk.png?imageslim)

点击热搜榜则跳转的是search router

- 查看vue router里面的跳转逻辑

src/router/index.js

![mark](http://myphoto.mtianyan.cn/blog/180304/7aGG6eJlEg.png?imageslim)

list为商品分类，而search为搜索

![mark](http://myphoto.mtianyan.cn/blog/180304/ADL17HB8Hk.png?imageslim)

可以看出component指向同一个组件。只是url不一样。当然url附带的参数也不一样；

>一个是id 一个是keyword

src/views/list/list.vue 中

在页面生命周期的create方法中进行了getalldata的调用:

```
created () {
            this.getAllData ();
        },
```

getAllData中我们可以获取到`this.$route.params`虽然共用的是一个组件，但是路径里传的参数不同。

```
  getAllData () {
                console.log(this.$route.params)
                if(this.$route.params.id){
                    this.top_category = this.$route.params.id;
                    this.getMenu(this.top_category); // 获取左侧菜单列表
                }else{
                    this.getMenu(null); // 获取左侧菜单列表
                    this.pageType = 'search'
                    this.searchWord = this.$route.params.keyword;
                }

                this.getCurLoc(); // 获取当前位置
                this.getListData(); //获取产品列表
                this.getPriceRange(); // 获取价格区间
            },
```

>如果传过来的是id，那么会是上面代码中的getMenu,传入当前的category的id作为参数。

获取到某一个category的二级三级分类。

```
getMenu(id) {
                if(id != null){
                  getCategory({
                    id:this.$route.params.id
                  }).then((response)=> {
                    this.cateMenu = response.data.sub_cat;
                    this.currentCategoryName = response.data.name
                  }).catch(function (error) {
                    console.log(error);
                  });
                }else {
                  getCategory({}).then((response)=> {
                    this.cateMenu = response.data;
                    this.isObject = false
                  }).catch(function (error) {
                    console.log(error);
                  });
                }

            },
```

如果id不为空，也就是分类查询二级三级，那么则调用getCategory，将当前的cateMenu和currentCategoryName 使用response.data中的进行填充。

src/views/list/list-nav/listNav.vue:

![mark](http://myphoto.mtianyan.cn/blog/180304/aKfj5KgldE.png?imageslim)

可以看到传递进来的参数，我们是在list中获取到的数据想要传递到listnav中来，就要用

```
<list-nav :currentCategoryName="currentCategoryName" :cateMenu="cateMenu" :proNum="proNum" :isObject="isObject" @on-change="changeMenu"></list-nav>
```

通过冒号后面加变量及参数名。将当前页内值传递到list_nav.因为不止要获得类别，还要获取商品一共多少件。

左侧的列表调用的都是getCategory函数，之前我们已经将其配置为本地化数据。

### 商品的列表页

获取产品的列表会判断我们是只几天的search。还是要获取商品getGoods

```
getListData() {
                if(this.pageType=='search'){
                  getGoods({
                    search: this.searchWord, //搜索关键词
                  }).then((response)=> {
                    this.listData = response.data.results;
                    this.proNum = response.data.count;
                  }).catch(function (error) {
                    console.log(error);
                  });
                }else {
                  getGoods({
                    page: this.curPage, //当前页码
                    top_category: this.top_category, //商品类型
                    ordering: this.ordering, //排序类型
                    pricemin: this.pricemin, //价格最低 默认为‘’ 即为不选价格区间
                    pricemax: this.pricemax // 价格最高 默认为‘’
                  }).then((response)=> {

                    this.listData = response.data.results;
                    this.proNum = response.data.count;
                  }).catch(function (error) {
                    console.log(error);
                  });
                }

            },
```

>搜索的时候不需要知道当前的页码，排序规则。而如果page_type不是搜索(page_type的设定是位于getalldata中根据请求参数判断设定的)，那么我们可以调用getGoods

getGoods位于api.js中

```
//获取商品列表
export const getGoods = params => { return axios.get(`${local_host}/goods/`, { params: params }) }
```

getListData中的getgoods可以看到前端使用的参数名称。

我们可以前往view中将分页的参数修改为page与前端保持一致。

```
    page_query_param = "page"
```

参数top_category就是我们的第一级别。它是将我们参数中的id值传入。

因此在后台我们也需要配套的做一个category的过滤器。

如何获取一级分类下的所有商品。

goods/filters.py：class GoodsFilter

```
    top_category = filters.NumberFilter(name="category",method='top_category_filter')

    def top_category_filter(self, queryset, name, value):
        # 不管当前点击的是一级目录二级目录还是三级目录。
        return queryset.filter(Q(category_id=value)|Q(category__parent_category_id=value)|Q(category__parent_category__parent_category_id=value))
```

然后将queryset return回去。


pricemin以及pricemax与我们的前端代码中的不一致。

所以修改goods/filters.py中的与前端保持一致。

分页数据中会自带一个count值。

![mark](http://myphoto.mtianyan.cn/blog/180304/a6lHeGe1H3.png?imageslim)

往上翻一点； getListdata中的getGoods里面我们获取到了response.data.count;
然后存入了proNum中。proNum是我们当前页面data中的变量，通过list nav:传递到了list nav页面

```
                <list-sort @on-sort="changeSort" :proNum="proNum"></list-sort>
```

>list sort也是同样传递了这个变量过去。

- 设置分页size与前端保持一致

```
class GoodsPagination(PageNumberPagination):
    page_size = 12
```

- GoodsListViewSet中的order字段与前端保持一致

```
    ordering_fields = ('sold_num', 'shop_price')
```

### vue的商品搜索功能

```
search_fields = ('name', 'goods_brief', 'goods_desc')
```

没有需要配置的接口，因为search仍然调用的是后端的goods接口。

价格区间

```
<price-range :priceRange="priceRange" @on-change="changePrice"></price-range>
```

为price-range组件传递了priceRange参数

```
 getPriceRange () {
                this.$http.post('/priceRange', {
                    params: {
                        proType: this.type, //商品类型
                    }
                }).then((response)=> {

                    this.priceRange = response.data;
                }).catch(function (error) {
                    console.log(error);
                });
            },
```

getPriceRange会在getAllData也就是crate生命周期被调用。

$http.post是发起向mock中的数据接口的请求。

```
Mock.mock('/priceRange',
```

>推荐将这些数据设置到后台中。价格区间根据不同的商品类别等计算。
