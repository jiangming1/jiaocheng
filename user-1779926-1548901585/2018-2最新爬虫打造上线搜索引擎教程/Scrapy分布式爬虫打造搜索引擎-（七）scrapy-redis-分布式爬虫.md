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
##七、scrapy-redis 分布式爬虫

###1. 分布式爬虫设计及redis介绍

多个爬虫如何进行调度，一个集中的状态管理器

优点：

- 利用多机器带宽
- 利用多ip加速爬取速度

两个问题：

1. request队列的集中管理
2. 去重集中管理

分布式。

###2. redis命令
hexists course_dict mtianyan
hexists course_dict mtianyan2

>Redis HEXISTS命令被用来检查哈希字段是否存在。
返回值
回复整数，1或0。
- 1, 如果哈希包含字段。
- 0 如果哈希不包含字段，或key不存在。

hdel course_dict mtianyan
>Redis HDEL命令用于从存储在键散列删除指定的字段。如果没有这个哈希中存在指定的字段将被忽略。如果键不存在，它将被视为一个空的哈希与此命令将返回0。
返回值回复整数，从散列中删除的字段的数量，不包括指定的但不是现有字段。

hgetall course_dict

>Redis Hgetall 命令用于返回哈希表中，所有的字段和值。
在返回值里，紧跟每个字段名(field name)之后是字段的值(value)，所以返回值的长度是哈希表大小的两倍。

hset course_dict bobby "python scrapy"

>Redis Hset 命令用于为哈希表中的字段赋值 。
如果哈希表不存在，一个新的哈希表被创建并进行 HSET 操作。
如果字段已经存在于哈希表中，旧值将被覆盖。

hkey course_dict

>Redis Keys 命令用于查找所有符合给定模式 pattern 的 key 。。

hvals course_dict

>Redis Hvals 命令返回哈希表所有字段的值。

lpush mtianyan "scary"
rpush mtianyan "scary"

>存入key-value

lrange mtianyan 0 10

>取出mtianyan的0到10

![redis命令.png](http://upload-images.jianshu.io/upload_images/1779926-82031cfa2b9af70c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

|命令        | 说明   |  
| --------   | -----:  | 
| lpop/rpop      | 左删除/右删除 | 
| llen mtianyan      |   长度  |
| lindex mtianyan 3      |    第几个元素   | 
| sadd        |    集合做减法  | 
| siner      |   交集   | 
| spop       |    随机删除   | 
| srandmember        |    随机选择多个元素   | 
| smembers         |    获取set所有元素   | 
| srandmember        |    随机选择多个元素   | 
| zadd        |    每个数有分数  | 
| zcount key 0 100      |    0-100分数据量统计  | 

###3. scrapy-redis搭建分布式爬虫

需要的环境：
>Python 2.7, 3.4 or 3.5
Redis >= 2.8
Scrapy >= 1.1
redis-py >= 2.10

`pip install redis`

**setting.py设置**

```python
SCHEDULER = "scrapy_redis.scheduler.Scheduler"
DUPEFILTER_CLASS = "scrapy_redis.dupefilter.RFPDupeFilter"
ITEM_PIPELINES = {
    'scrapy_redis.pipelines.RedisPipeline': 300
}
```

**要继承redisspider**

```python
from scrapy_redis.spiders import RedisSpider

class MySpider(RedisSpider):
    name = 'myspider'

    def parse(self, response):
        # do stuff
        pass
```

**启动spider**

`scrapy runspider myspider.py`

####push urls to redis:放置初始url进入队列

`redis-cli lpush myspider:start_urls http://google.com`

**搭建示例**
1. 创建新的scrapy项目
2. 去github拷贝scrapy-redis源码

不同spider使用不同redis list
将队列从内存放入redis中
next_requests

所有的yield出去的request会被
ScrapyRedisTest\scrapy_redis\scheduler.py
的以及重写的enqueue_request接收
