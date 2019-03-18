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
##八、elasticsearch搭建搜索引擎
>elasticsearch介绍：一个基于lucene的搜索服务器，分布式多用户的全文搜索引擎 java开发的 基于restful web接口 
自己搭建的网站或者程序，添加搜索功能比较困难
所以我们希望搜索解决方案要高效
零配置并且免费
能够简单的通过json和http与搜索引擎交互
希望搜索服务很稳定
简单的将一台服务器扩展到多台服务器

**内部功能：**
分词 搜索结果打分 解析搜索要求
全文搜索引擎：solr sphinx 
很多大公司都用elasticsearch 戴尔 Facebook 微软等等

1. elasticsearch对Lucene进行了封装，既能存储数据，又能分析数据，适合与做搜索引擎
关系数据搜索缺点：
    无法对搜素结果进行打分排序
    没有分布式，搜索麻烦，对程序员的要求比较高
    无法解析搜索请求，对搜索的内容无法进行解析，如分词等
    数据多了，效率低
    需要分词，把关系，数据，重点分出来

2. nosql数据库：
    文档数据库 json代码，在关系数据库中数据存储，需要存到多个表，内部有多对多等关系之类的，需要涉及到多个表才能将json里面的内容存下来，nosql直接将一个json的内容存起来，作为一个文档存档到数据库。
mongodb：

###1. elasticsearch安装与配置
>1. java sdk安装
2. elasticsearch安装官网下载 不使用官网的版本，提供原始的插件不多
3. elasticsearc-rtf github搜索，中文发行版，已经安装了很多插件 https://github.com/medcl/elasticsearch-rtf
4. 运行elasticsearch的方法，在bin文件目录下进入命令行，执行elasticsearch.bat
5.配置文件：elasticsearch-rtf\elasticsearch-rtf-master\config\elasticsearch.yml

![查看elasticsearch安装情况.png](http://upload-images.jianshu.io/upload_images/1779926-84340c76287bd524.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###2. elasticsearch两个重要插件：head和kibana的安装

head插件相当于Navicat，用于管理数据库，基于浏览器

https://github.com/mobz/elasticsearch-head

```
Running with built in server

git clone git://github.com/mobz/elasticsearch-head.git
cd elasticsearch-head
npm install
npm run start
open http://localhost:9100/
```

####配置elasticsearch与heade互通

![head安装完成](http://upload-images.jianshu.io/upload_images/1779926-45b0ab9d1030c91c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####kibana.bat

![kibana.png](http://upload-images.jianshu.io/upload_images/1779926-f7ba853e61e8b637.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###2. elasticsearch基础概念

1. 集群：一个或多个节点组织在一起
2. 节点：一个集群中的一台服务器
3. 分片：索引划分为多份的能力，允许水平分割，扩展容量，多个分片响应请求
4. 副本：分片的一份或多分，一个节点失败，其他节点顶上

|index | 数据库|
|type  | 表|
|document | 行|
|fields | 列|

集合搜索和保存：增加了五种方法：
OPTIONS & PUT & DELETE & TRACE & CONNECT

###3. 倒排索引：

![倒排索引](http://upload-images.jianshu.io/upload_images/1779926-a2d90a6f90511675.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![倒排索引](http://upload-images.jianshu.io/upload_images/1779926-e61a277016449a3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####倒排索引待解决的问题：
![倒排索引](http://upload-images.jianshu.io/upload_images/1779926-e61a277016449a3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![创建索引](http://upload-images.jianshu.io/upload_images/1779926-b39e996d99c70d2e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![head查看索引.png](http://upload-images.jianshu.io/upload_images/1779926-1604a12ed995ea8d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###4. elasticsearch命令

>PUT lagou/job/1
1为id

>PUT lagou/job/
不指明id自动生成uuid。

>修改部分字段
POST lagou/job/1/_update

>DELETE lagou/job/1

elasticserach批量操作:

`查询index为testdb下的job1表的id为1和job2表的id为2的数据`
```
GET _mget
{
    "docs":[
    {
    "_index":"testdb",
    "_type":"job1",
    "_id":1
    },
    {
    "_index":"testdb",
    "_type":"job2",
    "_id":2
    }
    ]
}
```

`index已经指定了，所有在doc中就不用指定了`

```
GET testdb/_mget{
    "docs":[
    {
    "_type":"job1",
    "_id":1
    },
    {
    "_type":"job2",
    "_id":2
    }
    ]
}
```

`连type都一样，只是id不一样`

```
GET testdb/job1/_megt
{
    "docs":[
    {
    "_id":1
    },
    {
    "_id":2
    }
    ]
}
```

`或者继续简写`

```
GET testdb/job1/_megt
{
    "ids":[1,2]
}
```

elasticsearch的bulk批量操作：可以合并多个操作，比如index，delete，update，create等等，包括从一个索引到另一个索引：

- action_and_meta_data\n
- option_source\n
- action_and_meta_data\n
- option_source\n
- ....
- action_and_meta_data\n
- option_source\n

每个操作都是由两行构成，除了delete除外，由元信息行和数据行组成
注意数据不能美化，即只能是两行的形式，而不能是经过解析的标准的json排列形式，否则会报错

```
POST _bulk
{"index":...}
{"field":...}
```

####elasticserach的mapping映射
>elasticserach的mapping映射：创建索引时，可以预先定义字段的类型以及相关属性，每个字段定义一种类型，属性比mysql里面丰富，前面没有传入，因为elasticsearch会根据json源数据来猜测是什么基础类型。M挨批评就是我们自己定义的字段的数据类型，同时告诉elasticsearch如何索引数据以及是否可以被搜索。
作用：会让索引建立的更加细致和完善，对于大多数是不需要我们自己定义

相关属性的配置

- String类型： 两种text keyword。text会对内部的内容进行分析，索引，进行倒排索引等，为设置为keyword则会当成字符串，不会被分析，只能完全匹配才能找到String。 在es5已经被废弃了
- 日期类型：date 以及datetime等
- 数据类型:integer long double等等
- bool类型
- binary类型
- 复杂类型：object nested
- geo类型：geo-point地理位置
- 专业类型：ip competition
- object ：json里面内置的还有下层{}的对象
- nested：数组形式的数据

####elasticserach查询：
大概分为三类：

- 基本查询:
- 组合查询：
- 过滤：查询同时，通过filter条件在不影响打分的情况下筛选数据

**match查询:**
>后面为关键词，关于python的都会提取出来，match查询会对内容进行分词，并且会自动对传入的关键词进行大小写转换，内置ik分词器会进行切分，如python网站，只要搜到存在的任何一部分，都会返回
GET lagou/job/_search

```
{
    "query":{
        "match":{
            "title":"python"
        }
    }
}
```

**term查询**
>区别，对传入的值不会做任何处理，就像keyword，只能查包含整个传入的内容的，一部分也不行，只能完全匹配

**terms查询**
>title里传入多个值，只要有一个匹配，就会返回结果

**控制查询的返回数量**

```
GET lagou/_serach
{
    "query":{
        "match":{
            "title":"python"
        }
    },
    "form":1,
    "size":2
}
```

通过这里就可以完成分页处理洛，从第一条开始查询两条

**match_all 返回所有**
GET lagou/_search
{
    "query":{
        "match_all":{}
    }
}

**match_phrase查询 短语查询**

```
GET lagou/_search
{
    "query":{
        "match_phrase":{
            "title":{
                "query":"python系统",
                "slop":6
            }
        }
    }
}
```

python系统，将其分词，分为词条，满足词条里面的所有词才会返回结果，slop参数说明两个词条之间的最小距离

**multi_match查询**
>比如可以指定多个字段，比如查询title和desc这两个字段包含python的关键词文档

```
GET lagou/_search
{
    "query":{
        "multi_match":{
            "query":"python",
            "fileds":["title^3","desc"]
        }
    }
}
```

>query为要查询的关键词 fileds在哪些字段里查询关键词，只要其中某个字段中出现了都返回
^3的意思为设置权重，在title中找到的权值为在desc字段中找到的权值的三倍

**指定返回字段**

```
GET lagou/_search{
    "stored_fields":["title","company_name"],
    "query":{
        "match":{
            "title":"pyhton"
        }
    }
}
```

**通过sort把结果排序**

```
GET lagou/_search
{
    "query";{
        "match_all":{}
    },
    "sort":[{
        "comments":{
            "order":"desc"
        }
    }]
}

```
sort是一个数组，里面是一个字典，key就是要sort的字段，asc desc是升序降序的意思

**查询范围 range查询**
GET lagou/_search
{
    "query";{
        "range":{
            "comments":{
                "gte":10,
                "lte":20,
                "boost":2.0
            }
        }
    }
}

>range是在query里面的，boost是权重，gte lte是大于等于 小于等于的意思
对时间的范围查询，则是以字符串的形式传入

**wildcard模糊查询，可以使用通配符**
`*`

**组合查询：bool查询**

bool查询包括了must should must_not filter来完成
格式如下：

```
bool:{
    "filter":[],
    "must":[],
    "should":[],
    "must_not":[],
}

```

###5. 把爬取的数据保存至elasticsearch

```python
class ElasticsearchPipeline(object):
    #将数据写入到es中

    def process_item(self, item, spider):
        #将item转换为es的数据
        item.save_to_es()

        return item
```

####elasticsearch-dsl-py
>High level Python client for Elasticsearch

`pip install elasticsearch-dsl`

####items.py 中将数据保存至es

```python
 def save_to_es(self):
        article = ArticleType()
        article.title = self['title']
        article.create_date = self["create_date"]
        article.content = remove_tags(self["content"])
        article.front_image_url = self["front_image_url"]
        if "front_image_path" in self:
            article.front_image_path = self["front_image_path"]
        article.praise_nums = self["praise_nums"]
        article.fav_nums = self["fav_nums"]
        article.comment_nums = self["comment_nums"]
        article.url = self["url"]
        article.tags = self["tags"]
        article.meta.id = self["url_object_id"]

        article.suggest = gen_suggests(ArticleType._doc_type.index, ((article.title,10),(article.tags, 7)))

        article.save()

        redis_cli.incr("jobbole_count")

        return
```
###6. elasticsearch结合django搭建搜索引擎

获取elasticsearch的查询接口

```python
 body={
                    "query":{
                        "multi_match":{
                            "query":key_words,
                            "fields":["tags", "title", "content"]
                        }
                    },
                    "from":(page-1)*10,
                    "size":10,
                    "highlight": {
                        "pre_tags": ['<span class="keyWord">'],
                        "post_tags": ['</span>'],
                        "fields": {
                            "title": {},
                            "content": {},
                        }
                    }
                }
```

使django与其交互。

![搜索界面](http://upload-images.jianshu.io/upload_images/1779926-1c550de187cbf9f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![结果界面](http://upload-images.jianshu.io/upload_images/1779926-9608d37c848236d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
