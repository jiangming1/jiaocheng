最终项目上线演示地址: http://search.mtianyan.cn
- 第三节：本节我们先得了解点你做爬虫做搜索引擎的居家必备知识基础。

Github地址: https://github.com/mtianyan/ArticleSpider (欢迎先点个star后上车)

# 爬虫基础知识

1. 正则表达式
2. 深度优先和广度优先遍历算法
3. url去重的常见策略

## 技术选型
**scrapy vs requests+beautifulsoup**

- requests和beautifulsoup都只是库，scrapy是框架。
- scrapy 可以加入requests和beautifulsoup
- scrapy基于twisted(异步IO框架)，性能好
- 方便扩展,有很多内置功能
- 内置的`css`和`xpath selector`(lxml:c实现的框架)很方便，速度快

>beautifulsoup最大的缺点就是慢。

```
pip install scrapy
```

可能遇到的报错信息: Failed building wheel for Twisted

http://www.lfd.uci.edu/~gohlke/pythonlibs/#twisted  本地安装

### 网页分类

1. 静态网页
2. 动态页面
3. webservice(restful api): 前台通过ajax与后台进行交互

### 爬虫能做什么？

1. 搜索引擎：百度 垂直领域搜索引擎(只爬某领域)
2. 推荐引擎：今日头条
3. 机器学习的数据样本
4. 数据分析(金融数据分析) 舆情分析

## 正则表达式

为什么必须会正则表达式？

应用场景: `1天前`中提取出`1`

### 特殊字符:

>可以判断是否匹配模式，字符串中提取指定字符

- `^`：代表以该字符为开头。如`^b`就是b为开头。
- `.`: 代表任意一个字符。如`^b.`就是b开头后面一个字母任意
- `*`: 代表前一个字符可以出现任意次（0个也可以）。如`^b.*`就是b开头后面可有任意数个任意字母
- `$`: 代表前一个字符为结尾符合。 如:`.*3$`就是以3为结尾的任意字符串
- `?`： 在限定次数词后加一个"?"，则表示匹配尽可能少的字符: 非贪婪匹配。
	- 一般情况从右往左匹配正则，非贪婪遇到符合最小条件就返回。
	- `?`可以实现从左往右，非贪婪指遇到第一个就结束。
	`.*?(b.*?b).*`就是首先开始的b和找到的第一个b之间的字符串，不向后贪婪。

### Example Code
```python
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/1/17 0017 16:20'

import re

line1 = "mtianyan123"
line2 = "moooommtianyan123"

# 定义正则表达式的字符串模式
# ^m代表以m开头；.代表任意字符；* 代表前面字符可以重复任意遍
# 3$代表以3为结尾
regex_str1 = "^m.*3$"

# 两个参数: 定义的字符串，以及我们要检验的字符串
if re.match(regex_str1, line1):
    print("yes")

# 默认为贪婪模式: 反向由字符串模式从右向左匹配
# m前面无所谓啥，m和后一个m中间无所谓啥。后面也无所谓有啥。目标:moooom
# 括号括起来我们需要的字符串.也就是只提取这一部分。
regex_str2 = ".*(m.*m).*"
match_obj = re.match(regex_str2, line2)
if match_obj:
    # 只取第一个括号东西。
    print (match_obj.group(1))
# mm

# 使用?使他从左边开始但是右边还是贪婪模式匹配。从右匹配到右起第一个
regex_str3 = ".*?(m.*m).*"
match_obj = re.match(regex_str3, line2)
if match_obj:
    # 只取第一个括号东西。
    print (match_obj.group(1))
# moooomm

# 使用?使他从左边开始但是右边还是贪婪模式匹配。
# 在第二个m前也加问号。让它从左起开始。找这样的条件的b，不要继续向后贪婪
regex_str4 = ".*?(m.*?m).*"
match_obj = re.match(regex_str4, line2)
if match_obj:
    # 只取第一个括号东西。
    print (match_obj.group(1))
# moooom
```

>- 两个问号都不加: 默认从右开始找匹配模式的字符，找到即返回。
- 加前面一个问号: 从左开始，但是会一直贪婪到最长的符合字符。因为后面m还是从右开始的。
- 后面加一个问号: 第二个字符从左开始找。
- 两个问号都加：从左开始找字符m 和 m

- `+`: 代表前面的字符至少出现一次: 如`b.+b`指两个b之间至少一个字符

>`+` 与`*` 与`{2}`都属于限定词限定前面出现多少次

- `{2}` `{2,5}` `{2, }`: 
	- 代表前面的字符出现2次。
	- 代表前面的字符出现2到5次. 
	- 代表前面字符出现2次及两次以上
- `|`：代表`或`的意思 `b|c`，在模式字符串从左到右, 看先匹配到那个。先找b
- `()`: 代表子字符串。如((a|b)123)代表group(1)为外层括号里的a123|b123；group(2)为内层括号值 `a` `b`
- `[]`: 代表[]内的字符满足任意一个都可以。
	- [abcdefg]123代表以abcdefg中一个字母开头的,后面是123的都可以。
- `[0-9]` `[a-z]`
	- 代表可以为0-9范围内任意一个字符
	- 代表a-z范围任意一个字符 
- `[^1]` `^`为取反，只要不等于1就可以

>注: 进入[]的字符都不再具有特殊含义`[.*]`就指匹配到`.`或者`*`

- `\s` `\S`
	- `\s`代表匹配到一个空格。
	- `\S`代表匹配到一个非空格
- `\w ` `\W`
	- 匹配任意字符 等价: [a-zA-Z0-9_] 
	- 非单词字符
- `[\u4E00-\u9FA5]`: 匹配中文汉字

>`"study in 浙江大学"` 匹配regex_str = `.*?[\u4E00-\u9FA5]+大学`

如果不加问号。则从右开始，只会匹配到江大学

- `\d`: 匹配数字 

例子: `"xxx出生于2001年"` 匹配`.*?(\d+)年`

>不加?会只匹配到1.因为它从右往左找第一个符合条件的。
- 加上问号，从左往右，找到符合条件的。

```
import re

line = "XXX出生于2001年"
# line = "XXX出生于2001/6/1"
# line = "XXX出生于2001-6-1"
# line = "XXX出生于2001-06-01"
# line = "XXX出生于2001-06"

regex_str = ".*出生于(\d{4}[年/-]\d{1,2}([月/-]\d{1,2}日|[月/-]\d{1,2}|[月/-]$|$))"

match_obj = re.match(regex_str, line)
if match_obj:
    print (match_obj.group(1))
```

>`([月/-]\d{1,2}日|[月/-]\d{1,2}|[月/-]$|$)`满足或的关系

## 深度优先和广度优先

1. 网站的树结构
2. 深度优先算法和实现
3. 广度优先算法和实现

网站url树结构:

分层设计
子域名：
1. jobbole.com
    1.1 blog.bogbole.com
    1.2 python.bogbole.com
        1.1.1 python.bogbole.com/123

![mark](http://upload-images.jianshu.io/upload_images/1779926-5ab1510dd304f2aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图为网站的实际url结构图,可以看到其中的环路链接:

>从首页到某个具体页面节点。
但是下面的链接节点又会有链接指向首页,这样会让爬虫自己在里面转圈圈。

### 去重, 建立已爬取url列表

1. 深度优先
2. 广度优先

>跳过已爬取的链接, 对于二叉树的遍历问题

![mark](http://upload-images.jianshu.io/upload_images/1779926-1ca2a1bbed104626.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

深度优先(递归实现)：

>顺着一条路，走到最深处。然后回头。

输出: A->B->D->E->I->C->F->G-H (scrapy默认使用)

广度优先(队列实现):

>分层遍历：遍历完儿子辈。然后遍历孙子辈

输出: A->B->C->D->E->F->G->H->I

Python实现深度优先过程code:

```python
def depth_tree(tree_node):
    if tree_node is not None:
        print (tree_node._data)
        if tree_node._left is not None:
            return depth_tree(tree_node.left)
        if tree_node._right is not None:
            return depth_tree(tree_node,_right)
```

递归太深没有跳出，会造成内存溢出。

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

## 爬虫去重策略
1. 将访问过的url保存到`数据库`中,获取url时查询一下是否爬过了。
2. 将url保存到`set`中。只需要`O(1)`的代价就可以查询到url
3. url经过`md5`等方法哈希后保存到set中，将`url压缩到固定长度`而且`不重复`
4. 用`bitmap`方法，将访问过的url通过`hash函数`映射到某一位.
5. `bloomfilter`方法对bitmap进行改进，多重hash函数降低冲突可能性。

>方法2保守估计:`100000000*2byte*50`个字符/1024/1024/1024 = 9G,占用内存过大。

scrapy去重使用的是第三种方法:

>后面分布式scrapy-redis会讲解bloomfilter方法。hash函数会引起冲突，冲突解决。

## Python字符串编码问题解决：

1. 计算机只能处理数字，文本转换为数字才能处理，计算机中8个bit作为一个字节，
所以一个字节能表示的最大数字就是255。
2. 计算机是美国人发明的，所以一个字节就可以标识所有单个字符
，所以ASCII(一个字节)编码就成为美国人的标准编码
3. 但是ASCII处理中文明显不够，中文不止255个汉字，所以中国制定了GB2312编码
，用两个字节表示一个汉字。GB2312将ASCII也包含进去了。同理，日文，韩文，越来越多的国家为了解决这个问题就都发展了一套编码，标准越来越多，如果出现多种语言混合显示就一定会出现乱码
4. 于是unicode出现了，通过16个bit, 32个bit.它将所有语言包含进去了。
5. 看一下ASCII和unicode编码:
    - 字母A用ASCII编码十进制是65，二进制 0100 0001
    - 汉字"中" 已近超出ASCII编码的范围，用unicode编码是20013二进制是01001110 00101101
    - A用unicode编码只需要前面补0二进制是 00000000 0100 0001
6. 乱码问题解决了，但是如果内容全是英文，unicode编码比ASCII编码需要多一倍的存储空间，传输也会变慢。
7. 所以此时出现了可变长的编码"utf-8" ,把英文：1字节，汉字3字节，特别生僻的变成4-6字节，如果传输大量的英文，utf8作用就很明显。

>Unicode编码虽然占用空间但是因为占用空间大小等额，在内存中处理会简单一些。

**读取文件，进行操作时:转换为unicode编码进行处理**
**保存文件时，转换为utf-8编码。以便于传输**

![mark](http://upload-images.jianshu.io/upload_images/1779926-5890c8c0d087e7c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

读文件进行decode(解码)操作转码为Unicode，但是decode必须指明原始编码方式。
Unicode编码 encode(编码) 操作转码为其他我们指定编码格式。

读文件的库都会自动将文件转换为Unicode编码，但需要我们指明编码。

Python3中将所有字符转换为Unicode。

**python2 默认编码格式为`ASCII`，Python3 默认编码为 `utf-8`**

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

![mark](http://upload-images.jianshu.io/upload_images/1779926-a2fa5c0a00e5832e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>内存中是通过Unicode进行编码的, s 是一个非Unicode的编码。windows下是gb2312编码。Linux下是utf-8的编码。

encode方法之前，必须确保前面的变量是Unicode。

![mark](http://upload-images.jianshu.io/upload_images/1779926-8f1cd0f106a8e9fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

说明windows中s确实是gb2312保存的，而且decode方法并不会直接改变字符串本身。而是返回一个编码过后的字符串。

![mark](http://upload-images.jianshu.io/upload_images/1779926-5494787279c20ade.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

说明Linux中s是以utf-8保存的。encode编码之前必须走中间路径: 转换为Unicode

![mark](http://upload-images.jianshu.io/upload_images/1779926-393f84b119753371.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-26df79e0cf5e6326.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到windows和Linux的默认编码格式都是ascii、

Python2文件头添加utf-8编码说明。可以使得读文件的解释器指定decode的参数。
Python3已经不用加了，因为它把默认的s取消掉了。直接将字符串全部当unicode处理。
可以直接编码。

![mark](http://upload-images.jianshu.io/upload_images/1779926-b0ae4823b2fb6c14.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到windows,Linux中Python3的默认编码为utf-8.
