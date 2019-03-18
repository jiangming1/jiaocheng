## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

>使用Python3.6与Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

项目支持支付宝支付(暂不支持微信支付),支持手机短信验证码注册， 支持第三方登录。集成了sentry错误监控系统。

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节内容: restful api 与前端源码介绍

4- restful api 与前端源码介绍

## restful api 介绍

- 前后端分离优缺点

为什么要前后端分离

1. pc,app,pad多端适应

>后端模板渲染的方式适合pc，app端不好弄。

2. SPA开发模式开始流行

>单页面应用。最流行的方式就是前后端分离，后端提供接口。

3. 前后端开发职责不清

Django的template由谁写？拆分，模板语言。
php java template 语言使用。

4. 开发效率问题，前后端互相等待
5. 前端一直配合着后端，能力受限
6. 后台开发语言和模板高度耦合，导致开发语言依赖严重。

前后端分离缺点:
	
	- 前后端学习门槛增加，后端的if else语法。这些根据数据展示页面交给前端做了
	- 数据依赖导致文档重要性增加，以前交给后端，后端一个人看懂
	- 前端工作量加大
	- seo的难度加大，爬虫请求不到数据。有专门的优化
	- 后端开发模式迁移增加成本

### restful api

restful api 目前是前后端分离最佳实践(一套标准，建议)

1. 轻量，直接通过http，不需要额外协议，post/get/put/delete操作
2. 面向资源，一目了然，具有自解释性。

资源是名词，post等是动词

推荐阅读: 理解restful架构 阮一峰

## vue基本概念介绍

1. 前端工程化
2. 数据双向绑定
3. 组件化开发

nodejs 包管理。

mvvm: 数据驱动view，view又改变数据。

双向数据绑定。

表单变成一个组件。而以前bootstrap就得引入库，库中有很多我们不需要的东西。

### vue开发中的几个概念

1. webpack

>重要，属于js工具。

- es6到es5代码转换，vue不能被浏览器认识。

最终想让浏览器认识就得html js css

![mark](http://upload-images.jianshu.io/upload_images/1779926-b6ec2c6cb765062e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到看起来什么都没写。

webpack将所有东西打包变成一个js文件。 

2. vue vuex(组件通讯) vue_router(单页面的路由) axios

>前端内部自己跳转，发送ajax请求。不推荐操作dom。
jQuery导入进来只用ajax。不如axios。

3. ES6, babel

es6语法。babel将es6转换为es5。

## vue项目的目录结构

vue的组件方案

babel配置文件

mock数据。

![mark](http://upload-images.jianshu.io/upload_images/1779926-708da3d3f752620f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- api就是我们现在请求网络的api，所有组件的api通过这个设置。
- axios设置了全局拦截401
- components是基础组件
- router 路由
- style  全局静态文件

views中可以看到我们所有组件。

- header组件。
- 轮播图
- 新品
- 分页组件
- 个人中心三组件
- 结算组件

想知道页面是由哪些vue的组件凑成。

![mark](http://upload-images.jianshu.io/upload_images/1779926-57307ee1e364a1be.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用vue官方提供的插件。

app.vue 入口文件。
