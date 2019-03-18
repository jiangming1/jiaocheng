#Python分布式爬虫打造搜索引擎 
**基于Scrapy、Redis、elasticsearch和django打造一个完整的搜索引擎网站**

推荐前往我的个人博客进行阅读：http://blog.mtianyan.cn/
Github地址: https://github.com/mtianyan/ArticleSpider (欢迎先点star后上车)
目录分章效果更佳哦

##分章查看目录：
1. [Scrapy分布式爬虫打造搜索引擎 - (一)基础知识](http://www.jianshu.com/p/23b6d6b57ec5)
2. [Scrapy分布式爬虫打造搜索引擎 - (二)伯乐在线爬取所有文章](http://www.jianshu.com/p/395591050986)
3.  [Scrapy分布式爬虫打造搜索引擎 - (三)知乎网问题和答案爬取](http://www.jianshu.com/p/7e31c1cbfa6f)
4.  [Scrapy分布式爬虫打造搜索引擎 -  (四)通过CrawlSpider对拉勾网进行整站爬取](http://www.jianshu.com/p/ce8aa46496ef)
5.  [Scrapy分布式爬虫打造搜索引擎-（五）爬虫与反爬虫的战争](http://www.jianshu.com/p/9ff03b41c184)
6.  [Scrapy分布式爬虫打造搜索引擎-（六）scrapy进阶开发](http://www.jianshu.com/p/6cc890328320)
7.  [Scrapy分布式爬虫打造搜索引擎-（七）scrapy-redis 分布式爬虫](http://www.jianshu.com/p/c8cc5828c2ad)
8.  [Scrapy分布式爬虫打造搜索引擎-（八）elasticsearch结合django搭建搜索引擎](http://www.jianshu.com/p/7f175cd60fed)
##二、伯乐在线爬取所有文章
###1. 初始化文件目录
基础环境
>1. python 3.5.1
>2. JetBrains PyCharm 2016.3.2
>3. mysql+navicat

为了便于日后的部署：我们开发使用了虚拟环境。

```
pip install virtualenv
pip install virtualenvwrapper-win
安装虚拟环境管理
mkvirtualenv articlespider3
创建虚拟环境
workon articlespider3
直接进入虚拟环境
deactivate
退出激活状态
workon
知道有哪些虚拟环境
```

####scrapy项目初始化介绍
>自行官网下载py35对应得whl文件进行pip离线安装
Scrapy            1.3.3

**命令行创建scrapy项目**
```
cd desktop
scrapy startproject ArticleSpider
```

**scrapy目录结构**

scrapy借鉴了django的项目思想
>- `scrapy.cfg`：配置文件。
- `setings.py`：设置

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

**创建我们的spider**
```
cd ArticleSpider
scrapy genspider jobbole blog.jobbole.com
```
可以看到直接为我们创建好的空项目里已经有了模板代码。如下：

```python
# -*- coding: utf-8 -*-
import scrapy


class JobboleSpider(scrapy.Spider):
    name = "jobbole"
    allowed_domains = ["blog.jobbole.com"]
    # start_urls是一个带爬的列表，
    #spider会为我们把请求下载网页做到，直接到parse阶段
    start_urls = ['http://blog.jobbole.com/']
    def parse(self, response):
    	pass
```

scray在命令行启动某一个Spyder的命令:
```
scrapy crawl jobbole
```
**在windows报出错误**

`ImportError: No module named 'win32api'`

```
pip install pypiwin32#解决
```

**创建我们的调试工具类***

在项目根目录里创建main.py
作为调试工具文件
```python
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
execute(["scrapy", "crawl" , "jobbole"])
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

###2. 提取伯乐在线内容
####xpath的使用
xpath让你可以不懂前端html，不看html的详细结构，只需要会右键查看就能获取网页上任何内容。速度远超beautifulsoup。
目录:

    1. xpath简介
    2. xpath术语与语法
    3. xpath抓取误区：javasrcipt生成html与html源文件的区别
    4. xpath抓取实例

为什么要使用xpath？

- xpath使用路径表达式在xml和html中进行导航
- xpath包含有一个标准函数库
- xpath是一个w3c的标准
- xpath速度要远远超beautifulsoup。

**xpath节点关系**

1. 父节点`*上一层节点*`
2. 子节点
3. 兄弟节点`*同胞节点*`
4. 先辈节点`*父节点，爷爷节点*`
5. 后代节点`*儿子，孙子*`
xpath语法:

| 表达式| 说明           | 
| ------------- |:-------------:| 
|article | 选取所有article元素的所有子节点 |
| /article|选取根元素article    |
| article/a| 选取所有属于article的子元素的a元素|
|//div|选取所有div元素（不管出现在文档里的任何地方）|
|article//div|选取所有属于article元素的后代的div元素，不管它出现在article之下的任何位置|
|//@class|选取所有名为class的属性|

**xpath语法-谓语:**

| 表达式| 说明           | 
| ------------- |:-------------:| 
|/article/div[1|选取属于article子元素的第一个div元素|
|/article/div[last()]|选取属于article子元素的最后一个div元素|
|/article/div[last()-1]|选取属于article子元素的倒数第二个div元素|
|//div[@color]|选取所有拥有color属性的div元素|
|//div[@color='red']|选取所有color属性值为red的div元素|

**xpath语法:**

| 表达式| 说明           | 
| ------------- |:-------------:| 
|/div/*|选取属于div元素的所有子节点|
|//*|选取所有元素|
|//div[@*]|选取所有带属性的div 元素|
|//div/a 丨//div/p|选取所有div元素的a和p元素|
|//span丨//ul|选取文档中的span和ul元素|
|article/div/p丨//span|选取所有属于article元素的div元素的p元素以及文档中所有的 span元素|

**xpath抓取误区**

[firebugs插件](https://addons.mozilla.org/en-us/firefox/addon/firebug/?src=dp-dl-dependencies)

取某一个网页上元素的xpath地址
>如:http://blog.jobbole.com/110287/

在标题处右键使用firebugs查看元素。
然后在`<h1>2016 腾讯软件开发面试题（部分）</h1>`右键查看xpath

```python
# -*- coding: utf-8 -*-
import scrapy

class JobboleSpider(scrapy.Spider):
    name = "jobbole"
    allowed_domains = ["blog.jobbole.com"]
    start_urls = ['http://blog.jobbole.com/110287/']

    def parse(self, response):
        re_selector = response.xpath("/html/body/div[3]/div[3]/div[1]/div[1]/h1")
        # print(re_selector)
        pass
```
调试debug可以看到
```
re_selector =(selectorlist)[]
```

可以看到返回的是一个空列表，
列表是为了如果我们当前的xpath路径下还有层级目录时可以进行选取
空说明没取到值：

**我们可以来chorme里观察一下**
>chorme取到的值
`//*[@id="post-110287"]/div[1]/h1`

chormexpath代码

```python
# -*- coding: utf-8 -*-
import scrapy


class JobboleSpider(scrapy.Spider):
    name = "jobbole"
    allowed_domains = ["blog.jobbole.com"]
    start_urls = ['http://blog.jobbole.com/110287/']

    def parse(self, response):
        re_selector = response.xpath('//*[@id="post-110287"]/div[1]/h1')
        # print(re_selector)
        pass
```
可以看出此时可以取到值
>分析页面，可以发现页面内有一部html是通过JavaScript ajax交互来生成的，因此在f12检查元素时的页面结构里有，而xpath不对
xpath是基于html源代码文件结构来找的

xpath可以有多种多样的写法：

```python
#re_selector（firebugs获取）re2_selector （chrome获取）
#因为上文xpath抓取误区提到原因获取值为空
re_selector = response.xpath("/html/body/div[1]/div[3]/div[1]/div[1]/h1/text()")
re2_selector = response.xpath('//*[@id="post-110287"]/div[1]/h1/text()')
re3_selector = response.xpath('//div[@class="entry-header"]/h1/text()')
```
~~推荐使用id型。因为页面id唯一。~~

推荐使用class型，因为后期循环爬取可扩展通用性强。

通过了解了这些此时我们已经可以抓取到页面的标题，此时可以使用xpath利器照猫画虎抓取任何内容。只需要点击右键查看xpath。

**开启控制台调试**

`scrapy shell http://blog.jobbole.com/110287/`

**完整的xpath提取伯乐在线字段代码**

```python
# -*- coding: utf-8 -*-
import scrapy
import re

class JobboleSpider(scrapy.Spider):
    name = "jobbole"
    allowed_domains = ["blog.jobbole.com"]
    start_urls = ['http://blog.jobbole.com/110287/']

    def parse(self, response):
        #提取文章的具体字段
        title = response.xpath('//div[@class="entry-header"]/h1/text()').extract_first("")
        create_date = response.xpath("//p[@class='entry-meta-hide-on-mobile']/text()").extract()[0].strip().replace("·","").strip()
        praise_nums = response.xpath("//span[contains(@class, 'vote-post-up')]/h10/text()").extract()[0]
        fav_nums = response.xpath("//span[contains(@class, 'bookmark-btn')]/text()").extract()[0]
        match_re = re.match(".*?(\d+).*", fav_nums)
        if match_re:
            fav_nums = match_re.group(1)

        comment_nums = response.xpath("//a[@href='#article-comment']/span/text()").extract()[0]
        match_re = re.match(".*?(\d+).*", comment_nums)
        if match_re:
            comment_nums = match_re.group(1)

        content = response.xpath("//div[@class='entry']").extract()[0]

        tag_list = response.xpath("//p[@class='entry-meta-hide-on-mobile']/a/text()").extract()
        tag_list = [element for element in tag_list if not element.strip().endswith("评论")]
        tags = ",".join(tag_list)
        pass
```
####css选择器的使用：

```python
# 通过css选择器提取字段
        # front_image_url = response.meta.get("front_image_url", "")  #文章封面图
        title = response.css(".entry-header h1::text").extract_first()
        create_date = response.css("p.entry-meta-hide-on-mobile::text").extract()[0].strip().replace("·","").strip()
        praise_nums = response.css(".vote-post-up h10::text").extract()[0]
        fav_nums = response.css(".bookmark-btn::text").extract()[0]
        match_re = re.match(".*?(\d+).*", fav_nums)
        if match_re:
            fav_nums = int(match_re.group(1))
        else:
            fav_nums = 0

        comment_nums = response.css("a[href='#article-comment'] span::text").extract()[0]
        match_re = re.match(".*?(\d+).*", comment_nums)
        if match_re:
            comment_nums = int(match_re.group(1))
        else:
            comment_nums = 0

        content = response.css("div.entry").extract()[0]

        tag_list = response.css("p.entry-meta-hide-on-mobile a::text").extract()
        tag_list = [element for element in tag_list if not element.strip().endswith("评论")]
        tags = ",".join(tag_list)
        pass
```
###3. 爬取所有文章
**yield关键字**

```python
#使用request下载详情页面，下载完成后回调方法parse_detail()提取文章内容中的字段
yield Request(url=parse.urljoin(response.url,post_url),callback=self.parse_detail)
```
**scrapy.http import Request下载网页**

```python
from scrapy.http import Request
Request(url=parse.urljoin(response.url,post_url),callback=self.parse_detail)
```
**parse拼接网址应对herf内有可能网址不全**

```python
from urllib import parse
url=parse.urljoin(response.url,post_url)
parse.urljoin("http://blog.jobbole.com/all-posts/","http://blog.jobbole.com/111535/")
#结果为http://blog.jobbole.com/111535/
```
**class层级关系**

```python
next_url = response.css(".next.page-numbers::attr(href)").extract_first("")
#如果.next .pagenumber 是指两个class为层级关系。而不加空格为同一个标签
```
**twist异步机制**

Scrapy使用了Twisted作为框架，Twisted有些特殊的地方是它是事件驱动的，并且比较适合异步的代码。在任何情况下，都不要写阻塞的代码。阻塞的代码包括:

 - 访问文件、数据库或者Web
 - 产生新的进程并需要处理新进程的输出，如运行shell命令
 - 执行系统层次操作的代码，如等待系统队列

**实现全部文章字段下载的代码：**

```python
    def parse(self, response):
        """
                1. 获取文章列表页中的文章url并交给scrapy下载后并进行解析
                2. 获取下一页的url并交给scrapy进行下载， 下载完成后交给parse
                """
        # 解析列表页中的所有文章url并交给scrapy下载后并进行解析
        post_urls = response.css("#archive .floated-thumb .post-thumb a::attr(href)").extract()
        for post_url in post_urls:
            #request下载完成之后，回调parse_detail进行文章详情页的解析
            # Request(url=post_url,callback=self.parse_detail)
            print(response.url)
            print(post_url)
            yield Request(url=parse.urljoin(response.url,post_url),callback=self.parse_detail)
            #遇到href没有域名的解决方案
            #response.url + post_url
            print(post_url)
        # 提取下一页并交给scrapy进行下载
        next_url = response.css(".next.page-numbers::attr(href)").extract_first("")
        if next_url:
            yield Request(url=parse.urljoin(response.url, post_url), callback=self.parse)
```
**全部文章的逻辑流程图**

![所有文章流程图](http://upload-images.jianshu.io/upload_images/1779926-d0f10707f0b49b08.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###4. scrapy的items整合字段
数据爬取的任务就是从非结构的数据中提取出结构性的数据。
items 可以让我们自定义自己的字段（类似于字典，但比字典的功能更齐全）

**在当前页，需要提取多个url**

原始写法,extract之后则生成list列表，无法进行二次筛选：

```python
post_urls = response.css("#archive .floated-thumb .post-thumb a::attr(href)").extract()
```
改进写法:

```python
post_nodes = response.css("#archive .floated-thumb .post-thumb a")
        for post_node in post_nodes:
            #获取封面图的url
            image_url = post_node.css("img::attr(src)").extract_first("")
            post_url = post_node.css("::attr(href)").extract_first("")
```

**在下载网页的时候把获取到的封面图的url传给parse_detail的response**
在下载网页时将这个封面url获取到，并通过meta将他发送出去。在callback的回调函数中接收该值

```python
yield Request(url=parse.urljoin(response.url,post_url),meta={"front_image_url":image_url},callback=self.parse_detail)

front_image_url = response.meta.get("front_image_url", "")
```
**urljoin的好处**
如果你没有域名，我就从response里取出来，如果你有域名则我对你起不了作用了

**编写我们自定义的item并在jobboled.py中填充。

```python
class JobBoleArticleItem(scrapy.Item):
    title = scrapy.Field()
    create_date = scrapy.Field()
    url = scrapy.Field()
    url_object_id = scrapy.Field()
    front_image_url = scrapy.Field()
    front_image_path = scrapy.Field()
    praise_nums = scrapy.Field()
    comment_nums = scrapy.Field()
    fav_nums = scrapy.Field()
    content = scrapy.Field()
    tags = scrapy.Field()
```

import之后实例化，实例化之后填充：

```python
1. from ArticleSpider.items import JobBoleArticleItem
2. article_item = JobBoleArticleItem()
3. article_item["title"] = title
        article_item["url"] = response.url
        article_item["create_date"] = create_date
        article_item["front_image_url"] = [front_image_url]
        article_item["praise_nums"] = praise_nums
        article_item["comment_nums"] = comment_nums
        article_item["fav_nums"] = fav_nums
        article_item["tags"] = tags
        article_item["content"] = content
```

**yield article_item将这个item传送到pipelines中**
pipelines可以接收到传送过来的item
将setting.py中的pipeline配置取消注释
```
# Configure item pipelines
# See http://scrapy.readthedocs.org/en/latest/topics/item-pipeline.html
ITEM_PIPELINES = {
   'ArticleSpider.pipelines.ArticlespiderPipeline': 300,
}
```
当我们的item被传输到pipeline我们可以将其进行存储到数据库等工作

**setting设置下载图片pipeline**
```
ITEM_PIPELINES={
'scrapy.pipelines.images.ImagesPipeline': 1,
}
```
H:\CodePath\pyEnvs\articlespider3\Lib\site-packages\scrapy\pipelines
里面有三个scrapy默认提供的pipeline
提供了文件，图片，媒体。

ITEM_PIPELINES是一个数据管道的登记表，每一项具体的数字代表它的优先级，数字越小，越早进入。

**setting设置下载图片的地址**
```
# IMAGES_MIN_HEIGHT = 100
# IMAGES_MIN_WIDTH = 100
```
设置下载图片的最小高度，宽度。

新建文件夹images在

```python
IMAGES_URLS_FIELD = "front_image_url"
project_dir = os.path.abspath(os.path.dirname(__file__))
IMAGES_STORE = os.path.join(project_dir, 'images')
```

**安装PIL**
`pip install pillow`

**定制自己的pipeline使其下载图片后能保存下它的本地路径**
get_media_requests()接收一个迭代器对象下载图片
item_completed获取到图片的下载地址

![自定义图片pipeline的调试信息](http://upload-images.jianshu.io/upload_images/1779926-b46e91f577c5bf6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

继承并重写item_completed()

```python
from scrapy.pipelines.images import ImagesPipeline

class ArticleImagePipeline(ImagesPipeline):
    #重写该方法可从result中获取到图片的实际下载地址
    def item_completed(self, results, item, info):
        for ok, value in results:
            image_file_path = value["path"]
        item["front_image_path"] = image_file_path

        return item
```

setting中设置使用我们自定义的pipeline，而不是系统自带的

```python
ITEM_PIPELINES = {
   'ArticleSpider.pipelines.ArticlespiderPipeline': 300,
   # 'scrapy.pipelines.images.ImagesPipeline': 1,
    'ArticleSpider.pipelines.ArticleImagePipeline':1,
}
```

![保存下来的本地地址](http://upload-images.jianshu.io/upload_images/1779926-e3b33dc61bc8ab3a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**图片url的md5处理**
新建package utils

```python
import hashlib

def get_md5(url):
    m = hashlib.md5()
    m.update(url)
    return m.hexdigest()

if __name__ == "__main__":
    print(get_md5("http://jobbole.com".encode("utf-8")))
```

不确定用户传入的是不是:

```python
def get_md5(url):
	#str就是unicode了
    if isinstance(url, str):
        url = url.encode("utf-8")
    m = hashlib.md5()
    m.update(url)
    return m.hexdigest()
```
在jobbole.py中将url的md5保存下来

```python
from ArticleSpider.utils.common import get_md5
article_item["url_object_id"] = get_md5(response.url)
```

###5. 数据保存到本地文件以及mysql中
####保存到本地json文件
**import codecs打开文件避免一些编码问题，自定义JsonWithEncodingPipeline实现json本地保存**

```python 
class JsonWithEncodingPipeline(object):
    #自定义json文件的导出
    def __init__(self):
        self.file = codecs.open('article.json', 'w', encoding="utf-8")
    def process_item(self, item, spider):
        #将item转换为dict，然后生成json对象，false避免中文出错
        lines = json.dumps(dict(item), ensure_ascii=False) + "\n"
        self.file.write(lines)
        return item
    #当spider关闭的时候
    def spider_closed(self, spider):
        self.file.close()
```
setting.py注册pipeline

```python
ITEM_PIPELINES = {
   'ArticleSpider.pipelines.JsonWithEncodingPipeline': 2,
   # 'scrapy.pipelines.images.ImagesPipeline': 1,
    'ArticleSpider.pipelines.ArticleImagePipeline':1,
}
```

**scrapy exporters JsonItemExporter导出**

scrapy自带的导出：

           - 'CsvItemExporter', 
           - 'XmlItemExporter',
           - 'JsonItemExporter'

`from scrapy.exporters import JsonItemExporter`

```python
class JsonExporterPipleline(object):
    #调用scrapy提供的json export导出json文件
    def __init__(self):
        self.file = open('articleexport.json', 'wb')
        self.exporter = JsonItemExporter(self.file, encoding="utf-8", ensure_ascii=False)
        self.exporter.start_exporting()

    def  close_spider(self, spider):
        self.exporter.finish_exporting()
        self.file.close()

    def process_item(self, item, spider):
        self.exporter.export_item(item)
        return item
```

设置setting.py注册该pipeline

```python
'ArticleSpider.pipelines.JsonExporterPipleline ': 2
```

####保存到数据库(mysql)
数据库设计数据表，表的内容字段是和item一致的。数据库与item的关系。类似于django中model与form的关系。
**日期的转换，将字符串转换为datetime**

```python
import datetime
 try:
            create_date = datetime.datetime.strptime(create_date, "%Y/%m/%d").date()
        except Exception as e:
            create_date = datetime.datetime.now().date()
```

**数据库表设计**

![jobbole数据表设计](http://upload-images.jianshu.io/upload_images/1779926-909b623357abad96.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 三个num字段均设置不能为空，然后默认0.
- content设置为longtext
- 主键设置为url_object_id

**数据库驱动安装**
`pip install mysqlclient`

Linux报错解决方案:
ubuntu:
`sudo apt-get install libmysqlclient-dev`
centos:
`sudo yum install python-devel mysql-devel`

**保存到数据库pipeline(同步）编写**

```python
import MySQLdb
class MysqlPipeline(object):
    #采用同步的机制写入mysql
    def __init__(self):
        self.conn = MySQLdb.connect('127.0.0.1', 'root', 'password', 'article_spider', charset="utf8", use_unicode=True)
        self.cursor = self.conn.cursor()

    def process_item(self, item, spider):
        insert_sql = """
            insert into jobbole_article(title, url, create_date, fav_nums)
            VALUES (%s, %s, %s, %s)
        """
        self.cursor.execute(insert_sql, (item["title"], item["url"], item["create_date"], item["fav_nums"]))
        self.conn.commit()
```

**保存到数据库的(异步Twisted)编写**
因为我们的爬取速度可能大于数据库存储的速度。异步操作。
设置可配置参数
seeting.py设置
```
MYSQL_HOST = "127.0.0.1"
MYSQL_DBNAME = "article_spider"
MYSQL_USER = "root"
MYSQL_PASSWORD = "123456"
```
代码中获取到设置的可配置参数
twisted异步：

```python
import MySQLdb.cursors
from twisted.enterprise import adbapi

#连接池ConnectionPool
#    def __init__(self, dbapiName, *connargs, **connkw):
class MysqlTwistedPipline(object):
    def __init__(self, dbpool):
        self.dbpool = dbpool

    @classmethod
    def from_settings(cls, settings):
        dbparms = dict(
            host = settings["MYSQL_HOST"],
            db = settings["MYSQL_DBNAME"],
            user = settings["MYSQL_USER"],
            passwd = settings["MYSQL_PASSWORD"],
            charset='utf8',
            cursorclass=MySQLdb.cursors.DictCursor,
            use_unicode=True,
        )
        #**dbparms-->("MySQLdb",host=settings['MYSQL_HOST']
        dbpool = adbapi.ConnectionPool("MySQLdb", **dbparms)

        return cls(dbpool)

    def process_item(self, item, spider):
        #使用twisted将mysql插入变成异步执行
        query = self.dbpool.runInteraction(self.do_insert, item)
        query.addErrback(self.handle_error, item, spider) #处理异常

    def handle_error(self, failure, item, spider):
        #处理异步插入的异常
        print (failure)

    def do_insert(self, cursor, item):
        #执行具体的插入
        #根据不同的item 构建不同的sql语句并插入到mysql中
        insert_sql, params = item.get_insert_sql()
        cursor.execute(insert_sql, params)

``` 
可选django.items

https://github.com/scrapy-plugins/scrapy-djangoitem

可以让我们保存的item直接变成django的models.

####scrapy的itemloader来维护提取代码

itemloadr提供了一个容器，让我们配置某一个字段该使用哪种规则。
add_css add_value add_xpath
```python
from scrapy.loader import ItemLoader
# 通过item loader加载item
        front_image_url = response.meta.get("front_image_url", "")  # 文章封面图
        item_loader = ItemLoader(item=JobBoleArticleItem(), response=response)
        item_loader.add_css("title", ".entry-header h1::text")
        item_loader.add_value("url", response.url)
        item_loader.add_value("url_object_id", get_md5(response.url))
        item_loader.add_css("create_date", "p.entry-meta-hide-on-mobile::text")
        item_loader.add_value("front_image_url", [front_image_url])
        item_loader.add_css("praise_nums", ".vote-post-up h10::text")
        item_loader.add_css("comment_nums", "a[href='#article-comment'] span::text")
        item_loader.add_css("fav_nums", ".bookmark-btn::text")
        item_loader.add_css("tags", "p.entry-meta-hide-on-mobile a::text")
        item_loader.add_css("content", "div.entry")
        #调用这个方法来对规则进行解析生成item对象
        article_item = item_loader.load_item()

```
![直接使用itemloader的问题](http://upload-images.jianshu.io/upload_images/1779926-d34dc1f60a28f9f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 所有值变成了list
2. 对于这些值做一些处理函数
**item.py中对于item process处理函数**
MapCompose可以传入函数对于该字段进行处理，而且可以传入多个

```python
from scrapy.loader.processors import MapCompose
def add_mtianyan(value):
    return value+"-mtianyan"

 title = scrapy.Field(
        input_processor=MapCompose(lambda x:x+"mtianyan",add_mtianyan),
    )
```
*注意：此处的自定义方法一定要写在代码前面。*

```python
    create_date = scrapy.Field(
        input_processor=MapCompose(date_convert),
        output_processor=TakeFirst()
    )
```
只取list中的第一个值。

**自定义itemloader实现默认提取第一个**

```python
class ArticleItemLoader(ItemLoader):
    #自定义itemloader实现默认提取第一个
    default_output_processor = TakeFirst()
```

**list保存原值**

```python
def return_value(value):
    return value

front_image_url = scrapy.Field(
        output_processor=MapCompose(return_value)
    )
```

**下载图片pipeline增加if增强通用性**

```python
class ArticleImagePipeline(ImagesPipeline):
    #重写该方法可从result中获取到图片的实际下载地址
    def item_completed(self, results, item, info):
        if "front_image_url" in item:
            for ok, value in results:
                image_file_path = value["path"]
            item["front_image_path"] = image_file_path

        return item
```
**自定义的item带处理函数的完整代码**

```python
class JobBoleArticleItem(scrapy.Item):
    title = scrapy.Field()
    create_date = scrapy.Field(
        input_processor=MapCompose(date_convert),
    )
    url = scrapy.Field()
    url_object_id = scrapy.Field()
    front_image_url = scrapy.Field(
        output_processor=MapCompose(return_value)
    )
    front_image_path = scrapy.Field()
    praise_nums = scrapy.Field(
        input_processor=MapCompose(get_nums)
    )
    comment_nums = scrapy.Field(
        input_processor=MapCompose(get_nums)
    )
    fav_nums = scrapy.Field(
        input_processor=MapCompose(get_nums)
    )
    #因为tag本身是list，所以要重写
    tags = scrapy.Field(
        input_processor=MapCompose(remove_comment_tags),
        output_processor=Join(",")
    )
    content = scrapy.Field()
```
