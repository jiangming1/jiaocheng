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
##四、通过CrawlSpider对招聘网站拉钩网进行整站爬取

**推荐工具cmder**
http://cmder.net/
下载full版本，使我们在windows环境下也可以使用linux部分命令。
配置path环境变量

###1. 设计拉勾网的数据表结构

![拉勾网数据库表设计](http://upload-images.jianshu.io/upload_images/1779926-c22f6fb27848ef5a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###2. 初始化拉钩网项目并解读crawl源码
`scrapy genspider --list`
查看可使用的初始化模板
ailable templates:
  - basic
  - crawl
  - csvfeed
  - xmlfeed

```python
scrapy genspider -t crawl lagou www.lagou.com
```

**cmd与pycharm不同，mark root **
setting.py 设置目录

**crawl模板**

```python
class LagouSpider(CrawlSpider):
    name = 'lagou'
    allowed_domains = ['www.lagou.com']
    start_urls = ['http://www.lagou.com/']

    rules = (
        Rule(LinkExtractor(allow=r'Items/'), callback='parse_item', follow=True),
    )

    def parse_item(self, response):
        i = {}
        #i['domain_id'] = response.xpath('//input[@id="sid"]/@value').extract()
        #i['name'] = response.xpath('//div[@id="name"]').extract()
        #i['description'] = response.xpath('//div[@id="description"]').extract()
        return i
```

**源码阅读剖析**
https://doc.scrapy.org/en/1.3/topics/spiders.html#crawlspider

提供了一些可以让我们进行简单的follow的规则，link，迭代爬取

rules：
>规则，crawel spider读取并执行

parse_start_url(response)：

example：

rules是一个可迭代对象，里面有Rule实例->LinkExtractor的分析
`allow=('category\.php', ), callback='parse_item',`
allow允许的url模式。callback，要回调的函数名。
因为rules里面没有self，无法获取到方法。

```python
import scrapy
from scrapy.spiders import CrawlSpider, Rule
from scrapy.linkextractors import LinkExtractor

class MySpider(CrawlSpider):
    name = 'example.com'
    allowed_domains = ['example.com']
    start_urls = ['http://www.example.com']

    rules = (
        # Extract links matching 'category.php' (but not matching 'subsection.php')
        # and follow links from them (since no callback means follow=True by default).
        Rule(LinkExtractor(allow=('category\.php', ), deny=('subsection\.php', ))),

        # Extract links matching 'item.php' and parse them with the spider's method parse_item
        Rule(LinkExtractor(allow=('item\.php', )), callback='parse_item'),
    )

    def parse_item(self, response):
        self.logger.info('Hi, this is an item page! %s', response.url)
        item = scrapy.Item()
        item['id'] = response.xpath('//td[@id="item_id"]/text()').re(r'ID: (\d+)')
        item['name'] = response.xpath('//td[@id="item_name"]/text()').extract()
        item['description'] = response.xpath('//td[@id="item_description"]/text()').extract()
        return item 
```

**分析拉勾网模板代码**
1. 将http加上s
2. 重命名parse_item为我们自定义的parse_job
3. 点击`class LagouSpider(CrawlSpider):`的CrawlSpider，进入crawl源码
4. `class CrawlSpider(Spider):`可以看出它继承于spider
5.  *入口：`def start_requests(self):`*
6. alt+左右方向键，不同代码跳转
7. *5->之后默认parse CrawlSpider里面有parse函数。但是这次我们不能向以前一样覆盖*

Crawl.py核心函数parse。
>parse函数调用_parse_response

```python
 def parse(self, response):
        return self._parse_response(response, self.parse_start_url, cb_kwargs={}, follow=True)
```

_parse_response

1. 判断是否有callback即有没有self.parse_start_url
2. 我们可以重载parse_start_url加入自己的处理
3. 把参数传递给函数，并调用process_results函数

_parse_response函数

```python
    def _parse_response(self, response, callback, cb_kwargs, follow=True):
        if callback:
            cb_res = callback(response, **cb_kwargs) or ()
            cb_res = self.process_results(response, cb_res)
            for requests_or_item in iterate_spider_output(cb_res):
                yield requests_or_item

        if follow and self._follow_links:
            for request_or_item in self._requests_to_follow(response):
                yield request_or_item
```

parse_start_url的return值将会被process_results方法接收处理
如果不重写，因为返回为空，然后就相当于什么都没做

```python
    def process_results(self, response, results):
        return results
```

点击followlink

```python
    def set_crawler(self, crawler):
        super(CrawlSpider, self).set_crawler(crawler)
        self._follow_links = crawler.settings.getbool('CRAWLSPIDER_FOLLOW_LINKS', True)
```
如果setting中有这个参数，则可以进一步执行到parse

_requests_to_follow
1. 判断传入的是不是response，如果不是直接returns
2. 针对当前response设置一个空set，去重
3. 把self的rules通过enumerate变成一个可迭代对象
4. 跳转rules详情
5. 拿到link通过link_extractor.extract_links抽取出具体的link
6. 执行我们的process_links
7. link制作完成发起Request,回调_response_downloaded函数
8. 然后执行parse_respose

```python
    def _requests_to_follow(self, response):
        if not isinstance(response, HtmlResponse):
            return
        seen = set()
        for n, rule in enumerate(self._rules):
            links = [lnk for lnk in rule.link_extractor.extract_links(response)
                     if lnk not in seen]
            if links and rule.process_links:
                links = rule.process_links(links)
            for link in links:
                seen.add(link)
                r = Request(url=link.url, callback=self._response_downloaded)
                r.meta.update(rule=n, link_text=link.text)
                yield rule.process_request(r)
```
_compile_rules

1. 在我们初始化时会调用_compile_rules
2. `copy.copy(r) for r in self.rules]`将我们的rules进行一个copy
3. 调用回调函数get_method。
4. 调用rules里面我们定义的process_links
5. 调用rules里面我们定义的process_request

```python
    def _compile_rules(self):
        def get_method(method):
            if callable(method):
                return method
            elif isinstance(method, six.string_types):
                return getattr(self, method, None)

        self._rules = [copy.copy(r) for r in self.rules]
        for rule in self._rules:
            rule.callback = get_method(rule.callback)
            rule.process_links = get_method(rule.process_links)
            rule.process_request = get_method(rule.process_request)
```

        self.process_links = process_links
        self.process_request = process_request

可以通过在rules里面传入我们自己的处理函数，实现对url的自定义。
达到负载均衡，多地不同ip访问。

_response_downloaded
通过rule取到具体的rule
调用我们自己的回调函数

```python
    def _response_downloaded(self, response):
        rule = self._rules[response.meta['rule']]
        return self._parse_response(response, rule.callback, rule.cb_kwargs, rule.follow)
```

- allow ：符合这个url我就爬取
- deny : 符合这个url规则我就放弃
- allow_domin : 这个域名下的我才处理
- allow_domin : 这个域名下的我不处理
- restrict_xpaths：进一步限定xpath

```python
self, allow=(), deny=(), allow_domains=(), deny_domains=(), restrict_xpaths=(),
                 tags=('a', 'area'), attrs=('href',), canonicalize=True,
                 unique=True, process_value=None, deny_extensions=None, restrict_css=()
```
extract_links
如果有restrict_xpaths，他会进行读取执行

```python
    def extract_links(self, response):
        base_url = get_base_url(response)
        if self.restrict_xpaths:
            docs = [subdoc
                    for x in self.restrict_xpaths
                    for subdoc in response.xpath(x)]
        else:
            docs = [response.selector]
        all_links = []
        for doc in docs:
            links = self._extract_links(doc, response.url, response.encoding, base_url)
            all_links.extend(self._process_links(links))
        return unique_list(all_links)
```

get_base_url:

urllib.parse.urljoin替我们拼接好url

```python
def get_base_url(text, baseurl='', encoding='utf-8'):
    """Return the base url if declared in the given HTML `text`,
    relative to the given base url.

    If no base url is found, the given `baseurl` is returned.

    """

    text = to_unicode(text, encoding)
    m = _baseurl_re.search(text)
    if m:
        return moves.urllib.parse.urljoin(
            safe_url_string(baseurl),
            safe_url_string(m.group(1), encoding=encoding)
        )
    else:
        return safe_url_string(baseurl)
```

####编写rule规则

```python
    rules = (
        Rule(LinkExtractor(allow=("zhaopin/.*",)), follow=True),
        Rule(LinkExtractor(allow=("gongsi/j\d+.html",)), follow=True),
        Rule(LinkExtractor(allow=r'jobs/\d+.html'), callback='parse_job', follow=True),
    )
```
###3. 设计lagou的items
**需要用到的方法**

```python
from w3lib.html import remove_tags
def remove_splash(value):
    #去掉工作城市的斜线
    return value.replace("/","")

def handle_jobaddr(value):
    addr_list = value.split("\n")
    addr_list = [item.strip() for item in addr_list if item.strip()!="查看地图"]
    return "".join(addr_list)
```

**定义好的item**
```python
class LagouJobItem(scrapy.Item):
    #拉勾网职位信息
    title = scrapy.Field()
    url = scrapy.Field()
    url_object_id = scrapy.Field()
    salary = scrapy.Field()
    job_city = scrapy.Field(
        input_processor=MapCompose(remove_splash),
    )
    work_years = scrapy.Field(
        input_processor = MapCompose(remove_splash),
    )
    degree_need = scrapy.Field(
        input_processor = MapCompose(remove_splash),
    )
    job_type = scrapy.Field()
    publish_time = scrapy.Field()
    job_advantage = scrapy.Field()
    job_desc = scrapy.Field()
    job_addr = scrapy.Field(
        input_processor=MapCompose(remove_tags, handle_jobaddr),
    )
    company_name = scrapy.Field()
    company_url = scrapy.Field()
    tags = scrapy.Field(
        input_processor = Join(",")
    )
    crawl_time = scrapy.Field()

    
```

**重写的itemloader**
设置默认只提取第一个

```python
class LagouJobItemLoader(ItemLoader):
    #自定义itemloader
    default_output_processor = TakeFirst()
```

####4. 提取字段值并存入数据库
```python
    def parse_job(self, response):

        #解析拉勾网的职位
        item_loader = LagouJobItemLoader(item=LagouJobItem(), response=response)
        item_loader.add_css("title", ".job-name::attr(title)")
        item_loader.add_value("url", response.url)
        item_loader.add_value("url_object_id", get_md5(response.url))
        item_loader.add_css("salary", ".job_request .salary::text")
        item_loader.add_xpath("job_city", "//*[@class='job_request']/p/span[2]/text()")
        item_loader.add_xpath("work_years", "//*[@class='job_request']/p/span[3]/text()")
        item_loader.add_xpath("degree_need", "//*[@class='job_request']/p/span[4]/text()")
        item_loader.add_xpath("job_type", "//*[@class='job_request']/p/span[5]/text()")

        item_loader.add_css("tags", '.position-label li::text')
        item_loader.add_css("publish_time", ".publish_time::text")
        item_loader.add_css("job_advantage", ".job-advantage p::text")
        item_loader.add_css("job_desc", ".job_bt div")
        item_loader.add_css("job_addr", ".work_addr")
        item_loader.add_css("company_name", "#job_company dt a img::attr(alt)")
        item_loader.add_css("company_url", "#job_company dt a::attr(href)")
        item_loader.add_value("crawl_time", datetime.now())

        job_item = item_loader.load_item()

        return job_item
```
**获得的拉勾网item数据**
![拉勾网item数据](http://upload-images.jianshu.io/upload_images/1779926-1247e16b04708ea3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
####5. items中添加get_insert_sql实现存入数据库

```python
 def get_insert_sql(self):
        insert_sql = """
            insert into lagou_job(title, url, url_object_id, salary, job_city, work_years, degree_need,
            job_type, publish_time, job_advantage, job_desc, job_addr, company_name, company_url,
            tags, crawl_time) VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s)
            ON DUPLICATE KEY UPDATE salary=VALUES(salary), job_desc=VALUES(job_desc)
        """
        params = (
            self["title"], self["url"], self["url_object_id"], self["salary"], self["job_city"],
            self["work_years"], self["degree_need"], self["job_type"],
            self["publish_time"], self["job_advantage"], self["job_desc"],
            self["job_addr"], self["company_name"], self["company_url"],
            self["job_addr"], self["crawl_time"].strftime(SQL_DATETIME_FORMAT),
        )

        return insert_sql, params
```
