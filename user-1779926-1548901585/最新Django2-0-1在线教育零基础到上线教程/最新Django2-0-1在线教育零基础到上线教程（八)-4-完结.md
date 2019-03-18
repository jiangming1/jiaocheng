## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

## 8-4 课程章节信息

章节信息，评论信息。

course comments 和 course video放入 template

它也有head和foot继承我们的base页面

![mark](http://upload-images.jianshu.io/upload_images/1779926-1588d6c27ba74039.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

配置相应的url:

```python
# 处理课程章节信息页面的view

class CourseInfoView(View):
    def get(self, request, course_id):
        # 此处的id为表默认为我们添加的值。
        course = Course.objects.get(id=int(course_id))
        # 是否收藏课程
        return render(request, "course-video.html", {
            "course": course,
        })

```

```
    # 课程章节信息页
    url(r'^info/(?P<course_id>\d+)/$', CourseInfoView.as_view(), name="course_info"),
```

用户点击开始学习链接修改

![mark](http://upload-images.jianshu.io/upload_images/1779926-d34dbb985ee59d1f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为课程添加章节以及视频。

## 8-5 章节视频信息

为video表添加视频对应的url信息。

```
  url = models.CharField(max_length=200, default="http://blog.mtianyan.cn/" ,verbose_name=u"访问地址")
```

将章节信息填充进页面

通过课程可以找到章节:course.lesson_set

![mark](http://upload-images.jianshu.io/upload_images/1779926-f4da10e0b50885e7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

video的时长添加

```
 # 使用分钟做后台记录(存储最小单位)前台转换
    learn_times = models.IntegerField(default=0, verbose_name=u"学习时长(分钟数)")
```

资源下载功能:


后台自行上传点文件

```
all_resources = CourseResource.objects.filter(course=course)

          "all_resources":all_resources,
```

或者前端直接:

![mark](http://upload-images.jianshu.io/upload_images/1779926-8d93aa48922aed99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

创建课程与讲师之间的外键关联

```
    teacher = models.ForeignKey(Teacher,verbose_name=u"讲师", null=True, blank=True)
```

前往课程，设置讲师。

**注意:不要在Unicode方法里使用外键字段很容易报错。**

增加课程需知字段和老师告诉你学什么？

```
  you_need_know = models.CharField(max_length=300, default=u"一颗勤学的心是本课程必要前提",verbose_name=u"课程须知")
    teacher_tell = models.CharField(max_length=300, default=u"按时交作业,不然叫家长",verbose_name=u"老师告诉你")
```

将这两个字段显示到页面。

对应commit:

8-4&5完成课程章节信息，课程资源，课程老师信息。

## 8-6 课程评论页面

配置课程评论的url和view

```
class CommentsView(View):
    def get(self, request, course_id):
        # 此处的id为表默认为我们添加的值。
        course = Course.objects.get(id=int(course_id))
        all_resources = CourseResource.objects.filter(course=course)
        return render(request, "course-comment.html", {
            "course": course,
            "all_resources": all_resources,
        })

```

```
# 课程章节信息页
    re_path('comments/(?P<course_id>\d+)/', CommentsView.as_view(), name="course_comments"),
```

course video中跳转到评论链接

发表评论功能

ajax操作。如果发布成功就会刷新页面。

新建view用于添加评论:

```
# ajax方式添加评论
class AddCommentsView(View):
    def post(self, request):
        if not request.user.is_authenticated:
            # 未登录时返回json提示未登录，跳转到登录页面是在ajax中做的
            return HttpResponse('{"status":"fail", "msg":"用户未登录"}', content_type='application/json')
        course_id = request.POST.get("course_id", 0)
        comments = request.POST.get("comments", "")
        if int(course_id) > 0 and comments:
            course_comments = CourseComments()
            # get只能取出一条数据，如果有多条抛出异常。没有数据也抛异常
            # filter取一个列表出来，queryset。没有数据返回空的queryset不会抛异常
            course = Course.objects.get(id = int(course_id))
            # 外键存入要存入对象
            course_comments.course = course
            course_comments.comments = comments
            course_comments.user = request.user
            course_comments.save()
            return HttpResponse('{"status":"success", "msg":"评论成功"}', content_type='application/json')
        else:
            return HttpResponse('{"status":"fail", "msg":"评论失败"}', content_type='application/json')
```

添加配套的url:

```python
    # 添加课程评论,已经把参数放到post当中了
    path('add_comment/', AddCommentsView.as_view(), name="add_comment"),
```

js代码

```
<script type="text/javascript">
    //添加评论
    $('#js-pl-submit').on('click', function(){
        var comments = $("#js-pl-textarea").val()
        if(comments == ""){
            alert("评论不能为空")
            return
        }
        $.ajax({
            cache: false,
            type: "POST",
            url:"{% url 'course:add_comment' %}",
            data:{'course_id':{{ course.id }}, 'comments':comments},
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
                    window.location.reload();//刷新当前页面.
                }
            },
        });
    });

</script>
```

后端已经将all_comments传过来了。然后for循环输出。

本小节完毕对应commit:

```
8-6完成课程评论功能，添加评论并展示到页面。
```

## 8-7 相关课程推荐:

>学过该课程的还学过

CourseInfoView

添加

```python
# 选出学了这门课的学生关系
        user_courses = UserCourse.objects.filter(course= course)
        # 从关系中取出user_id
        user_ids = [user_course.user_id for user_course in user_courses]
        # 这些用户学了的课程,外键会自动有id，取到字段
        all_user_courses = UserCourse.objects.filter(user_id__in=user_ids)
        # 取出所有课程id
        course_ids = [all_user_course.course_id for all_user_course in all_user_courses]
        # 获取学过该课程用户学过的其他课程
        relate_courses = Course.objects.filter(id__in=course_ids).order_by("-click_nums")[:5]
        # 是否收藏课程
        return render(request, "course-video.html", {
            "course": course,
            "all_resources": all_resources,
            "relate_courses":relate_courses,
        })
```

>重点: 两个下划线代表我传进来的是一个list，你进行遍历。

comments也做同样处理

用户未登录，不要让他能点进view

如果使用的是方法型编程可以使用装饰器`loginrequired`

而我们使用的是类。所以要继承。

```python
from django.contrib.auth.mixins import LoginRequiredMixin
class CourseInfoView(LoginRequiredMixin, View):
    login_url = '/login/'
    redirect_field_name = 'redirect_to'
```

>8-7完成相关课程推荐功能，取出相关课程去除本身。课程评论login require鉴权添加。登录页面重定向回登录前浏览页面

## 8-8 课程播放页面

将视频播放页面拷贝到template目录

使用开源库video js

![mark](http://upload-images.jianshu.io/upload_images/1779926-9ea5479f0e8165a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-7743fd38993082ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-b9607b955ad7075c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

添加访问的url和view；

```
    # 课程视频播放页
    url(r'^video/(?P<video_id>\d+)/$', VideoPlayView.as_view(), name="video_play"),
```

```python
# 播放视频的view
class VideoPlayView(LoginRequiredMixin, View):
    login_url = '/login/'
    redirect_field_name = 'next'
    def get(self, request, video_id):
        # 此处的id为表默认为我们添加的值。
        video = Video.objects.get(id=int(video_id))
        # 找到对应的course
        course = video.lesson.course
        # 查询用户是否开始学习了该课，如果还未学习则，加入用户课程表
        user_courses = UserCourse.objects.filter(user=request.user, course=course)
        if not user_courses:
            user_course = UserCourse(user=request.user, course=course)
            user_course.save()
        # 查询课程资源
        all_resources = CourseResource.objects.filter(course=course)
        # 选出学了这门课的学生关系
        user_courses = UserCourse.objects.filter(course=course)
        # 从关系中取出user_id
        user_ids = [user_course.user_id for user_course in user_courses]
        # 这些用户学了的课程,外键会自动有id，取到字段
        all_user_courses = UserCourse.objects.filter(user_id__in=user_ids)
        # 取出所有课程id
        course_ids = [user_course.course_id for user_course in all_user_courses]
        # 获取学过该课程用户学过的其他课程
        relate_courses = Course.objects.filter(id__in=course_ids).order_by("-click_nums").exclude(id=course.id)[:4]
        # 是否收藏课程
        return render(request, "course-play.html", {
            "course": course,
            "all_resources": all_resources,
            "relate_courses": relate_courses,
            "video": video,
        })



```

title 与面包屑的修改

对应commit:

>第八章公开课模块全部完成，完结撒花。
