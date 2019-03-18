## 下载代码

```
git clone -b ArticleSpider-elasticsearch https://github.com/mtianyan/Art icleSpider.git
```

这里我下载到了desktop目录。

## 介绍

1. scrapyd是一个service用来运行我们的spider实例
2. 通过http json 控制spider

## 安装

安装到虚拟环境之下:

>我们之前运行爬虫的虚拟环境

```
workon articlespider3
scrapyd
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-c8a1afc9fbc210ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这时访问浏览器的6800端口没有错误。

![mark](http://upload-images.jianshu.io/upload_images/1779926-47c62384dd5e399f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-49b1dd778be33254.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看到我现在的目录在桌面

scrapyd是一个服务器。

我们部署需要两个东西，一个是scrapyd 一个是scrapy client

client打包发送到server

对应的虚拟环境要能好好运行。scrapyd部署到服务器。

## 模拟客户端

打开一个新窗口

不用进入我们原先的虚拟环境。

```
pip install scrapyd-client
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-b68d5e6507874a7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

安装完成后cd到我们的项目目录。

![mark](http://upload-images.jianshu.io/upload_images/1779926-359cf0b33046e2ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

前往配置我们的scrapy.cfg

![mark](http://upload-images.jianshu.io/upload_images/1779926-a30d765296dca421.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

名称需要自己记住。如我的mtianyan
找到自己当前窗口对应系统环境的scrapyd-deploy文件

![mark](http://upload-images.jianshu.io/upload_images/1779926-a9298af0da8d6c55.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以直接scrapyd-deploy命令，但是这个是提供给unix用户的。

### windows用户专属

在当前目录创建文件scrapyd-deploy.bat

```
@echo off

"D:\softEnvDown\Anaconda2\envs\py3\python.exe" "D:\softEnvDown\Anaconda2\envs\py3\Scripts\scrapyd-deploy" %1 %2 %3 %4 %5 %6 %7 %8 %9
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-4450ff971fdeccd9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

首先找到虚拟环境下的python.exe路径

![mark](http://upload-images.jianshu.io/upload_images/1779926-1cbb1d9175234355.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

找到scrapyd-deploy路径。

![mark](http://upload-images.jianshu.io/upload_images/1779926-44a7bd844aed894c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到此时scrapyd-deploy可用

![mark](http://upload-images.jianshu.io/upload_images/1779926-f87f234cbaad7358.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

-l 查看已经启动的scrapyd服务。


### 窗口一也就是原来运行我们项目虚拟环境的那个。

![mark](http://upload-images.jianshu.io/upload_images/1779926-503dbd77f8223d5b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

cd进项目目录

```
scrapy list
```

确保可以查看到我们的spider项目。

## 开始部署

-p 要和配置文件中保持一致

![mark](http://upload-images.jianshu.io/upload_images/1779926-8595200b97ddddb3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
scrapyd-deploy mtianyan -p ArticleSpider
```
这是因为我们刚才想检查scrapy list把服务关了

![mark](http://upload-images.jianshu.io/upload_images/1779926-076ada2117564b6d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-6893ccde61cfb750.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

开启服务后重新运行

![mark](http://upload-images.jianshu.io/upload_images/1779926-28593f1282a239c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打包上传成功。

### 前往验证项目上传完成

![mark](http://upload-images.jianshu.io/upload_images/1779926-73ac0d655981c2ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-0f00ccda697c61e4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到我运行scrapyd的目录中已经有eggs 和 dbs

![mark](http://upload-images.jianshu.io/upload_images/1779926-f60e07d125dfee31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

eggs中又有我们的scrapy项目

![mark](http://upload-images.jianshu.io/upload_images/1779926-d2d8e90585cff1a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 开始运行我们的scrapy项目

```
curl http://localhost:6800/daemonstatus.json
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-5bc7a61ab3289851.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

查看当前运行状态

```
curl http://localhost:6800/schedule.json -d project=ArticleSpider -d spider=jobbole
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-b9884df89db8ddff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-12510f2c083756bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到运行成功。

### 本地运行可以打包报错_job(我github代码已经解决了这个错误)

jobbole.py中

```
def __init__(self, **kwargs):

```
添加参数 `**kwargs`

### 上传了错误的项目如何删除

```
curl http://localhost:6800/delproject.json -d project=ArticleSpider
```

浏览器中finish字段为空表示无异常

log 就是调试时的log

### 取消spider任务

```
curl http://localhost:6800/cancel.json -d project=ArticleSpider -d job=2a9b218a13e011e888cb28d2449bc99e
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-440bddf74a33dc3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

jobid就是刚才启动时返回的那个。

![mark](http://upload-images.jianshu.io/upload_images/1779926-f6ed64c678446d82.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-f81fdce3c02cba17.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到已经停止finish了。

### 看到有几个project，spider

```
curl http://localhost:6800/listprojects.json
```

查看有几个spider

```
curl http://localhost:6800/listspiders.json?project=ArticleSpider
```

### job是另一个概念，查看jobs

一个爬虫运行多次是多个job

```
curl http://localhost:6800/listjobs.json?project=ArticleSpider | python -m json.tool
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-28e294a28469eecc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
