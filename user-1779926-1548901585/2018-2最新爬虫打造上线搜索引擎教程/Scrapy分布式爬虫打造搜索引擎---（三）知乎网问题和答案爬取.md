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
##三、知乎网问题和答案爬取

###1. 基础知识
####session和cookie机制

>cookie：
浏览器支持的存储方式
key-value

>http无状态请求，两次请求没有联系

<img src="http://upload-images.jianshu.io/upload_images/1779926-f01f9c33e578427d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" width="50%" height="50%"/>

session的工作原理

（1）当一个session第一次被启用时，一个唯一的标识被存储于本地的cookie中。

（2）首先使用session_start()函数，从session仓库中加载已经存储的session变量。

（3）通过使用session_register()函数注册session变量。

（4）脚本执行结束时，未被销毁的session变量会被自动保存在本地一定路径下的session库中.

####request模拟知乎的登录
**http状态码**

<img src="http://upload-images.jianshu.io/upload_images/1779926-e5d75b510a604f78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" width="50%" height="50%"/>

**获取crsftoken**

```python
def get_xsrf():
    #获取xsrf code
    response = requests.get("https://www.zhihu.com",headers =header)
    # # print(response.text)
    # text ='<input type="hidden" name="_xsrf" value="ca70366e5de5d133c3ae09fb16d9b0fa"/>'
    match_obj = re.match('.*name="_xsrf" value="(.*?)"', response.text)
    if match_obj:
        return (match_obj.group(1))
    else:
        return ""
```

python模拟知乎登录代码：

```python
# _*_ coding: utf-8 _*_

import requests
try:
    import cookielib
except:
    import http.cookiejar as cookielib
import re

__author__ = 'mtianyan'
__date__ = '2017/5/23 16:42'


import requests
try:
    import cookielib
except:
    import http.cookiejar as cookielib

import re

session = requests.session()
session.cookies = cookielib.LWPCookieJar(filename="cookies.txt")
try:
    session.cookies.load(ignore_discard=True)
except:
    print ("cookie未能加载")

agent = "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/53.0.2785.104 Safari/537.36"
header = {
    "HOST":"www.zhihu.com",
    "Referer": "https://www.zhizhu.com",
    'User-Agent': agent
}

def is_login():
    #通过个人中心页面返回状态码来判断是否为登录状态
    inbox_url = "https://www.zhihu.com/question/56250357/answer/148534773"
    response = session.get(inbox_url, headers=header, allow_redirects=False)
    if response.status_code != 200:
        return False
    else:
        return True

def get_xsrf():
    #获取xsrf code
    response = session.get("https://www.zhihu.com", headers=header)
    response_text = response.text
    #reDOTAll 匹配全文
    match_obj = re.match('.*name="_xsrf" value="(.*?)"', response_text, re.DOTALL)
    xsrf = ''
    if match_obj:
        xsrf = (match_obj.group(1))
        return xsrf


def get_index():
    response = session.get("https://www.zhihu.com", headers=header)
    with open("index_page.html", "wb") as f:
        f.write(response.text.encode("utf-8"))
    print ("ok")

def get_captcha():
    import time
    t = str(int(time.time()*1000))
    captcha_url = "https://www.zhihu.com/captcha.gif?r={0}&type=login".format(t)
    t = session.get(captcha_url, headers=header)
    with open("captcha.jpg","wb") as f:
        f.write(t.content)
        f.close()

    from PIL import Image
    try:
        im = Image.open('captcha.jpg')
        im.show()
        im.close()
    except:
        pass

    captcha = input("输入验证码\n>")
    return captcha

def zhihu_login(account, password):
    #知乎登录
    if re.match("^1\d{10}",account):
        print ("手机号码登录")
        post_url = "https://www.zhihu.com/login/phone_num"
        post_data = {
            "_xsrf": get_xsrf(),
            "phone_num": account,
            "password": password,
            "captcha":get_captcha()
        }
    else:
        if "@" in account:
            #判断用户名是否为邮箱
            print("邮箱方式登录")
            post_url = "https://www.zhihu.com/login/email"
            post_data = {
                "_xsrf": get_xsrf(),
                "email": account,
                "password": password
            }

    response_text = session.post(post_url, data=post_data, headers=header)
    session.cookies.save()

# get_index()
# is_login()
# get_captcha()
zhihu_login("phone", "password")
zhihu_login("shouji", "mima")
```
###2. scrapy创建知乎爬虫登录

```python
scrapy genspider zhihu www.zhihu.com
```

因为知乎我们需要先进行登录，所以我们重写它的start_requests

```python
    def start_requests(self):
        return [scrapy.Request('https://www.zhihu.com/#signin', headers=self.headers, callback=self.login)]

```
1. 下载首页然后回调login函数。

2. login函数请求验证码并回调login_after_captcha函数.此处通过meta将post_data传送出去，后面的回调函数来用。

```python
    def login(self, response):
        response_text = response.text
        #获取xsrf。
        match_obj = re.match('.*name="_xsrf" value="(.*?)"', response_text, re.DOTALL)
        xsrf = ''
        if match_obj:
            xsrf = (match_obj.group(1))

        if xsrf:
            post_url = "https://www.zhihu.com/login/phone_num"
            post_data = {
                "_xsrf": xsrf,
                "phone_num": "phone",
                "password": "password",
                "captcha": ""
            }

            import time
            t = str(int(time.time() * 1000))
            captcha_url = "https://www.zhihu.com/captcha.gif?r={0}&type=login".format(t)
            #请求验证码并回调login_after_captcha.
            yield scrapy.Request(captcha_url, headers=self.headers, 
            	meta={"post_data":post_data}, callback=self.login_after_captcha)
```
3. login_after_captcha函数将验证码图片保存到本地，然后使用PIL库打开图片，肉眼识别后在控制台输入验证码值
然后接受步骤一的meta数据，一并提交至登录接口。回调check_login检查是否登录成功。

```python
    def login_after_captcha(self, response):
        with open("captcha.jpg", "wb") as f:
            f.write(response.body)
            f.close()

        from PIL import Image
        try:
            im = Image.open('captcha.jpg')
            im.show()
            im.close()
        except:
            pass

        captcha = input("输入验证码\n>")

        post_data = response.meta.get("post_data", {})
        post_url = "https://www.zhihu.com/login/phone_num"
        post_data["captcha"] = captcha
        return [scrapy.FormRequest(
            url=post_url,
            formdata=post_data,
            headers=self.headers,
            callback=self.check_login
        )]
```

4. check_login函数，验证服务器的返回数据判断是否成功
scrapy会对request的URL去重(RFPDupeFilter)，加上dont_filter则告诉它这个URL不参与去重.

源码中的startrequest:

```python
    def start_requests(self):
        for url in self.start_urls:
            yield self.make_requests_from_url(url)
```
我们将原本的start_request的代码放在了现在重写的，回调链最后的check_login

```python
 def check_login(self, response):
        #验证服务器的返回数据判断是否成功
        text_json = json.loads(response.text)
        if "msg" in text_json and text_json["msg"] == "登录成功":
            for url in self.start_urls:
                yield scrapy.Request(url, dont_filter=True, headers=self.headers)
```
![登录代码流程](http://upload-images.jianshu.io/upload_images/1779926-31ff3c83ea890269.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###3. 知乎数据表设计
![知乎答案版本1](http://upload-images.jianshu.io/upload_images/1779926-cf6b7ac1027726fe.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>上图为知乎答案版本1

![知乎答案版本2](http://upload-images.jianshu.io/upload_images/1779926-e972fd3af04fc8f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>上图为知乎答案版本2

**设置数据表字段**

| 问题字段| 回答字段           | 
| ------------- |:-------------:| 
| zhihu_id | zhihu_id| 
| topics | url | 
| url |question_id| 
| title | author_id | 
| content |content| 
| answer_num |parise_num| 
| comments_num |comments_num| 
| watch_user_num |create_time| 
| click_num |update_time | 
| crawl_time |crawl_time| 

![知乎问题表](http://upload-images.jianshu.io/upload_images/1779926-ce3f15b285ed1e64.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![知乎答案表](http://upload-images.jianshu.io/upload_images/1779926-4be3a480d6f73679.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


**知乎url分析**

点具体问题下查看更多。
可获得接口：
>https://www.zhihu.com/api/v4/questions/25914034/answers?include=data%5B%2A%5D.is_normal%2Cis_collapsed%2Ccollapse_reason%2Cis_sticky%2Ccollapsed_by%2Csuggest_edit%2Ccomment_count%2Ccan_comment%2Ccontent%2Ceditable_content%2Cvoteup_count%2Creshipment_settings%2Ccomment_permission%2Cmark_infos%2Ccreated_time%2Cupdated_time%2Creview_info%2Crelationship.is_authorized%2Cis_author%2Cvoting%2Cis_thanked%2Cis_nothelp%2Cupvoted_followees%3Bdata%5B%2A%5D.author.follower_count%2Cbadge%5B%3F%28type%3Dbest_answerer%29%5D.topics&limit=20&offset=43&sort_by=default

**重点参数：**
`offset=43`
`isend = true`
`next` 
![点击更多接口返回](http://upload-images.jianshu.io/upload_images/1779926-942806c2e9ed83cc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**href="/question/25460323"** 

```python
all_urls = [parse.urljoin(response.url, url) for url in all_urls]
```

1. 从首页获取所有a标签。如果提取的url中格式为 /question/xxx 就下载之后直接进入解析函数parse_question
如果不是question页面则直接进一步跟踪。

```python 
def parse(self, response):
    """
            提取出html页面中的所有url 并跟踪这些url进行一步爬取
            如果提取的url中格式为 /question/xxx 就下载之后直接进入解析函数
            """
    all_urls = response.css("a::attr(href)").extract()
    all_urls = [parse.urljoin(response.url, url) for url in all_urls]
    #使用lambda函数对于每一个url进行过滤，如果是true放回列表，返回false去除。
    all_urls = filter(lambda x:True if x.startswith("https") else False, all_urls)
    for url in all_urls:
        match_obj = re.match("(.*zhihu.com/question/(\d+))(/|$).*", url)
        if match_obj:
            # 如果提取到question相关的页面则下载后交由提取函数进行提取
            request_url = match_obj.group(1)
            yield scrapy.Request(request_url, headers=self.headers, callback=self.parse_question)
        else:
            # 如果不是question页面则直接进一步跟踪
            yield scrapy.Request(url, headers=self.headers, callback=self.parse)
```

2. 进入parse_question函数处理
**创建我们的item

item要用到的方法ArticleSpider\utils\common.py：

```python
def extract_num(text):
    #从字符串中提取出数字
    match_re = re.match(".*?(\d+).*", text)
    if match_re:
        nums = int(match_re.group(1))
    else:
        nums = 0

    return nums
```
setting.py中设置
`SQL_DATETIME_FORMAT = "%Y-%m-%d %H:%M:%S"
SQL_DATE_FORMAT = "%Y-%m-%d"`
使用：

```python
from ArticleSpider.settings import SQL_DATETIME_FORMAT
```

**知乎的问题 item**

```python
class ZhihuQuestionItem(scrapy.Item):
    #知乎的问题 item
    zhihu_id = scrapy.Field()
    topics = scrapy.Field()
    url = scrapy.Field()
    title = scrapy.Field()
    content = scrapy.Field()
    answer_num = scrapy.Field()
    comments_num = scrapy.Field()
    watch_user_num = scrapy.Field()
    click_num = scrapy.Field()
    crawl_time = scrapy.Field()

    def get_insert_sql(self):
        #插入知乎question表的sql语句
        insert_sql = """
            insert into zhihu_question(zhihu_id, topics, url, title, content, answer_num, comments_num,
              watch_user_num, click_num, crawl_time
              )
            VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s)
            ON DUPLICATE KEY UPDATE content=VALUES(content), answer_num=VALUES(answer_num), comments_num=VALUES(comments_num),
              watch_user_num=VALUES(watch_user_num), click_num=VALUES(click_num)
        """
        zhihu_id = self["zhihu_id"][0]
        topics = ",".join(self["topics"])
        url = self["url"][0]
        title = "".join(self["title"])
        content = "".join(self["content"])
        answer_num = extract_num("".join(self["answer_num"]))
        comments_num = extract_num("".join(self["comments_num"]))

        if len(self["watch_user_num"]) == 2:
            watch_user_num = int(self["watch_user_num"][0])
            click_num = int(self["watch_user_num"][1])
        else:
            watch_user_num = int(self["watch_user_num"][0])
            click_num = 0

        crawl_time = datetime.datetime.now().strftime(SQL_DATETIME_FORMAT)

        params = (zhihu_id, topics, url, title, content, answer_num, comments_num,
                  watch_user_num, click_num, crawl_time)

        return insert_sql, params
```
**知乎问题回答item**

```python
class ZhihuAnswerItem(scrapy.Item):
    #知乎的问题回答item
    zhihu_id = scrapy.Field()
    url = scrapy.Field()
    question_id = scrapy.Field()
    author_id = scrapy.Field()
    content = scrapy.Field()
    parise_num = scrapy.Field()
    comments_num = scrapy.Field()
    create_time = scrapy.Field()
    update_time = scrapy.Field()
    crawl_time = scrapy.Field()

    def get_insert_sql(self):
        #插入知乎question表的sql语句
        insert_sql = """
            insert into zhihu_answer(zhihu_id, url, question_id, author_id, content, parise_num, comments_num,
              create_time, update_time, crawl_time
              ) VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s)
              ON DUPLICATE KEY UPDATE content=VALUES(content), comments_num=VALUES(comments_num), parise_num=VALUES(parise_num),
              update_time=VALUES(update_time)
        """

        create_time = datetime.datetime.fromtimestamp(self["create_time"]).strftime(SQL_DATETIME_FORMAT)
        update_time = datetime.datetime.fromtimestamp(self["update_time"]).strftime(SQL_DATETIME_FORMAT)
        params = (
            self["zhihu_id"], self["url"], self["question_id"],
            self["author_id"], self["content"], self["parise_num"],
            self["comments_num"], create_time, update_time,
            self["crawl_time"].strftime(SQL_DATETIME_FORMAT),
        )

        return insert_sql, params
```

**有了两个item之后，我们继续完善我们的逻辑**

```python
    def parse_question(self, response):
        #处理question页面， 从页面中提取出具体的question item
        if "QuestionHeader-title" in response.text:
            #处理新版本
            match_obj = re.match("(.*zhihu.com/question/(\d+))(/|$).*", response.url)
            if match_obj:
                question_id = int(match_obj.group(2))

            item_loader = ItemLoader(item=ZhihuQuestionItem(), response=response)
            item_loader.add_css("title", "h1.QuestionHeader-title::text")
            item_loader.add_css("content", ".QuestionHeader-detail")
            item_loader.add_value("url", response.url)
            item_loader.add_value("zhihu_id", question_id)
            item_loader.add_css("answer_num", ".List-headerText span::text")
            item_loader.add_css("comments_num", ".QuestionHeader-actions button::text")
            item_loader.add_css("watch_user_num", ".NumberBoard-value::text")
            item_loader.add_css("topics", ".QuestionHeader-topics .Popover div::text")

            question_item = item_loader.load_item()
        else:
            #处理老版本页面的item提取
            match_obj = re.match("(.*zhihu.com/question/(\d+))(/|$).*", response.url)
            if match_obj:
                question_id = int(match_obj.group(2))

            item_loader = ItemLoader(item=ZhihuQuestionItem(), response=response)
            # item_loader.add_css("title", ".zh-question-title h2 a::text")
            item_loader.add_xpath("title", "//*[@id='zh-question-title']/h2/a/text()|//*[@id='zh-question-title']/h2/span/text()")
            item_loader.add_css("content", "#zh-question-detail")
            item_loader.add_value("url", response.url)
            item_loader.add_value("zhihu_id", question_id)
            item_loader.add_css("answer_num", "#zh-question-answer-num::text")
            item_loader.add_css("comments_num", "#zh-question-meta-wrap a[name='addcomment']::text")
            # item_loader.add_css("watch_user_num", "#zh-question-side-header-wrap::text")
            item_loader.add_xpath("watch_user_num", "//*[@id='zh-question-side-header-wrap']/text()|//*[@class='zh-question-followers-sidebar']/div/a/strong/text()")
            item_loader.add_css("topics", ".zm-tag-editor-labels a::text")

            question_item = item_loader.load_item()

        yield scrapy.Request(self.start_answer_url.format(question_id, 20, 0), headers=self.headers, callback=self.parse_answer)
        yield question_item
```

**处理问题回答提取出需要的字段**

```python
    def parse_answer(self, reponse):
        #处理question的answer
        ans_json = json.loads(reponse.text)
        is_end = ans_json["paging"]["is_end"]
        next_url = ans_json["paging"]["next"]

        #提取answer的具体字段
        for answer in ans_json["data"]:
            answer_item = ZhihuAnswerItem()
            answer_item["zhihu_id"] = answer["id"]
            answer_item["url"] = answer["url"]
            answer_item["question_id"] = answer["question"]["id"]
            answer_item["author_id"] = answer["author"]["id"] if "id" in answer["author"] else None
            answer_item["content"] = answer["content"] if "content" in answer else None
            answer_item["parise_num"] = answer["voteup_count"]
            answer_item["comments_num"] = answer["comment_count"]
            answer_item["create_time"] = answer["created_time"]
            answer_item["update_time"] = answer["updated_time"]
            answer_item["crawl_time"] = datetime.datetime.now()

            yield answer_item

        if not is_end:
            yield scrapy.Request(next_url, headers=self.headers, callback=self.parse_answer)
```

**知乎提取字段流程图：**

![知乎问题及答案提取流程图](http://upload-images.jianshu.io/upload_images/1779926-c12492078a2369f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

深度优先：
1. 提取出页面所有的url，并过滤掉不需要的url
2. 如果是questionurl就进入question的解析
3. 把该问题的爬取完了然后就返回初始解析

####将item写入数据库

**pipelines.py错误处理**
插入时错误可通过该方法监控
```python
    def handle_error(self, failure, item, spider):
        #处理异步插入的异常
        print (failure)
```
**改造pipeline使其变得更通用**
原本具体硬编码的pipeline

```python
  def do_insert(self, cursor, item):
        #执行具体的插入
        insert_sql = """
                    insert into jobbole_article(title, url, create_date, fav_nums)
                    VALUES (%s, %s, %s, %s)
                """
        cursor.execute(insert_sql, (item["title"], item["url"], item["create_date"], item["fav_nums"]))
```

改写后的：

```python
    def do_insert(self, cursor, item):
		#根据不同的item 构建不同的sql语句并插入到mysql中
		insert_sql, params = item.get_insert_sql()
		cursor.execute(insert_sql, params)
```
可选方法一：

```python
    if item.__class__.__name__ == "JobBoleArticleItem":
    	#执行具体的插入
    	insert_sql = """
    	            insert into jobbole_article(title, url, create_date, fav_nums)
    	            VALUES (%s, %s, %s, %s)
    	        """
    	cursor.execute(insert_sql, (item["title"], item["url"], item["create_date"], item["fav_nums"]))
```

推荐方法：
把sql语句等放到item里面：
jobboleitem类内部方法

```python
    def get_insert_sql(self):
        insert_sql = """
            insert into jobbole_article(title, url, create_date, fav_nums)
            VALUES (%s, %s, %s, %s) ON DUPLICATE KEY UPDATE content=VALUES(fav_nums)
        """
        params = (self["title"], self["url"], self["create_date"], self["fav_nums"])

        return insert_sql, params
```

知乎问题：

```python
    def get_insert_sql(self):
        #插入知乎question表的sql语句
        insert_sql = """
            insert into zhihu_question(zhihu_id, topics, url, title, content, answer_num, comments_num,
              watch_user_num, click_num, crawl_time
              )
            VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s)
            ON DUPLICATE KEY UPDATE content=VALUES(content), answer_num=VALUES(answer_num), comments_num=VALUES(comments_num),
              watch_user_num=VALUES(watch_user_num), click_num=VALUES(click_num)
        """
        zhihu_id = self["zhihu_id"][0]
        topics = ",".join(self["topics"])
        url = self["url"][0]
        title = "".join(self["title"])
        content = "".join(self["content"])
        answer_num = extract_num("".join(self["answer_num"]))
        comments_num = extract_num("".join(self["comments_num"]))

        if len(self["watch_user_num"]) == 2:
            watch_user_num = int(self["watch_user_num"][0])
            click_num = int(self["watch_user_num"][1])
        else:
            watch_user_num = int(self["watch_user_num"][0])
            click_num = 0

        crawl_time = datetime.datetime.now().strftime(SQL_DATETIME_FORMAT)

        params = (zhihu_id, topics, url, title, content, answer_num, comments_num,
                  watch_user_num, click_num, crawl_time)

        return insert_sql, params
```

知乎回答：

```python
    def get_insert_sql(self):
        #插入知乎回答表的sql语句
        insert_sql = """
            insert into zhihu_answer(zhihu_id, url, question_id, author_id, content, parise_num, comments_num,
              create_time, update_time, crawl_time
              ) VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s)
              ON DUPLICATE KEY UPDATE content=VALUES(content), comments_num=VALUES(comments_num), parise_num=VALUES(parise_num),
              update_time=VALUES(update_time)
        """

        create_time = datetime.datetime.fromtimestamp(self["create_time"]).strftime(SQL_DATETIME_FORMAT)
        update_time = datetime.datetime.fromtimestamp(self["update_time"]).strftime(SQL_DATETIME_FORMAT)
        params = (
            self["zhihu_id"], self["url"], self["question_id"],
            self["author_id"], self["content"], self["parise_num"],
            self["comments_num"], create_time, update_time,
            self["crawl_time"].strftime(SQL_DATETIME_FORMAT),
        )

        return insert_sql, params
```
**第二次爬取到相同数据，更新数据**

```sql
ON DUPLICATE KEY UPDATE content=VALUES(content), answer_num=VALUES(answer_num), comments_num=VALUES(comments_num),
              watch_user_num=VALUES(watch_user_num), click_num=VALUES(click_num)
```

**调试技巧**

```python
            if match_obj:
                #如果提取到question相关的页面则下载后交由提取函数进行提取
                request_url = match_obj.group(1)
                yield scrapy.Request(request_url, headers=self.headers, callback=self.parse_question)
                #方便调试
                break
            else:
                #方便调试
                pass
                #如果不是question页面则直接进一步跟踪
                #方便调试
                # yield scrapy.Request(url, headers=self.headers, callback=self.parse)
```

```python
    #方便调试
        # yield question_item
```

**错误排查**
[key error] title 
pipeline中debug定位到哪一个item的错误。
