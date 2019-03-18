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
##五、爬虫与反爬虫
###1. 基础知识
如何使我们的爬虫不被禁止掉

爬虫：
>自动获取数据的程序，关键是批量的获取

反爬虫：
>使用技术手段防止爬虫程序的方法

误伤：
>反爬虫技术将普通用户识别为爬虫，效果再好也不能用

学校，网吧，出口的公网ip只有一个，所以禁止ip不能用。

ip动态分配。a爬封b

成本：
>反爬虫人力和机器成本

拦截：
>拦截率越高，误伤率越高

反爬虫的目的：

![反爬虫的目的](http://upload-images.jianshu.io/upload_images/1779926-d48e923ecb3d20ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

爬虫与反爬虫的对抗过程：

![爬虫与反爬虫斗争](http://upload-images.jianshu.io/upload_images/1779926-77314219c9f1757e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用检查可以查看到价格，而查看网页源代码无法查看到价格字段。
scrapy下载到的网页时网页源代码。
js（ajax）填充的动态数据无法通过网页获取到。

###2. scrapy架构及源码介绍

![scrapy组件分析图](http://upload-images.jianshu.io/upload_images/1779926-8381473a93549bc0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![scrapy官方架构图](http://upload-images.jianshu.io/upload_images/1779926-8f93d40d66c9fe04.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1. 我们编写的spider，然后yield一个request发送给engine
2. engine拿到什么都不做然后给scheduler
3. engine会生成一个request给engine
4. engine拿到之后通过downloadermiddleware 给downloader
5. downloader再发送response回来给engine。
6. engine拿到之后，response给spider。
7. spider进行处理，解析出item & request，
8. item->给itempipeline；如果是request，跳转步骤二

path：articlespider3\Lib\site-packages\scrapy\core

- engine.py：
- scheduler.py
- downloader

- item
- pipeline
- spider

**engine.py：重要函数schedule**

1. enqueue_request：把request放scheduler
2. _next_request_from_scheduler:从调度器拿。

```python
    def schedule(self, request, spider):
        self.signals.send_catch_log(signal=signals.request_scheduled,
                request=request, spider=spider)
        if not self.slot.scheduler.enqueue_request(request):
            self.signals.send_catch_log(signal=signals.request_dropped,
                                        request=request, spider=spider)
```

articlespider3\Lib\site-packages\scrapy\core\downloader\handlers
>支持文件，ftp，http下载(https).

后期定制middleware：

- spidermiddlewire
- downloadmiddlewire

django和scrapy结构类似


###3. scrapy的两个重要类：request和response
类似于django httprequest

```python
yield Request(url=parse.urljoin(response.url, post_url))
```
request参数：

```python
class Request(object_ref):

    def __init__(self, url, callback=None, method='GET', headers=None, body=None,
                 cookies=None, meta=None, encoding='utf-8', priority=0,
                 dont_filter=False, errback=None):
```

cookies：
Lib\site-packages\scrapy\downloadermiddlewares\cookies.py

```python
cookiejarkey = request.meta.get("cookiejar")
```

- priority: 优先级，影响调度顺序
- dont_filter：我的同样的request不会被过滤
- errback：错误时的回调函数

https://doc.scrapy.org/en/1.2/topics/request-response.html?highlight=response

**errback example：**

```python
class ErrbackSpider(scrapy.Spider):
    name = "errback_example"
    start_urls = [
        "http://www.httpbin.org/",              # HTTP 200 expected
        "http://www.httpbin.org/status/404",    # Not found error
        "http://www.httpbin.org/status/500",    # server issue
        "http://www.httpbin.org:12345/",        # non-responding host, timeout expected
        "http://www.httphttpbinbin.org/",       # DNS error expected
    ]

    def start_requests(self):
        for u in self.start_urls:
            yield scrapy.Request(u, callback=self.parse_httpbin,
                                    errback=self.errback_httpbin,
                                    dont_filter=True)

    def parse_httpbin(self, response):
        self.logger.info('Got successful response from {}'.format(response.url))
        # do something useful here...

    def errback_httpbin(self, failure):
        # log all failures
        self.logger.error(repr(failure))

        # in case you want to do something special for some errors,
        # you may need the failure's type:

        if failure.check(HttpError):
            # these exceptions come from HttpError spider middleware
            # you can get the non-200 response
            response = failure.value.response
            self.logger.error('HttpError on %s', response.url)

        elif failure.check(DNSLookupError):
            # this is the original request
            request = failure.request
            self.logger.error('DNSLookupError on %s', request.url)

        elif failure.check(TimeoutError, TCPTimedOutError):
            request = failure.request
            self.logger.error('TimeoutError on %s', request.url)
```

**response类**

```python
 def __init__(self, url, status=200, headers=None, body=b'', flags=None, request=None):
        self.headers = Headers(headers or {})
```

**response的参数：**
request：yield出来的request，会放在response，让我们知道它是从哪里来的

###4. 自行编写随机更换useagent

1. setting中设置

```python
user_agent_list = [
    'Mozilla/5.0 (Windows NT 6.1; WOW64; rv:51.0) Gecko/20100101 Firefox/51.0',
    'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.104 Safari/537.36',
]
```

然后在代码中使用。

```python
	from settings import user_agent_list
    import random
    random_index =random.randint(0,len(user_agent_list))
    random_agent = user_agent_list[random_index]

    'User-Agent': random_agent
```

```python
                import random
                random_index = random.randint(0, len(user_agent_list))
                random_agent = user_agent_list[random_index]
                self.headers["User-Agent"] = random_agent
                yield scrapy.Request(request_url, headers=self.headers, callback=self.parse_question)
```

但是问题：每个request之前都得这样做。

###5. middlewire配置及编写fake UseAgent代理池
取消DOWNLOADER_MIDDLEWARES的注释状态

```python
DOWNLOADER_MIDDLEWARES = {
   'ArticleSpider.middlewares.MyCustomDownloaderMiddleware': 543,
}
```
articlespider3\Lib\site-packages\scrapy\downloadermiddlewares\useragent.py

```python
class UserAgentMiddleware(object):
    """This middleware allows spiders to override the user_agent"""

    def __init__(self, user_agent='Scrapy'):
        self.user_agent = user_agent

    @classmethod
    def from_crawler(cls, crawler):
        o = cls(crawler.settings['USER_AGENT'])
        crawler.signals.connect(o.spider_opened, signal=signals.spider_opened)
        return o

    def spider_opened(self, spider):
        self.user_agent = getattr(spider, 'user_agent', self.user_agent)

    def process_request(self, request, spider):
        if self.user_agent:
            request.headers.setdefault(b'User-Agent', self.user_agent)
```

重要方法process_request

**配置默认useagent为none

```python
DOWNLOADER_MIDDLEWARES = {
   'ArticleSpider.middlewares.MyCustomDownloaderMiddleware': 543,
    'scrapy.downloadermiddlewares.useragent.UserAgentMiddleware': None
}
```

**使用fakeuseragent**
`pip install fake-useragent`

settinf.py设置随机模式`RANDOM_UA_TYPE = "random"`

```python
from fake_useragent import UserAgent

class RandomUserAgentMiddlware(object):
    #随机更换user-agent
    def __init__(self, crawler):
        super(RandomUserAgentMiddlware, self).__init__()
        self.ua = UserAgent()
        self.ua_type = crawler.settings.get("RANDOM_UA_TYPE", "random")

    @classmethod
    def from_crawler(cls, crawler):
        return cls(crawler)

    def process_request(self, request, spider):
        def get_ua():
            return getattr(self.ua, self.ua_type)

        request.headers.setdefault('User-Agent', get_ua())
```

###6. 使用西刺代理创建ip代理池保存到数据库*

ip动态变化：重启路由器等

ip代理的原理：

不直接发送自己真实ip，而使用中间代理商（代理服务器），那么服务器不知道我们的ip也就不会把我们禁掉
setting.py设置
``

```python
class RandomProxyMiddleware(object):
    #动态设置ip代理
    def process_request(self, request, spider):
        request.meta["proxy"] = "http://111.198.219.151:8118"
```

**使用西刺代理创建代理池保存到数据库**

```python
# _*_ coding: utf-8 _*_
__author__ = 'mtianyan'
__date__ = '2017/5/24 16:27'
import requests
from scrapy.selector import Selector
import MySQLdb

conn = MySQLdb.connect(host="127.0.0.1", user="root", passwd="ty158917", db="article_spider", charset="utf8")
cursor = conn.cursor()


def crawl_ips():
    #爬取西刺的免费ip代理
    headers = {"User-Agent":"Mozilla/5.0 (Windows NT 6.1; WOW64; rv:52.0) Gecko/20100101 Firefox/52.0"}
    for i in range(1568):
        re = requests.get("http://www.xicidaili.com/nn/{0}".format(i), headers=headers)

        selector = Selector(text=re.text)
        all_trs = selector.css("#ip_list tr")


        ip_list = []
        for tr in all_trs[1:]:
            speed_str = tr.css(".bar::attr(title)").extract()[0]
            if speed_str:
                speed = float(speed_str.split("秒")[0])
            all_texts = tr.css("td::text").extract()

            ip = all_texts[0]
            port = all_texts[1]
            proxy_type = all_texts[5]

            ip_list.append((ip, port, proxy_type, speed))

        for ip_info in ip_list:
            cursor.execute(
                "insert proxy_ip(ip, port, speed, proxy_type) VALUES('{0}', '{1}', {2}, 'HTTP')".format(
                    ip_info[0], ip_info[1], ip_info[3]
                )
            )

            conn.commit()


class GetIP(object):
    def delete_ip(self, ip):
        #从数据库中删除无效的ip
        delete_sql = """
            delete from proxy_ip where ip='{0}'
        """.format(ip)
        cursor.execute(delete_sql)
        conn.commit()
        return True

    def judge_ip(self, ip, port):
        #判断ip是否可用
        http_url = "http://www.baidu.com"
        proxy_url = "http://{0}:{1}".format(ip, port)
        try:
            proxy_dict = {
                "http":proxy_url,
            }
            response = requests.get(http_url, proxies=proxy_dict)
        except Exception as e:
            print ("invalid ip and port")
            self.delete_ip(ip)
            return False
        else:
            code = response.status_code
            if code >= 200 and code < 300:
                print ("effective ip")
                return True
            else:
                print  ("invalid ip and port")
                self.delete_ip(ip)
                return False


    def get_random_ip(self):
        #从数据库中随机获取一个可用的ip
        random_sql = """
              SELECT ip, port FROM proxy_ip
            ORDER BY RAND()
            LIMIT 1
            """
        result = cursor.execute(random_sql)
        for ip_info in cursor.fetchall():
            ip = ip_info[0]
            port = ip_info[1]

            judge_re = self.judge_ip(ip, port)
            if judge_re:
                return "http://{0}:{1}".format(ip, port)
            else:
                return self.get_random_ip()



# print (crawl_ips())
if __name__ == "__main__":
    get_ip = GetIP()
    get_ip.get_random_ip()
```
**使用scrapy_proxies创建ip代理池**

`pip install scrapy_proxies`

收费，但是简单
https://github.com/scrapy-plugins/scrapy-crawlera

tor隐藏。vpn
http://www.theonionrouter.com/

###7. 通过云打码实现验证码的识别

http://www.yundama.com/

```python
# _*_ coding: utf-8 _*_
__author__ = 'mtianyan'
__date__ = '2017/6/24 16:48'

import json
import requests

class YDMHttp(object):
    apiurl = 'http://api.yundama.com/api.php'
    username = ''
    password = ''
    appid = ''
    appkey = ''

    def __init__(self, username, password, appid, appkey):
        self.username = username
        self.password = password
        self.appid = str(appid)
        self.appkey = appkey

    def balance(self):
        data = {'method': 'balance', 'username': self.username, 'password': self.password, 'appid': self.appid, 'appkey': self.appkey}
        response_data = requests.post(self.apiurl, data=data)
        ret_data = json.loads(response_data.text)
        if ret_data["ret"] == 0:
            print ("获取剩余积分", ret_data["balance"])
            return ret_data["balance"]
        else:
            return None

    def login(self):
        data = {'method': 'login', 'username': self.username, 'password': self.password, 'appid': self.appid, 'appkey': self.appkey}
        response_data = requests.post(self.apiurl, data=data)
        ret_data = json.loads(response_data.text)
        if ret_data["ret"] == 0:
            print ("登录成功", ret_data["uid"])
            return ret_data["uid"]
        else:
            return None

    def decode(self, filename, codetype, timeout):
        data = {'method': 'upload', 'username': self.username, 'password': self.password, 'appid': self.appid, 'appkey': self.appkey, 'codetype': str(codetype), 'timeout': str(timeout)}
        files = {'file': open(filename, 'rb')}
        response_data = requests.post(self.apiurl, files=files, data=data)
        ret_data = json.loads(response_data.text)
        if ret_data["ret"] == 0:
            print ("识别成功", ret_data["text"])
            return ret_data["text"]
        else:
            return None

def ydm(file_path):
    username = ''
    # 密码
    password = ''
    # 软件ＩＤ，开发者分成必要参数。登录开发者后台【我的软件】获得！
    appid = 
    # 软件密钥，开发者分成必要参数。登录开发者后台【我的软件】获得！
    appkey = ''
    # 图片文件
    filename = 'image/1.jpg'
    # 验证码类型，# 例：1004表示4位字母数字，不同类型收费不同。请准确填写，否则影响识别率。在此查询所有类型 http://www.yundama.com/price.html
    codetype = 5000
    # 超时时间，秒
    timeout = 60
    # 检查

    yundama = YDMHttp(username, password, appid, appkey)
    if (username == 'username'):
        print('请设置好相关参数再测试')
    else:
        # 开始识别，图片路径，验证码类型ID，超时时间（秒），识别结果
        return yundama.decode(file_path, codetype, timeout);

if __name__ == "__main__":
    # 用户名
    username = ''
    # 密码
    password = ''
    # 软件ＩＤ，开发者分成必要参数。登录开发者后台【我的软件】获得！
    appid = 
    # 软件密钥，开发者分成必要参数。登录开发者后台【我的软件】获得！
    appkey = ''
    # 图片文件
    filename = 'image/captcha.jpg'
    # 验证码类型，# 例：1004表示4位字母数字，不同类型收费不同。请准确填写，否则影响识别率。在此查询所有类型 http://www.yundama.com/price.html
    codetype = 5000
    # 超时时间，秒
    timeout = 60
    # 检查
    if (username == 'username'):
        print ('请设置好相关参数再测试')
    else:
        # 初始化
        yundama = YDMHttp(username, password, appid, appkey)

        # 登陆云打码
        uid = yundama.login();
        print('uid: %s' % uid)

        # 登陆云打码
        uid = yundama.login();
        print ('uid: %s' % uid)

        # 查询余额
        balance = yundama.balance();
        print ('balance: %s' % balance)

        # 开始识别，图片路径，验证码类型ID，超时时间（秒），识别结果
        text = yundama.decode(filename, codetype, timeout);


```

###8. cookie的禁用。& 设置下载速度

http://scrapy-chs.readthedocs.io/zh_CN/latest/topics/autothrottle.html

setting.py:

```python
# Disable cookies (enabled by default)
COOKIES_ENABLED = False
```

设置下载速度：

```python
# The initial download delay
#AUTOTHROTTLE_START_DELAY = 5
```

给不同的spider设置自己的setting值

```python
    custom_settings = {
        "COOKIES_ENABLED": True
    }
```

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
