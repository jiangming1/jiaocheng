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
未来是什么时代？是数据时代！数据分析服务、互联网金融，数据建模、自然语言处理、医疗病例分析……越来越多的工作会基于数据来做，而爬虫正是快速获取数据最重要的方式，相比其它语言，Python爬虫更简单、高效

###一、基础知识学习:

####爬取策略的深度优先和广度优先
目录：
>1. 网站的树结构
2. 深度优先算法和实现
3. 广度优先算法和实现

网站url树结构分层设计:

- bogbole.com
    - blog.bogbole.com
    - python.bogbole.com
        - python.bogbole.com/123

环路链接问题：
>从首页到下面节点。
但是下面的链接节点又会有链接指向首页

所以：我们需要对于链接进行去重

**1. 深度优先**
**2. 广度优先**

>跳过已爬取的链接
对于二叉树的遍历问题

深度优先(递归实现)：
    顺着一条路，走到最深处。然后回头

广度优先(队列实现):
    分层遍历：遍历完儿子辈。然后遍历孙子辈

Python实现深度优先过程code：
```python
def depth_tree(tree_node):
    if tree_node is not None:
        print (tree_node._data)
        if tree_node._left is not None:
            return depth_tree(tree_node.left)
        if tree_node._right is not None:
            return depth_tree(tree_node,_right)
```
Python实现广度优先过程code：
```python
def level_queue(root):
    #利用队列实现树的广度优先遍历
    if root is None:
        return
    my_queue = []
    node = root
    my_queue.append(node)
    while my_queue:
        node = my_queue.pop(0)
        print (node.elem)
        if node.lchild is not None:
            my_queue.append(node.lchild)
        if node.rchild is not None:
            my_queue.append(node.rchild)
```
####爬虫网址去重策略
1. 将访问过的url保存到数据库中
2. 将url保存到set中。只需要O(1)的代价就可以查询到url
> 100000000\*2byte*50个字符/1024/1024/1024 = 9G
3. url经过md5等方法哈希后保存到set中，将url压缩到固定长度而且不重复
4. 用bitmap方法，将访问过的url通过hash函数映射到某一位
5. bloomfilter方法对bitmap进行改进，多重hash函数降低冲突

scrapy去重使用的是第三种方法：后面分布式scrapy-redis会讲解bloomfilter方法。

####Python字符串编码问题解决：

>1. 计算机只能处理数字，文本转换为数字才能处理，计算机中8个bit作为一个字节，
所以一个字节能表示的最大数字就是255
2. 计算机是美国人发明的，所以一个字节就可以标识所有单个字符
，所以ASCII(一个字节)编码就成为美国人的标准编码
3. 但是ASCII处理中文明显不够，中文不止255个汉字，所以中国制定了GB2312编码
，用两个字节表示一个汉字。GB2312将ASCII也包含进去了。同理，日文，韩文，越来越多的国家为了解决这个问题就都发展了一套编码，标准越来越多，如果出现多种语言混合显示就一定会出现乱码
4. 于是unicode出现了，它将所有语言包含进去了。
5. 看一下ASCII和unicode编码:
    1. 字母A用ASCII编码十进制是65，二进制 0100 0001
    2. 汉字"中" 已近超出ASCII编码的范围，用unicode编码是20013二进制是01001110 00101101
    3. A用unicode编码只需要前面补0二进制是 00000000 0100 0001
6. 乱码问题解决的，但是如果内容全是英文，unicode编码比ASCII编码需要多一倍的存储空间，传输也会变慢。
7. 所以此时出现了可变长的编码"utf-8" ,把英文：1字节，汉字3字节，特别生僻的变成4-6字节，如果传输大量的英文，utf8作用就很明显。

**读取文件，进行操作时转换为unicode编码进行处理**
**保存文件时，转换为utf-8编码。以便于传输**
读文件的库会将转换为unicode

*python2 默认编码格式为`ASCII`，Python3 默认编码为 `utf-8`*
```python
#python3
import sys
sys.getdefaultencoding()
s.encoding('utf-8')
```
```python
#python2
import sys
sys.getdefaultencoding()
s = "我和你"
su = u"我和你"
~~s.encode("utf-8")#会报错~~
s.decode("gb2312").encode("utf-8")
su.encode("utf-8")
```
