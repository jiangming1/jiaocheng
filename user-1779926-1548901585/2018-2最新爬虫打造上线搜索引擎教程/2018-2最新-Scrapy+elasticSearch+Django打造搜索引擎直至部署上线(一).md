
最终项目上线演示地址: http://search.mtianyan.cn
- 第一节：开始我的表演之前, 先讲讲我们会学到什么，点上面链接看看我们能做一个什么。
Github地址: https://github.com/mtianyan/ArticleSpider (欢迎先点个赞)

# 简介

聚焦Python分布式爬虫必学框架Scrapy打造搜索引擎   

人工智能时代，数据先行。围绕着数据可以做的事情越来越多。

![mark](http://upload-images.jianshu.io/upload_images/1779926-659186649d31a936.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>比如上面这些领域所需要的数据都是可以通过爬虫来完成收集的。

通过Scrapy分布式爬虫爬取数据，以及结合elasticsearch与Django搭建搜索引擎。

>适用人群：初学者，与想进阶的开发者。

收获：获取想要的数据 && 深入认知网络知识和编程知识。

## 课程学习流程目录：

1. 环境配置和基础知识铺垫
2. 爬取真实数据：三个主流网站(伯乐在线, 知乎, 拉钩网)
3. scrapy突破反爬虫技术
4. scrapy进阶开发
5. scrapy redis 分布式爬虫：可以快速搭建一套分布式爬虫。
6. elasticsearch django 实现搜索引擎

### 爬虫基础知识:

1. 正则表达式
2. 深度优先和广度优先遍历算法
3. url去重的常见策略

### 爬取三个网站
>介绍完基础知识通过爬取：

>技术社区(伯乐在线), 问答网站(知乎), 知名招聘网站(拉钩网) 来介绍Scrapy常用功能以及分析网站结构和网络请求。

>学会通过`xpath` + `css` 提取数据, 爬取时也会使用模拟登录

![mark](http://upload-images.jianshu.io/upload_images/1779926-030b0e645e93f373.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

会对Scrapy的`spider` `item` `item loader` `pipeline` `feed export` `CrawSpider`
都进行学习。

### 介绍如何突破网站对于爬虫的限制：

![mark](http://upload-images.jianshu.io/upload_images/1779926-c9433a64f65582ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### scrapy进阶开发中介绍: scrapy的原理, 基于scrapy的中间件开发。

![mark](http://upload-images.jianshu.io/upload_images/1779926-a119de4a1a2a72d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>对于上面这些小的知识点也都进行讲解。

### 通过scrapy redis搭建一套分布式爬虫。
充分利用多台服务器效率。让爬取速度更快。

>- 通过源码分析: 让大家理解scrapy-redis分布式爬虫
>- 集成`bloomfilter`到scrapy-redis中

### elasticsearch 结合 django 制作搜索引擎

elasticsearch 作为当前最流行的的分布式搜索引擎，被用到很多大公司。我们将用它结合django做一个搜索引擎。

>文章搜索 && 问答搜索 && 职位搜索

下面还会有`热门搜索`和`我的搜索`。输入关键词之后`联想输入`点击搜索，进入详情页面。
对搜索关键字进行`标红处理`，对于数据进行了统计，可以收起左侧。右侧可以看到热门, 和我的记录, 下方有一个`分页`的效果。点击某一项会跳转到网站。

## 课程的收获:

- 开发爬虫所需要用到的`技术`以及`网站分析技巧`
- 理解`scrapy的原理`和所有组件的使用以及`分布式爬虫scrapy-redis`的使用和原理。
- 理解分布式开源搜索引擎`elasticsearch`的使用以及`搜索引擎的原理`
- 体验`django`如何快速搭建网站
