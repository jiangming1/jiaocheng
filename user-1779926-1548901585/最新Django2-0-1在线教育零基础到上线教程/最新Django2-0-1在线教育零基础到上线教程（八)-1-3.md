## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

## 8-1 课程列表

拷贝课程列表页到template目录

创建课程相关的urls.py

Mxonline2/urls.py中声明包含到course的url中：

```
    # 课程app的url配置
    url(r"^course/", include('courses.urls', namespace="course")),
```

django2.0.1版本:

```
    # 课程app的url配置
    path("course/", include('courses.urls', namespace="course")),
```

书写处理列表展示相关的view

courses/views.py

```
class CourseListView(View):
    def get(self, request):
        return render(request, "course-list.html", { })
```

courses/urls.py

```
# encoding: utf-8
from courses.views import CourseListView

__author__ = 'mtianyan'
__date__ = '2018/1/13 0013 00:39'

from django.conf.urls import url

urlpatterns = [
    # 课程列表url
    url(r'^list/$', CourseListView.as_view(), name="list"),

]
```

django2.0.1版本:

```
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/1/13 0013 01:57'

# encoding: utf-8
from courses.views import CourseListView
from django.urls import path
app_name = "courses"
urlpatterns = [
    # 课程列表url
    path('list/', CourseListView.as_view(), name="list"),

]

```

此时访问没有样式。我们开始对于course list html进行工作
可以观察到它和orglist一样可以有共同的头尾。所以继承base页面

xadmin中添加一些课程。

然后在view中返回课程数据

```
class CourseListView(View):
    def get(self, request):
        all_course = Course.objects.all()
        return render(request, "course-list.html", {
            "all_course":all_course,
            
        })
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-3976f6f2f0d2d283.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

保留一个div

![mark](http://upload-images.jianshu.io/upload_images/1779926-2dde0d56e76b9904.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>通过外键字段取外键表中字段

### 分页功能

拷贝代码:

```
from pure_pagination import Paginator, EmptyPage, PageNotAnInteger
 # 对课程机构进行分页
        # 尝试获取前台get请求传递过来的page参数
        # 如果是不合法的配置参数默认返回第一页
        try:
            page = request.GET.get('page', 1)
        except PageNotAnInteger:
            page = 1
        # 这里指从allorg中取五个出来，每页显示5个
        p = Paginator(all_orgs, 4, request=request)
        orgs = p.page(page)
```

改动完成：

```
class CourseListView(View):
    def get(self, request):
        all_course = Course.objects.all()
        # 对课程进行分页
        # 尝试获取前台get请求传递过来的page参数
        # 如果是不合法的配置参数默认返回第一页
        try:
            page = request.GET.get('page', 1)
        except PageNotAnInteger:
            page = 1
        # 这里指从allorg中取五个出来，每页显示5个
        p = Paginator(all_course,6 , request=request)
        courses = p.page(page)
        return render(request, "course-list.html", {
            "all_course":courses,

        })
```


在html中使用时注意object_list

>此时的all_course已经不是一个queryset，而是一个purepage对象。

![mark](http://upload-images.jianshu.io/upload_images/1779926-26a2db1d15e596ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 对于页码进行修改

![mark](http://upload-images.jianshu.io/upload_images/1779926-64f30fd33b3311b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

直接把orglist中的那段拿过来就行了。自行替换变量名称

此时已经好了。

### 排序功能

将之前的sort逻辑拷贝过来:

```
# 进行排序
        sort = request.GET.get('sort', "")
        if sort:
            if sort == "students":
                all_orgs = all_orgs.order_by("-students")
            elif sort == "courses":
                all_orgs = all_orgs.order_by("-course_nums")
```

修改完成:

```
        # 进行排序
        sort = request.GET.get('sort', "")
        if sort:
            if sort == "students":
                all_course = all_course.order_by("-students")
            elif sort == "hot":
                all_course = all_course.order_by("-click_nums")
```

应放在分页之前。让分页处理所有筛选过的数据

return render时添加

```
            "sort":sort,
```

>用来判断激活状态。

![mark](http://upload-images.jianshu.io/upload_images/1779926-328124bfa5053a04.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-ed8c90123aad0aa3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

修改a标签参数

```
    # 热门课程推荐
        hot_courses = Course.objects.all().order_by("-students")[:3]
        return render
         "hot_courses":hot_courses
```

修改html中
![mark](http://upload-images.jianshu.io/upload_images/1779926-cbe1c790936b4ac4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

for循环填充内容

![mark](http://upload-images.jianshu.io/upload_images/1779926-684356fcee55fbfb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里的degree我们在数据库中填写的是字母。如何显示为中文。

- 个人猜测: template if

get_degree_display degree是字段名。专门用于choice字段显示

本小节完成对应commit：

>8-1完成课程列表页展示，分页，热门课程。

## 8-2 课程详情页1

拷贝course_detail进入template目录

可以看出这个页面也是继承base页面的。将course_list的页面框架拿过来

替换面包屑。

![mark](http://upload-images.jianshu.io/upload_images/1779926-d95e655c89cf141d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

配置url访问

django2.0.1：

```
    # 课程详情页
    re_path('course/(?P<course_id>\d+)/', CourseDetailView.as_view(), name="course_detail"),
```

书写对应访问的view

```
 课程详情处理view

class CourseDetailView(View):
    def get(self, request, course_id):
        return  render(request, "course-detail.html", {

        })
```

尝试访问：

在列表展示页放入详情的url。

![mark](http://upload-images.jianshu.io/upload_images/1779926-1e60d63aa8c0707a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

有参数类型的把参数也传进来

进行数据填充:先取出当前的课程

```
        # 此处的id为表默认为我们添加的值。
        course = Course.objects.get(id = int(course_id))

                return  render(request, "course-detail.html", {
            "course":course,
        })
```

html中取出数据:

![mark](http://upload-images.jianshu.io/upload_images/1779926-a789f4e6596bddea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

课程的章节数如何实现？

models.py中自定义方法

```
    def get_zj_nums(self):
        # 获取课程章节数的方法
        return self.lesson_set.all().count()
```

添加课程类别字段

```
    category = models.CharField(max_length=20, default=u"", verbose_name=u"课程类别")
```

```
makemigrations
migrate
```

operation中专门有张表是做用户学习记录的。

UserCourse查询有哪些学生学习了这门课

```
    # 获取学习这门课程的用户
    def get_learn_users(self):
        # 谁的里面添加了它做外键，他都可以取出来
        return self.usercourse_set.all()[:5]
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-09b470546eb4befa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

链式调用取出数据

添加一些用户课程进行验证

![mark](http://upload-images.jianshu.io/upload_images/1779926-5725ea8c1d16698b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到已经大功告成

课程详情的view中添加clicknums+1

```
        # 增加课程点击数
        course.click_nums += 1
        course.save()
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-899e69891bd0625a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 8-3 课程详情页2

![mark](http://upload-images.jianshu.io/upload_images/1779926-6a3617d392c3d732.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

tab_cont1 中填充我们自己的内容。

![mark](http://upload-images.jianshu.io/upload_images/1779926-2b24323d03b4aab7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

教师数自定义函数

```
def get_teacher_nums:
	return self.teacher_set.all().count
```

不用自定义函数的方法如下

![mark](http://upload-images.jianshu.io/upload_images/1779926-58fe976a62992781.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 课程是否相关

定义课程的tag ，如果tag相同，那么是相关课程。

courses/models.py:

```
    tag = models.CharField(max_length=15, verbose_name=u"课程标签", default=u"")
```

更改数据库后必然。此处略。

```
 tag = course.tag
        if tag:
        # 需要从1开始不然会推荐自己
            relate_courses = Course.objects.filter(tag=tag)[1:2]
        else:
            relate_courses = []
```

return render加上：

```
            "relate_courses":relate_courses,
```

### 收藏功能:

将block js写到页面底部。


```
<script type="text/javascript">
//收藏分享
function add_fav(current_elem, fav_id, fav_type){
    $.ajax({
        cache: false,
        type: "POST",
        url:"{% url "org:add_fav" %}",
        data:{'fav_id':fav_id, 'fav_type':fav_type},
        async: true,
        beforeSend:function(xhr, settings){
            xhr.setRequestHeader("X-CSRFToken", "{{ csrf_token }}");
        },
        success: function(data) {
            if(data.status == 'fail'){
                if(data.msg == '用户未登录'){
                    window.location.href="/login/";
                }else{
                    alert(data.msg)
                }

            }else if(data.status == 'success'){
                current_elem.text(data.msg)
            }
        },
    });
}

$('#jsLeftBtn').on('click', function(){
    add_fav($(this), {{ course.id }}, 1);
});

$('#jsRightBtn').on('click', function(){
    add_fav($(this), {{ course.course_org.id }}, 2);
});


</script>
```

刷新后又不见了的问题，从view中传递has_fav的参数。前台进行判断。

```
  # 是否收藏课程
        has_fav_course = False
        has_fav_org = False

        # 必须是用户已登录我们才需要判断。
        if request.user.is_authenticated:
            if UserFavorite.objects.filter(user=request.user, fav_id=course.id, fav_type=1):
                has_fav_course = True
            if UserFavorite.objects.filter(user=request.user, fav_id=course.course_org.id, fav_type=2):
                has_fav_org = True
```

return render

```
            "has_fav_course":has_fav_course,
            "has_fav_org":has_fav_org,
```

html中使用；

![mark](http://upload-images.jianshu.io/upload_images/1779926-16c181b9f582096d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-2f230e15debb69bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>8-2&3完成课程详情页展示，课程详情页机构，相关推荐课程。收藏课程，收藏机构。
