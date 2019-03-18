## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

## 7-4 课程机构列表页数据展示2

### 前去html中进行数据填充

![mark](http://upload-images.jianshu.io/upload_images/1779926-3bc25b1c7b1fb235.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>可以看到所有城市是通过a标签，当前选中城市为active。

![mark](http://upload-images.jianshu.io/upload_images/1779926-40d118e1ee22f368.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

之后把下面的写死的城市删除掉。

![mark](http://upload-images.jianshu.io/upload_images/1779926-83dfdbfbaa20542d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这时就是我们在后台添加的数据了

![mark](http://upload-images.jianshu.io/upload_images/1779926-2eb22db915719213.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到每个课程机构都是一个dl

同理使用for循环。

### 如何将imageField转换为图片地址

数据库中img存放的是字符串：相对路径

![mark](http://upload-images.jianshu.io/upload_images/1779926-53103bff844f22bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-4e076b8495da8019.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图这种取法会取出一个相对地址。

![mark](http://upload-images.jianshu.io/upload_images/1779926-7c3f56ea58b34976.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将setting中配置的mediaurl放在前面可以补全地址。

### 设置media处理器

![mark](http://upload-images.jianshu.io/upload_images/1779926-8d0f3064ef7c0046.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-7b269fdf75aadd09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>注册之后，mediaurl将可以在html中使用

![mark](http://upload-images.jianshu.io/upload_images/1779926-bb9af0c094309efc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

图片还是没有显示。因为url中没有处理图片相应路径的url

Django1.9.8 urls.py:

```python
from django.views.static import serve
    # 处理图片显示的url,使用Django自带serve,传入参数告诉它去哪个路径找，我们有配置好的路径MEDIAROOT
    url(r'^media/(?P<path>.*)$', serve, {"document_root": MEDIA_ROOT })
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-9d831518aec0223a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Django2.0.1 urls.py:

```python
from django.views.static import serve
# 处理图片显示的url,使用Django自带serve,传入参数告诉它去哪个路径找，我们有配置好的路径MEDIAROOT
    re_path(r'^media/(?P<path>.*)', serve, {"document_root": MEDIA_ROOT })

```

### 完善xadmin的adminx为机构添加分类索引字段
organization/adminx.py

```python
# 机构课程信息管理器
class CourseOrgAdmin(object):
    list_display = ['name', 'desc','category', 'click_nums', 'fav_nums','add_time' ]
    search_fields = ['name', 'desc', 'category','click_nums', 'fav_nums']
    list_filter = ['name', 'desc','category' ,'click_nums', 'fav_nums','city__name','address','add_time']
```

### 去除加载小圈圈

static/css/style.css中scrollLoading置为空:

```
.scrollLoading {
}
```

>完成后台数据添加，列表页数据展示。对应7-3&7-4

## 7-5 列表分页功能

github搜索django-pure-pagination

```python
pip install django-pure-pagination
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-275219a33e37848b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

install app中添加:

```python
'pure_pagination',
```

可设置参数；

```
PAGINATION_SETTINGS = {
    'PAGE_RANGE_DISPLAYED': 10,
    'MARGIN_PAGES_DISPLAYED': 2,
    'SHOW_FIRST_PAGE_WHEN_INVALID': True,
}
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-58d6c360e4ae963f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`PAGE_RANGE_DISPLAYED`是总共会显示多少个page。(包括省略号，包括两边和中间)
`MARGIN_PAGES_DISPLAYED`是旁边会显示多少个。
`SHOW_FIRST_PAGE_WHEN_INVALID`:当输入页数不合法是否要跳到第一页

官方实例；

```python
from django.shortcuts import render_to_response

from pure_pagination import Paginator, EmptyPage, PageNotAnInteger

	# 尝试获取页数参数
    try:
        page = request.GET.get('page', 1)
    except PageNotAnInteger:
        page = 1
    # objects是取到的数据
    objects = ['john', 'edward', 'josh', 'frank']

    # Provide Paginator with the request object for complete querystring generation
    # 对于取到的数据进行分页处理。
    p = Paginator(objects, request=request)
    # 此时前台显示的就应该是我们此时获取的第几页的数据
    people = p.page(page)

    return render_to_response('index.html', {
        'people': people,
    }
```

我们对照着的实现：


```python
from pure_pagination import Paginator, EmptyPage, PageNotAnInteger
class OrgView(View):
    def get(self,request):
        # 查找到所有的课程机构
        all_orgs = CourseOrg.objects.all()
        # 总共有多少家机构使用count进行统计
        org_nums = all_orgs.count()
        # 取出所有的城市
        all_city = CityDict.objects.all()
        # 对课程机构进行分页
        # 尝试获取前台get请求传递过来的page参数
        # 如果是不合法的配置参数默认返回第一页
        try:
            page = request.GET.get('page', 1)
        except PageNotAnInteger:
            page = 1
        # 这里指从allorg中取五个出来，每页显示5个
        p = Paginator(all_orgs, 5, request=request)
        orgs = p.page(page)

        return render(request, "org-list.html", {
            "all_orgs":orgs,
            "all_city": all_city,
            "org_nums": org_nums,
        })

```

### 对于html中分页进行配置
>不再是objects，而是objectlist

![mark](http://upload-images.jianshu.io/upload_images/1779926-1493d3cb2a0b5ad9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>使用默认的render
![mark](http://upload-images.jianshu.io/upload_images/1779926-1b62adaffd345c87.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-de5a88f372285e72.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 自定义html的样式

![mark](http://upload-images.jianshu.io/upload_images/1779926-30dc35d6c19cf3fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

本小节完成对应commit:

>7-3, 4 & 5:完成列表数据展示列表分页功能：使用pure_pagination

## 7-6 分类筛选功能

![mark](http://upload-images.jianshu.io/upload_images/1779926-aa8b0670ecbf66e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当用户点击某一个city时对应加上参数city的id

### 在后台处理这个city

![mark](http://upload-images.jianshu.io/upload_images/1779926-39abc6680f10002d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

获取传入的参数进行进一步筛选。

![mark](http://upload-images.jianshu.io/upload_images/1779926-1ec9f66912ea1bc6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>将city_id传回html，使得可以知道哪个是选中的。

![mark](http://upload-images.jianshu.io/upload_images/1779926-6a43ae8fba8f53ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因为city.id是后端传回来的值是一个int。所以我们要做类型转换。

![mark](http://upload-images.jianshu.io/upload_images/1779926-1ae5d63ae1f5d7d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当city_id为空的时候，显示全部。

### 后台处理类别

```
  # 类别筛选
        category = request.GET.get('ct', "")
        if category:
            # 我们就在机构中作进一步筛选类别
            all_orgs = all_orgs.filter(category=category)
```

返回前台类别值以active

```
  return render(request, "org-list.html", {
            "all_orgs":orgs,
            "all_city": all_city,
            "org_nums": org_nums,
            "city_id":city_id,
            "category":category,
        })
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-72d903ef5200a1e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于类别做同样的ifequal判断

![mark](http://upload-images.jianshu.io/upload_images/1779926-2305e782e28994d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图所示进行城市与分类的联动:

当选择全部类别的时候，就只通过当前城市id。
当选择全部城市的时候，就只通过当前目录id。
当两者都选的时候使用&连接。

刚才统计机构数目过早，应该移到后面后面已经筛选过后，

```
 # 总共有多少家机构使用count进行统计
        org_nums = all_orgs.count()
```

### 课程机构排名

```
   # 热门机构,如果不加负号会是有小到大。
        hot_orgs = all_orgs.order_by("-click_nums")[:3]
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-ddd1ad924aa2a513.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

循环时内置变量forloop.counter取当前循环到第几次

待完成:点击排名机构的连接

### 课程机构排序。

学习人数，课程数

organization/models.py
CourseOrg

```python
 # 当学生点击学习课程，找到所属机构，学习人数加1
    students = models.IntegerField(default=0, verbose_name=u"学习人数")
    # 当发布课程就加1
    course_nums =  models.IntegerField(default=0, verbose_name=u"课程数")
```

```
makemigrations organization
migrate organization
```

前端页面学习人数，添加参数sort

![mark](http://upload-images.jianshu.io/upload_images/1779926-b05db6ec30dc16e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
 # 进行排序
        sort = request.GET.get('sort', "")
        if sort:
            if sort == "students":
                all_orgs = all_orgs.order_by("-students")
            elif sort == "courses":
                all_orgs = all_orgs.order_by("-course_nums")
```

添加选择效果

![mark](http://upload-images.jianshu.io/upload_images/1779926-504eec2c00e2a6c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
