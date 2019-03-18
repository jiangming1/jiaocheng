## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

十二: web安全防护

## sql注入攻击与防范


sql注入的危害

![mark](http://upload-images.jianshu.io/upload_images/1779926-5d766e11de0738fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于用户的输入进行合法性判断。

```
class LoginUnsafeView(View):
    def get(self, request):
        return render(request, "login.html", {})
    def post(self, request):
        user_name = request.POST.get("username", "")
        pass_word = request.POST.get("password", "")

        import MySQLdb
        conn = MySQLdb.connect(host='127.0.0.1', user='root', passwd='root', db='mxonline', charset='utf8')
        cursor = conn.cursor()
        sql_select = "select * from users_userprofile where email='{0}' and password='{1}'".format(user_name, pass_word)

        result = cursor.execute(sql_select)
        for row in cursor.fetchall():
            # 查询到用户
            pass
        print 'hello'

urls.py
from users.views import LoginUnsafeView

urlpatterns = [
    url('^login/', LoginUnsafeView.as_view(), name='login'),
]
```

参数中加入sql语句拼接字符串使之为真。

使用django的orm，它已经对这些做了处理

## xss攻击

xss跨站脚本攻击(Cross Site Scripting)的危害

![mark](http://upload-images.jianshu.io/upload_images/1779926-2d013c71d3d2240a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

正常流程

![mark](http://upload-images.jianshu.io/upload_images/1779926-3654a343edd8dd7e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-36ab833452623bb5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当传入iPhone6时，这个字符会被显示到页面中。

![mark](http://upload-images.jianshu.io/upload_images/1779926-015f06294fcbaa4f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将这段代码改成js代码

![mark](http://upload-images.jianshu.io/upload_images/1779926-199eed9caa61b751.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

黑客拿到你的cookie信息。然后伪装成用户。

Xss攻击防范

![mark](http://upload-images.jianshu.io/upload_images/1779926-2cbfc4555ccbfadf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## crsf攻击与防护

crsf跨站请求伪造(Cross-site request forgery)的危害

![mark](http://upload-images.jianshu.io/upload_images/1779926-11a1555439dae316.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-71fc9e5878285fc3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

用户并没有向a请求，而是访问了b。b要求用户访问a的。
原因：用户向a的每次请求都会带上session id

图片中插入。

提交form表单必须添加crsf token

攻击网站无法生成crsf token
