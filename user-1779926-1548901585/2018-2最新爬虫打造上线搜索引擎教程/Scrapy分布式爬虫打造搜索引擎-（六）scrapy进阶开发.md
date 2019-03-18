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
##六、scrapy进阶开发

###1. Selenium动态页面抓取

Selenium （浏览器自动化测试框架)
Selenium是一个用于Web应用程序测试的工具。Selenium测试直接运行在浏览器中，就像真正的用户在操作一样。支持的浏览器包括IE（7, 8, 9, 10, 11），Mozilla Firefox，Safari，Google Chrome，Opera等。这个工具的主要功能包括：测试与浏览器的兼容性——测试你的应用程序看是否能够很好得工作在不同浏览器和操作系统之上。测试系统功能——创建回归测试检验软件功能和用户需求。支持自动录制动作和自动生成 .Net、Java、Perl等不同语言的测试脚本


![Selenium架构图](http://upload-images.jianshu.io/upload_images/1779926-b3de4a107a839138.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
安装
`pip install selenium`

文档地址：
http://selenium-python.readthedocs.io/api.html
安装webdriver.exe

####天猫价格获取

```python

from selenium import webdriver
from scrapy.selector import Selector

browser = webdriver.Chrome(executable_path="C:/chromedriver.exe")

#天猫价格获取
browser.get("https://detail.tmall.com/item.htm?spm=a230r.1.14.3.yYBVG6&id=538286972599&cm_id=140105335569ed55e27b&abbucket=15&sku_properties=10004:709990523;5919063:6536025")
t_selector = Selector(text=browser.page_source)
print (t_selector.css(".tm-price::text").extract())
# print (browser.page_source)
browser.quit()
```

####知乎模拟登录

```python
from selenium import webdriver
from scrapy.selector import Selector

browser = webdriver.Chrome(executable_path="C:/chromedriver.exe")
#知乎模拟登陆
browser.get("https://www.zhihu.com/#signin")

browser.find_element_by_css_selector(".view-signin input[name='account']").send_keys("18487255487")
browser.find_element_by_css_selector(".view-signin input[name='password']").send_keys("ty158917")

browser.find_element_by_css_selector(".view-signin button.sign-button").click()
```

####微博模拟登录

微博开放平台api

```python
from selenium import webdriver
from scrapy.selector import Selector

browser = webdriver.Chrome(executable_path="C:/chromedriver.exe")
#selenium 完成微博模拟登录
browser.get("http://weibo.com/")
import time
time.sleep(5)
browser.find_element_by_css_selector("#loginname").send_keys("1147727180@qq.com")
browser.find_element_by_css_selector(".info_list.password input[node-type='password'] ").send_keys("tudoudou5283")
browser.find_element_by_xpath('//*[@id="pl_login_form"]/div/div[3]/div[6]/a').click()
```

####模拟JavaScript鼠标下滑

```python
from selenium import webdriver
from scrapy.selector import Selector

browser = webdriver.Chrome(executable_path="C:/chromedriver.exe")
#开源中国博客
browser.get("https://www.oschina.net/blog")
import time
time.sleep(5)
for i in range(3):
    browser.execute_script("window.scrollTo(0, document.body.scrollHeight); var lenOfPage=document.body.scrollHeight; return lenOfPage;")
    time.sleep(3)
```

####页面不加载图片

```python
from selenium import webdriver
from scrapy.selector import Selector

# 设置chromedriver不加载图片
chrome_opt = webdriver.ChromeOptions()
prefs = {"profile.managed_default_content_settings.images":2}
chrome_opt.add_experimental_option("prefs", prefs)

browser = webdriver.Chrome(executable_path="C:/chromedriver.exe",chrome_options=chrome_opt)
browser.get("https://www.oschina.net/blog")
```

####phantomjs无界面的浏览器获取天猫价格

```python
#phantomjs, 无界面的浏览器， 多进程情况下phantomjs性能会下降很严重

browser = webdriver.PhantomJS(executable_path="C:/phantomjs-2.1.1-windows/bin/phantomjs.exe")
browser.get("https://detail.tmall.com/item.htm?spm=a230r.1.14.3.yYBVG6&id=538286972599&cm_id=140105335569ed55e27b&abbucket=15&sku_properties=10004:709990523;5919063:6536025")
t_selector = Selector(text=browser.page_source)
print (t_selector.css(".tm-price::text").extract())
print (browser.page_source)
# browser.quit()
```

###2.selenium集成进scrapy

**如何集成**

####创建中间件。

```python
from selenium import webdriver
from scrapy.http import HtmlResponse
class JSPageMiddleware(object):

    #通过chrome请求动态网页
    def process_request(self, request, spider):
        if spider.name == "jobbole":
            browser = webdriver.Chrome(executable_path="C:/chromedriver.exe")
            spider.browser.get(request.url)
            import time
            time.sleep(3)
            print ("访问:{0}".format(request.url))

            return HtmlResponse(url=spider.browser.current_url, body=spider.browser.page_source, encoding="utf-8", request=request)
```

**使用selenium集成到具体spider中**
####信号量：

dispatcher.connect 信号的映射，当spider结束该做什么

```python
from scrapy.xlib.pydispatch import dispatcher
from scrapy import signals
    #使用selenium
    
    def __init__(self):
        self.browser = webdriver.Chrome(executable_path="D:/Temp/chromedriver.exe")
        super(JobboleSpider, self).__init__()
        dispatcher.connect(self.spider_closed, signals.spider_closed)
    
    def spider_closed(self, spider):
        #当爬虫退出的时候关闭chrome
        print ("spider closed")
        self.browser.quit()
```

####python下无界面浏览器

`pip install pyvirtualdisplay`

linux使用：

```python
from pyvirtualdisplay import Display
display = Display(visible=0, size=(800, 600))
display.start()

browser = webdriver.Chrome()
browser.get()
```

错误：cmd=['xvfb','help']
os error 

`sudo apt-get install xvfb`

`pip install xvfbwrapper`

scrapy-splash:
支持分布式，稳定性不如chorme

>https://github.com/scrapy-plugins/scrapy-splash

selenium grid
支持分布式

splinter
https://github.com/cobrateam/splinter

####scrapy的暂停重启

`scrapy crawl lagou -s JOBDIR=job_info/001`

pycharm进程直接杀死 kiil -9

一次 ctrl+c可接受信号

Lib\site-packages\scrapy\dupefilters.py

先hash将url变成定长的字符串
然后使用集合set去重

**telnet**
远程登录

`telnet localhost 6023` 连接当前spider
`est()`命令查看spider当前状态

`spider.settings["COOKIES_ENABLED"]`

Lib\site-packages\scrapy\extensions\telnet.py

**数据收集 & 状态收集**
Scrapy提供了方便的收集数据的机制。数据以key/value方式存储，值大多是计数值。 该机制叫做数据收集器(Stats Collector)，可以通过 Crawler API 的属性 stats 来使用。在下面的章节 常见数据收集器使用方法 将给出例子来说明。

无论数据收集(stats collection)开启或者关闭，数据收集器永远都是可用的。 因此您可以import进自己的模块并使用其API(增加值或者设置新的状态键(stat keys))。 该做法是为了简化数据收集的方法: 您不应该使用超过一行代码来收集您的spider，Scrpay扩展或任何您使用数据收集器代码里头的状态。

http://scrapy-chs.readthedocs.io/zh_CN/latest/topics/stats.html


状态收集，数据收集器

```python
    # 收集伯乐在线所有404的url以及404页面数
    handle_httpstatus_list = [404]
```
