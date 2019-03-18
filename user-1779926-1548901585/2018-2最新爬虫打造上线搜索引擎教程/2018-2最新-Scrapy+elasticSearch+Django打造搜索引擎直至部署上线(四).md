最终项目上线演示地址: http://search.mtianyan.cn
- 第四节：开工啦，开工啦。这节我们爬点数据试试手，并且存起来。存到json，mysql。下载图片通通试一试，通通试一试。

Github地址: https://github.com/mtianyan/ArticleSpider (欢迎先点个star后上车)

# 伯乐在线爬取所有文章
## scrapy框架介绍及网站分析

scrapy百度百科:
>Scrapy，Python开发的一个快速,高层次的屏幕抓取和web抓取框架，用于抓取web站点并从页面中提取结构化的数据。Scrapy用途广泛，可以用于数据挖掘、监测和自动化测试。

确定要爬取的网站: 伯乐在线

对于url结构进行了解，以及我们需要哪些数据。

`/all-posts/`的所有文章内容。

前提: 如果网站直接提供抓取所有文章内容的url。我们要首先考虑直接用。

观察列表分页中第二页url变化：

url = http://blog.jobbole.com/all-posts/page/2/

>因此我们可以通过只更换url中的page参数来实现爬取所有
>但是当它的文章更多的时候我们就得去改源码。

改良版: 获取下一页url形式来爬取所有内容。只要有下一页就提取下一页。 

>基础环境python3.5.3 

## 配置所需虚拟环境

```
mkvirtualenv articlespider3
创建虚拟环境
workon articlespider3
直接进入虚拟环境
deactivate
退出激活状态
workon
知道有哪些虚拟环境
```

虚拟环境内安装scrapy

>自行官网下载py35对应得whl文件进行pip离线安装

## 创建scrapy项目，目录结构分析。

### 命令行创建scrapy项目

```
scrapy startproject ArticleSpider
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-5c91292778510b6b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>这里可以看到，我们应用的是系统自带的模板。scrapy是可以自定义模板的。

### scrapy目录结构

通过pycharm 打开scrapy的项目。

![mark](http://upload-images.jianshu.io/upload_images/1779926-201af78ad1143cb3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到scrapy的目录结构如上图。

scrapy借鉴了django的项目思想

>`scrapy.cfg`：配置文件。 & `setings.py`：设置

Setting文件包含了很多scrapy相关的设置:

```
BOT_NAME = 'ArticleSpider' # 工程名
SPIDER_MODULES = ['ArticleSpider.spiders'] # 存放spider的路径
NEWSPIDER_MODULE = 'ArticleSpider.spiders'
```

pipelines.py:

>做跟数据存储相关的东西

middilewares.py:

>自己定义的middlewares 定义方法, 处理响应的IO操作

items.py(类似于django中的form)：

>定义数据保存的格式。比django的form简单，因为字段类型单一。
定义我们所要爬取的信息的相关属性。Item对象是种类似于表单，用来保存获取到的数据

spider文件夹：

存放我们具体的爬虫。

### 创建具体的spider

```
cd ArticleSpider
scrapy genspider jobbole blog.jobbole.com
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-9578322894c17be0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>使用基础模板为我们创建了具体的spider。

```
# -*- coding: utf-8 -*-
import scrapy


class JobboleSpider(scrapy.Spider):
    name = "jobbole"
    allowed_domains = ["blog.jobbole.com"]
    # start_urls是一个待爬取的列表.
    # spider会为我们请求下载网页，直接到parse阶段
    start_urls = ['http://blog.jobbole.com/']
    def parse(self, response):
        pass
```

>比如我们可以直接把url拼凑500个出来，直接放进列表中。

![mark](http://upload-images.jianshu.io/upload_images/1779926-095af3f9fe9ebc16.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 可以看到`start_requests`是对于`start_urls`进行遍历，然后交给`make_requests_from_url`来进行处理。
- `make_requests_from_url`会return一个request。会被yield命令直接交给scrapy的下载器。
下载器会去根据request下载东西。下载完成之后，会跑到Parse中。

### scrapy启动spider,main文件调试:

```
scrapy crawl jobbole
```

在windows报出错误：

`ImportError: No module named 'win32api'`

```
pip install pypiwin32 #解决
```

在项目根目录里创建`main.py` 作为调试工具文件

```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/1/17 0017 19:50'
from scrapy.cmdline import execute

import sys
import os

# 将项目根目录加入系统环境变量中国。
# os.path.abspath(__file__)为当前文件所在绝对路径
# os.path.dirname() 获取文件的父目录。

sys.path.append(os.path.dirname(os.path.abspath(__file__)))

print(os.path.abspath(__file__))
# D:\CodeSpace\PythonProject\ArticleSpider\main.py

print(os.path.dirname(os.path.abspath(__file__)))
# D:\CodeSpace\PythonProject\ArticleSpider

# 调用execute函数执行scrapy命令，相当于在控制台cmd输入该命令
# 可以传递一个数组参数进来:
execute(["scrapy", "crawl" , "jobbole"])
```

### 不遵守reboots协议设置

```
ROBOTSTXT_OBEY = False
```

在jobble.py打上断点:

```
def parse(self, response):
    pass
```

可以看到他返回的htmlresponse对象:

(HtmlResponse)对象内部：
- url: 'http://blog.jobbole.com/'
- body: `网页内容`
- `_DEFAULT_ENCODING= 'ascii'`
- encoding= 'utf-8'

可以看出scrapy已经为我们做到了将网页下载下来。而且编码也进行了转换

## xpath提取伯乐在线网页内容

>提取目标伯乐在线的一篇文章的标题，日期，评论，正文。

### xpath简介及语法

1. xpath简介
2. xpath术语

>- xpath使用路径表达式在xml和html中进行导航
- xpath包含有一个标准函数库
- xpath是一个w3c的标准

xpath节点关系:

html中被尖括号包起来的被称为一个节点。

1. 父节点 `上一层节点`
2. 子节点 `下一层节点`
3. 兄弟节点 `同胞节点`
4. 先辈节点 `父节节点，爷爷节点`
5. 后代节点 `儿子节点，孙子节点`

xpath语法:

| 表达式| 说明           | 
| ------------- |:-------------:| 
|article | 选取所有article元素的所有子节点 |
| /article|选取根元素article(html中根元素都是html;xml可以自定义根节点)    |
| article/a| 选取所有属于article的子元素的a元素|
|//div|选取所有div元素（不管出现在文档里的任何地方）|
|article//div|选取所有属于article元素的后代的div元素，不管它出现在article之下的任何位置|
|//@class|选取所有名为class的属性|

xpath语法-谓语:

| 表达式| 说明           | 
| ------------- |:-------------:| 
|/article/div[1|选取属于article子元素的第一个div元素|
|/article/div[last()]|选取属于article子元素的最后一个div元素|
|/article/div[last()-1]|选取属于article子元素的倒数第二个div元素|
|//div[@color]|选取所有拥有color属性的div元素|
|//div[@color='red']|选取所有color属性值为red的div元素|

xpath语法:

| 表达式| 说明           | 
| ------------- |:-------------:| 
|/div/*|选取属于div元素的所有子节点|
|//*|选取所有元素|
|//div[@*]|选取所有带属性的div 元素|
|//div/a 丨//div/p|选取所有div元素的a和p元素|
|//span丨//ul|选取文档中的span和ul元素|
|article/div/p丨//span|选取所有属于article元素的div元素的p元素以及文档中所有的 span元素|

[firebugs插件](https://addons.mozilla.org/en-us/firefox/addon/firebug/?src=dp-dl-dependencies)

取某一个网页上元素的xpath地址
>如:http://blog.jobbole.com/110287/

手写: /html/body/div[3]/div[3]/div[1]/div[1]/h1

在标题处右键使用Firefox浏览器最新开发版查看元素。
然后在`<h1>2016 腾讯软件开发面试题（部分）</h1>`右键查看xpath

复制出来的xpath: `/html/body/div[3]/div[3]/div[1]/div[1]/h1`

```python
# -*- coding: utf-8 -*-
import scrapy

class JobboleSpider(scrapy.Spider):
    name = "jobbole"
    allowed_domains = ["blog.jobbole.com"]
    start_urls = ['http://blog.jobbole.com/110287/']

    def parse(self, response):
        re_selector = response.xpath("/html/body/div[3]/div[3]/div[1]/div[1]/h1")
        print(re_selector)
        pass
```

调试`debug`可以看到`re_selector =(selectorlist)[]`

可以看到返回的是一个空列表, 列表是为了如果我们当前的xpath路径下还有层级目录
可以进行进一步的后续选取。

空说明没取到值, 我们可以来chorme里观察一下。

chorme取到的值

```
//*[@id="post-110287"]/div[1]/h1
```

html中全局id唯一。

chorme版代码:

```python
# -*- coding: utf-8 -*-
import scrapy


class JobboleSpider(scrapy.Spider):
    name = "jobbole"
    allowed_domains = ["blog.jobbole.com"]
    start_urls = ['http://blog.jobbole.com/110287/']

    def parse(self, response):
        # 取任何节点。id等于post-110287.
        re_selector = response.xpath('//*[@id="post-110287"]/div[1]/h1')
        # print(re_selector)
        pass
```

可以看出此时可以取到值,如果想取到里面的值直接`h1/text()`

我们右键检查是页面上的所有JavaScript等都运行完了之后的页面。
真实爬虫get到的内容是`查看网页源代码中内容`

>分析页面，可以发现页面内有一部html是通过JavaScript ajax交互来生成的，因此在f12检查元素时的页面结构里有，而xpath不对
xpath是基于html源代码文件结构来找的

xpath可以有多种多样的写法：

```python
re_selector = response.xpath("/html/body/div[1]/div[3]/div[1]/div[1]/h1/text()")
re2_selector = response.xpath('//*[@id="post-110287"]/div[1]/h1/text()')
re3_selector = response.xpath('//div[@class="entry-header"]/h1/text()')
```

~~推荐使用id型。因为页面id唯一。~~

推荐使用class型，因为后期循环爬取可扩展通用性强。

通过了解了这些此时我们已经可以抓取到页面的标题，此时可以使用xpath利器照猫画虎抓取任何内容。只需要点击右键查看xpath。

### cmd下运行python命令中文乱码解决方案

```
chcp 65001
```
### scrapy shell 运用

```
scrapy shell http://blog.jobbole.com/110287/
```

### 完整的xpath提取伯乐在线字段代码

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
        # '2016 腾讯软件开发面试题（部分）'
        create_date = response.xpath("//p[@class='entry-meta-hide-on-mobile']/text()").extract()[0].strip().replace("·","").strip()
        # 2017/02/18
        praise_nums =  response.xpath('//div[@class="post-adds"]/span/h10/text()').extract_first("")
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

- `xpath/text()`获取到的是一个`selector`。通过`extract()`获取一个内容值的list。而`extract_first("")|extract()[0]`则是获取list中第一个元素。
- xpath返回的是一个可以继续执行xpath的对象。而extract之后就是数组了。
- 获取p标签里面值，会过滤掉标签。
- `strip()`去除无效字符。`replace()`方法把我们不想要的字符替换成其他无效字符，然后`strip()`掉。
- `span[contains(@class, 'vote-post-up')]`有多个class属性值时。选取其中一个的写法。
- `[element for element in tag_list if not element.strip().endswith("评论")]`
使用列表生成式去掉以评论结尾的。
- `join`方法将数组变成字符串便于数据库一个字段保存。

## css选择器的使用：

渲染样式时:通过css选择器选择元素，为其添加样式。
`class="container"` 与之匹配的css选择器`.container`

| 表达式 | 说明 |
| ------------- | ------------- |
| * | 选择所有节点 |
| #container| 选择id为container的节点 |
| .container  | 选取所有class包含container的节点 |
| li a | 选取所有li下的所有a节点 |
| ul + p| (兄弟)选择ul后面的第一个p元素 |
| div#container > ul | (父子)选取id为container的div的第一个ul子元素 |

| 表达式 | 说明 |
| ------------- | ------------- |
| ul ~ p | 选取与ul相邻的所有p元素 |
| a[title] | 选取所有有title属性的a元素 |
| a[color="red"] | 选取所有color属性为red值的a |
| a[href*="jobbole"] | 选取所有href属性包含jobbole的a元素 |
| a[href^="http"] | 选取所有href属性值以http开头的a元素 |
| a[hre$=".jpg"] | 选取所有href属性值以.jpg结尾的a元素 |
| input[type=radio]:checked | 选取选中的radio的元素 |

| 表达式 | 说明 |
| ------------- | ------------- |
| div:not(#container) | 选取所有id非container的div元素 |
| li:nth-child(3) | 选取第三个li元素 |
| tr:nth-child(2n) | 第偶数个tr |

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
>`extract_first()`让我们免去做异常处理。数组下标有可能没有。

## 爬取所有文章

我们需要获取下一页的url并交给scrapy进行下载

### yield关键字

使用request下载详情页面，下载一篇文章的详情完成后回调方法`parse_detail()`提取文章内容中的字段

```python
yield Request(url=parse.urljoin(response.url,post_url),callback=self.parse_detail)
```
### scrapy.http: Request下载网页

```python
from scrapy.http import Request
Request(url=parse.urljoin(response.url,post_url),callback=self.parse_detail)
```
>`parse.urljoin`拼接网址应对herf内有可能网址不全

```python
from urllib import parse
url=parse.urljoin(response.url,post_url)
parse.urljoin("http://blog.jobbole.com/all-posts/","http://blog.jobbole.com/111535/")

# 结果为http://blog.jobbole.com/111535/
```
### class选择器空格 & 不加空格

```python
next_url = response.css(".next.page-numbers::attr(href)").extract_first("")
# 如果.next .pagenumber 是指两个class为层级关系。而不加空格为同一个标签
```
### twist异步机制

Scrapy使用了Twisted作为框架，Twisted有些特殊的地方是它是事件驱动的，并且比较适合异步的代码。在任何情况下，都不要写阻塞的代码。阻塞的代码包括:

 - 访问文件、数据库或者Web
 - 产生新的进程并需要处理新进程的输出，如运行shell命令
 - 执行系统层次操作的代码，如等待系统队列

### 实现全部文章字段下载的代码: 

```python
       def parse(self, response):
        """
        1. 获取文章列表页中的文章url交给scrapy下载并进行解析
        2. 获取下一页的url并交给scrapy进行下载,  下载完成后交给parse
        """
        # 解析列表页中的所有文章url并交给scrapy下载后并进行解析
        # 不使用extra成值的list可以进行二次筛选
        post_urls = response.css(
            "#archive .floated-thumb .post-thumb a::attr(href)").extract()
        # post_url 是我们每一页的具体的文章url。
        for post_url in post_urls:
            # 下面这个request是文章详情页面. 使用回调函数每下载完一篇就callback进行这一篇的具体解析。
            # 我们现在获取到的是完整的地址可以直接进行调用。如果不是完整地址: 根据response.url + post_url
            # def urljoin(base, url)完成url的拼接
            # 初始化好的Request如何交给scrapy进行下载: yield关键字
            yield  Request(url=parse.urljoin(response.url, post_url),callback=self.parse_detail)
            # Requ est(url=post_url, callback=self.parse_detail)

        # 提取下一页并交给scrapy进行下载
        next_url = response.css(".next.page-numbers::attr(href)").extract_first("")
        # 如果.next .pagenumber 是指两个class为层级关系。而不加空格为同一个标签
        if next_url:
            # 如果还有next url 就调用下载下一页，回调parse函数找出下一页的url。
            yield Request(url=parse.urljoin(response.url, next_url), callback=self.parse)
```
>parse_detail方法就是我们上一节实现的字段提取。

### 全部文章的逻辑流程图

![所有文章流程图](http://upload-images.jianshu.io/upload_images/1779926-d0f10707f0b49b08.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>要点: 当next_url还存在也就是还有下一页时我们需要下载的是next_url这个页面。

## scrapy的items整合字段

数据爬取的任务就是从`非结构的数据`中提取出`结构性的数据`。
`items`可以让我们自定义自己的字段（类似于字典，但比字典的功能更齐全）

>博主个人认为就是一个个item对象。每个字段是item对象的一个属性

>通过爬虫爬过来的数据通过item进行实例化, 当我们对`item`进行实例化之后。我们在我们的spider中对它做yield的时候。

比如我们通过`parse_detail`生成到了一个item对象时。
我们直接把这个类yield,scrapy识别到这是一个item的实例时。
会直接将item路由到pipeline中。

>优点: 可以在pipeline中做一个集中的数据保存，去重。


### Request使用的细节:解决图片问题

> 上一节中我们并没有进行图片字段的抓取。获取列表页的封面图。

目标: 希望获取到图片的url, 并把图片的url放到request里面。
通过request, 在它进行callback回调parse_detail时。我们能获取这个值保存下来。



>给request添加能传递的参数, `meta={}`传递一个字典过来。 

实现: 在下载列表页时将这个封面url获取到，并通过meta将他发送出去。在callback的回调函数中接收该值。

原始写法: `extract`之后则生成`list`列表，无法进行二次筛选。
所以我们将`extract`延后。便于我们对于列表每个单项文章既记录文章url,又记录图片地址。

将我们以前的代码:

```python
post_urls = response.css("#archive .floated-thumb .post-thumb a::attr(href)").extract()
```

改为如下:

```python
post_nodes = response.css("#archive .floated-thumb .post-thumb a")
        for post_node in post_nodes:
            #获取封面图的url
            image_url = post_node.css("img::attr(src)").extract_first("")
            post_url = post_node.css("::attr(href)").extract_first("")
```

在列表页的时候把获取到的封面图的url传给`parse_detail`的`response`

```python
# 发送
yield Request(url=parse.urljoin(response.url,post_url),meta={"front_image_url":image_url},callback=self.parse_detail)

# 接收
def parse_detail(self, response):
    front_image_url = response.meta.get("front_image_url", "")
```
urljoin的好处:

如果你没有域名，我就从response里取出来，如果你有域名则我对你起不了作用了

>图片的url有两种格式,全地址和早期的不全地址。这里`urljoin`就可以实现
对两者的兼容。

上面代码中我们将图片url放进了Request的meta信息中, 然后在response中获取meta进行接收。

### 编写Jobbole的items类

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

>这里可以看出items比django的models form弱在类型单一`Field`。
但是强在无论什么类型都可以接收。


### 实例化items并为其填充数据

import之后实例化，实例化之后填充：

```python
from ArticleSpider.items import JobBoleArticleItem
article_item = JobBoleArticleItem()
        article_item["title"] = title
        article_item["url"] = response.url
        article_item["create_date"] = create_date
        article_item["front_image_url"] = [front_image_url]
        article_item["praise_nums"] = praise_nums
        article_item["comment_nums"] = comment_nums
        article_item["fav_nums"] = fav_nums
        article_item["tags"] = tags
        article_item["content"] = content
```

### yield article_item将这个item传送到pipelines

```python
yield article_item
```
pipelines可以接收到传送过来的item

- 将setting.py中的pipeline配置取消注释

```
# Configure item pipelines
# See http://scrapy.readthedocs.org/en/latest/topics/item-pipeline.html
ITEM_PIPELINES = {
   'ArticleSpider.pipelines.ArticlespiderPipeline': 300,
}
```

此时debug可以发现,我们的item被传输到pipeline.我们可以将其进行存储到数据库等工作

>调试小技巧；将`next_url`换为`post_url`让它只爬一页。

### setting设置下载图片pipeline

>添加scrapy自带的imagepipeline。

```
ITEM_PIPELINES={
'scrapy.pipelines.images.ImagesPipeline': 1,
}
```

D:/CodeSpace/PythonEnvs/articlespider3/Lib/site-packages/scrapy/pipelines
里面有三个scrapy默认提供的pipeline: 分别提供了文件，图片，媒体的下载。

`ITEM_PIPELINES`是一个数据管道的登记表, 每一项具体的数字代表它的优先级，数字越小，越早进入。

数据在每个被声明的pipeline中流动,被处理之后流向下一个。

### setting设置哪个字段是图片


```python
import os

IMAGES_URLS_FIELD = "front_image_url"
project_dir = os.path.abspath(os.path.dirname(__file__))
IMAGES_STORE = os.path.join(project_dir, 'images')
```

- 设置哪个字段是图片, imagePipeline会在传来的url中自动寻找。
- 设置下载的路径: 新建文件夹images，放在项目目录下。




### 图片必备:安装PIL库

`pip install pillow`

报错处理:

```
raise ValueError('Missing scheme in request url: %s' % self._url)
ValueError: Missing scheme in request url: h

```

原因: 

>如果我们在setting中配置了`IMAGES_URLS_FIELD `,这个值在传递到pipeline时
会被当成list处理。而我们只传了一个值进来。

```
article_item["front_image_url"] = [front_image_url]
```

### 定制自己的pipeline使其下载图片后能保存下它的本地路径

目标: 将图片的本地路径保存起来


>imagesPipeline中有很多可以设置的参数

```
# IMAGES_MIN_HEIGHT = 100
# IMAGES_MIN_WIDTH = 100
```

设置下载图片的最小高度，宽度。可以帮我们过滤掉一部分小图片

重要函数；转换, 过滤图片.

`get_media_requests()`接收一个迭代器对象(我们之前设置的field)进行for循环下载图片
`item_completed` 可以获取到图片的实际下载存放地址

继承`ImagesPipeline`并重写`item_completed()`

```python
from scrapy.pipelines.images import ImagesPipeline

class ArticleImagePipeline(ImagesPipeline):
    # 重写该方法可从result中获取到图片的实际下载地址
    def item_completed(self, results, item, info):
        for ok, value in results:
            image_file_path = value["path"]
        item["front_image_path"] = image_file_path
        return item
```

>result是一个tuple, 而dict里面的path存放了文件的路径。
获取文件路径, 并填充进front_image_path。

一定要把item `return`出去，因为下一个pipeline还要接收。

setting中设置使用我们自定义的pipeline，而不是系统自带的

```python
ITEM_PIPELINES = {
   'ArticleSpider.pipelines.ArticlespiderPipeline': 300,
   # 'scrapy.pipelines.images.ImagesPipeline': 1,
    'ArticleSpider.pipelines.ArticleImagePipeline':1,
}
```

![自定义图片pipeline的调试信息](http://upload-images.jianshu.io/upload_images/1779926-b46e91f577c5bf6a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>上图通过调试来看results里面放着啥: 可以看到第一个值是一个布尔值, 表示有没有成功。
第二个值是一个字典: `checksum` `path` 原始`url`路径都存放在里面。

![保存下来的本地地址](http://upload-images.jianshu.io/upload_images/1779926-e3b33dc61bc8ab3a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 图片url的md5处理

新建package: `utils`

```python
import hashlib

def get_md5(url):
    m = hashlib.md5()
    m.update(url)
    return m.hexdigest()

if __name__ == "__main__":
    print(get_md5("http://jobbole.com".encode("utf-8")))
```

进阶版: 如果不确定用户传入的是不是Unicode

```python
def get_md5(url):
    # str就是unicode了
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

## 数据保存到本地文件以及mysql中
### 保存到本地json文件

`import codecs`避免打开文件时的一些编码问题。
自定义`JsonWithEncodingPipeline`实现json本地保存

```python 
# 自定义的将伯乐在线内容保存到本地json的pipeline
class JsonWithEncodingPipeline(object):
    # 自定义json文件的导出
    def __init__(self):
        # 使用codecs打开避免一些编码问题。
        self.file = codecs.open('article.json', 'w', encoding="utf-8")

    def process_item(self, item, spider):
        # 将item转换为dict,然后调用dumps方法生成json对象，false避免中文出错
        lines = json.dumps(dict(item), ensure_ascii=False) + "\n"
        self.file.write(lines)
        return item

    # 当spider关闭的时候: 这是一个spider_closed的信号量。
    def spider_closed(self, spider):
        self.file.close()
```

### setting.py注册JsonWithEncodingPipeline

```python
ITEM_PIPELINES = {
   'ArticleSpider.pipelines.JsonWithEncodingPipeline': 2,
   # 'scrapy.pipelines.images.ImagesPipeline': 1,
    'ArticleSpider.pipelines.ArticleImagePipeline':1,
}
```

### scrapy exporters: JsonItemExporter导出

scrapy/exporters.py

scrapy自带的导出：

 - 'CsvItemExporter', 
 - 'XmlItemExporter',
 - 'JsonItemExporter'

`from scrapy.exporters import JsonItemExporter`

```python
class JsonExporterPipleline(object):
    # 调用scrapy提供的json export导出json文件
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
'ArticleSpider.pipelines.JsonExporterPipeline ': 2
```

注意：注意：注意：在setting中注册pipeline时千万不能加空格。

## 保存到数据库(mysql)

数据库设计数据表，表的内容字段是和item中应该是一致的。
数据库与item的关系。类似于django中model与form的关系。

### 日期的转换，将字符串转换为datetime

```python
import datetime
try:
    create_date = datetime.datetime.strptime(create_date, "%Y/%m/%d").date()
except Exception as e:
    create_date = datetime.datetime.now().date()
```

### 数据库表设计

![mark](http://upload-images.jianshu.io/upload_images/1779926-beb9e5445828e317.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 三个num字段均设置不能为空，然后默认0.
- content设置为longtext
- 主键设置为url_object_id: 后面会利用这个主键进行更新

```
pip install mysqlclient
```

Linux报错解决方案:

```
ubuntu:
sudo apt-get install libmysqlclient-dev
centos
sudo yum install python-devel mysql-devel
```

### 保存到数据库pipeline(同步)编写

```python
import MySQLdb
class MysqlPipeline(object):
    #采用同步的机制写入mysql
    def __init__(self):
        self.conn = MySQLdb.connect('127.0.0.1', 'root', 'password', 'articlespider', charset="utf8", use_unicode=True)
        self.cursor = self.conn.cursor()

    def process_item(self, item, spider):
        insert_sql = """
            insert into jobbole_article(title, url, create_date, fav_nums)
            VALUES (%s, %s, %s, %s)
        """
        self.cursor.execute(insert_sql, (item["title"], item["url"], item["create_date"], item["fav_nums"]))
        self.conn.commit()
```

>如果插入出现异常将不为null都暂时去掉。

## 保存到数据库的(异步Twisted)编写[通用版]

>因为我们的爬取速度很有肯大于数据库存储的速度。
插入速度是跟不上我们的爬取速度的。

提供连接池使我们的mysql插入变为异步操作。

设置可配置参数`seeting.py`设置：

```
MYSQL_HOST = "127.0.0.1"
MYSQL_DBNAME = "articlespider"
MYSQL_USER = "root"
MYSQL_PASSWORD = "123456"
```

代码中获取到设置的可配置参数, 实现twisted异步：

```python
# 异步操作mysql插入
class MysqlTwistedPipeline(object):
    def __init__(self, dbpool):
        self.dbpool = dbpool

    @classmethod
    # 自定义组件或扩展很有用的方法: 这个方法名字固定, 是会被scrapy调用的。
    # 这里传入的cls是指当前的MysqlTwistedPipline class
    def from_settings(cls, settings):
        # setting值可以当做字典来取值
        dbparms = dict(
            host = settings["MYSQL_HOST"],
            db = settings["MYSQL_DBNAME"],
            user = settings["MYSQL_USER"],
            passwd = settings["MYSQL_PASSWORD"],
            charset='utf8',
            cursorclass=MySQLdb.cursors.DictCursor,
            use_unicode=True,
        )
        # 连接池ConnectionPool
        # def __init__(self, dbapiName, *connargs, **connkw):
        dbpool = adbapi.ConnectionPool("MySQLdb", **dbparms)

        # 此处相当于实例化pipeline, 要在init中接收。
        return cls(dbpool)

    def process_item(self, item, spider):
        # 使用twisted将mysql插入变成异步执行：参数1：我们自定义一个函数,里面可以写我们的插入逻辑
        query = self.dbpool.runInteraction(self.do_insert, item)
        # 添加自己的处理异常的函数
        query.addErrback(self.handle_error, item, spider)

    def do_insert(self, cursor, item):
        insert_sql = """
            insert into jobbole_article(title, url, create_date, fav_nums)
            VALUES (%s, %s, %s, %s)
        """
        # 使用VALUES实现传值
        cursor.execute(insert_sql, (item["title"], item["url"], item["create_date"], item["fav_nums"]))

    def handle_error(self, failure, item, spider):
        # 处理异步插入的异常
        print (failure)
``` 

>通过关键的方法: `@classmethod` `def from_settings(cls, settings):`
>scrapy提供的是一个异步的容器: 到底用哪个库连接mysql是我们可以指明的。

**adbapi**可以将我们mysql的操作变成异步操作。`adbapi.ConnectionPool`

升级通用版后面会讲到: 

```python
# 根据不同的item 构建不同的sql语句并插入到mysql中
insert_sql, params = item.get_insert_sql()
cursor.execute(insert_sql, params)
```

将django的orm集成进来: `django.items`

https://github.com/scrapy-plugins/scrapy-djangoitem

可以让我们保存的item直接变成django的models.

## itemloader来维护字段提取代码

`itemloadr`提供了一个容器，让我们配置某一个字段该使用哪种规则。

三个常用的方法: `add_css` `add_value` `add_xpath`

>可以让我们维护自己的字段提取代码: 并且能够有复用的可能。

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

![mark](http://upload-images.jianshu.io/upload_images/1779926-3a7429e44587c729.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 所有值变成了list
2. 对于这些值都没有经过后期处理函数处理。

>但是itemloader可以让代码变整洁, 甚至可以把这些规则存在数据库。 

### item.py中编写`input_processor`处理函数

MapCompose可以传入函数对于该字段进行处理，而且可以传入多个函数。

```python
from scrapy.loader.processors import MapCompose

def add_mtianyan(value):
    return value+"-mtianyan"

 title = scrapy.Field(
        input_processor=MapCompose(lambda x:x+"mtianyan",add_mtianyan),
    )
```


**注意：此处的自定义方法一定要写在代码前面。**

```python
# 时间转换
def date_convert(value):
    try:
        create_date = datetime.datetime.strptime(value, "%Y/%m/%d").date()
    except Exception as e:
        create_date = datetime.datetime.now().date()

    return create_date

    create_date = scrapy.Field(
        input_processor=MapCompose(date_convert),
        output_processor=TakeFirst()
    )
```

`TakeFirst`实现只取list中的第一个值。不需要每个都加上output。

### 自定义itemloader实现默认提取第一个

```python
class ArticleItemLoader(ItemLoader):
    #自定义itemloader实现默认提取第一个
    default_output_processor = TakeFirst()
```

将jobbole.py中的itemloader替换为我们自己的。

```
item_loader = ArticleItemLoader(item=JobBoleArticleItem(), response=response)
```

### list保存原值

```python
def return_value(value):
    return value

front_image_url = scrapy.Field(
        output_processor=MapCompose(return_value)
    )
```

对于字段的预处理进行单独方法的提取，做到代码重用。


### get_nums & remove_comment_tags

```
def get_nums(value):
    match_re = re.match(".*?(\d+).*", value)
    if match_re:
        nums = int(match_re.group(1))
    else:
        nums = 0

    return nums

# 去除标签中提取的评论方法
def remove_comment_tags(value):
    if "评论" in value:
        return ""
    else:
        return value
```
### 下载图片pipeline增加if增强通用性

>这里我们的pipeline是会重用的。可能后面的爬虫的items也会经过这个pipeline。
这就需要我们做出判断: items中有这个字段再执行

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

### 自定义的item带处理函数的完整代码**

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
