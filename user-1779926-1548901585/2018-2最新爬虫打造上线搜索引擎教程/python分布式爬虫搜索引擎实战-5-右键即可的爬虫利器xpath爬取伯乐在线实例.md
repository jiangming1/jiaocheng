>提取目标伯乐在线

xpath让你可以不懂前端html，不看html的详细结构，只需要会右键查看就能获取网页上任何内容。速度远超beautifulsoup。

###xpath
目录:
    1. xpath简介
    2. xpath术语与语法
    3. xpath抓取误区：javasrcipt生成html与html源文件的区别
    4. xpath抓取实例

为什么要使用xpath？
>xpath使用路径表达式在xml和html中进行导航
xpath包含有一个标准函数库
xpath是一个w3c的标准
xpath速度要远远超beautifulsoup。

###xpath节点关系

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

###xpath语法-谓语:

| 表达式| 说明           | 
| ------------- |:-------------:| 
|/article/div[1|选取属于article子元素的第一个div元素|
|/article/div[last()]|选取属于article子元素的最后一个div元素|
|/article/div[last()-1]|选取属于article子元素的倒数第二个div元素|
|//div[@color]|选取所有拥有color属性的div元素|
|//div[@color='red']|选取所有color属性值为red的div元素|

###xpath语法:

| 表达式| 说明           | 
| ------------- |:-------------:| 
|/div/*|选取属于div元素的所有子节点|
|//*|选取所有元素|
|//div[@*]|选取所有带属性的div 元素|
|//div/a 丨//div/p|选取所有div元素的a和p元素|
|//span丨//ul|选取文档中的span和ul元素|
|article/div/p丨//span|选取所有属于article元素的div元素的p元素以及文档中所有的 span元素|
###xpath抓取误区：
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
re_selector = response.xpath("/html/body/div[1]/div[3]/div[1]/div[1]/h1/text()")
re2_selector = response.xpath('//*[@id="post-110287"]/div[1]/h1/text()')
re3_selector = response.xpath('//div[@class="entry-header]/h1/text()')
```
推荐使用id型。因为页面id唯一。

通过了解了这些此时我们已经可以抓取到页面的标题，此时可以使用xpath利器照猫画虎抓取任何内容。只需要点击右键查看xpath。
