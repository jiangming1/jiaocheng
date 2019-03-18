## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

# Flask 构建微电影视频网站   

已上线演示地址: http://movie.mtianyan.cn
项目源码地址：https://github.com/mtianyan/movie_project

持续更新教程与代码commit。欢迎大家一起学习，star。

## 介绍微电影网站整体开发流程

>flask：轻量级，冗余度小，扩展丰富，可以自由选择组合各种插件，性能优越。

小型项目快速开发 大型项目毫无压力 创业公司和创业者们追捧的web框架

本门课程目标: 前台加后台微电影网站.

## flask框架知识：

1. 学会使用整型，浮点型，路径型，字符串型正则表达式路由转化器
2. 学会使用post与get请求，上传文件，cookie获取与响应，404请求。
3. 学会使用模板自动转义，定义过滤器，定义全局上下文处理器，Jinja2语法，包含，继承，定义宏。
4. 学会使用flask-wtf定义表单模型，字段类型，字段验证，视图处理表单，模板使用表单。
5. 学会使用flask-sqlachemy定义数据库模型，添加数据，修改数据，查询数据，删除数据，数据库事件，数据迁移。
6. 学会使用蓝图优化项目结构，实现微电影网站前台与后台业务逻辑。
7. 学会flask的部署方法，安装编译nginx服务，安装编译python3.6服务，安装mysql服务以及通过nginx反向代理对视频流媒体限制下载速率，限制单个ip能发起的播放连接数。

微内核 + 丰富的扩展插件：

**会用到的插件**

![flask插件](http://upload-images.jianshu.io/upload_images/1779926-5b67a39926a7996c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![flask插件](http://upload-images.jianshu.io/upload_images/1779926-646ebe6dffaee112.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**视频技术**

![视频技术](http://upload-images.jianshu.io/upload_images/1779926-35084dcd76cf8914.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 课程整体目录

### 课程介绍

- 介绍微电影网站整体开发流程
- flask简介
- 学完课程掌握的知识点

### 环境搭建与工具

- 搭建开发环境安装依赖包 virtualenv虚拟化环境的使用
- pycharm编辑器使用，介绍pip下载工具的使用

### 项目优化与模型设计

- 使用flask的蓝图 Blueprint 规划项目结构
- 使用flask sqlalchemy定义和业务需求相关的数据库模型
- 结合mysql数据库生成数据表

### 前端搭建

- 实现前台后台html布局页面搭建
- 学习jinjia2引擎语法
- 引入静态资源文件，404错误页面的处理。

### 后端开发

- flask sqlalchemy结合mysql数据表进行增删改查操作
- flask数据分页查询，路由装饰器定义，模板中变量调用，登录会话机制，上传文件。
- flask wtforms 表单验证、flask自定义应用上下文，自定义权限装饰器对管理系统进行基于角色权限的访问控制。
- flask的多表关联查询，关键字模糊查询

### 网站部署

- 实现在centos服务器上搭建nginx + mysql + python 环境
- 使用nginx反向代理，多端口多进程部署微电影网站
- 配置nginx流媒体访问限制参数

### 系统介绍：

前台首页-电影筛选-电影列表- 播放详情-评论：收藏-搜索-注册-登录-会员中心（修改会员资料，查看评论记录，登录日志，收藏电影）

后台-标签-电影管理-预告-会员-评论-收藏-日志-权限管理-角色-管理员。

适合人群：pythoner

>一个框架可不够哦。

个人必备基础:

- 有自学能力
- 需要掌握python基本语法。
- 会使用mysql数据库
- 会linux基本操作
- 有前端基础知识

## 课程介绍

基于python的flask框架 + mysql数据库

![项目整体模块](http://upload-images.jianshu.io/upload_images/1779926-afba3c2fc208a4a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

开发及生产环境：

![技术路线](http://upload-images.jianshu.io/upload_images/1779926-a4234b0e5a51012c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

国内知名的使用python做web开发如下:

![国内python](http://upload-images.jianshu.io/upload_images/1779926-6f99cff9aa6926f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

各大python框架对比：

![框架对比](http://upload-images.jianshu.io/upload_images/1779926-7a1c9bb9088715c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## flask简介

Werkzeug封装了许多功能：文件上传。生成密码，校验密码等。

![flask简介](http://upload-images.jianshu.io/upload_images/1779926-e708d333083e2209.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

微内核框架：

![微内核框架](http://upload-images.jianshu.io/upload_images/1779926-93bcf3c1e8c6e898.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
