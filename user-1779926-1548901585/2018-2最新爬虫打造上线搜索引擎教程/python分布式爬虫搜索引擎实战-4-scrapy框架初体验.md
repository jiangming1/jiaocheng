基础环境
>python3.5.3 


```
pip install virtualenv
pip install virtualenvwrapper-win
安装虚拟环境管理
mkvirtualenv articlespider3
创建虚拟环境
workon testvir2
直接进入虚拟环境
```
>deactivate
退出激活状态
workon
知道有哪些虚拟环境

##虚拟环境内安装scrapy
>自行官网下载py35对应得whl文件进行pip离线安装

##命令行创建scrapy项目
```
scrapy startproject ArticleSpider
```

##scrapy目录结构
scrapy借鉴了django的项目思想
>`scrapy.cfg`：配置文件。
`setings.py`：设置

```
SPIDER_MODULES = ['ArticleSpider.spiders'] #存放spider的路径
NEWSPIDER_MODULE = 'ArticleSpider.spiders'
```

pipelines.py:
>做跟数据存储相关的东西

middilewares.py:
>自己定义的middlewares 定义方法，处理响应的IO操作

__init__.py:
>项目的初始化文件。

items.py：
>定义我们所要爬取的信息的相关属性。Item对象是种类似于表单，用来保存获取到的数据

###创建spider
```
cd ArticleSpider
scrapy genspider jobbole blog.jobbole.com
```

```
# -*- coding: utf-8 -*-
import scrapy


class JobboleSpider(scrapy.Spider):
    name = "jobbole"
    allowed_domains = ["blog.jobbole.com"]
    # start_urls是一个带爬的列表，
    #spider会为我们把请求下载网页做到，直接到parse阶段
    start_urls = ['http://blog.jobbole.com/']
    def parse(self, response):
```

scray 启动某一个Spyder的命令:
```
scrapy crawl jobbole
```
在windows报出错误：
`ImportError: No module named 'win32api'`

```
pip install pypiwin32#解决
```

在项目根目录里创建main.py
作为调试工具文件
```
# _*_ coding: utf-8 _*_
__author__ = 'mtianyan'
__date__ = '2017/3/28 12:06'

from scrapy.cmdline import execute

import sys
import os

#将系统当前目录设置为项目根目录
#os.path.abspath(__file__)为当前文件所在绝对路径
#os.path.dirname为文件所在目录
#H:\CodePath\spider\ArticleSpider\main.py
#H:\CodePath\spider\ArticleSpider
sys.path.append(os.path.dirname(os.path.abspath(__file__)))
#执行命令，相当于在控制台cmd输入改名了
execute(["scrapy", "crawl" , "jobble"])
```
**settings.py的设置不遵守reboots协议**
`ROBOTSTXT_OBEY = False`

在jobble.py打上断点:
```
def parse(self, response):
    pass
```
可以看到他返回的htmlresponse对象:
对象内部：
>- body:网页内容
- _DEFAULT_ENCODING= 'ascii'
- encoding= 'utf-8'

可以看出scrapy已经为我们做到了将网页下载下来。而且编码也进行了转换.
下一节将对于已下载的网页进行解析。
