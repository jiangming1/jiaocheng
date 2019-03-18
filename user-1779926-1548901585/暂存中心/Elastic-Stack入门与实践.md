
## Elastic Stack 产品简介

- 安装jdk1.8
- 下载安装es
- 运行: bin/elasticsearch

下载tar包 & 解压 & 运行

基本的信息，集群名，Lucene版本。

ElasticSearch配置说明:

配置文件位于config目录中:
	- elasticsearch.yml es的相关设置
	- jvm.options jvm的相关参数
	- log4j2.properties 日志相关配置

内存大小，一些参数。日志输出。

```
-Xms2g
-Xms2g
```
内存不是很大可以改小。

![mark](http://myphoto.mtianyan.cn/blog/180203/iAc47fLB8F.png?imageslim)

yml关键配置说明:
- cluster.name 集群名称，以此作为是否同一集群的判断条件
- node.name 节点名称，以此作为集群中不同节点的区分条件
- network.host/http.port 网络地址和端口。用于http和transport服务使用
- path.data 数据存储地址
- path.log 日志存储地址

ElasticSearch 配置说明:

Development 与 Production 模式说明

- 以transport的地址是否绑定在localhost为判断标准 network.host
- Development 模式下载启动时会以warning的方式提示配置检查异常
- Production模式在启动时会以error的方式提示配置异常并退出

启动之前会进行检查。如果改在了真实的ip上，就会进行这些检查

参数修改的第二种方式；

```
bin/elasticsearch -Ehttp.port=19200
```

通过-E参数名称来修改配置

## ElasticSearch 本地启动集群。

```
bin\elasticsearch
bin\elasticsearch -E http.port=8200 -E path.data=node2
bin\elasticsearch -E http.port=7200 -E path.data=node3
```
通过接口查看是否是集群

```
8200/_cat/nodes?v
```

![mark](http://myphoto.mtianyan.cn/blog/180203/bDl6cC7B9h.png?imageslim)

`*`代表主节点.

```
8200/_cluster/stats
```

可以看到集群的很多详细信息。

### Kibana安装与运行

下载安装 Kibana

运行；

```
bin/kibana
```

下载与es相同的版本。Kibana 是nodejs开发的，所以要下载对应版本的。

前往config目录修改kibana.yml中es.url的说明。

默认使用端口5601

### Kibana配置详解

- 配置位于config文件夹中

Kibana.yml 关键配置说明

- server.host/server.port 访问Kibana用的地址和端口。

>如果希望外网可以访问需要修改一下, 你需要修改为指定的端口。

- elasticSearch.url 待访问 elasticSearch的地址

### Kibana常用功能说明

- Discover 数据搜索查看
- Visualize 图表制作
- Dashboard 仪表盘制作 将图表进行综合展示
- Timelion 时序数据的高级可视化分析(写一些查询语言)
- Devtools 开发者工具
- management 配置管理

### ElasticSearch 常用术语

- Document 文档数据
- Index索引 (Nysql中数据库)
- Type 索引中的数据类型
- Field 字段，文档的属性。
- Query DSL 查询语法

### ElasticSearch CRUD

- Create 创建文档
- Read 读取文档
- Update 更新文档
- delete 删除文档

![mark](http://myphoto.mtianyan.cn/blog/180203/ek6DB3dCAA.png?imageslim)

在index accounts 中的type person中创建了id为1的文档。

文档内容为下面json格式的定义。 里面的name等都是文档的字段 ，实体的属性。

![mark](http://myphoto.mtianyan.cn/blog/180203/ed38HeKj06.png?imageslim)

点击左下角collapse可以让界面更简约一点。

![mark](http://myphoto.mtianyan.cn/blog/180203/IFmE22mg5E.png?imageslim)

获取文档通过文档的id

使用 post `/accounts/person/1/_update`

删除文档

```
DELETE /accounts/person/1

DELETE /accounts
```
### Es查询简介

![mark](http://myphoto.mtianyan.cn/blog/180203/HJHah87mHa.png?imageslim)

1. 直接在查询的url后面跟上`q` 跟上查询语法
2. Query DSL 通过json格式查询

![mark](http://myphoto.mtianyan.cn/blog/180203/Fj94kfhC14.png?imageslim)


```
GET /account/person/_search?q=john
GET accounts/person/_search
{
	"query": {
		"term": {
			"name":{
				"value": "alfred"
			}
		}
	}
}

```

以body形式发起请求。

es官网的reference docs中有关于Query DSL的说明。

## Beats 入门

Lightweight Data Shipper 轻量级的数据传送者

- Filebeat 日志文件
- Metricbeat 度量数据 cpu nginx 内存的度量信息(可视化的展示分析报告)
- Packetbeat主要是对网络数据。
- winlogbeat windows 数据
- Heartbeat 健康检查

Filebeat简介

- 处理流程:
	- 输入input
	- 处理filter
	- 输出output

![mark](http://myphoto.mtianyan.cn/blog/180203/6HJjLGfmGI.png?imageslim)

filebeat可以输出到多种地方，es, logstach, kafka, redis

由prospector (观察者勘探者) & Harvester (收割者)组成

一个监听一个收割。会针对每一个文件启动一个harvest。

filebeat Input配置简介

yaml 语法；

![mark](http://myphoto.mtianyan.cn/blog/180203/35a3i5FJL1.png?imageslim)

yaml对于不止一个prospector 也就是数组是通过`-` 实现的。

输入类型:`log` paths 用于指定日志的路径。

可以配置多个日志路径。input有两个类型: 一个是log 一个是stdin

### Filebeat Output 配置简介

- Console & elasticSearch & logstash & kafka & redis & file

![mark](http://myphoto.mtianyan.cn/blog/180203/jDb9kgLJmg.png?imageslim)

输出到elasticSearch中。如果你对于es加了权限认证，你要加入用户名和密码。

![mark](http://myphoto.mtianyan.cn/blog/180203/070eLHhAG6.png?imageslim)

开发的时候经常输出到控制台。pretty 做json的格式化

### Filebeat Filter配置简介

- Input时处理: Include_lines & exclude_lines & exclude_files

达到条件我就读入或者不读入这一行。当文件名不符合时被排除

- Output前处理 processer

drop_event 满足某一个条件不输出而是直接扔掉。
drop_fields 扔掉某一个字段
decode_json_fields 符合字段中的json格式做一个解析
Include_fields 加入一些字段，或只想取一些字段。

![mark](http://myphoto.mtianyan.cn/blog/180203/8lA5JED3G1.png?imageslim)

当我们匹配到一个正则表达式。message字段是以dbg开头。就把他drop掉。

![mark](http://myphoto.mtianyan.cn/blog/180203/hH2fGJFBcE.png?imageslim)

Inner字段里面是一个json。但是内部做了一个字符串处理。通过processors的处理

![mark](http://myphoto.mtianyan.cn/blog/180203/akaiK5ldA9.png?imageslim)

Filebeat + ElasticSearch Ingest Node

Filebeat缺乏数据转换的能力

5.0 ElasticSearch新增ingest node

- 新增的node类型
- 在数据写入es前对数据进行处理转换。
- pipeline api

如果想快速的存储进es中

写合适的Filebeat的配置文件，配置pipeline。对于Kibana进行配置、

官方推出Filebeat Module 实现开箱即用

![mark](http://myphoto.mtianyan.cn/blog/180203/ajJB4I8lG9.png?imageslim)

指定nginx路径。使用nginx的module。导入Kibana。

最佳实践的参考。

### Filebeat 收集nginx log

- 通过stdin 收集日志
- 通过console 输出结果

demo 实践

beats 下载。 filebeat golang开发。下载对应平台的。

data存读取收集到的日志，执行文件，最全的配置。

module是我们上面介绍的模块。

```
head -n 2 ~/Downloads/nginx_logs/nginx.log
```

设置filebea的输入输出

![mark](http://myphoto.mtianyan.cn/blog/180203/hjaL8j54D7.png?imageslim)

输出:

![mark](http://myphoto.mtianyan.cn/blog/180203/9baG7KCbBH.png?imageslim)

```
head -n 2 ~/Downloads/nginx_logs/nginx.log|./filebeat -e -c nginx.yml
```

使用Filebeat来接收日志

输入了两条，Filebeat输出了两条。nginx日志的每一条变成了一个json

### Packetbeat简介

实时抓取网络包，自动解析应用层协议 

ICMP (v4 and v6) DNS HTTP MYSQL Redis

wireshark 轻量级的。

packetbeat 解析http请求

解析 elasticSearch http 请求

![mark](http://myphoto.mtianyan.cn/blog/180203/fGlH9IckjD.png?imageslim)

interfaces.device 是指定监听哪一个网卡

linux下可以指定成 any 但是mac只能指定为本地 lo0、

协议指定抓取 http协议的包。端口9200。

send_request 默认false 记录http请求的body信息。不开的话抓取的信息比较简单。

将body中json格式的数据记录下来。

```
output.console:
	pretty: true
```

Packetbeat 运行

```
sudo ./packerbeat -e -c es.yml -strict.perms=false
```

抓包是需要有一定权限，配置文件的权限设置false就不会被检查

### Beats入门

Packetbeat 运行

点击beats 下载packetbeat。解压。

整体结构非常类似。es跑起来，

```
sudo ./packerbeat -e -c es.yml -strict.perms=false
```
es.yml

![mark](http://myphoto.mtianyan.cn/blog/180203/ieG4Fcb25g.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180203/DBFaEg4f34.png?imageslim)

配置input 和 output

## Logstash入门

Data Shipper 
	- ETL
	- Extract
	- Transform
	- Load

提取转换和加载。 开源的服务端的数据处理流。同时从多个数据源去提取数据，
转换数据最后把数据发送到你要存储的地方。

Filter 是 logstash 远强于 beats的地方。

grok 是一种基于正则的将非格式化数据转换为格式化数据的语法的。
mutate 对结构化之后的数据进行增删改查。
drop date等常见的对于数据的处理。

output ：可以输出到多种地方。

### Logstash 配置

处理流程 -- Input 和 Output 配置

![mark](http://myphoto.mtianyan.cn/blog/180203/aibdJBJ42A.png?imageslim)

可以用上面命令进行本地的快速调试、

Grok 

基于正则表达式提供了丰富可重用的模式(pattern)
基于此可以将非结构化的数据做结构化的处理。

Date 

将字符串类型的时间字段转换为时间戳类型，方便后续数据处理。
后续通过时间进行查询，基于时间进行分组。

Mutate

进行增加删除修改替换等字段相关的处理

Filter配置 Grok示例

![mark](http://myphoto.mtianyan.cn/blog/180203/g2ldCDA9aJ.png?imageslim)

grok将非结构化数据转换为结构化数据。

%{IP:client} IP是一个已存在的正则表达式模式。后面的client是结构化之后的字段名。

![mark](http://myphoto.mtianyan.cn/blog/180203/G6F503Lhc8.png?imageslim)

grok将非结构化的数据转化为结构化的数据。

### Logstash 演示

Logstash基于jvm。下载解压

Logstash三大配置 date将字符串转换成时间戳 地理位置的处理 用户代理的处理

- input
- Filter
- output

```
head -n 2 ~/nginx_logs/nginx.log|bin/logstash -f nginx_logstatsh.conf
```

## 实战: 分析Elasticsearch 查询语句。

目标: 

	- 收集ElasticSearch集群的查询语句
	- 分析查询语句的常用语句、响应时长等

方案:
	
	- 应用 Packetbeat + Logstash 完成数据收集工作
	- 使用 Kibana + ElasticSearch 完成数据分析工作

![mark](http://myphoto.mtianyan.cn/blog/180203/DL4hG52AeI.png?imageslim)

监控的集群； production cluster 业务集群es查询语句情况

packetbeat 监听 es的端口。将搜索查询语句统计

发送到Logstash，Logstash做一些处理，存储到监控集群。
通过监控集群的Kibana进行可视化。

方案: 
	- Production Cluster
		ElasticSearch 9200
		Kibana 5601
	- Monitoring Cluster
		ElasticSearch 8200

```
bin/elasticsearch -Ecluster.name=sniff_search -Ehttp.port=8200
-Epath.data=sniff
```
kibana 8601

```
bin /kibana -e http://127.0.0.0.1:8200 -p 8601
```

生成集群和监控集群不能是同一个，会造成压力过大，陷入死循环。

![mark](http://myphoto.mtianyan.cn/blog/180203/F070A5agcJ.png?imageslim)

在5044 端口接收beats 的输入。如果在request中有search这个关键词才进行处理。
从request中提取查询语句。以及是对哪一个index做出的查询。

做的一些补全操作。output只做查询语句。

![mark](http://myphoto.mtianyan.cn/blog/180203/4hH9mKjK60.png?imageslim)

packetbeat 将输出输出到5044

笔者实践:

我的机器上已经跑着es + kibana 的一套搜索引擎业务集群。

![mark](http://myphoto.mtianyan.cn/blog/180203/D98KfHeJ4l.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180203/hjBdgFikck.png?imageslim)

分别运行在5601和9200端口。

此时启动监控日志集群和Kibana

![mark](http://myphoto.mtianyan.cn/blog/180203/eGmlcH2Bmk.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180203/CE6edjBjkm.png?imageslim)

因为懒以及怕内存不够用，上面的监控集群只运行了一个master节点。

修改Kibana配置文件中

![mark](http://myphoto.mtianyan.cn/blog/180203/kK4Ae7C3Fb.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180203/F12d6GflDA.png?imageslim)

此时Kibana 5602 es 9201 

![mark](http://myphoto.mtianyan.cn/blog/180203/kmbhHCcfjh.png?imageslim)

首先启动logstash 使用文件 sniff_search.conf

设置 beats 监听端口5044 & 将文件输出到监控集群 9201

```
bin/logstash -f sniff_search.conf
```
![mark](http://myphoto.mtianyan.cn/blog/180204/1BhCFIK4Ee.png?imageslim)

我用的windows使用命令如上。


### 准备packctbeat

![mark](http://myphoto.mtianyan.cn/blog/180204/jLF7ACFAC5.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180204/BCBCdICbHj.png?imageslim)

```
./packerbeat -e -c es.yml -strict.perms=false
packerbeat.bat -e -c sniff_search.yml -strict.perms=false
```

运行报错，找不到wpcap、安装。https://www.winpcap.org/install/default.htm
一旦通过management 加入了索引，就可以根据discover查看数据。

visualize 创建饼图等。

## Kibana使用。

## 总结与建议

![mark](http://myphoto.mtianyan.cn/blog/180204/fG179kKmLl.png?imageslim)

学会查阅和搜索官方文档

学会在社区正确地提问题

学会开拓视野的方法 - Elastic 日报

