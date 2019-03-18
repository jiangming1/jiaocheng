## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

## 9-1 讲师列表页

teacherlist 和 teacher detail 一起放到template目录之下

继承base页面

![mark](http://upload-images.jianshu.io/upload_images/1779926-e5db223afd96b52f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 书写view与配置url

```
    # 讲师列表
    path('teacher_list/', TeacherListView.as_view(), name="teacher_list"),
```

添加讲师的年龄字段

```
    age = models.IntegerField(default=18, verbose_name=u"年龄")
```

### 分页仿照orglist 注意object_list

view

```
# 课程讲师列表页
class TeacherListView(View):
        def get(self, request):
            all_teacher = Teacher.objects.all()
            # 总共有多少老师使用count进行统计
            teacher_nums = all_teacher.count()
            # 对讲师进行分页
            # 尝试获取前台get请求传递过来的page参数
            # 如果是不合法的配置参数默认返回第一页
            try:
                page = request.GET.get('page', 1)
            except PageNotAnInteger:
                page = 1
            # 这里指从allorg中取五个出来，每页显示5个
            p = Paginator(all_teacher, 4, request=request)
            teachers = p.page(page)
            return render(request, "teachers-list.html", {
            "all_teacher":teachers,
            "teacher_nums":teacher_nums
            })
```

### 排序 & 讲师排行榜

```
 sort = request.GET.get("sort", "")
            if sort:
                if sort == "hot":
                    all_teacher = all_teacher.order_by("-click_nums")
```

```
 "sort":sort
```

将sort return到前端。实现active

排行榜讲师

```
            # 排行榜讲师
            rank_teacher = Teacher.objects.all().order_by("-fav_nums")[:5]
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-bd7a8603f7da3e4f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

forloop.count 取出当前是第几次循环

## 9-2 讲师详情页

![mark](http://upload-images.jianshu.io/upload_images/1779926-45db0547327267a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 配置url和view

列表页中配置入口


```
# 教师详情页面

class TeacherDetailView(View):
    def get(self, request, teacher_id):
        teacher = Teacher.objects.get(id = int(teacher_id))
        all_course = teacher.course_set.all()
        # 排行榜讲师
        rank_teacher = Teacher.objects.all().order_by("-fav_nums")[:5]

        has_fav_teacher = False
        if UserFavorite.objects.filter(user=request.user, fav_type=3, fav_id= teacher.id):
            has_fav_teacher = True
        has_fav_org = False
        if  UserFavorite.objects.filter(user=request.user, fav_type=2, fav_id= teacher.org.id):
            has_fav_org = True
        return render(request, "teacher-detail.html", {
            "teacher":teacher,
            "all_course":all_course,
            "rank_teacher":rank_teacher,
            "has_fav_teacher":has_fav_teacher,
            "has_fav_org":has_fav_org,
        })

```

```
    # 访问机构讲师
    re_path('teacher/detail/(?P<teacher_id>\d+)/', TeacherDetailView.as_view(), name="teacher_detail"),
```

>第九章完结
