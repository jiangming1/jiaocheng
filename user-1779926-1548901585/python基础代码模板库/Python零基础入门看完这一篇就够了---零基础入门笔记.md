学习python有一年多了，希望通过这篇**两万字**的学习笔记来复习了，也能让后来者少走一点弯路。在`慕课网`课程笔记的同时加入了一部分自己的经验补充。
- [√] 慕课网Python开发环境搭建: 配开发环境
- [√] 廖雪峰老师在慕课网的课程: Python入门

Mark之前别忘了点个赞哦。
推荐前往我的个人博客进行阅读：http://blog.mtianyan.cn/ 
**分章目录**，超宽代码显示区。更爽哦。
知识点标注`ctrl + f` 搜索`知识点`查看。重要及较难编程题采用`天涯`标注。

# Python开发环境搭建

搭建环境分为两个版本：
- 基础版，供初学者快速安装体验。
- 进阶版, 供对于数据科学，机器学习有兴趣者安装。

`推荐：`安装进阶版，一步到位。

## 基础版：Windows下安装python环境(2.7 | 3.x)

>官网： https://www.python.org/


### 安装包下载。
选择download下的windows。点击进入。

![download下的windows](http://upload-images.jianshu.io/upload_images/1779926-6c1b1786c1c2134a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下图中红框为64位版本。32位版本可以选择Windows x86 executable installer

![选择3.7下的executable installer](http://upload-images.jianshu.io/upload_images/1779926-7d2044c2a9c2045c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**2.7版本的安装包下载：**

![2.7版本](http://upload-images.jianshu.io/upload_images/1779926-3a24a247dce81ddf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 安装python。

点击下一步下一步进行默认安装即可。（跟平常装个qq啥的没两样）

### 安装完成的测试。

`win（即徽标键）` + `R` 输入`cmd` ：
打开命令行。输入`python`不报错的进入python控制台下。

## 进阶版：Windows下Anaconda2(Python2)和Anaconda3(Python3)的共存

**转载**

原文地址：http://blog.csdn.net/infin1te/article/details/50445217

>Anaconda是一个Python的科学计算发行版，包含了超过300个流行的用于科学、数学、工程和数据分析的Python Packages。由于Python有2和3两个版本，因此Anaconda也在Python2和Python3的基础上推出了两个发行版，即Anaconda2和Anaconda3。

以上文字摘自转载博客。通俗讲就是一个python的各种科学计算包的大合集版本。省去了自己安装大量基本包的过程。

`Tips:` 3.x版本建议选择Python 3.5.1 |Anaconda 4.1.0 (64-bit)

以后如果要使用python进行TensorFlow windows版的配置可以省下时间。

**这是博主自入python坑以来找到的最好的共存方法，没有出过问任何题**！！！

**这是博主自入python坑以来找到的最好的共存方法，没有出过问任何题**！！！

**这是博主自入python坑以来找到的最好的共存方法，没有出过问任何题**！！！

![最终实现](http://upload-images.jianshu.io/upload_images/1779926-175e73a5d9f16da2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## Linux下的python使用。

- Linux 默认安装python，建议安装IPython；
- `sudo apt-get install ipython`安装Ipython（支持Tab键自动补齐）
- 使用Vim来创建`.py`文件
- 输入`python`即可查看当前版本

>IPython是Python的交互式Shell，提供了代码自动补完，自动缩进，高亮显示，执行Shell命令等非常有用的特性。特别是它的代码补完功能.

## python文件类型(常识)

python执行过程：

`.py`文件 --> python解释器 --> 字节码文件 --> python解释器 --> 二进制文件 --> 内存、运行 --> 打印结果

字节码文件:

- `.pyc` 
转换方式： `python -m py_compile xxx.py`
作用：提高程序的加载速度

- `.pyo`(优化编译的.pyc文件)
转换方式： `python -O -m py_compile xxx.py`
作用：提高程序的运行速度

## eclipse下的python环境安装。

添加python开发环境到eclipse：

- 点击`help`——`install New Software`
- 点击`add`，弹出新窗口：
- Name:填PyDev
- Location:填 http://pydev.org/updates
- 确认后会出现 PyDev，勾选Pydev。
- pydev for eclipse--> next--> accept-->finish
- file--> new--> project -->Pydev下 pydevProject

python的dev下载地址：http://pydev.org/updates

![mark](http://upload-images.jianshu.io/upload_images/1779926-56f916d7b9bb2c93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# Python的初次体验

## python语言介绍

全世界有几百种编程语言，但是流行的只有十几种，python就是其中一种。荷兰人龟叔于1989年圣诞节创立。

特点：**优雅，明确，简单**。

**适合的领域：**

- web网站和各种网络服务；
- 系统工具和脚本；

作为**胶水语言**把其他语言开发的模块包装起来方便使用。

Python是一门高级语言，所以**不适合**贴近硬件的代码:

- 比如驱动程序（首选C）
- 移动开发，有各自的语言，（objectC，swift/java）
- 游戏开发（首选C/C++）。

**Python实际应用：**

YouTube，豆瓣，搜狐邮箱；Openstack开源云计算平台。Google，Yahoo，NASA。

语言之间的对比：

>C编译为机器码；JAVA编译为字节码；python为解释执行。

![python与其他语言对比](http://upload-images.jianshu.io/upload_images/1779926-cf9a89d1ed19acf1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**缺点：** 运行慢，Python源码不能加密。

## Python版本的选择

博主建议选择安装环境篇的进阶版：2.7版本与3.x版本共存。

3.x版本建议选择Python 3.5.1 |Anaconda 4.1.0 (64-bit)
以后如果要使用python进行TensorFlow windows版的配置可以省下时间。

## windows下安装python

参考：搭建Python开发环境

## 第一个python程序

cmd下输入python。进入交互式环境。

- 命令行模式启动python:
`python`

- 命令行模式执行python文件
`python 目录/xxx.py`

- 命令行模式关闭python：
`exit()`

注意：不要使用word，或者windows下自带的记事本来进行代码编写。

推荐使用：

- 轻量级：sublime Text 或 editplus
- 重量级(较大工程) : pycharm Professional

2.7版本专属：

`print 'hello,world!'`

3.x版本(2.7版本也可以正常运行)：

`print ("hello,world!")`

# Python变量和数据类型 

>讲解Python基本的数据类型.包括整数、浮点数、字符串和布尔类型，以及变量的概念和基本的数据运算。

## 数据类型

### 整数

在Python程序中，整数的表示方法和数学上的写法一模一样.

例如：1，100，-8080，0，等等。**十六进制**用`0x`前缀和`0-9`，`a-f`表示.

例如：0xff00，0xa5b4c3d2，等等。

### 浮点数

浮点数也就是小数，之所以称为浮点数: 因为**按照科学记数法表示时，一个浮点数的小数点位置是可变的**

比如，1.23x10^9和12.3x10^8是相等的。

浮点数可以用数学写法: 如1.23，3.14，-9.01，等等。但是对于很大或很小的浮点数，就必须用科学计数法表示，把10用e替代，1.23x10^9就是1.23e9，或者12.3e8，0.000012可以写成1.2e-5，等等。

整数和浮点数在计算机内部存储的方式是不同的，整数运算永远是精确的（除法难道也是精确的？是的！），而浮点数运算则可能会有四舍五入的误差。

知识点：python2与3不同整除

python2.7下：`/` 和 `//` 都是整数除法。

例: 1/2结果为0.后面小数部分会直接去除掉。

python3.x下：

  -  `/` 为浮点数除法(如：`1/2=0.5`)
  - `//`为整数除法(如: `1//2 = 0`）

```python
a = 1	
b = 2
print a+b
#python2.7下想要浮点数除法就得使用类型转换。
print float(a)/b

```

### 字符串

字符串是以''或""括起来的任意文本，比如'abc'，"xyz"等等。请注意，''或""本身只是一种表示方式，不是字符串的一部分.

因此，字符串'abc'只有a，b，c这3个字符。

### 布尔值

布尔值和布尔代数的表示完全一致，一个布尔值只有True、False两种值，要么是True，要么是False，在Python中，可以直接用True、False表示布尔值（请注意大小写），也可以通过布尔运算计算出来。

布尔值可以用`and`、`or`和`not`运算。

and运算是与运算，只有所有都为 True，and运算结果才是 True。

or运算是或运算，只要其中有一个为 True，or 运算结果就是 True。

not运算是非运算，它是一个单目运算符，把 True 变成 False，False 变成 True。

### 空值

空值是Python里一个特殊的值，用`None`表示。

`None`不能理解为0，因为0是有意义的，而`None`是一个特殊的空值。

### 编程小任务：

1. 计算十进制整数 45678 和十六进制整数 0x12fd2 之和。
2. 请用字符串表示出Learn Python in imooc。
3. 请计算以下表达式的布尔值（注意==表示判断是否相等）：
```
100 < 99
0xff == 255
```

题目答案：

```python
print 45678+0x12fd2
print "Learn Python in imooc" 
print 100<99 
print 0xff == 255
```

运行结果：

```
123456
Learn Python in imooc
False
True
```

## print语句

print语句可以向屏幕上输出指定的文字。比如输出'hello, world'，用代码实现如下：

```python
print 'hello, world'
```

**注意：**

- 当我们在Python交互式环境下编写代码时，>>>是Python解释器的提示符，不是代码的一部分。

- 当我们在文本编辑器中编写代码时，千万不要自己添加 >>>。

print语句也可以跟上多个字符串，用逗号`,`隔开，就可以连成一串输出：

```python
print 'The quick brown fox', 'jumps over', 'the lazy dog'
```

运行结果：

```
The quick brown fox jumps over the lazy dog
```
print会依次打印每个字符串，知识点：**遇到逗号`,`会输出一个空格.**


print也可以打印整数，或者计算结果：

```python
>>> print 300
300    #运行结果
>>> print 100 + 200
300    #运行结果
```

漂亮做法：

```python
>>> print '100 + 200 =', 100 + 200
100 + 200 = 300     #运行结果
```

>注意: 对于100 + 200，Python解释器自动计算出结果300.
但是，'100 + 200 ='是字符串而非数学公式，Python把它视为字符串.

### 编程任务：请用两种方式打印出 hello, python.

实现代码：

```python
#input code
print 'hello, python.'
print 'hello,','python.'
```

运行结果：

```
hello, python.
hello, python.
```

## 注释

Python的注释以`#`开头，后面的文字直到行尾都算注释

```python
# 这一行全部都是注释...
print 'hello' # 这也是注释

# 暂时不想运行下面一行代码:
# print 'hello, python.'
```

注释还有一个巧妙的用途，就是一些代码我们不想运行，但又不想删除，就可以用注释暂时屏蔽掉：

### 编程任务:

将代码编辑器中的 "print 'hello'" 语句修改成注释语句

实现代码：

```python
# print 'hello'
```

### 注释：多行注释
```python
'''
下面是一行被注释代码
下面是两行被注释代码
'''
```

## 什么是变量

在Python中，变量的概念基本上和初中代数的方程变量是一致的。

例如，对于方程式`y=x*x` ，`x`就是变量。

当`x=2`时，计算结果是`4`。当`x=5`时，计算结果是`25`。

只是在计算机程序中，变量不仅可以是数字，还可以是任意数据类型。

在Python程序中，变量是用一个变量名表示。

知识点：**变量名必须是大小写英文、数字和下划线 `_` 的组合，且不能用数字开头。**比如：

```python
a = 1
t_007 = 'T007'
```

变量a是一个整数。变量t_007是一个字符串。

在Python中，等号`=`是赋值语句，可以把任意数据类型赋值给变量，同一个变量可以反复赋值，而且可以是不同类型的变量，例如：

```python
a = 123    # a是整数
print a
a = 'imooc'   # a变为字符串
print a
```

知识点: **这种变量本身类型不固定的语言称之为动态语言，与之对应的是静态语言。**

>静态语言在定义变量时必须指定变量类型，如果赋值的时候类型不匹配，就会报错。例如Java是静态语言，赋值语句如下（// 表示注释）：

```java
//这些是java代码
int a = 123; // a是整数类型变量
a = "mooc"; // 错误：不能把字符串赋给整型变量
```

和静态语言相比，动态语言更灵活，就是这个原因。
**请不要把赋值语句的等号等同于数学的等号**。比如下面的代码：

```python
x = 10
x = x + 2
```

如果从数学上理解`x = x + 2`那无论如何是不成立的.

在程序中，赋值语句先计算右侧的表达式x + 2，得到结果12，再赋给变量x。由于x之前的值是10，重新赋值后，x的值变成12。


最后，知识点: **理解变量在计算机内存中的表示也非常重要**。当我们写：a = 'ABC'时，Python解释器干了两件事情：

1. 在内存中创建了一个'ABC'的字符串；

2. 在内存中创建了一个名为a的变量，并把它指向'ABC'。

也可以把一个变量a**赋值**给另一个变量b，这个操作实际上是把**变量b指向变量a所指向的数据**，例如下面的代码：

```python
a = 'ABC'
b = a
a = 'XYZ'
print b
```

最后一行打印出变量b的内容到底是'ABC'呢还是'XYZ'？如果从数学意义上理解，就会错误地得出b和a相同，也应该是'XYZ'，但实际上b的值是'ABC'，让我们一行一行地执行代码，就可以看到到底发生了什么事：

- 执行`a = 'ABC'`，解释器创建了字符串  `'ABC'`和变量 `a`，并把`a`指向 `'ABC'`：

![示意图](http://upload-images.jianshu.io/upload_images/1779926-fab133818b593823.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行`b = a`，解释器创建了变量 `b`，并把b指向 a 指向的字符串`'ABC'`：

![图2](http://upload-images.jianshu.io/upload_images/1779926-e1ff488b5d0c3fa5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

执行`a = 'XYZ'`，解释器创建了字符串`'XYZ'`，并把a的指向改为'XYZ'，但b并没有更改：

![图3](http://upload-images.jianshu.io/upload_images/1779926-9f8b48cc8ad9685a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以，最后打印变量b的结果自然是'ABC'了。

### 编程任务：

>等差数列可以定义为每一项与它的前一项的差等于一个常数，可以用变量 x1 表示等差数列的第一项，用 d 表示公差，请计算数列

1 4 7 10 13 16 19 ...

前 100 项的和。

实现代码:

```python
x1 = 1
d = 3
n = 100
x100 = x1+(100-1)*d
s2 = (x1+x100)*100/2
s = n*x1+n*(n-1)*d/2
print s,s2
```

等差数列公式：

- `（首项+尾项）*项数/2`
- `项数*首项+项数*(项数-1)*公差/2`


运行结果：

```
14950 14950
```

## 定义字符串

字符串可以用`''`或者`""`括起来表示。

如果字符串本身包含`'`怎么办？比如我们要表示字符串 `I'm OK `，这时，可以用`" "`括起来表示：

```python
"I'm OK"
'Learn "Python" in imooc'
```
类似的，知识点: **如果字符串包含`"`，我们就可以用`' '`括起来表示：**

如果字符串既包含`'`又包含`"`怎么办？

知识点：转义

这个时候，就需要对字符串的某些特殊字符进行**`转义`**，Python字符串用`\`进行转义。

要表示字符串 `Bob said "I'm OK".`
由于 `'` 和`"`会引起歧义，因此，我们在它前面插入一个`\`表示这是一个**普通字符**，不代表字符串的起始，因此，这个字符串又可以表示为

```python
'Bob said \"I\'m OK\".'
# 在要保留原状的字符串前面加上右斜杠
```
注意：转义字符 `\`不计入字符串的内容中。

常用的转义字符还有：

- `\n`表示换行
- `\t` 表示一个制表符
- `\\` 表示 `\` 字符本身

### 编程任务：

>请将下面两行内容用Python的字符串表示并打印出来：

```
　　Python was started in 1989 by "Guido".
　　Python is free and easy to learn.
```

```python
s = 'Python was started in 1989 by"Guido".\nPython is free and easy to learn.'
print s
```

## raw字符串与多行字符串

如果一个字符串包含很多需要转义的字符，对每一个字符都进行转义会很麻烦。为了避免这种情况，我们可以在字符串前面加个前缀 `r` ，表示这是一个 raw 字符串，里面的字符就不需要转义了。例如：

```python
r'\(~_~)/ \(~_~)/'
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-7b574ca26237bd95.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

解释：

>这个例子举得不是很好。
可以看出raw加上之后。可能产生误会的`\`被修改为`\\`(`\\` 表示 `\` 字符本身)

- 不加上r 只有`\`和`(`并没有合成转义字符。
- 加上r。`\`需要被转义，经过转义后显示出来还是自己。

### 知识点: 个人小题(r的强大作用)

![mark](http://upload-images.jianshu.io/upload_images/1779926-4402557a533ed53b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图效果可以看出r的强大作用。


但是`r'我是一段字符'`表示法不能表示多行字符串(`r'''一段字符'''`)，也不能表示包含`'`和 `"`的字符串（为什么？）

因为如果`r'mtian'yan'` 

>r遇到左边第一个`'`,会继续往后找闭合的标志`'`然后找到mtian的地方。它任务结束了。代码继续往下执行。当扫到yan这里他就会报错。

???(更深层待续)

或者`r"mtian"yan"`

或导致r提前结束掉。后面的就无法继续匹配到对应的。

知识点: 多行字符串，可以用`'''...'''`表示：

```python
'''Line 1
Line 2
Line 3'''
#上面这个字符串的表示方法和下面的是完全一样的：
'Line 1\nLine 2\nLine 3'
```

还可以在多行字符串前面添加 r ，把这个多行字符串也变成一个raw字符串：

```python
r'''Python is created by "Guido".
It is free and easy to learn.
Let's start learn Python in imooc!'''
```

### 编程任务：

请把下面的字符串用`r'''...'''`的形式改写，并用print打印出来：

```
'\"To be, or not to be\": that is the question.\nWhether it\'s nobler in the mind to suffer.'
```

```python
print r'''"To be,or not to be":that is the question.
Whether it's nobler in the mind to suffer.'''
```

## 知识点: Unicode字符串

字符串还有一个编码问题。

因为计算机只能处理数字，如果要处理文本，就必须先把文本转换为数字才能处理。最早的计算机在设计时采用**8个比特（bit）作为一个字节（byte）**，所以，一个字节能表示的最大的整数就是255（二进制11111111=十进制255），0 - 255被用来表示大小写英文字母、数字和一些符号，这个编码表被称为**ASCII编码**，比如大写字母 A 的编码是65，小写字母 z 的编码是122。

如果要表示中文，显然一个字节是不够的，至少需要两个字节，而且还不能和ASCII编码冲突，所以，中国制定了**GB2312编码**，用来把中文编进去。

类似的，日文和韩文等其他语言也有这个问题。为了统一所有文字的编码，Unicode应运而生。Unicode把所有语言都统一到一套编码里，这样就不会再有乱码问题了。

Unicode通常用**两个字节表示一个字符**，**原有的英文编码从单字节变成双字节，只需要把高字节全部填为0就可以**。

因为Python的诞生比Unicode标准发布的时间还要早，所以最早的Python只支持ASCII编码，普通的字符串'ABC'在Python内部都是ASCII编码的。

Python在后来添加了对Unicode的支持，**以Unicode表示的字符串用u'...'表示**，比如：

```python
print u'中文'
中文
注意: 不加 u ，中文就不能正常显示。(这个应该是很早版本才会。笔者现在已经无法复现)
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-b1aeb728d904b507.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

转载: http://blog.csdn.net/lxdcyh/article/details/4018054

字符串在Python内部的表示是unicode编码，因此，在做编码转换时，通常需要以unicode作为中间编码，即先将其他编码的字符串解码`decode`成unicode，再从unicode编码`encode`成另一种编码。 

- decode的作用是将其他编码的字符串转换成unicode编码，如`str1.decode('gb2312')`，表示将gb2312编码的字符串str1转换成unicode编码。 

- encode的作用是将unicode编码转换成其他编码的字符串，如str2.encode('gb2312')，表示将unicode编码的字符串str2转换成gb2312编码。

- 代码中字符串的默认编码与代码文件本身的编码一致。 

如：s='中文'

>如果是在utf8的文件中，该字符串就是utf8编码，如果是在gb2312的文件中，则其编码为gb2312。这种情况下，要进行编码转换，都需要先用decode方法将其转换成unicode编码，再使用encode方法将其转换成其他编码。**通常，在没有指定特定的编码方式时，都是使用的系统默认编码创建的代码文件**

如果字符串是这样定义：s=u'中文'

则该字符串的编码就被指定为unicode了，即python的内部编码，而与代码文件本身的编码无关。因此，对于这种情况做编码转换，**只需要直接使用`encode`方法**将其转换成指定编码即可。

如果一个字符串已经是unicode了，再进行解码则将出错，因此通常要对其编码方式是否为unicode进行判断：

```python
isinstance(s, unicode) 
#用来判断是否为unicode 
```

用非unicode编码形式的str来`encode`会报错 

如何获得系统的默认编码？

```
#!/usr/bin/env python
#coding=utf-8
import sys
print sys.getdefaultencoding()  
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-911c7f429bbb3810.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

该段程序在Win10(1079)上输出为：ascii 

在某些IDE中，字符串的输出总是出现乱码，甚至错误，**其实是由于IDE的结果输出控制台自身不能显示字符串的编码，而不是程序本身的问题。**

如在UliPad(注:UliPad是wxPython的动力，导向和灵活的编程器)中运行如下代码：

```
s=u"中文"
print s 
```

会提示：UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-1: ordinal not in range(128)。
这是因为UliPad在控制台信息输出窗口是按照ascii编码输出的（系统的默认编码是ascii），而上面代码中的字符串是Unicode编码的，所以输出时产生了错误。

将最后一句改为：`print s.encode('gb2312')`

则能正确输出“中文”两个字。

若最后一句改为：`print s.encode('utf8')`

则输出：/xe4/xb8/xad/xe6/x96/x87，这是控制台信息输出窗口按照ascii编码输出utf8编码的字符串的结果。

![mark](http://upload-images.jianshu.io/upload_images/1779926-9d68d709e6548fc6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


`unicode(str,'gb2312')`与`str.decode('gb2312')`是一样的，都是将gb2312编码的str转为unicode编码 

使用`str.__class__`可以查看str的编码形式为str类型。

window默认编码gbk；linux默认编码utf8 

![mark](http://upload-images.jianshu.io/upload_images/1779926-c84b023ec2504a4a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

原理说了半天，最后来个包治百病的吧：
(天涯)：下面代码

```python
#!/usr/bin/env python  
#coding=utf-8

def getCoding(strInput):
    '''
    获取编码格式
    '''
    if isinstance(strInput, unicode):
        return "unicode"
    try:
        strInput.decode("utf8")
        return 'utf8'
    except:
        pass
    try:
        strInput.decode("gbk")
        return 'gbk'
    except:
        pass
        
def tran2UTF8(strInput):
    '''
    转化为utf8格式
    '''
    strCodingFmt = getCoding(strInput)
    if strCodingFmt == "utf8":
        return strInput
    elif strCodingFmt == "unicode":
        return strInput.encode("utf8")
    elif strCodingFmt == "gbk":
        return strInput.decode("gbk").encode("utf8")

def tran2GBK(strInput):
    '''
    转化为gbk格式
    '''
    strCodingFmt = getCoding(strInput)
    if strCodingFmt == "gbk":
        return strInput
    elif strCodingFmt == "unicode":
        return strInput.encode("gbk")
    elif strCodingFmt == "utf8":
        return strInput.decode("utf8").encode("gbk")

s = "中文"

if isinstance(s, unicode):  
#s=u"中文"  
    print s.encode('gb2312')
    print "我是Unicode编码的"
elif getCoding(s) == "utf8":  
#s="中文"  
    print s.decode('utf-8').encode('gb2312')
    print "我是utf-8编码的"
else:
	print s.decode('gbk').encode('gbk')
	print "我是gbk编码的"

```

![mark](http://upload-images.jianshu.io/upload_images/1779926-d6190ab67fd595a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图结果一：以utf-8格式保存的py文件。
图二：以ascii格式保存的py文件。

编码检测包 chardet 

知识点：因此，转码的时候一定要先搞明白，**字符串str是什么编码，然后decode成unicode，然后再encode成其他编码**

插入数据库报错的解决方案:
UnicodeDecodeError: ‘ascii’ codec can’t decode byte 

```
import sys
reload(sys)
sys.setdefaultencoding('utf8')
```

Unicode字符串除了多了一个 u 之外，与普通字符串没啥区别，转义字符和多行表示法仍然有效：

转义：

```python
u'中文\n日文\n韩文'
#多行：

u'''第一行
第二行'''

#raw+多行：

ur'''Python的Unicode字符串支持"中文",
"日文",
"韩文"等多种语言'''
```

如果中文字符串在Python环境下遇到 UnicodeDecodeError，这是因为.py文件保存的格式有问题。可以在第一行添加注释

```python
# -*- coding: utf-8 -*-

#简洁版
#coding=utf-8
```
目的是告诉Python解释器，用UTF-8编码读取源代码。
然后用Notepad++ 另存为... 并选择UTF-8格式保存。

### 编程任务：

用多行Unicode字符串表示下面的唐诗并打印：

静夜思

床前明月光，
疑是地上霜。
举头望明月，
低头思故乡。

知识点: https://www.python.org/dev/peps/pep-0263/

python定义文件编码到底用哪种？

```
# coding=<encoding name> 
#!/usr/bin/python
# -*- coding: <encoding name> -*-
#!/usr/bin/python
# vim: set fileencoding=<encoding name> :
```

这些都可以只要第一二行能满足如下正则表达式

```
^[ \t\v]*#.*?coding[:=][ \t]*([-_.a-zA-Z0-9]+)
```

```python
# -*- coding: utf-8 -*-
# This Python file uses the following encoding: utf-8
# 花式标明
print '''静夜思

床前明月光，
疑是地上霜。
举头望明月，
低头思故乡。
'''
```

如果不标明文件编码或找不到。python会默认你是ASCII

## 整数和浮点数

Python支持对整数和浮点数直接进行四则混合运算，运算规则和数学上的四则运算规则完全一致。

基本的运算：

```python
1 + 2 + 3   # ==> 6
4 * 5 - 6   # ==> 14
7.5 / 8 + 2.1   # ==> 3.0375
```

使用括号可以提升优先级，这和数学运算完全一致，注意只能使用小括号，但是括号可以嵌套很多层：

```python
(1 + 2) * 3    # ==> 9
(2.2 + 3.3) / (1.5 * (9 - 0.3))    # ==> 0.42145593869731807
```

和数学运算不同的地方是，Python的整数运算结果仍然是整数，浮点数运算结果仍然是浮点数：

```python
1 + 2    # ==> 整数 3
1.0 + 2.0    # ==> 浮点数 3.0
```

但是整数和浮点数混合运算的结果就变成浮点数了：

```python
1 + 2.0    # ==> 浮点数 3.0
```

为什么要区分整数运算和浮点数运算呢？

这是因为整数运算的结果永远是精确的，而浮点数运算的结果不一定精确，因为计算机内存再大，也无法精确表示出无限循环小数，比如 0.1 换成二进制表示就是无限循环小数。

那整数的除法运算遇到除不尽的时候，结果难道不是浮点数吗？我们来试一下：

```python
11 / 4    # ==> 2
```

令很多初学者惊讶的是，Python的整数除法，即使除不尽，结果仍然是整数，余数直接被扔掉。不过，Python提供了一个**求余**的运算 % 可以计算余数：

```python
11 % 4    # ==> 3
```

如果我们要计算 `11 / 4` 的精确结果，按照“整数和浮点数混合运算的结果是浮点数”的法则，把两个数中的一个变成浮点数再运算就没问题了：

```python
11.0 / 4    # ==> 2.75
```

### 编程任务：

请计算 2.5 + 10 / 4 ,并解释计算结果为什么不是期望的 5.0 ?

请修复上述运算，使得计算结果是 5.0

```python
print 2.5 + 10.0 / 4
```

运行结果：

```
5.0
```

## 布尔类型

我们已经了解了Python支持布尔类型的数据，布尔类型只有True和False两种值，但是布尔类型有以下几种运算：

**与运算**：只有两个布尔值都为 True 时，计算结果才为 True。

```python
True and True   # ==> True
True and False   # ==> False
False and True   # ==> False
False and False   # ==> False
```

**或运算**：只要有一个布尔值为 True，计算结果就是 True。

```python
True or True   # ==> True
True or False   # ==> True
False or True   # ==> True
False or False   # ==> False
```

**非运算**：把True变为False，或者把False变为True：

```python
not True   # ==> False
not False   # ==> True
```

布尔运算在计算机中用来做**条件判断**，根据计算结果为`True`或者`False`，计算机可以自动执行不同的后续代码。

在Python中，布尔类型还可以与其他数据类型做 `and`、`or`和`not`运算，请看下面的代码：

知识点：**Python把0、空字符串''和None看成 False**，**其他数值和非空字符串都看成 True。短路运算**

```python
a = True
print a and 'a=T' or 'a=F'
```

计算结果不是布尔类型，而是字符串 `'a=T'`，这是为什么呢？

因为**Python把0、空字符串''和None看成 False**，**其他数值和非空字符串都看成 True**，所以：

True and 'a=T' 计算结果是 'a=T'
继续计算 'a=T' or 'a=F' 计算结果还是 'a=T'
要解释上述结果，又涉及到 and 和 or 运算的一条重要法则：**短路计算**。

1. 在计算 `a and b `时，如果 a 是 False，则根据与运算法则，整个结果必定为 False，因此返回 a；如果 a 是 True，则整个计算结果必定取决与 b，因此返回 b。

2. 在计算 `a or b` 时，如果 a 是 True，则根据或运算法则，整个计算结果必定为 True，因此返回 a；如果 a 是 False，则整个计算结果必定取决于 b，因此返回 b。

![mark](http://upload-images.jianshu.io/upload_images/1779926-80e63a1d438e5b46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以Python解释器在做布尔运算时，**只要能提前确定计算结果，它就不会往后算了，直接返回结果**。

### 编码任务：

请运行如下代码，并解释打印的结果：

```python
a = 'python'
print 'hello,', a or 'world'
b = ''
print 'hello,', b or 'world'
```

```python
# -*- coding: utf-8 -*-
a = 'python'
print 'hello,', a or 'world'
#a为非空，则输出a
b = ''
#b为空，输出world
print 'hello,', b or 'world'
```

运行结果：

```
hello, python
hello, world
```
# Python集合类型:list和tuple

## 创建list

Python内置的一种数据类型是列表：`list`。list是一种有序的集合，可以随时添加和删除其中的元素。

比如，列出班里所有同学的名字，就可以用一个list表示：

```python
>>> ['Michael', 'Bob', 'Tracy']
['Michael', 'Bob', 'Tracy']
```
list是数学意义上的有序集合，也就是说，list中的元素是按照顺序排列的。

构造list非常简单，按照上面的代码，直接用 `[ ] `把list的所有元素都括起来，就是一个list对象。通常，我们会把list赋值给一个变量，这样，就可以通过变量来引用list：

```python
>>> classmates = ['Michael', 'Bob', 'Tracy']
>>> classmates # 打印classmates变量的内容
['Michael', 'Bob', 'Tracy']
```

由于Python是动态语言，所以list中包含的元素并不要求都必须是同一种数据类型，我们完全可以在list中包含各种数据：

```
>>> L = ['Michael', 100, True]
```
一个元素也没有的list，就是空list：

```
>>> empty_list = []
```

### 编程任务

>假设班里有3名同学：Adam，Lisa和Bart，他们的成绩分别是 95.5，85 和 59，请按照 名字, 分数, 名字, 分数... 的顺序按照分数从高到低用一个list表示，然后打印出来。

```python
L = ['Adam', 95.5,'Lisa', 85,  'Bart', 59]
print L
```

运行结果:

```
['Adam', 95.5, 'Lisa', 85, 'Bart', 59]
```

注：list本身就是有序的。所以直接打印即可。

## Python按照索引访问list

由于list是一个有序集合，所以，我们可以用一个list按分数从高到低表示出班里的3个同学：

```
>>> L = ['Adam', 'Lisa', 'Bart']
```
那我们如何从list中获取指定第 N 名的同学呢？方法是通过索引来获取list中的指定元素。

需要特别注意的是，**索引从 0 开始**，也就是说，第一个元素的索引是0，第二个元素的索引是1，以此类推。

因此，要打印第一名同学的名字，用 L[0]:

```
>>> print L[0]
Adam

#要打印第二名同学的名字，用 L[1]:
>>> print L[1]
Lisa

#要打印第三名同学的名字，用 L[2]:
>>> print L[2]
Bart

```

要打印第四名同学的名字，用 L[3]:

```
>>> print L[3]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range
```
报错了！IndexError意思就是索引超出了范围，因为上面的list只有3个元素，有效的索引是 0，1，2。

所以，使用索引时，**千万注意不要越界**。

### 编程任务

>三名同学的成绩可以用一个list表示：L = [95.5, 85, 59]

请按照索引分别打印出第一名、第二名、第三名，同时测试 print L[3]。

实现代码：

```python
L = [95.5,85,59]
print L[0]
print L[1]
print L[2]
print L[3]
```

运行结果：

```
Traceback (most recent call last):
  File "index.py", line 5, in 
    print L[3]
IndexError: list index out of range
95.5
85
59
```
知识点：正序从0开始，逆序从-1开始是最好一个list内容。

当索引数字为负数时，表示逆序读出List中的内容，记住List的最后一个空间的编号为-1开始

## 倒序访问list

我们还是用一个list按分数从高到低表示出班里的3个同学：

```
>>> L = ['Adam', 'Lisa', 'Bart']
```

这时，老师说，请分数最低的同学站出来。

要写代码完成这个任务，我们可以先数一数这个 list，发现它包含3个元素，因此，最后一个元素的索引是2：

```
>>> print L[2]
Bart
```

Bart同学是最后一名，俗称**倒数第一**，所以，我们可以用 -1 这个索引来表示最后一个元素：

```python
>>> print L[-1]
Bart
```

Bart同学表示躺枪。

类似的，倒数第二用 -2 表示，倒数第三用 -3 表示，倒数第四用 -4 表示：

```python
>>> print L[-2]
Lisa
>>> print L[-3]
Adam
>>> print L[-4]
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
IndexError: list index out of range
L[-4] 报错了，因为倒数第四不存在，一共只有3个元素。
```

使用倒序索引时，也要注意**不要越界。**

### 编程任务

>三名同学的成绩可以用一个list表示：L = [95.5, 85, 59]

请按照倒序索引分别打印出倒数第一、倒数第二、倒数第三。

实现代码：

```python
L = [95.5, 85, 59]
print L[-1]
print L[-2]
print L[-3]
print L[-4]
```
运行结果：

```
Traceback (most recent call last):
  File "index.py", line 5, in 
    print L[-4]
IndexError: list index out of range
59
85
95.5
```

## list添加新元素(append insert)

现在，班里有3名同学：

```
>>> L = ['Adam', 'Lisa', 'Bart']
```

今天，班里转来一名新同学 Paul，如何把新同学添加到现有的 list 中呢？

第一个办法是用 list 的 `append()` 方法，把新同学追加到 list 的末尾：

```
>>> L = ['Adam', 'Lisa', 'Bart']
>>> L.append('Paul')
>>> print L
['Adam', 'Lisa', 'Bart', 'Paul']
```
append()总是把新的元素添加到 list 的尾部。

如果 Paul 同学表示自己总是考满分，要求添加到第一的位置，怎么办？

方法是用list的 `insert()`方法，它接受两个参数，第一个参数是索引号，第二个参数是待添加的新元素：

```
>>> L = ['Adam', 'Lisa', 'Bart']
>>> L.insert(0, 'Paul')
>>> print L
['Paul', 'Adam', 'Lisa', 'Bart']
```

`L.insert(0, 'Paul')` 的意思是，'Paul'将被添加到索引为 0 的位置上（也就是第一个），而原来索引为 0 的Adam同学，以及后面的所有同学，都自动向后移动一位。

### 编程任务

>假设新来一名学生Paul，Paul 同学的成绩比Bart好，但是比Lisa差，他应该排到第三名的位置，请用代码实现。

代码实现:

```python
L = ['Adam', 'Lisa', 'Bart']
L.insert(2,'paul')
print L
```

运行结果:

```python
['Adam', 'Lisa', 'paul', 'Bart']
```

正向第三名索引号为2.倒数第三名索引号为-3

## list删除元素(pop)

Paul同学刚来几天又要转走了，那么我们怎么把Paul 从现有的list中删除呢？

如果Paul同学排在最后一个，我们可以用list的pop()方法删除：

```
>>> L = ['Adam', 'Lisa', 'Bart', 'Paul']
>>> L.pop()
'Paul'
>>> print L
['Adam', 'Lisa', 'Bart']
```

pop()方法总是**删掉list的最后一个元素，并且它还返回这个元素，**所以我们执行 L.pop() 后，会打印出 'Paul'。

如果Paul同学不是排在最后一个怎么办？比如Paul同学排在第三：

```
>>> L = ['Adam', 'Lisa', 'Paul', 'Bart']
```
要把Paul踢出list，我们就必须先定位Paul的位置。由于Paul的索引是2，因此，用 pop(2)把Paul删掉：

```
>>> L.pop(2)
'Paul'
>>> print L
['Adam', 'Lisa', 'Bart']
```

两种方式：直接`pop()`默认删除第一个，括号内指定参数：索引，删除索引位置上。

### 编码任务
```
L = ['Adam', 'Lisa', 'Paul', 'Bart']
```
Paul的索引是2，Bart的索引是3，如果我们要把Paul和Bart都删掉，请解释下面的代码为什么不能正确运行：

```
L.pop(2)
L.pop(3)
```

怎样调整代码可以把Paul和Bart都正确删除掉？


解释：因为语句是按顺序执行的删除了Paul之后。
索引号3已经越界。我们要删除的Bart已经变成2了。

知识点：这教育我们删除list时要秉着从前到后顺序。

## List替换元素

假设现在班里仍然是3名同学：


`>>> L = ['Adam', 'Lisa', 'Bart']`

现在，Bart同学要转学走了，碰巧来了一个Paul同学，要更新班级成员名单，我们可以先把Bart删掉，再把Paul添加进来。

另一个办法是直接用Paul把Bart给替换掉：

```
>>> L[2] = 'Paul'
>>> print L
L = ['Adam', 'Lisa', 'Paul']
```
对list中的某一个索引赋值，就可以直接用新的元素**替换**掉原来的元素，list包含的元素个数保持不变。

由于Bart还可以用 -1 做索引，因此，下面的代码也可以完成同样的替换工作：

`>>> L[-1] = 'Paul'`

### 编程任务

>班里的同学按照分数排名是这样的：L = ['Adam', 'Lisa', 'Bart']
但是，在一次考试后，Bart同学意外取得第一，而Adam同学考了倒数第一。

请通过对list的索引赋值，生成新的排名。

实现代码：

```python
L = ['Adam', 'Lisa', 'Bart']
L[0]='Bart'
L[-1]='Adam'
print L
```

运行结果：

```
['Bart', 'Lisa', 'Adam']
```

## 创建tuple

tuple是另一种有序的列表，中文翻译为“ 元组 ”。tuple 和 list 非常类似，但是，知识点：**tuple一旦创建完毕，就不能修改了。**

同样是表示班里同学的名称，用tuple表示如下：

```
>>> t = ('Adam', 'Lisa', 'Bart')
```
创建tuple和创建list唯一不同之处是用( )替代了[ ]。

现在，这个 t 就不能改变了，tuple没有 `append()`方法，也没有`insert()`和`pop()`方法。所以，新同学没法直接往 tuple 中添加，老同学想退出 tuple 也不行。

获取 tuple 元素的方式和 list 是一模一样的，我们可以正常使用 `t[0]`，`t[-1]`等索引方式访问元素，但是不能赋值成别的元素，不信可以试试：

```
>>> t[0] = 'Paul'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
```

### 编程任务

>创建一个tuple，顺序包含0 - 9这10个数。

实现代码：

```python
t = (0,1,2,3,4,5,6,7,8,9)
print t
```

运行结果：

```
(0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
```

## 创建单元素tuple

tuple和list一样，可以包含 0 个、1个和任意多个元素。

包含多个元素的 tuple，前面我们已经创建过了。

包含 0 个元素的 tuple，也就是空tuple，直接用 ()表示：

```
>>> t = ()
>>> print t
()
```

创建包含1个元素的 tuple 呢？来试试：

```
>>> t = (1)
>>> print t
1
```

好像哪里不对！t 不是 tuple ，而是整数1。为什么呢？

知识点：单元素tuple的`()`被当做优先级。`(1)`变成整数1.单元素括号结尾加`,`

因为()既可以表示tuple，又可以作为括号表示运算时的优先级，结果 (1) 被Python解释器计算出结果 1，导致我们得到的不是tuple，而是整数 1。

正是因为用()定义单元素的tuple有歧义，所以 Python 规定，单元素 tuple 要多加一个逗号`,`，这样就避免了歧义：

```
>>> t = (1,)
>>> print t
(1,)
```
Python在打印单元素tuple时，也自动添加了一个`,`，为了更明确地告诉你这是一个tuple。

多元素 tuple 加不加这个额外的`,`效果是一样的：

```
>>> t = (1, 2, 3,)
>>> print t
(1, 2, 3)
```

### 编程任务
下面代码为什么没有创建出包含一个学生的 tuple：

```python
t = ('Adam')
print t
```

请修改代码，确保 t 是一个tuple。

因为单元素tuple的括号被当做是优先级标志。要加上额外`,`标识这是一个元组。

实现代码：

```python
t = ('Adam',)
print t
```

运行结果:

```
('Adam',)
```

## “可变”的tuple(指向不变。指向的东西可以变)

前面我们看到了tuple一旦创建就不能修改。现在，我们来看一个`可变`的tuple：

```
>>> t = ('a', 'b', ['A', 'B'])
```

注意到 t 有 3 个元素：`'a'`，`'b'`和一个`list：['A', 'B']`。list作为一个整体是tuple的第3个元素。list对象可以通过 `t[2]` 拿到：

```python
>>> L = t[2]
# 然后，我们把list的两个元素改一改：

>>> L[0] = 'X'
>>> L[1] = 'Y'

```

再看看tuple的内容：

```
>>> print t
('a', 'b', ['X', 'Y'])

```

不是说tuple一旦定义后就不可变了吗？怎么现在又变了？

别急，我们先看看定义的时候tuple包含的3个元素：

![mark](http://upload-images.jianshu.io/upload_images/1779926-85dbc3734bc4d75a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当我们把list的元素'A'和'B'修改为'X'和'Y'后，tuple变为：

![mark](http://upload-images.jianshu.io/upload_images/1779926-449873ba2fea2480.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

表面上看，tuple的元素确实变了，但其实变的不是 tuple 的元素，而是list的元素。

tuple一开始指向的list并没有改成别的list，所以，tuple所谓的**“不变”**是说，tuple的每个元素，指向永远不变。即指向'a'，就不能改成指向'b'，指向一个list，就不能改成指向其他对象，**但指向的这个list本身是可变的！**

理解了`指向不变`后，要创建一个内容也不变的tuple怎么做？那就必须保证tuple的每一个元素本身也不能变。

### 编程任务：

>定义了tuple：t = ('a', 'b', ['A', 'B'])

由于 t 包含一个list元素，导致tuple的内容是可变的。能否修改上述代码，让tuple内容不可变？

解答：将里面的list替换成一个不可变的元素。比如tuple。

实现代码:

```
t = ('a', 'b', ('A', 'B'))
print t
```

运行结果：

```
('a', 'b', ('A', 'B'))
```

# Python的条件判断和循环语句

## if语句
计算机之所以能做很多自动化的任务，因为它可以自己做条件判断。

比如，输入用户年龄，根据年龄打印不同的内容，在Python程序中，可以用if语句实现：

```python
age = 20
if age >= 18:
    print 'your age is', age
    print 'adult'
print 'END'
```

注意: Python代码的`缩进规则`。**具有相同缩进的代码被视为代码块，**上面的3，4行 print 语句就构成一个代码块（但不包括第5行的print）。如果 if 语句判断为 True，就会执行这个代码块。

知识点: **缩进请严格按照Python的习惯写法：4个空格，不要使用Tab**，更不要混合Tab和空格，否则很容易造成因为缩进引起的语法错误。

注意: if 语句后接表达式，然后用:表示代码块开始。

如果你在Python交互环境下敲代码，还要特别留意缩进，并且退出缩进需要多敲一行回车：

```
>>> age = 20
>>> if age >= 18:
...     print 'your age is', age
...     print 'adult'
...
```

```
your age is 20
adult
```

### 编程任务

>如果成绩达到60分或以上，视为passed。

假设Bart同学的分数是75，请用if语句判断是否能打印出 passed:

实现代码:

```python
score = 75
if score>=60:
    print 'passed'
```

运行结果:

```
passed
```

## if-else

当 if 语句判断表达式的结果为 True 时，就会执行 if 包含的代码块：

```python
if age >= 18:
    print 'adult'
```

如果我们想判断年龄在18岁以下时，打印出 'teenager'，怎么办？

方法是再写一个 if:

```python
if age < 18:
    print 'teenager'
```

或者用 `not` 运算：

```python
if not age >= 18:
    print 'teenager'
```

细心的同学可以发现，这两种条件判断是“非此即彼”的，要么符合条件1，要么符合条件2，因此，完全可以用一个 `if ... else ...` 语句把它们统一起来：

```python
if age >= 18:
    print 'adult'
else:
    print 'teenager'
```

利用 `if ... else ...`语句，我们可以根据条件表达式的值为 `True` 或者 `False` ，分别执行 if 代码块或者 else 代码块。

注意: else 后面有个`:`。

### 编程任务

>如果成绩达到60分或以上，视为passed，否则视为failed。

假设Bart同学的分数是55，请用if语句打印出 passed 或者 failed:


实现代码：

```python
score = 55
if score>=60:
    print 'passed'
else:
    print 'failed'
```

运行结果:

```
failed
```

## if-elif-else

有的时候，一个 if ... else ... 还不够用。比如，根据年龄的划分：

- 条件1：18岁或以上：adult
- 条件2：6岁或以上：teenager
- 条件3：6岁以下：kid

我们可以用一个 if age >= 18 判断是否符合条件1，如果不符合，再通过一个 if 判断 age >= 6 来判断是否符合条件2，否则，执行条件3：

```
if age >= 18:
    print 'adult'
else:
    if age >= 6:
        print 'teenager'
    else:
        print 'kid'
```

这样写出来，我们就得到了一个两层嵌套的 if ... else ... 语句。这个逻辑没有问题，但是，如果继续增加条件，比如3岁以下是 baby：

```
if age >= 18:
    print 'adult'
else:
    if age >= 6:
        print 'teenager'
    else:
        if age >= 3:
            print 'kid'
        else:
            print 'baby'
```

这种缩进只会越来越多，代码也会越来越难看。

**要避免嵌套结构的 if ... else ...，**我们可以用 if ... 多个elif ... else ... 的结构，一次写完所有的规则：

```
if age >= 18:
    print 'adult'
elif age >= 6:
    print 'teenager'
elif age >= 3:
    print 'kid'
else:
    print 'baby'
```

`elif` 意思就是 `else if`。这样一来，我们就写出了结构非常清晰的一系列条件判断。

**特别注意**: 这一系列条件判断会从上到下依次判断，如果某个判断为 True，执行完对应的代码块，后面的条件判断就直接忽略，不再执行了。

请思考下面的代码：

```
age = 8
if age >= 6:
    print 'teenager'
elif age >= 18:
    print 'adult'
else:
    print 'kid'
```

当 age = 8 时，结果正确，但 age = 20 时，为什么没有打印出 adult？

如果要修复，应该如何修复？

知识点解答: 因为当age=20.第一个条件>=6满足就短路了。
因此我们在设置条件应该从严格到松泛.

```
age = 20
if age >= 18:
    print 'teenager'
elif age >= 6:
    print 'adult'
else:
    print 'kid'
```

### 编程任务
>如果按照分数划定结果：

- 90分或以上：excellent
- 80分或以上：good
- 60分或以上：passed
- 60分以下：failed

请编写程序根据分数打印结果。

实现代码:

```python
score = 85

if score>=90:
    print 'excellent'
elif score>=80:
    print 'good'
elif score>=60:
    print 'passed'
else:
    print 'failed'
```

运行结果:

```
good
```

##  for循环

list或tuple可以表示一个有序集合。如果我们想依次访问一个list中的每一个元素呢？比如 list：

```python
L = ['Adam', 'Lisa', 'Bart']
print L[0]
print L[1]
print L[2]
```

如果list只包含几个元素，这样写还行，如果list包含1万个元素，我们就不可能写1万行print。

这时，`循环`就派上用场了。

Python的 for 循环就可以依次把list或tuple的每个元素迭代出来：

```
L = ['Adam', 'Lisa', 'Bart']
for name in L:
    print name
```

注意:  name 这个变量是在 for 循环中定义的(这是一个临时变量名字可自定义)，意思是，依次取出list中的每一个元素，并把元素赋值给 name，然后执行for循环体（就是缩进的代码块）。

这样一来，遍历一个list或tuple就非常容易了。


### 编程任务

>班里考试后，老师要统计平均成绩，已知4位同学的成绩用list表示如下：L = [75, 92, 59, 68]

请利用for循环计算出平均成绩。

实现代码:

```python
L = [75, 92, 59, 68]
sum = 0.0
for x in L:
    sum =sum+x
print sum / 4
```

运行结果：

```
73.5
```

## while循环

和 for 循环不同的另一种循环是 `while` 循环，while 循环不会迭代 list 或 tuple 的元素，而是根据表达式判断循环是否结束。

比如要从 0 开始打印不大于 N 的整数：

```
N = 10
x = 0
while x < N:
    print x
    x = x + 1
```

while循环每次先判断 `x < N`，如果为True，则执行循环体的代码块,否则，退出循环。

在循环体内，x = x + 1 会让 x 不断增加，最终因为 x < N 不成立而退出循环。

如果没有这一个语句，while循环在判断 x < N 时总是为True，就会无限循环下去，变成死循环，所以要特别留意while循环的**退出条件。**

### 编程任务
>利用while循环计算100以内奇数的和。

实现代码:

```
sum = 0
x = 1
while x<=100:
    sum=sum+x
    x=x+2
print sum
```

知识点: 奇数只需要从1开始不断加2都是奇数。

运行结果：

```
2500
```

## break退出循环

用`for` 循环或者 `while` 循环时，如果要在循环体内直接退出循环，可以使用 `break 语句`。

比如计算1至100的整数和，我们用while来实现：

```
sum = 0
x = 1
while True:
    sum = sum + x
    x = x + 1
    if x > 100:
        break
print sum
```


咋一看， while True 就是一个死循环，但是在循环体内，我们还判断了 x > 100 条件成立时，用break语句退出循环，这样也可以实现循环的结束。

### 编程任务
>利用 while True 无限循环配合 break 语句，计算 1 + 2 + 4 + 8 + 16 + ... 的前20项的和。

实现代码:

```python
sum = 0
x = 1
n = 1
while True:
    sum =sum+x
    x =2*x
    n =n+1
    if n >20:
        break
print sum
```

运行结果:

```
1048575
```

## continue继续循环

在循环过程中，可以用`break`退出当前循环，还可以用`continue`跳过后续循环代码，继续下一次循环。

假设我们已经写好了利用for循环计算平均分的代码：

```
L = [75, 98, 59, 81, 66, 43, 69, 85]
sum = 0.0
n = 0
for x in L:
    sum = sum + x
    n = n + 1
print sum / n
```

现在老师只想统计及格分数的平均分，就要把 `x < 60` 的分数剔除掉，这时，利用`continue`，可以做到当 `x < 60`的时候，不继续执行循环体的后续代码，直接进入下一次循环：

```
for x in L:
    if x < 60:
        continue
    sum = sum + x
    n = n + 1
```
coutinue: 跳过下面的代码。开始下一次循环。

### 编程任务
>对已有的计算 0 - 100 的while循环进行改造，通过增加 continue 语句，使得只计算奇数的和：

```python
sum = 0
x = 1
while True:
    sum = sum + x
    x = x + 1
    if x > 100:
        break
print sum
```

思路: if判断到是偶数，continue跳过。

实现代码:

```python
sum = 0
x = 0
while True:
    x = x + 1
    if x > 100:
        break
    if x%2==0:
        continue
    sum = sum+x
    
print sum
```

运行结果:

```
2500
```

##  多重循环(嵌套循环)

在循环内部，还可以嵌套循环，我们来看一个例子：

```
for x in ['A', 'B', 'C']:
    for y in ['1', '2', '3']:
        print x + y
```

`x` 每循环一次，`y`就会循环 3 次，这样，我们可以打印出一个全排列：

```
A1
A2
A3
B1
B2
B3
C1
C2
C3
```

### 编程任务
>对100以内的两位数，请使用一个两重循环打印出所有十位数数字比个位数数字小的数，例如，23（2 < 3）。

代码实现。

```python
tens_place = [1,2,3,4,5,6,7,8,9]
ones_place = [0,1,2,3,4,5,6,7,8,9]
for x in tens_place:
    for y in ones_place:
        if x<y:
            print x*10 + y
```

运行结果：

```
12
13
14
15
16
17
18
19
23
略
```

# 重要的数据类型Dict和Set

## 什么是dict

我们已经知道，list 和 tuple 可以用来表示顺序集合，例如，班里同学的名字：

```
['Adam', 'Lisa', 'Bart']
```

或者考试的成绩列表：

```
[95, 85, 59]
```

但是，要根据名字找到对应的成绩，用两个 list 表示就不方便。

如果把名字和分数关联起来，组成类似的查找表：

```
'Adam' ==> 95
'Lisa' ==> 85
'Bart' ==> 59
```

给定一个名字，就可以直接查到分数。

Python的 `dict` 就是专门干这件事的。用 dict 表示`名字-成绩`的查找表如下：

```
d = {
    'Adam': 95,
    'Lisa': 85,
    'Bart': 59
}
```

我们把名字称为`key`，对应的成绩称为`value`，dict就是通过 `key `来查找 `value`。

花括号 `{}` 表示这是一个dict，然后按照 `key: value`, 写出来即可。**最后一个 key: value 的逗号可以省略。**

知识点： 区别小课堂

- 单元素的tuple必须在后面多加一个逗号。
- dict最后的逗号可以省略

由于dict也是集合，`len()` 函数可以计算任意集合的大小：

```
>>> len(d)
3
```

知识点：**注意: 一个 key-value 算一个，因此，dict大小为3。**

### 编程任务
>新来的Paul同学成绩是 75 分，请编写一个dict，把Paul同学的成绩也加进去。

```
d = {
    'Adam': 95,
    'Lisa': 85,
    'Bart': 59
}
```

实现代码:

```
d = {
    'Adam': 95,
    'Lisa': 85,
    'Bart': 59,
    'Paul': 75
    
}
```

## 访问dict

我们已经能创建一个dict，用于表示名字和成绩的对应关系：

```
d = {
    'Adam': 95,
    'Lisa': 85,
    'Bart': 59
}
```

那么，如何根据名字来查找对应的成绩呢？

可以简单地使用 `d[key]` 的形式来查找对应的 value，这和 list 很像，不同之处是，list 必须使用索引返回对应的元素，而dict使用key：

```
>>> print d['Adam']
95
>>> print d['Paul']
Traceback (most recent call last):
  File "index.py", line 11, in <module>
    print d['Paul']
KeyError: 'Paul'
```

注意: 通过 key 访问 dict 的value，只要 key 存在，dict就返回对应的value。如果key不存在，会直接报错：`KeyError`。

知识点：避免 KeyError 发生，有两个办法：

- 是先判断一下 key 是否存在，用 `in` 操作符：

```
if 'Paul' in d:
    print d['Paul']
```

如果 'Paul' 不存在，if语句判断为False，自然不会执行 print d['Paul'] ，从而避免了错误。

- 是使用dict本身提供的一个`get`方法，在Key不存在的时候，返回None：

```
>>> print d.get('Bart')
59
>>> print d.get('Paul')
None
```

### 编程任务
根据如下dict：
```
d = {
    'Adam': 95,
    'Lisa': 85,
    'Bart': 59
}
```

请打印出：
Adam: 95
Lisa: 85
Bart: 59

实现代码:

```
d = {
    'Adam': 95,
    'Lisa': 85,
    'Bart': 59
}
print 'Adam:',d['Adam']
print 'Lisa:',d.get('Lisa')
print 'Bart:',d['Bart']
```

运行结果：

```
Adam: 95
Lisa: 85
Bart: 59
```

## dict的特点

**知识点**：dict查找速度快。list查找速度随着元素增加而逐渐下降。
缺点：内存占用大。list慢但内存占用小。

dict的第一个特点是`查找速度快`，无论dict有10个元素还是10万个元素，查找速度都一样。而list的查找速度随着元素增加而逐渐下降。

不过dict的查找速度快不是没有代价的，dict的缺点是占用内存大，还会浪费很多内容，list正好相反，占用内存小，但是查找速度慢。

由于dict是按 key 查找，所以，**在一个dict中，key不能重复。**

dict的第二个特点就是存储的key-value序对是`没有顺序`的！这和list不一样：

```
d = {
    'Adam': 95,
    'Lisa': 85,
    'Bart': 59
}
```

当我们试图打印这个dict时：

```
>>> print d
{'Lisa': 85, 'Adam': 95, 'Bart': 59}
```

打印的顺序不一定是我们创建时的顺序，而且，不同的机器打印的顺序都可能不同，这说明 知识点:**dict内部是无序的，不能用dict存储有序的集合。**

知识点：dict的第三个特点是作为 key 的元素必须不可变，Python的基本类型如字符串、整数、浮点数都是不可变的，都可以作为 key。 但是list是可变的，就不能作为 key。

可以试试用list作为key时会报什么样的错误。

不可变这个限制仅作用于key，value是否可变无所谓：

```
{
    '123': [1, 2, 3],  # key 是 str，value是list
    123: '123',  # key 是 int，value 是 str
    ('a', 'b'): True  # key 是 tuple，并且tuple的每个元素都是不可变对象，value是 boolean
}
```


最常用的key还是字符串，因为用起来最方便。

### 编程任务
>请设计一个dict，可以根据分数来查找名字，已知成绩如下：

```
Adam: 95,
Lisa: 85,
Bart: 59.
```

实现代码:

```
d = {
     95:'Adam',
     85:'Lisa',
     59:'Bart'
}
```

运行结果：无

## 更新dict

dict是可变的，也就是说，我们可以随时往dict中添加新的 `key-value`。比如已有dict：

```
d = {
    'Adam': 95,
    'Lisa': 85,
    'Bart': 59
}
```

要把新同学'Paul'的成绩 72 加进去，用赋值语句：

```
>>> d['Paul'] = 72
```

再看看dict的内容：

```
>>> print d
{'Lisa': 85, 'Paul': 72, 'Adam': 95, 'Bart': 59}
```

**如果 key 已经存在，则赋值会用新的 value 替换掉原来的 value：**

```
>>> d['Bart'] = 60
>>> print d
{'Lisa': 85, 'Paul': 72, 'Adam': 95, 'Bart': 60}
```

### 编程任务
>请根据Paul的成绩 72 更新下面的dict：
```
d = {

    95: 'Adam',

    85: 'Lisa',

    59: 'Bart'

}
```

实现代码:

```python
d = {
    95: 'Adam',
    85: 'Lisa',
    59: 'Bart'
}
d[72] = 'Paul'
print d
```

运行结果：

```
{72: 'Paul', 59: 'Bart', 85: 'Lisa', 95: 'Adam'}
```
##  遍历dict

由于dict也是一个集合，所以，遍历dict和遍历list类似，都可以通过 for 循环实现。

直接使用for循环可以遍历 dict 的 `key`：

```python
>>> d = { 'Adam': 95, 'Lisa': 85, 'Bart': 59 }
>>> for key in d:
...     print key
... 
Lisa
Adam
Bart
```

由于通过 `key` 可以获取对应的 `value`，因此，在循环体内，可以获取到value的值。

注：这里的key只是一个约定俗称的变量，可以改为其他名字。但是推荐用key。

### 编程任务
>请用 for 循环遍历如下的dict，打印出 name: score 来。

```
d = {
    'Adam': 95,
    'Lisa': 85,
    'Bart': 59
}
```

实现代码：

```
d = {
    'Adam': 95,
    'Lisa': 85,
    'Bart': 59
}
for key in d:
    print key+":",d[key]
```

运行结果:

```
Lisa: 85
Adam: 95
Bart: 59
```

## 什么是set

dict的作用是建立一组 `key` 和一组 `value` 的映射关系，dict的key是**不能重复的**。

有的时候，我们只想要 dict 的 key，不关心 key 对应的 value，目的就是**保证这个集合的元素不会重复，**这时，set就派上用场了。

set 持有一系列元素，这一点和 list 很像，但是set的元素**没有重复，而且是无序的**，这点和 dict 的 key很像。

知识点: 创建 set 的方式是**调用 set() 并传入一个 list**，list的元素将作为set的元素：

```python
>>> s = set(['A', 'B', 'C'])
可以查看 set 的内容：

>>> print s
set(['A', 'C', 'B'])
```

请注意，上述打印的形式类似 list， 但它不是 list，仔细看还可以发现，**打印的顺序和原始 list 的顺序有可能是不同的，因为set内部存储的元素是无序的。**

因为set不能包含重复的元素，所以，当我们传入包含重复元素的 list 会怎么样呢？

```
>>> s = set(['A', 'B', 'C', 'C'])
>>> print s
set(['A', 'C', 'B'])
>>> len(s)
3
```

结果显示，set会**自动去掉重复的元素**，原来的list有4个元素，但set只有3个元素。

### 编程任务
>请用set表示班里的4位同学：Adam, Lisa, Bart, Paul

实现代码:

```python
s = set(['Adam', 'Lisa', 'Bart', 'Paul'])
print s
```

运行结果:

```
set(['Lisa', 'Paul', 'Adam', 'Bart'])
```

## 访问set

由于set存储的是**无序集合**，所以我们**没法通过索引**来访问。

访问 set中的某个元素实际上就是判断一个元素是否在set中。

例如，存储了班里同学名字的set：

```
>>> s = set(['Adam', 'Lisa', 'Bart', 'Paul'])
```

我们可以用 `in` 操作符判断：

Bart是该班的同学吗？

```
>>> 'Bart' in s
True
Bill是该班的同学吗？

>>> 'Bill' in s
False
bart是该班的同学吗？

>>> 'bart' in s
False
```

知识点：**大小写很重要**，'Bart' 和 'bart'被认为是两个不同的元素。

### 编程任务
>由于上述set不能识别小写的名字，请改进set，使得 'adam' 和 'bart'都能返回True。

既然大小写是不同的。那我们的set中就把大小写都包含。

实现代码:

```python
s = set(['Adam', 'Lisa', 'Bart', 'Paul','adam', 'lisa', 'bart', 'paul'])
print 'adam' in s
print 'bart' in s
```

运行结果.

```
True
True
```

## set的特点

**set的内部结构和dict很像，唯一区别是不存储value**，因此，判断一个元素是否在set中`速度很快`。

**set存储的元素和dict的key类似，必须是不变对象**，因此，任何可变对象是不能放入set中的。

最后，set存储的元素也是没有顺序的。

set的这些特点，可以应用在哪些地方呢？

星期一到星期日可以用字符串`'MON', 'TUE', ... 'SUN'`表示。

假设我们让用户输入星期一至星期日的某天，如何判断用户的输入是否是一个有效的星期呢？

可以用 if 语句判断，但这样做非常繁琐：

```python
x = '???' # 用户输入的字符串
if x!= 'MON' and x!= 'TUE' and x!= 'WED' ... and x!= 'SUN':
    print 'input error'
else:
    print 'input ok'
```

注意：if 语句中的...表示没有列出的其它星期名称，测试时，请输入完整。

如果事先创建好一个set，包含`'MON' ~ 'SUN'`：

```
weekdays = set(['MON', 'TUE', 'WED', 'THU', 'FRI', 'SAT', 'SUN'])
```

再判断输入是否有效，只需要判断该字符串是否在set中：

```python
x = '???' # 用户输入的字符串
if x in weekdays:
    print 'input ok'
else:
    print 'input error'
这样一来，代码就简单多了。

```

### 编程任务
>月份也可以用set表示，请设计一个set并判断用户输入的月份是否有效。
月份可以用字符串`'Jan', 'Feb', ...`表示。

实现代码:

```python
months = set(['Jan', 'Feb', 'Mar', 'Apr', 'May', 'Jun', 'Jul','Aug','Sep','Oct','Nov','Dec'])
x1 = 'Feb'
x2 = 'Sun'

if x1 in months:
    print 'x1: ok'
else:
    print 'x1: error'

if x2 in months:
    print 'x2: ok'
else:
    print 'x2: error'
```

运行结果:

```
x1: ok
x2: error
```

## 遍历set

由于 set 也是一个集合，所以，遍历 set 和遍历 list 类似，都可以通过 for 循环实现。

直接使用 for 循环可以遍历 set 的元素：

```python
>>> s = set(['Adam', 'Lisa', 'Bart'])
>>> for name in s:
...     print name
... 
Lisa
Adam
Bart
```

注意: 观察 for 循环在遍历set时，**元素的顺序和list的顺序很可能是不同的，**而且不同的机器上运行的结果也可能不同。

### 编程任务
>请用 for 循环遍历如下的set，打印出 name: score 来。

```
s = set([('Adam', 95), ('Lisa', 85), ('Bart', 59)])
```

上面这个set中的每一个元素又是一个字典。

`set([ ])`是壳子。

`('Adam', 95), ('Lisa', 85), ('Bart', 59)`才是真正的内容

实现代码：

```python
s = set([('Adam', 95), ('Lisa', 85), ('Bart', 59)])
for name,score in s:
    print name,':',score
```

运行结果:

```
Lisa : 85
Adam : 95
Bart : 59
```

## 更新set(add remove)

由于set存储的是一组不重复的无序元素，因此，更新set主要做两件事：
- 是把新的元素添加到set中
- 是把已有元素从set中删除。(前提是如果有)

添加元素时，用set的`add()`方法：

```python
>>> s = set([1, 2, 3])
>>> s.add(4)
>>> print s
set([1, 2, 3, 4])
```

如果添加的元素已经存在于set中，`add()`不会报错，但是不会加进去了：

```python
>>> s = set([1, 2, 3])
>>> s.add(3)
>>> print s
set([1, 2, 3])
```

删除set中的元素时，用set的`remove()`方法：

```python
>>> s = set([1, 2, 3, 4])
>>> s.remove(4)
>>> print s
set([1, 2, 3])
```

如果删除的元素不存在set中，remove()会**报错**：

```
>>> s = set([1, 2, 3])
>>> s.remove(4)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyError: 4
```

所以用`add()`可以直接添加，而`remove()`前需要判断。

## 编程任务
>针对下面的set，给定一个list，对list中的每一个元素，如果在set中，就将其删除，如果不在set中，就添加进去。

```
s = set(['Adam', 'Lisa', 'Paul'])
L = ['Adam', 'Lisa', 'Bart', 'Paul']
```

实现代码：

```python
s = set(['Adam', 'Lisa', 'Paul'])
L = ['Adam', 'Lisa', 'Bart', 'Paul']
for name in L:
    if name in s:
        s.remove(name)
    else:
        s.add(name)
print s
```

## 函数定义与调用

Python之什么是函数
我们知道圆的面积计算公式为：

S = πr²

当我们知道半径r的值时，就可以根据公式计算出面积。假设我们需要计算3个不同大小的圆的面积：

```
r1 = 12.34
r2 = 9.08
r3 = 73.1
s1 = 3.14 * r1 * r1
s2 = 3.14 * r2 * r2
s3 = 3.14 * r3 * r3
```

当代码出现有规律的重复的时候，你就需要当心了，每次写`3.14 * x * x`不仅很麻烦，而且，如果要把3.14改成3.14159265359的时候，得全部替换。

有了函数，我们就不再每次写`s = 3.14 * x * x`，而是写成更有意义的函数调用 `s = area_of_circle(x)`，而函数 `area_of_circle`本身只需要写一次，就可以多次调用。

**抽象**是数学中非常常见的概念。举个例子：

计算数列的和，比如：1 + 2 + 3 + ... + 100，写起来十分不方便，于是数学家发明了求和符号`∑`，可以把1 + 2 + 3 + ... + 100记作：

```
100
∑n
n=1
```

这种抽象记法非常强大，因为我们看到`∑`就可以理解成求和，而不是还原成低级的加法运算。

而且，这种抽象记法是可扩展的，比如：

```
100
∑(n²+1)
n=1
```

还原成加法运算就变成了：

`(1 x 1 + 1) + (2 x 2 + 1) + (3 x 3 + 1) + ... + (100 x 100 + 1)`
可见，借助抽象，我们才能不关心底层的具体计算过程，而直接在更高的层次上思考问题。

写计算机程序也是一样，**函数就是最基本的一种代码抽象的方式。**

Python不但能非常灵活地定义函数，而且本身**内置**了很多有用的函数，可以直接调用。

### 编程任务
>写一个函数

实现代码：

```python
s = area_of_circle(x)
area_of_circle(x)
```

运行结果：

## 调用函数,内置函数

Python内置了很多有用的函数，我们可以直接调用。

要调用一个函数，需要知道**函数的名称**和**参数**，比如求绝对值的函数 `abs`，它接收一个参数。

可以直接从Python的官方网站查看文档：
http://docs.python.org/2/library/functions.html#abs

也可以在交互式命令行通过 `help(abs) `查看`abs`函数的帮助信息。

调用 abs 函数：

```python
>>> abs(100)
100
>>> abs(-20)
20
>>> abs(12.34)
12.34
```

调用函数的时候，如果传入的参数数量不对，会报`TypeError`的错误，并且Python会明确地告诉你：abs()有且仅有1个参数，但给出了两个：

```
>>> abs(1, 2)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: abs() takes exactly one argument (2 given)
```

如果传入的参数数量是对的，但参数类型不能被函数所接受，也会报`TypeError`的错误，并且给出错误信息：`str`是**错误的参数类型**：

```
>>> abs('a')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: bad operand type for abs(): 'str'
```

而比较函数 cmp(x, y) 就需要两个参数，如果 `x<y`，返回 `-1`，如果 `x==y`，返回`0`，如果 `x>y`，返回 `1`：

```
>>> cmp(1, 2)
-1
>>> cmp(2, 1)
1
>>> cmp(3, 3)
0
```

Python内置的常用函数还包括**数据类型转换函数**，比如   `int()`函数可以把其他数据类型转换为整数：

```
>>> int('123')
123
>>> int(12.34)
12
```

`str()`函数把其他类型转换成 `str`：

```
>>> str(123)
'123'
>>> str(1.23)
'1.23'
```

### 编程任务
>sum()函数接受一个list作为参数，并返回list所有元素之和。请计算 `1*1 + 2*2 + 3*3 + ... + 100*100`。

实现代码：

```python
L = []
L = []
x = 1
while x <= 100:
    L.append(x * x)
    x = x + 1
print sum(L)
```

运行结果:

```
338350
```

## 编写函数

在Python中，定义一个函数要使用 `def` 语句，依次写出`函数名`、`括号`、`括号中的参数`和`冒号`:，然后，在缩进块中编写函数体，函数的返回值用 `return`语句返回。

我们以自定义一个求绝对值的 my_abs 函数为例：

```
def my_abs(x):
    if x >= 0:
        return x
    else:
        return -x
```

请注意，**函数体内部的语句在执行时，一旦执行到`return`时，函数就执行完毕，并将结果返回。**因此，函数内部通过条件判断和循环可以实现非常复杂的逻辑。

知识点； **如果没有return语句，函数执行完毕后也会返回结果，**只是结果为 `None`。
`return None`可以简写为`return`。

### 编程任务
>请定义一个 `square_of_sum` 函数，它接受一个list，返回list中每个元素平方的和。

实现代码:

```python
def square_of_sum(L):
    sum = 0
    for x in L:
        sum = x*x+sum
    return sum

print square_of_sum([1, 2, 3, 4, 5])
print square_of_sum([-5, 0, 5, 15, 25])
```

运行结果:

```
55
900
```

## 函数之返回"多值"

函数可以返回多个值吗？答案是肯定的。

比如在游戏中经常需要从一个点移动到另一个点，给出坐标、位移和角度，就可以计算出新的坐标：

`math`包提供了`sin()`和 `cos()`函数，我们先用`import`引用它：

```
import math
def move(x, y, step, angle):
    nx = x + step * math.cos(angle)
    ny = y - step * math.sin(angle)
    return nx, ny
```

这样我们就可以同时获得返回值：

```
>>> x, y = move(100, 100, 60, math.pi / 6)
>>> print x, y
151.961524227 70.0
```

但其实这只是一种**假象，Python函数返回的仍然是单一值：**

```
>>> r = move(100, 100, 60, math.pi / 6)
>>> print r
(151.96152422706632, 70.0)
```

知识点：**用print打印返回结果，原来返回值是一个tuple！**

但是，在语法上，返回一个tuple可以省略括号，而多个变量可以同时接收一个tuple，按位置赋给对应的值，所以，知识点：**Python的函数返回多值其实就是返回一个tuple，**但写起来更方便。

### 编程任务
>一元二次方程的定义是：`ax² + bx + c = 0`

请编写一个函数，返回一元二次方程的两个解。

注意：Python的math包提供了sqrt()函数用于计算平方根。

实现代码:

```python
import math

def quadratic_equation(a, b, c):
    t = math.sqrt(b*b - 4*a*c)
    return (-b + t) / (2 * a),( -b - t )/ (2 * a)

print quadratic_equation(2, 3, 0)
print quadratic_equation(1, -6, 5)
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-905a43927aabee39.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运行结果:

```
(0.0, -1.5)
(5.0, 1.0)
```

## 递归函数

在函数内部，可以调用其他函数。知识点: **如果一个函数在内部调用自身本身，这个函数就是递归函数。**

举个例子，我们来计算阶乘 `n! = 1 * 2 * 3 * ... * n`，用函数 `fact(n)`表示，可以看出：

`fact(n) = n! = 1 * 2 * 3 * ... * (n-1) * n = (n-1)! * n = fact(n-1) * n`
所以，`fact(n)`可以表示为 `n * fact(n-1)`，只有`n=1`时需要特殊处理。

于是，fact(n)用递归的方式写出来就是：

```
def fact(n):
    if n==1:
        return 1
    return n * fact(n - 1)
```

上面就是一个递归函数。可以试试：

```
>>> fact(1)
1
>>> fact(5)
120
>>> fact(100)
93326215443944152681699238856266700490715968264381621468592963895217599993229915608941463976156518286253697920827223758251185210916864000000000000000000000000
```


如果我们计算fact(5)，可以根据函数定义看到计算过程如下：

```
===> fact(5)
===> 5 * fact(4)
===> 5 * (4 * fact(3))
===> 5 * (4 * (3 * fact(2)))
===> 5 * (4 * (3 * (2 * fact(1))))
===> 5 * (4 * (3 * (2 * 1)))
===> 5 * (4 * (3 * 2))
===> 5 * (4 * 6)
===> 5 * 24
===> 120
```

递归函数的优点是**定义简单，逻辑清晰**。知识点: 理论上，所有的递归函数都可以写成循环的方式，但循环的逻辑不如递归清晰。

知识点: 使用递归函数需要注意防止`栈溢出`。在计算机中，函数调用是通过栈（stack）这种数据结构实现的，每当进入一个函数调用，栈就会加一层栈帧，每当函数返回，栈就会减一层栈帧。由于栈的大小不是无限的，所以，递归调用的次数过多，会导致栈溢出。可以试试计算 `fact(10000)`。

### 编程任务(天涯)

>汉诺塔 (http://baike.baidu.com/view/191666.htm) 的移动也可以看做是递归函数。

我们对柱子编号为a, b, c，将所有圆盘从a移到c可以描述为：

如果a只有一个圆盘，可以直接移动到c；

如果a有N个圆盘，可以看成a有1个圆盘（底盘） + (N-1)个圆盘，首先需要把 (N-1) 个圆盘移动到 b，然后，将 a的最后一个圆盘移动到c，再将b的(N-1)个圆盘移动到c。

请编写一个函数，给定输入 n, a, b, c，打印出移动的步骤：

```
move(n, a, b, c)
```
例如，输入 move(2, 'A', 'B', 'C')，打印出：

```
A --> B
A --> C
B --> C
```

实现代码：

```python
def move(n, a, b, c):
    if n ==1:
        print a, '-->', c
        return
    move(n-1, a, c, b)
    print a, '-->', c
    move(n-1, b, a, c)
move(4, 'A', 'B', 'C')
```

运行结果:

```
A --> B
A --> C
B --> C
A --> B
C --> A
C --> B
A --> B
A --> C
B --> C
B --> A
C --> A
B --> C
A --> B
A --> C
B --> C
```

## 定义默认参数

定义函数的时候，还可以有默认参数。

例如Python自带的 `int()` 函数，其实就有两个参数，我们既可以传一个参数，又可以传两个参数：

```
>>> int('123')
123
>>> int('123', 8)
83
```

知识点: `int()`函数的第二个参数是**转换进制**，如果不传，默认是十进制 (base=10)，如果传了，就用传入的参数。

可见，函数的默认参数的作用是**简化调用**，你只需要把必须的参数传进去。但是在需要的时候，又可以传入额外的参数来覆盖默认参数值。

我们来定义一个计算 `x` 的`N次方`的函数:

```python
def power(x, n):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
```

假设计算平方的次数最多，我们就可以把 n 的默认值设定为 2：

```
def power(x, n=2):
    s = 1
    while n > 0:
        n = n - 1
        s = s * x
    return s
```

这样一来，计算平方就不需要传入两个参数了：

```
>>> power(5)
25
```

知识点: 由于函数的参数按从左到右的顺序匹配，所以**默认参数只能定义在必需参数的后面：**

```
# OK:
def fn1(a, b=1, c=2):
    pass
# Error:
def fn2(a=1, b):
    pass
```

个人: 这里我们可以把自己想象成计算机。在自己感到为难不知道哪个是哪个的时候。
那么恭喜你，计算机也不知道。

### 编程任务
>请定义一个 `greet() `函数，它包含一个默认参数，如果没有传入，打印 `'Hello, world.'`，如果传入，打印 `'Hello, xxx.'`

实现代码:

```python
def greet(x = 'World'):
    print 'Hello,'+x+'.'

greet()
greet('mtianyan')
```

运行结果:

```
Hello,World.
Hello,mtianyan.
```

## 知识点: 定义可变参数

如果想让一个函数能接受任意个参数，我们就可以定义一个可变参数：

```python
def fn(*args):
    print args
```

可变参数的名字前面有个 * 号，我们可以传入0个、1个或多个参数给可变参数：

```python
>>> fn()
()
>>> fn('a')
('a',)
>>> fn('a', 'b')
('a', 'b')
>>> fn('a', 'b', 'c')
('a', 'b', 'c')
```

可变参数也不是很神秘，Python解释器会把传入的`一组参数`组装成一个`tuple`传递给`可变参数`，因此，在函数内部，直接把变量 `args` 看成一个 `tuple` 就好了。

定义可变参数的目的也是为了简化调用。假设我们要计算任意个数的平均值，就可以定义一个可变参数：

```python
def average(*args):
    ...
```
这样，在调用的时候，可以这样写：

```
>>> average()
0
>>> average(1, 2)
1.5
>>> average(1, 2, 2, 3, 4)
2.4
```

## 编程任务
>请编写接受可变参数的 average() 函数。

```
def average(*args):
    sum = 0.0
    if len(args) == 0:
        return sum
    for x in args:
        sum = sum + x
    return sum / len(args)
print average()
print average(1, 2)
print average(1, 2, 2, 3, 4)
```

运行结果:

```
0.0
1.5
2.4
```

# 切片操作

## 对list进行切片
取一个list的**部分元素**是非常常见的操作。比如，一个list如下：

```
>>> L = ['Adam', 'Lisa', 'Bart', 'Paul']
```

取前3个元素，应该怎么做？

笨办法：

```
>>> [L[0], L[1], L[2]]
['Adam', 'Lisa', 'Bart']
```

之所以是笨办法是因为扩展一下，取前N个元素就没辙了。

取前N个元素，也就是索引为0-(N-1)的元素，可以用循环：

```
>>> r = []
>>> n = 3
>>> for i in range(n):
...     r.append(L[i])
... 
>>> r
['Adam', 'Lisa', 'Bart']
```

对这种经常取指定索引范围的操作，用循环十分繁琐，因此，Python提供了`切片（Slice）操作符`，能大大简化这种操作。

对应上面的问题，取前3个元素，用一行代码就可以完成切片：

```
>>> L[0:3]
['Adam', 'Lisa', 'Bart']
L[0:3]表示，从索引0开始取，直到索引3为止，但不包括索引3。即索引0，1，2，正好是3个元素。
```

知识点： **[0:3]表示，从索引0开始取，直到索引3为止，但不包括索引3。即索引0，1，2，正好是3个元素。**

- 如果第一个索引是0，还可以省略：

```
>>> L[:3]
['Adam', 'Lisa', 'Bart']
```

- 也可以从索引1开始，取出2个元素出来：

```
>>> L[1:3]
['Lisa', 'Bart']
```
- 只用一个 : ，表示从头到尾：

```
>>> L[:]
['Adam', 'Lisa', 'Bart', 'Paul']
```

因此，L[:]实际上**复制出了一个新list。**


知识点: 切片操作还可以指定`第三个`参数：

```
>>> L[::2]
['Adam', 'Bart']
```

第三个参数表示**每N个取一个**，上面的 L[::2] 会每两个元素取出一个来，也就是隔一个取一个。

把`list`换成`tuple`，切片操作完全相同，只是切片的结果也变成了`tuple`。

### 编程任务
>range()函数可以创建一个数列：

```
>>> range(1, 101)
[1, 2, 3, ..., 100]
```
请利用切片，取出：

1. 前10个数；
2. 3的倍数；
3. 不大于50的5的倍数。

实现代码:

```python
L = range(1, 101)

print L[:10]
print L[2::3]
print L[4:50:5]
```

运行结果:

```
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
[3, 6, 9, 12, 15, 18, 21, 24, 27, 30, 33, 36, 39, 42, 45, 48, 51, 54, 57, 60, 63, 66, 69, 72, 75, 78, 81, 84, 87, 90, 93, 96, 99]
[5, 10, 15, 20, 25, 30, 35, 40, 45, 50]
```

## 倒序切片
对于list，既然Python支持`L[-1]`取倒数第一个元素，那么它同样支持`倒数切片`，试试：

```python
>>> L = ['Adam', 'Lisa', 'Bart', 'Paul']

>>> L[-2:]
['Bart', 'Paul']

>>> L[:-2]
['Adam', 'Lisa']

>>> L[-3:-1]
['Lisa', 'Bart']

>>> L[-4:-1:2]
['Adam', 'Bart']
```

记住倒数第一个元素的索引是-1。知识点：**倒序切片包含起始索引，不包含结束索引。**

### 编程任务
>利用倒序切片对 1 - 100 的数列取出：
- 最后10个数；
- 最后10个5的倍数。

实现代码：

```python
L = range(1, 101)
print L[-10:]
print L[-46::5]
```

## 对字符串切片

字符串 `'xxx'`和 `Unicode`字符串 `u'xxx'`也可以看成是一种list，每个元素就是一个字符。因此，字符串也可以用切片操作，只是操作结果仍是字符串：

```
>>> 'ABCDEFG'[:3]
'ABC'
>>> 'ABCDEFG'[-3:]
'EFG'
>>> 'ABCDEFG'[::2]
'ACEG'
```

在很多编程语言中，针对字符串提供了很多各种截取函数，其实目的就是对字符串切片。知识点：Python没有针对字符串的截取函数，只需要切片一个操作就可以完成，非常简单。

### 编程任务
>字符串有个方法 `upper()` 可以把字符变成大写字母：

```
>>> 'abc'.upper()
'ABC'
```
但它会把所有字母都变成大写。请设计一个函数，它接受一个字符串，然后返回一个仅首字母变成大写的字符串。

提示：利用切片操作简化字符串操作。

实现代码:

```python
def firstCharUpper(s):
    return s[0].upper() + s[1:]

print firstCharUpper('hello')
print firstCharUpper('sunday')
print firstCharUpper('september')
```

运行结果：

```
Hello
Sunday
September
```

# 各种迭代方式

## 什么是迭代

在Python中，如果给定一个list或tuple，我们可以通过for循环来遍历这个list或tuple，这种遍历我们称为迭代`（Iteration）`。

在Python中，迭代是通过 `for ... in` 来完成的，而很多语言比如C或者Java，迭代list是通过下标完成的，比如Java代码：

```java
for (i=0; i<list.length; i++) {
    n = list[i];
}
```

可以看出，Python的for循环抽象程度要高于Java的for循环。

因为 Python 的 for循环不仅可以用在`list`或`tuple`上，还可以作用在`其他任何可迭代对象`上。

因此，迭代操作就是对于一个集合，**无论该集合是有序还是无序**，我们用 for 循环总是可以依次取出集合的每一个元素。

注意: **集合**是指包含一组元素的数据结构，我们已经介绍的包括：

1. 有序集合：list，tuple，知识点: str和**unicode**；
2. 无序集合：set
3. 无序集合并且具有 key-value 对：dict

而迭代是一个动词，它指的是一种操作，在Python中，就是 `for 循环`。

`迭代`与`按下标访问数组`最大的不同是，后者是一种具体的迭代实现方式，而前者只关心迭代结果，根本不关心迭代内部是如何实现的。

## 编程任务
>请用for循环迭代数列 1-100 并打印出7的倍数。

实现代码:

```python
for i in range(1, 101):
    if i % 7 == 0:
        print i
```

运行结果:

```
7
14
21
28
35
42
49
56
63
70
77
84
91
98
```

## 索引迭代

知识点：Python中，迭代永远是取出`元素本身`，而非元素的索引。

对于有序集合，元素确实是有索引的。有的时候，我们确实想在 `for 循环中拿到索引`，怎么办？

方法是使用 `enumerate() `函数：

```
>>> L = ['Adam', 'Lisa', 'Bart', 'Paul']
>>> for index, name in enumerate(L):
...     print index, '-', name
... 
0 - Adam
1 - Lisa
2 - Bart
3 - Paul
```

使用 `enumerate() `函数，我们可以在for循环中同时绑定索引index和元素name。但是，这不是 enumerate() 的特殊语法。实际上，enumerate() 函数把：

```
['Adam', 'Lisa', 'Bart', 'Paul']
```

变成了类似：

```
[(0, 'Adam'), (1, 'Lisa'), (2, 'Bart'), (3, 'Paul')]
```

因此，迭代的每一个元素实际上是一个`tuple`：

```
for t in enumerate(L):
    index = t[0]
    name = t[1]
    print index, '-', name
```

如果我们知道每个tuple元素都包含两个元素，for循环又可以进一步简写为：

```
for index, name in enumerate(L):
    print index, '-', name
```

这样不但代码更简单，而且还少了两条赋值语句。

可见，知识点: **索引迭代也不是真的按索引访问，而是由 enumerate() 函数自动把每个元素变成 (index, element) 这样的tuple，再迭代，就同时获得了索引和元素本身。**

### 编程任务(天涯)
>zip()函数可以把两个 list 变成一个 list：

```
>>> zip([10, 20, 30], ['A', 'B', 'C'])
[(10, 'A'), (20, 'B'), (30, 'C')]
```

在迭代 `['Adam', 'Lisa', 'Bart', 'Paul'] `时，如果我们想打印出名次 - 名字（名次从1开始)，请考虑如何在迭代中打印出来。

提示：考虑使用zip()函数和range()函数

实现代码:

```python
L = ['Adam', 'Lisa', 'Bart', 'Paul']
for index, name in zip(range(1, len(L)+1), L):
    print index, '-', name
```

运行结果:

```
1 - Adam
2 - Lisa
3 - Bart
4 - Paul
```

## 迭代dict的value

迭代dict的value
我们已经了解了dict对象本身就是`可迭代对象`，用 **for 循环直接迭代 dict，可以每次拿到dict的一个key。**

如果我们希望迭代 dict 对象的value，应该怎么做？

知识点：values()把dict转换成一个包含所有value的list
dict 对象有一个 `values()` 方法，这个方法把dict转换成一个包含所有value的list，这样，我们迭代的就是 dict的每一个 value：

```
d = { 'Adam': 95, 'Lisa': 85, 'Bart': 59 }
print d.values()
# [85, 95, 59]
for v in d.values():
    print v
# 85
# 95
# 59
```

如果仔细阅读Python的文档，还可以发现，`dict`除了`values()`方法外，还有一个 `itervalues()` 方法，**用 itervalues() 方法替代 values() 方法，迭代效果完全一样：**

```
d = { 'Adam': 95, 'Lisa': 85, 'Bart': 59 }
print d.itervalues()
# <dictionary-valueiterator object at 0x106adbb50>
for v in d.itervalues():
    print v
# 85
# 95
# 59
```

那这两个方法有何不同之处呢？

1. values() 方法实际上把一个 dict 转换成了包含 value 的list。
2. 但是 itervalues() 方法**不会转换**，它会在迭代过程中依次从 dict 中取出 value，所以 itervalues() 方法比 values() 方法**节省了生成 list 所需的内存。**
3. 打印 itervalues() 发现它返回一个 <dictionary-valueiterator> 对象，这说明在Python中，for 循环可作用的迭代对象远不止 list，tuple，str，unicode，dict等，知识点: **任何可迭代对象都可以作用于for循环，**而内部如何迭代我们通常并不用关心。

如果一个对象说自己可迭代，那我们就直接用 for 循环去迭代它，知识点: 可见，**迭代是一种抽象的数据操作，它不对迭代对象内部的数据有任何要求。**

### 编程任务
>给定一个dict：d = { 'Adam': 95, 'Lisa': 85, 'Bart': 59, 'Paul': 74 }

请计算所有同学的平均分。

实现代码:

```
d = { 'Adam': 95, 'Lisa': 85, 'Bart': 59, 'Paul': 74 }
sum = 0.0
for v in d.itervalues():
    sum = sum + v
print sum / len(d)

```

运行结果:

```
78.25
```

## 迭代dict的key和value
我们了解了如何迭代 dict 的key和value，那么，在一个 for 循环中，**能否同时迭代 key和value？**答案是肯定的。

首先，我们看看 dict 对象的 `items() `方法返回的值：

```
>>> d = { 'Adam': 95, 'Lisa': 85, 'Bart': 59 }
>>> print d.items()
[('Lisa', 85), ('Adam', 95), ('Bart', 59)]
```

可以看到，items() 方法把dict对象转换成了包含tuple的list，我们对这个list进行迭代，可以同时获得key和value：

```
>>> for key, value in d.items():
...     print key, ':', value
... 
Lisa : 85
Adam : 95
Bart : 59
```

和 `values() `有一个 `itervalues()` 类似，`items()` 也有一个对应的 `iteritems()`，知识点： **iteritems() 不把dict转换成list，而是在迭代过程中不断给出 tuple，所以， iteritems() 不占用额外的内存。**

## 编程任务
>请根据dict：d = { 'Adam': 95, 'Lisa': 85, 'Bart': 59, 'Paul': 74 }

打印出 `name : score`，最后再打印出平均分 `average : score`。

实现代码：

```python
d = { 'Adam': 95, 'Lisa': 85, 'Bart': 59, 'Paul': 74 }
sum = 0.0
for k, v in d.iteritems():
    sum = sum + v
    print k, ':', v
print 'average', ':', sum / len(d)
```

运行结果：

```
Lisa : 85
Paul : 74
Adam : 95
Bart : 59
average : 78.25
```

# 列表生成式:快速生成列表

生成列表
要生成`list [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]`，我们可以用`range(1, 11)`：

```python
>>> range(1, 11)
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

但如果要生成`[1x1, 2x2, 3x3, ..., 10x10]`怎么做？

- 方法一是循环：

```
>>> L = []
>>> for x in range(1, 11):
...    L.append(x * x)
... 
>>> L
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

但是循环太繁琐，而**列表生成式**则可以用一行语句代替循环生成上面的list：

```
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

这种写法就是Python特有的列表生成式。利用列表生成式，可以以非常简洁的代码生成 list。

知识点: 写列表生成式时，把要生成的元素 `x * x `放到前面，后面跟 for 循环，就可以把list创建出来，十分有用，多写几次，很快就可以熟悉这种语法。

## 编程任务
>请利用列表生成式生成列表 `[1x2, 3x4, 5x6, 7x8, ..., 99x100]`

提示：`range(1, 100, 2) `可以生`成list [1, 3, 5, 7, 9,...]`

实现代码：

```python
print [x * (x + 1) for x in range(1, 100, 2)]
```

运行结果：

```
[2, 12, 30, 56, 90, 132, 182, 240, 306, 380, 462, 552, 650, 756, 870, 992, 1122, 1260, 1406, 1560, 1722, 1892, 2070, 2256, 2450, 2652, 2862, 3080, 3306, 3540, 3782, 4032, 4290, 4556, 4830, 5112, 5402, 5700, 6006, 6320, 6642, 6972, 7310, 7656, 8010, 8372, 8742, 9120, 9506, 9900]
```

## 复杂表达式

使用for循环的迭代不仅可以迭代普通的`list`，还可以迭代`dict`。

假设有如下的dict：

```python
d = { 'Adam': 95, 'Lisa': 85, 'Bart': 59 }
```

完全可以通过一个复杂的列表生成式把它变成一个 HTML 表格：

```python
tds = ['<tr><td>%s</td><td>%s</td></tr>' % (name, score) for name, score in d.iteritems()]
print '<table>'
print '<tr><th>Name</th><th>Score</th><tr>'
print '\n'.join(tds)
print '</table>'
```

个人：`<tr><td>%s</td><td>%s</td></tr>` 中：
- 第一个%s是name的填充位置。
- 第二个%s为score的填充位置。

有多少个name和score，会通过循环生成多少个。
`<tr><th>Name</th><th>Score</th><tr>`设置表格头
print '\n'.join(tds)。列表里的项通过`\n`连接成字符串。

注：字符串可以通过`%`进行格式化，用指定的参数替代 `%s`。字符串的`join()`方法可以把一个 `list`拼接成一个字符串。

把打印出来的结果保存为一个html文件，就可以在浏览器中看到效果了：

```html
<table border="1">
<tr><th>Name</th><th>Score</th><tr>
<tr><td>Lisa</td><td>85</td></tr>
<tr><td>Adam</td><td>95</td></tr>
<tr><td>Bart</td><td>59</td></tr>
</table>
```

## 编程任务(天涯)
>在生成的表格中，对于没有及格的同学，请把分数标记为红色。

提示：红色可以用 `<td style="color:red">` 实现。


实现代码:

```
d = { 'Adam': 95, 'Lisa': 85, 'Bart': 59 }
def generate_tr(name, score):
    if score < 60:
        return '<tr><td>%s</td><td style="color:red">%s</td></tr>' % (name, score)
    return '<tr><td>%s</td><td>%s</td></tr>' % (name, score)
tds = [generate_tr(name, score) for name, score in d.iteritems()]
print '<table border="1">'
print '<tr><th>Name</th><th>Score</th><tr>'
print '\n'.join(tds)
print '</table>'
```

运行结果:

[图片上传失败...(image-837d01-1514981535184)]

## 条件过滤

列表生成式的 `for` 循环后面还可以加上 `if` 判断。例如：

```
>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

如果我们只想要偶数的平方，不改动 range()的情况下，可以加上 if 来筛选：

```
>>> [x * x for x in range(1, 11) if x % 2 == 0]
[4, 16, 36, 64, 100]
```

有了 if 条件，只有 if 判断为 True 的时候，才把循环的当前元素添加到列表中。

### 编程任务
>请编写一个函数，它接受一个 list，然后把list中的所有字符串变成大写后返回，非字符串元素将被忽略。

提示：

1. isinstance(x, str) 可以判断变量 x 是否是字符串；
2. 字符串的 upper() 方法可以返回大写的字母。

实现代码:

```python
def toUppers(L):
    return [x.upper() for x in L if isinstance(x, str)]
print toUppers(['Hello', 'world', 101])
```

运行结果:

```
['HELLO', 'WORLD']
```

## 多层表达式(知识点)
for循环可以`嵌套`，知识点：因此，在列表生成式中，也可以用`多层 for 循环来生成列表`。

对于字符串 `'ABC'` 和 `'123'`，可以使用两层循环，生成全排列：

```python
>>> [m + n for m in 'ABC' for n in '123']
['A1', 'A2', 'A3', 'B1', 'B2', 'B3', 'C1', 'C2', 'C3']
```

翻译成循环代码就像下面这样：

```python
L = []
for m in 'ABC':
    for n in '123':
        L.append(m + n)
```

### 编程任务(天涯)
>利用 3 层for循环的列表生成式，找出对称的 3 位数。例如，121 就是对称数，因为从右到左倒过来还是 121。

实现代码:

```
print [100 * n1 + 10 * n2 + n3 for n1 in range(1, 10) for n2 in range(10) for n3 in range(10) if n1==n3]
```

运行结果：

```
101, 111, 121, 131, 141, 151, 161, 171, 181, 191, 202, 212, 222, 232, 242, 252, 262, 272, 282, 292, 303, 313, 323, 333, 343, 353, 363, 373, 383, 393, 404, 414, 424, 434, 444, 454, 464, 474, 484, 494, 505, 515, 525, 535, 545, 555, 565, 575, 585, 595, 606, 616, 626, 636, 646, 656, 666, 676, 686, 696, 707, 717, 727, 737, 747, 757, 767, 777, 787, 797, 808, 818, 828, 838, 848, 858, 868, 878, 888, 898, 909, 919, 929, 939, 949, 959, 969, 979, 989, 999]
```
个人学习笔记，内容来自慕课网课程。请勿用作商业用途。
