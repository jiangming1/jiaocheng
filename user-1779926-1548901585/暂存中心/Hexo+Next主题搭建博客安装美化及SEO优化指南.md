## Hexo + Next主题 搭建博客 "真"零基础安装配置

最终成果: 本站

### node.js

官网下载:  **下一步下一步安装法**

node-v8.9.3-x64

C:\softEnvir\nodejs

验证安装:

![mark](http://upload-images.jianshu.io/upload_images/1779926-d07e722bd932de6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 设置npm下载目录

```
npm config set prefix D:\softEnvDown\nodejs\node_global
npm config set cache D:\softEnvDown\nodejs\node_cache
npm config ls
```

>npm模块安装的默认全局路径已经改到了相应的文件夹中，但是 这时候用户还是无法require这些模块。因为电脑系统现在还不知道你把默认路径给改了，所以需要在`win+R`-->“`sysdm.cpl`-->`高级`-->`环境变量`打开设置对话框。

- 首先在`系统变量`中新建，新建一个名为`NODE_PATH`的变量，变量值: 全局模块的文件夹中的node_modules文件夹的绝对路径.

即：`D:\softEnvDown\nodejs\node_global\node_modules`

配置文件目录：`C:\Users\mtian\.npmrc`

####  安装cnpm

`npm install -g cnpm --registry=https://registry.npm.taobao.org`

### Git安装

Git-2.15.1.2-64-bit（同样官网下载: **下一步下一步安装法**）

C:\softEnvir\Git

### hexo-cli: 1.0.4安装

cmd控制台下:

      `npm install -g hexo-cli`

### 初始化hexo博客目录

D:/mtianBlog:

`hexo init hexoBlog`

### 下载next主题及主题美化

官方文档地址： http://theme-next.iissnan.com/

下载主题next：v5.1.4。名字改为next

然后放入`D:\mtianBlog\hexoBlog\themes`

```
hexo clean

hexo s --debug
```

#### 主题美化。

推荐阅读：https://www.jianshu.com/p/f054333ac9e6

部分节选：

1. 圆形头像旋转。

打开`\themes\next\source\css\_common\components\sidebar\sidebar-author.styl`，在里面添加如下代码：

```css
.site-author-image {
  display: block;
  margin: 0 auto;
  padding: $site-author-image-padding;
  max-width: $site-author-image-width;
  height: $site-author-image-height;
  border: $site-author-image-border-width solid $site-author-image-border-color;

  /* 头像圆形 */
  border-radius: 80px;
  -webkit-border-radius: 80px;
  -moz-border-radius: 80px;
  box-shadow: inset 0 -1px 0 #333sf;

  /* 设置循环动画 [animation: (play)动画名称 (2s)动画播放时长单位秒或微秒 (ase-out)动画播放的速度曲线为以低速结束 
    (1s)等待1秒然后开始动画 (1)动画播放次数(infinite为循环播放) ]*/
 

  /* 鼠标经过头像旋转360度 */
  -webkit-transition: -webkit-transform 1.0s ease-out;
  -moz-transition: -moz-transform 1.0s ease-out;
  transition: transform 1.0s ease-out;
}

img:hover {
  /* 鼠标经过停止头像旋转 
  -webkit-animation-play-state:paused;
  animation-play-state:paused;*/

  /* 鼠标经过头像旋转360度 */
  -webkit-transform: rotateZ(360deg);
  -moz-transform: rotateZ(360deg);
  transform: rotateZ(360deg);
}

/* Z 轴旋转动画 */
@-webkit-keyframes play {
  0% {
    -webkit-transform: rotateZ(0deg);
  }
  100% {
    -webkit-transform: rotateZ(-360deg);
  }
}
@-moz-keyframes play {
  0% {
    -moz-transform: rotateZ(0deg);
  }
  100% {
    -moz-transform: rotateZ(-360deg);
  }
}
@keyframes play {
  0% {
    transform: rotateZ(0deg);
  }
  100% {
    transform: rotateZ(-360deg);
  }
}
```

2. 配置百度统计站长版。(官方文档有写)

baidu_analytics: ***


3. 配置leancloud (官方文档有写)

leancloud_visitors:
  enable: true
  app_id: *
  app_key: #<app_key>

4. 配置搜索(更推荐localSearch)

```
algolia:
  applicationID: '*'
  apiKey: '*'
  adminApiKey: '*'
  indexName: '*'
  chunkSize: 5000

$ export(windows 为 set) HEXO_ALGOLIA_INDEXING_KEY=***
$ hexo algolia
```
您好！当我集成Algolia时候一直出错——上传不了记录，请问知道如何解决嘛？

答案地址：https://github.com/iissnan/theme-next-docs/issues/162

#### 使用gulp进行博文压缩（持续集成自动部署前提）

在站点的根目录下执行以下命令：

```
$ npm install gulp -g
$ npm install gulp-minify-css gulp-uglify gulp-htmlmin gulp-htmlclean gulp --save
```

新建文件`gulpfile.js`:

![mark](http://upload-images.jianshu.io/upload_images/1779926-13afeca2c236b8b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

内容为：

```
var gulp = require('gulp');
var minifycss = require('gulp-minify-css');
var uglify = require('gulp-uglify');
var htmlmin = require('gulp-htmlmin');
var htmlclean = require('gulp-htmlclean');
// 压缩 public 目录 css
gulp.task('minify-css', function() {
    return gulp.src('./public/**/*.css')
        .pipe(minifycss())
        .pipe(gulp.dest('./public'));
});
// 压缩 public 目录 html
gulp.task('minify-html', function() {
  return gulp.src('./public/**/*.html')
    .pipe(htmlclean())
    .pipe(htmlmin({
         removeComments: true,
         minifyJS: true,
         minifyCSS: true,
         minifyURLs: true,
    }))
    .pipe(gulp.dest('./public'))
});
// 压缩 public/js 目录 js
gulp.task('minify-js', function() {
    return gulp.src('./public/**/*.js')
        .pipe(uglify())
        .pipe(gulp.dest('./public'));
});
// 执行 gulp 命令时执行的任务
gulp.task('default', [
    'minify-html','minify-css','minify-js'
]);
```

### 部署hexo：

`npm install hexo-deployer-git --save`

修改站点配置文件：

```
deploy:
  type: git
  repo: https://github.com/mtianyan/mtianyan.github.io
  branch: master
  message: new version first commit
```

需要使用的命令

```
git config --global user.email "1147727180@qq.com"

git config --global user.name "mtianyan"

hexo clean

hexo g && gulp

hexo d
```
### 将博客源码备份到github或码云(持续集成自动部署前提)


```
git init
git add .
git remote add origin https://github.com/mtianyan/hexoBlog-Github.git
git commit -m "first"
git push -u origin master

```

想要弄到码云替换第三行为下面：

```
git remote add origin https://gitee.com/mtianyan/hexoBlog-mayun.git
```
### SEO优化指南

### URL唯一化:

```
npm install hexo-abbrlink --save
```

站点配置文件里添加:

```
permalink: post/:abbrlink.html
abbrlink:
  alg: crc32  # 算法：crc16(default) and crc32
  rep: hex    # 进制：dec(default) and hex
```
#### 安装sitemap站点地图生成插件

```
npm install hexo-generator-sitemap --save
npm install hexo-generator-baidu-sitemap --save
```

在站点配置文件或主题配置文件加入。

![mark](http://upload-images.jianshu.io/upload_images/1779926-eebfad765a8aa001.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
sitemap: 
    path: sitemap.xml
baidusitemap:
    path: baidusitemap.xml
```

注意缩进

在`hexo-site\source`中新建文件`robots.txt`,内容如下，请自行替换

```
User-agent: *
Allow: /
Allow: /archives/
Allow: /categories/
Allow: /tags/ 
Allow: /about/ 
Disallow: /vendors/
Disallow: /js/
Disallow: /css/
Disallow: /fonts/
Disallow: /vendors/
Disallow: /fancybox/

Sitemap: http://mtianyan.gitee.io/sitemap.xml
Sitemap: http://mtianyan.gitee.io/baidusitemap.xml
```

`Allow`后面的就是你的`menu` 可以在主题配置文件中找到。

前往链接：https://www.google.com/webmasters/
添加你的网站。

![mark](http://upload-images.jianshu.io/upload_images/1779926-59f54bc2c56265bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下载验证文件放入`hexo-site\source`中

Tips: 站点配置文件忽略Google的验证文件。这样`clean`的时候就不会被删除了。

```
skip_render: 
  - README.md
  - google*****.html
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-afd6c79165f42192.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打开站点地图：添加站点地图。
![mark](http://upload-images.jianshu.io/upload_images/1779926-9dbead7f4049949b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以进入站点地图详情查看有没有报错。

**robots.txt测试**

![mark](http://upload-images.jianshu.io/upload_images/1779926-d8671cf1e707ceed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

确保0错误，0警告。

### 百度搜索资源平台

http://ziyuan.baidu.com/

![mark](http://upload-images.jianshu.io/upload_images/1779926-833ba0391765f30a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击用户中心。站点管理 进行 添加网站操作。

下载验证文件放入`hexo-site\source`中

![mark](http://upload-images.jianshu.io/upload_images/1779926-f5d501420bab7bcc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

验证完成之后点击域名进入控制台。

![mark](http://upload-images.jianshu.io/upload_images/1779926-e696cc365f425af4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于Robots文件进行验证。

![mark](http://upload-images.jianshu.io/upload_images/1779926-e38c61a466d07c38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击链接提交，往下多翻点

![mark](http://upload-images.jianshu.io/upload_images/1779926-e056c234c134385a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

选择sitemap：填入自己网站的sitemap地址。点击提交

![mark](http://upload-images.jianshu.io/upload_images/1779926-ca7ab839f67f3fa4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

查看状态。

![mark](http://upload-images.jianshu.io/upload_images/1779926-354aa5d2227d39db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 页面关键字优化
title

文件路径是`your-hexo-site\themes\next\layout\index.swig`

将文件中

```
{% block title %}{{ config.title }}{% if theme.index_with_subtitle and config.subtitle %} - {{config.subtitle }}
```

改为

```
{% block title %}{{ config.title }}{% if theme.index_with_subtitle and config.subtitle %} - {{config.subtitle }}{% endif %}{{ theme.description }} {% endblock %}
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-2b021d95d63d8e69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



