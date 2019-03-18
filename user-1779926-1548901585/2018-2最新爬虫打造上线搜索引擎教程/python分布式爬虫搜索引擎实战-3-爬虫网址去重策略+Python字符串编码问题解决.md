##爬虫网址去重策略
1. 将访问过的url保存到数据库中
2. 将url保存到set中。只需要O(1)的代价就可以查询到url
> 100000000\*2byte*50个字符/1024/1024/1024 = 9G
3. url经过md5等方法哈希后保存到set中，将url压缩到固定长度而且不重复
4. 用bitmap方法，将访问过的url通过hash函数映射到某一位
5. bloomfilter方法对bitmap进行改进，多重hash函数降低冲突

scrapy去重使用的是第三种方法：后面分布式scrapy-redis会讲解bloomfilter方法。

###Python字符串编码问题解决：

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
