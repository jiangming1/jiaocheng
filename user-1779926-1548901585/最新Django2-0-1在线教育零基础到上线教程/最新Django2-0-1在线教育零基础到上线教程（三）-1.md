## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

# 通过留言版功能回顾django基础知识

教程中本章对应上传的仓库为: https://github.com/mtianyan/DjangoGetStarted
>对应第一次commit:留言板仓库初始化。内容截止3-1一章结束。

- 将对于django目录结构，使用Django快速搭建可以提交的表单页面，models.py , urls.py, views.py。
- 从数据库中取出数据展示到html中：Django Template的配置。
- 即django的基础知识通过这个留言板项目进行一个全面细致的学习。

## 3-1 django目录结构

django目录：

    projectname : 保存Django项目的urls,setting，uwsgi文件

如下图新建一个Django项目`DjangoGetStarted`，使用我们上章节中已存在的虚拟环境`DjangoTest` (里面已经装好了django)

![mark](http://upload-images.jianshu.io/upload_images/1779926-dd689a184a2a4e36.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### django自动生成的目录

初始化完成后的目录如下：(如果不是，那么你们可能创建的不是django项目)

![mark](http://upload-images.jianshu.io/upload_images/1779926-169aba724eb07222.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到主目录`DjangoGetStarted`与项目目录`DjangoGetStarted`

- DjangoGetStarted(文件夹)：	
	- setting.py： 项目全局配置文件
	- urls.py： 主要的urls配置入口
	- wsgi.py： 是Django启动需要的文件。
- templates(文件夹)： 放置html文件
- manage.py： 启动Django需要的主要文件。(主要的Django命令都通过manage.py运行)

### 还需要我们自己创建的目录

app是Django里一个一个应用的文件夹单位。

通过 `Tools -> Run manage.py Task`创建app：

![mark](http://upload-images.jianshu.io/upload_images/1779926-e20ad0f0c03a20c1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### startapp message

可以看到当输入`startapp message`之后，创建了`message`应用。并存放在了：与项目目录同级目录。

![mark](http://upload-images.jianshu.io/upload_images/1779926-f906451db5de6ede.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 新建static目录

使用`static`目录来存放网站的静态文件：js，css，图片等。

#### 新建log目录

使用log目录来存放网站的日志文件

#### 新建media目录

使用media目录存放用户上传的图片等资源。

#### 解决项目大了之后app过多问题

1. 新建文件夹 apps
2. 将message文件夹拖入apps文件夹内：会自动生成`__init__.py`文件表明这是一个包。使得apps文件夹可导入。

![mark](http://upload-images.jianshu.io/upload_images/1779926-58561f0a83cc814a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>这时我们就会发现在导入我们的message的内容就得配置较长的路径。

![mark](http://upload-images.jianshu.io/upload_images/1779926-2452270b5ba7e870.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>每次前面都得加上`apps.`，这可烦死人啦。

**解决方案奉上**
>将`apps`目录右键`mark`成`Source Root`(Mark 方法查看第一章pycharm简单使用：目录颜色不同的原因)

![mark](http://upload-images.jianshu.io/upload_images/1779926-1b51889cce32e105.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

mark成功之后**变蓝**(变绿的话，只能摸摸头了，当然选择原谅)，然后可以直接使用短路径进行import

#### Mark后Pycharm 不报错，Cmd下运行报错。

Mark后pycharm知道这是一个项目的`Souce Root`路径了，但是cmd并不知道。

>在项目目录下通过cmd命令行使用`python manage.py runserver`

![mark](http://upload-images.jianshu.io/upload_images/1779926-c086c66c053542f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>pycharm中mark只是pycharm自身可以进行识别短路径。

**解决方案：**

>我们在setting文件中配置我们的`apps`路径:

![mark](http://upload-images.jianshu.io/upload_images/1779926-16e92d19c2ef8cd2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>图解读：我们需要在setting中向上图一样设置,程序就会接着报错。(换了一个错误了，滑稽脸)

```python
import sys
sys.path.insert(0,os.path.join(BASE_DIR,'apps'))
```

上述代码为将apps拼接项目绝对路径后的路径插入当前系统的环境变量path中，这样就可以成功解决(个屁屁啊)。

成功性测试(测试已失败)：

>这个import放到manage.py文件是不行的 你把manage.py中这行删除 因为django整个的配置还没有启动好 import django的model是不行的，

插播：忘了失败吧，我偷学下面方法养你。

**终极解决：将这个`import`方法比如urls.py.等可以成功启动。**或者自行删除该import。

![mark](http://upload-images.jianshu.io/upload_images/1779926-1f2407dfb62cb313.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

红色警告：
```
You have unapplied migrations; your app may not work properly 
until they are applied. Run 'python manage.py migrate' to apply them.
```
是因为我们没有进行数据库`models`进行初始化`migrate`.

`python manage.py migrate`我们之后会用到，现在不要做。

### github仓库项目初始化第一次commit。

![mark](http://upload-images.jianshu.io/upload_images/1779926-f24575db1e82b982.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>输入用户名密码，点击login。

![mark](http://upload-images.jianshu.io/upload_images/1779926-c53deb6a696cd693.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>选择左侧导航中`Git` 设置你的git.exe的路径

![mark](http://upload-images.jianshu.io/upload_images/1779926-abad5d49ce557cd2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击`Share project on GitHub`会弹出下图窗口

![mark](http://upload-images.jianshu.io/upload_images/1779926-8db68624fe6ae8fd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

填写你的项目`名称`，`描述`。点击`share`。

会弹窗让你选择需要上传的项目文件与commit信息。然后将项目上传至github。

**我教程中上传的仓库为:  https://github.com/mtianyan/DjangoGetStarted**

>对应第一次commit:留言板仓库初始化。内容截止3-1一章结束。
