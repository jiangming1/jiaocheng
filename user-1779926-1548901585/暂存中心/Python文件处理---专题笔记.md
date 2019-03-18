 任何语言都离不开对文件的操作，Python语言是如何来操作和管理文件的。掌握文件的基本概念、基本操作，了解文件属性、 linux 系统的文件管理机制及os模块对文件和目录的处理等相关内容。
- [√]慕课网Meshare_huang老师：Python文件处理

#  文件简介

Python的文件处理:

初学者：推荐使用虚拟机`VMwareStation` + `Deepin`

必备前置条件知识储备：

1. Linux系统知识
2. Linux下Python开发环境
3. 掌握Python基础知识

Python文件概念：

文件： Python中文件是**对象**依赖我们的具体系统。

Linux文件：**一切设备都可以看成文件**
- 例如：磁盘文件，管道，网络Socket，外设等。

文件属性：`用户`，`读`，`写`，`执行权限`；

在Linux控制台输入命令：

```
#cd进一个空目录
cd Desktop/ 
#创建一个文件夹
mkdir PythonFile 
cd PythonFile 
touch hello.py
vim hello.py
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-25d9f1999aad1ded.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用`i`进入`insert模式`进行插入代码。
插入完成后使用`esc`退出编辑模式，输入`:wq`进行保存。

![mark](http://upload-images.jianshu.io/upload_images/1779926-865038a401b69e7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-1c55a933b83b417c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过`ls -l`命令可以看到文件的一些基本属性：

- 第一个横杠代表是一个文件，还是一个目录。`-`代表是一个文件，`d`代表是一个目录
如下图1为一个目录。
![mark](http://upload-images.jianshu.io/upload_images/1779926-3a241ef6cb4c9968.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-3ae7d376fa09c96b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 第一组`rw-`代表当前用户对于当前文件有`读`有`写`没有`执行权限`。(`-`代表没有权限，`x`为可执行)
- 第二组`r--`：表示当前用户组对该文件有`读r` 没有`写w`的权限，没有`执行权限x`
- 第三组`r--`：表示其他用户对该文件有`读r`权限，但没有`写w`和`执行x`权限

- 第一个`mtianyan`代表文件的**拥有者**是`mtianyan` 
- 第二个`mtianyan` 代表这个文件所属的**用户组**是`mtianyan`
- `21`代表文件的大小是`21字节` 后面的时间是文件的创建时间。后面是文件名。

![mark](http://upload-images.jianshu.io/upload_images/1779926-6c448226e5523436.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到我们最为**当前用户**现在没有该文件执行权限。

使用`chmod`为文件**增加权限**说明：

- 如果给所有人添加可执行权限：`chmod a+x 文件名`；
- 如果给文件所有者添加可执行权限：`chmod u+x 文件名`；
- 如果给所在组添加可执行权限：`chmod g+x 文件名`；
- 如果给所在组以外的人添加可执行权限：`chmod o+x 文件名`；

![mark](http://upload-images.jianshu.io/upload_images/1779926-f6f272e649ed0173.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到我们通过`chmod +x hello.py`为当前`用户`，`用户组`，`其他用户`都添加了执行权限

Linux下首行一定要加这句话：`!/usr/bin/Python`，`!/usr/bin/Python`是告诉操作系统执行这个脚本的时候，调用`/usr/bin`下的python解释器。

![mark](http://upload-images.jianshu.io/upload_images/1779926-ab7743d8bdde0a34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打印出了`Helloworld`好像有点问题。以后对Linux熟了再回来看。

![mark](http://upload-images.jianshu.io/upload_images/1779926-66ab3051406afaae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`python`命令是可以正常执行的。

# 文件基础操作

主要讲解文件打开及文件不同的打开方式、使用`read`,`readlines`及`迭代器`访问文件、文件写入方式及`写缓存`处理、文件关闭及由不关闭导致的问题、文件指针及文件指针操作

## 文件打开方式

### open()

```python
open(name [,mode[buf]])
```

- `name` : 文件路径
- `mode` : 打开方式
- `buf`  : 缓冲buffering大小

`mode`方式有：`只读`，`只写`。`读写`。

### read()
```python
read([size]) 
```
读取文件(读取`size个字节`，默认读取全部)

- 如果文件长度大于size，我们就读size个长度。
- 如果文件长度小于size，我们就把文件读完。
- 如果没有设置size，默认全部读完。

### readline()

```python
readline([size])
```

读取一行

### readlines()

```python
readlines([size])
```

读取完文件(看到后面你就知道并不是)，返回每一行所组成的列表。我们就可以使用列表访问的方式，使用文件。

弊端：一次性读完，占用很大内存。

### write()

```python
write(str)
```

将字符串写入文件

### writelines()

```python
writelines(sequence_of_strings) : 写多行到文件
```
`sequence_of_strings`是一个由字符型组成的列表。一次性写入多行。

输入`ipython`进入ipython环境

![mark](http://upload-images.jianshu.io/upload_images/1779926-9430f40cf195621d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`type(f)`可以看到打开的文件类型就是一个文件对象。

![mark](http://upload-images.jianshu.io/upload_images/1779926-bb2089d5cfc7ae15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

拥有的方法可以使用`f.` + `Tab`自动补齐

![mark](http://upload-images.jianshu.io/upload_images/1779926-a1b6377566e20365.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
Error：File not open for writing
```
这里因为我们使用的是**默认无参数打开，此时我们并不拥有写权限。**
     


Python文件打开方式

| mode | 说明 |说明 |
| ------------- | ------------- |------------- |
| 'r' | 只读方式打开 | 文件必须存在 |
| 'w' | 只写方式打开 |文件不存在创建文件;存在则清空内容 |
| 'a'| 追加方式打开 |文件不存在创建文件|
| 'r+'/'W+' | 读写方式打开 |  |
| 'a+' | 追加和读写方式打开|  |


`'rb'`,`'wb'`,`'ab'`,`'rb+'`,`'wb+'`,`'ab+'`:都是采用二进制方式打开。

### 练习`'r'` `'w'` `'a'` `'r+'`

![mark](http://upload-images.jianshu.io/upload_images/1779926-50b573f6def2d6d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- **不带参数**的`open`是用**只读**方式打开的，所以文件**不存在会报错**。

![mark](http://upload-images.jianshu.io/upload_images/1779926-870bfa8f07a5506a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 请**注意关闭**文件`f.close()`否则`cat`时文件内容会为空。

![mark](http://upload-images.jianshu.io/upload_images/1779926-9e6a99fd446fe2bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 当我们**再次**打开文件使用`w`方式。已存在的文件会被**清空**才打开。

![mark](http://upload-images.jianshu.io/upload_images/1779926-a0ed71d1a74126a2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 我们以`a`方式打开此时我们可以对于文件进行**追加**操作，原有内容并没有被清空
新增内容被添加到了**最后**。

![mark](http://upload-images.jianshu.io/upload_images/1779926-ac0b40bb304cc6c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 当我们以`r+`方式打开，我们新增的内容会从**文件开始**替换原有内容的一部分。

![mark](http://upload-images.jianshu.io/upload_images/1779926-0df4705bc5783601.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 当我们以`w+`方式打开，文件可以被**读写**。但是文件内容被清空。

![mark](http://upload-images.jianshu.io/upload_images/1779926-da272277cc44e185.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 此时新文件可以被读写。

使用二进制方式打开的使用方法与上面介绍的基本一致。

意义在于： **比如读取一张图片的EXIF信息读取图片的长宽拍照日期**

如果我们以普通方式打开是以文本方式打开，不能读取到正确的内容。
当我们以二进制打开我们就可以读取到正确的`EXIF信息`。

学习了打开文件，读取文件，写入文件。文件打开方式的不同。

## 文件读取方式

### read()
```python
read([size]) 
```
读取文件(读取`size个字节`，默认读取全部)

- 如果文件长度大于size，我们就读size个长度。
- 如果文件长度小于size，我们就把文件读完。
- 如果没有设置size，默认全部读完。

### readline()

```python
readline([size])
```

读取一行

#### 示例操作
首先使用`touch mtianyan.txt` 和 `vi mtianyan.txt`
创建并写入内容：

```
blog.mtianyan.cn
blog.mtianyan.cn
blog.mtianyan.cn
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-4f52419cb53737d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看出

```python
readline(size):
	if len(line) > size:
		return size
	if len(line) < size:
		return len(line)
```
### readlines()

```python
readlines([size])
```

读取完文件(整数个buffer左右字节)，返回每一行所组成的列表。我们就可以使用列表访问的方式，使用文件。

弊端：一次性读完，占用很大内存。

#### 示例操作：

![mark](http://upload-images.jianshu.io/upload_images/1779926-3bbf1f8b7ea9528d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

虽然设置了`size`为`1`,但是还是把所有都读出来了。

![mark](http://upload-images.jianshu.io/upload_images/1779926-2daa702f3d85adfa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

输入`help(f.readlines)`查看定义：重复的调用`readline()`然后返回一个读到的行所组成的列表。

读取文件的大小：
- 如果`size`小于`缓冲区`的大小，则读取`缓冲区大小`的数据
- 如果`size`大于`缓冲区`的大小，读取`size大小`的数据，但不是完全的等于size的大小，一般读取比size大的整行的数据.

这个设置是python解释器内部的一个变量。

```python
import io
io.DEFAULT_BUFFER_SIZE
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-739cb8bb0d646350.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

设置参数小于`8192`每次能读取`8192`个字节。

- 设置参数`大于`一个`8192`小于`2*8192=16384`。则读取两个`buffer`值.
- `BUFFER`都是一整个一整个读

#### Linux命令使用

使用`vi`打开文件。`y`是复制当前行，`yy`是复制光标所在的一整行.
如果复制了一行，则`p`是在光标所在行的下一行粘贴，`10000p`就是粘贴10000次

- 命令`yy，10000p`复制`10000`行 .然后`:set nu` 显示行数

- `gg`： 
	- 跳转到文件头 `:1` 或 `gg`
	- 跳转到`文件末尾行数+gg` `:$` `Shift+g`
	- 跳转到指定行,例跳转到123行：`123gg`或`:123`.

使用`ctrl+z`可将程序切换到后台。用`fg`可将后台程序切换到前台。

![mark](http://upload-images.jianshu.io/upload_images/1779926-515fe79b1e68f562.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看到此时的文件以及有十几万个字节了

![mark](http://upload-images.jianshu.io/upload_images/1779926-ed469d4045097949.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看出这时候不再是一次性把文件读取完了，而是读取了`482`行。

`170051/100003`行，每行`17`个字节。

![mark](http://upload-images.jianshu.io/upload_images/1779926-4ba9cea3530aaa69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这时我们的`size`大于了缓冲区的大小`8192`。因此我们的`size`到了`8192`和`16384`
之间。

**重点：系统数据是按着`整个buffer，整个buffer`来读的。此时系统会读取差不多`两个buffer`的数据。也就是`963*17 =16371` 。**

待探究

#### iter: 使用迭代器读取文件

![mark](http://upload-images.jianshu.io/upload_images/1779926-f46d7c2d09b55cc4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

迭代器并不是把我们的文件全部存入了内存中。而是我们**每使用一次`next`方法就会自动读取下一行。**所以能在不消耗大量内存的情况下进行读取整个文件。

## 文件写入与写缓存

### write()

```python
write(str)
```

将字符串写入文件

### writelines()

```python
writelines(sequence_of_strings) : 写多行到文件
```
`sequence_of_strings`参数是一个`可迭代对象`:
- 可以是字符串
- 可以是字符串所组成的元组。
- 可以是一个字符串所组成的迭代器。
- 也可是一个字符串组成的列表

### 实际操作
![mark](http://upload-images.jianshu.io/upload_images/1779926-6ce4d3d7e4162533.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
In [37]: cat test.txt
test write
In [38]: f = open("mtianyan.txt",'w')
In [39]: f.writelines('123456')
In [40]: f.writelines((1,2,3))
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
<ipython-input-40-0a3726375675> in <module>()
----> 1 f.writelines((1,2,3))

TypeError: writelines() argument must be a sequence of strings

```

写入一个元组会报错 ：**提示必须是由字符串组成的序列。**

```python
In [41]: f.writelines(('1','2','3'))
In [42]: f.writelines(['1','2','3'])
In [43]: f.close()
In [44]: cat test.txt
test write
In [45]: cat mtianyan.txt
123456123123
```

可以看到我们都可以正常的写入由字符串组成的列表。

```python
In [46]: f = open("mtianyan.txt",'w')
In [47]: f.write("11111")
In [48]: cat mtianyan.txt

In [49]: 
```

文件内容为空，因为我们没有使用`f.close()`将文件关闭。

Linux系统有一种**写缓存机制**。如果我们不主动调用`f.close()`或 `flush`
他是不会写到磁盘文件的。

![mark](http://upload-images.jianshu.io/upload_images/1779926-bcb53ad98b20e489.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

写文件的过程中Python解释器进行**系统调用**。Linux系统内核将文件写入系统文件缓冲区。
当我们调用`f.close()`时，系统会更新文件到硬盘。

Python写磁盘的缓存机制

1. 主动调用`closed()`或`flush`方法。写缓存同步到磁盘
2. 写入数据量大于或等于写缓存，写缓存同步到磁盘。（此时系统写缓存又清空，然后就又继续缓存直到`close()`)

#### 实际操作

```python
In [62]: f = open("mtianyan.txt",'w')
In [63]: f.write('test write')
In [64]: cat mtianyan.txt
In [65]: f.flush()
In [66]: cat mtianyan.txt
test write
```

进行`flush()`操作时内容更新。

![mark](http://upload-images.jianshu.io/upload_images/1779926-417bda8856d0f3ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![mark](http://upload-images.jianshu.io/upload_images/1779926-c0c4dfddb55858bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

说明我的Linux的缓冲区就是这么大`147456`
当我们执行`close`文件会变大。因为全部写进去了。

## 文件关闭

1. 将**写缓存**同步到磁盘；
2. linux系统中每个进程**打开的文件个数是有限的；**
3. 如果打开文件数到了系统限制，在打开文件就会失败；

```
mtianyan@mtianyan-deep:~/Desktop/PythonFile$ ps
   PID TTY          TIME CMD
  5587 pts/1    00:00:00 bash
  5918 pts/1    00:00:02 ipython
```
看到ipython进程：

```
mtianyan@mtianyan-deep:~/Desktop/PythonFile$ cat /proc/5918/limits 
Limit                     Soft Limit           Hard Limit           Units     
Max open files            1024                 1048576              files     
```
- `file.fileno()`:文件描述符；每打开一个就会加1，直到1024失败
```
n [3]: for i in range(1027):
   ...:     f = open("mtianyan.txt",'r')
   ...:     print ("%d：%d" %(i,f.fileno()))

```

![mark](http://upload-images.jianshu.io/upload_images/1779926-53330de38f6e29ba.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

并没有起到效果。`fileno`在`10`和`12`不断来回

实现代码:

```python

In [11]: for i in range(1025):
    ...:     list_f.append(open("mtianyan.txt",'w'))
    ...:     print ("%d:%d" %(i,list_f[i].fileno()))
    ...:     

```

运行结果：

```
1010:1022
---------------------------------------------------------------------------
IOError                                   Traceback (most recent call last)
<ipython-input-10-dd5ee4024396> in <module>()
      1 for i in range(1025):
----> 2     list_f.append(open("mtianyan.txt",'w'))
      3     print ("%d:%d" %(i,list_f[i].fileno()))
      4 

IOError: [Errno 24] Too many open files: 'mtianyan.txt'
```

因此我们需要养成**良好编码习惯记得关闭文件。**

## 文件指针。

1. 写入文件后，必须打开才能读取写入的内容
2. 读取文件后，无法重新再次读取读取过的内容

比如：写完文件必须关闭才能读到我们写入的内容。

文件指针用于定位我们当前文件所执行到的位置：

- 使用`open()`打开时文件指针就在起始位置。 
- `read(3)`文件指针就会往后挪`3`个位置。指向`第四个位置`。
-  `write('mtianyan')`就会从当前文件指针`4`开始写。文件指针就会移动`8个字节`。

我们之前的问题都是因为我们无法使文件指针归于起始位置的，
下面我们学习自由操作文件指针：

```python
seek(offset[,whence]):移动文件指针
	offset 偏移量，可以为负数
	whence：偏移相对位置：
os.SEEK_SET:相对文件的起始位置0
os,SEEK_CUR:相对文件的当前位置1
os.SEEK_END:相对文件的结尾位置2
```

```python
In [1]: f = open('mtianyan.txt','r+')
In [2]: import os
In [3]: f.tell() 
Out[3]: 0
```
`f.tell()`（函数）返回当前文件的偏移返回的是一个整数，也许是一个长整数

```python
In [4]: f.read(3)
Out[4]: 'tes'
In [5]: f.tell()
Out[5]: 3
In [6]: f.seek(0, os.SEEK_SET)
In [7]: f.tell()
Out[7]: 0
```

可以看出我们可以使用`f.seek(0, os.SEEK_SET)`来进行指针归零。

```python
In [8]: f.seek(0,os.SEEK_END)
In [9]: f.tell()
Out[9]: 3139
In [10]: ls -l mtianyan.txt
-rw-r--r-- 1 mtianyan mtianyan 3139 1月   6 03:58 mtianyan.txt
In [11]: f.read(5)
Out[11]: ''
```

当然我们也可以把文件指针指到末尾去。文件指针已经在末尾了，向后继续读取会为空。

```python
In [12]: f.seek(-5,os.SEEK_END)
In [13]: f.read(5)
Out[13]: '231\n\n'
```

使用`f.seek(-5,os.SEEK_END)`可以将文件指针**倒着**移回去。

```
In [14]: f.tell()
Out[14]: 3139
In [15]: f.seek(-5,os.SEEK_CUR)
In [16]: f.tell()
Out[16]: 3134
```

读取`5`个之后，指针又跑到了`3139`：使用`f.seek(-5,os.SEEK_CUR)`
又可以从当前位置往前移。

当文件**偏移大于文件长度会**出现的问题：

```
In [17]: f.seek(0,os.SEEK_END)
In [18]: f.tell()
Out[18]: 3139
In [19]: f.seek(-3140,os.SEEK_END)
---------------------------------------------------------------------------
IOError                                   Traceback (most recent call last)
<ipython-input-19-331d0bc629ca> in <module>()
----> 1 f.seek(-3140,os.SEEK_END)

IOError: [Errno 22] Invalid argument

```
会报错：告诉我们这是一个**无效的参数。**类似于索引越界等。


# 文件属性及OS模块使用

讲解文件属性、标准文件、文件命令行参数、文件编码格式、使用os模块中方法对文件进行操作、使用os模块中的方法对目录进行操作

## 文件属性编码格式

### Python文件属性

- `file.fileno()`:文件描述符；每打开一个就会加1，直到1024失败
- `file.mode:`文件打开权限
- `file.encoding:`文件编码格式
- `file.closed:`文件是否关闭


```python
In [20]: f.fileno()
Out[20]: 12
In [21]: f.closed
Out[21]: False
In [22]: f.mode
Out[22]: 'r+'
In [23]: f.encoding

```

没有返回值：为`ASCII`码文件。应该与系统相关。

### Python标准文件

- 文件标准输入：`sys.stdin`；
- 文件标准输出：`sys.stdout`；
- 文件标准错误：`sys.stderr`;

```python
In [26]: import sys
In [27]: type(sys.stdin)
Out[27]: file
In [28]: sys.stdin.mode
Out[28]: 'r'
In [29]: sys.stdin.read()
```

系统的`sys.stdin`的`read()`是指从控制台读入数据。

```
In [32]: sys.stdin.fileno()
Out[32]: 0
```
系统的`sys.stdin`的`fileno`为0。也就是我们启动一个进程，他**首先**会**打开标准输入**：从控制台读数据。

`a = raw_input()` 其实内部是调用`sys.stdin`来读取数据的。

```
In [37]: sys.stdout.mode
Out[37]: 'w'
In [38]: sys.stdout.write("1000")
1000

```
`sys.stdout`只可以写，不可以读。写入相当于**写入到控制台，会直接显示**。

`print`实际内部调用的是`stdout`

```
In [39]: sys.stdout.fileno()
Out[39]: 1
```
`sys.stdout`是系统在启动程序时**第二个**打开的。

也就是系统会先为我们**打开输入**，**再打开输出**。

```python
In [40]: sys.stderr.mode
Out[40]: 'w'
In [41]: sys.stderr.write("error")
error
In [42]: sys.stderr.fileno()
Out[42]: 2
```
`sys.stderr`是系统在启动程序时第二个打开的。
**先开输入，再开输出。第三err输出。**

### Python文件命令行参数

`sys`模块提供了`sys.argv`，通过该属性可以得到命令行参数

很多程序都是这么做的：比如加上`-v`,根据**不同的参数完成不同的功能。**

`sys.argv`是一个字符串组成的列表

```
import sys

if __name__ == '__main__':
    print len(sys.argv)
    for arg in sys.argv:
        print arg
```

运行结果：

```
1
D:\mtianBlog\hexoBlog-Github\source\_posts\test.py
```
直接运行,我们没有提供参数，打印出了当前文件的绝对路径文件名。

### Python文件编码方式。

使用普通方式打开文件：写入`u天涯明月笙`。

```
In [3]: f.write(u'天涯明月笙')
---------------------------------------------------------------------------
UnicodeEncodeError                        Traceback (most recent call last)
<ipython-input-3-3cd15b559bd9> in <module>()
----> 1 f.write(u'天涯明月笙')

UnicodeEncodeError: 'ascii' codec can't encode characters in position 0-4: ordinal not in range(128)
```

会报错：`UnicodeEncodeError`因为我们的文件是`ascii`格式的。不能将`Unicode`字符串写入。

实现代码:

```python
In [1]: a = unicode.encode(u'天涯','utf-8')
In [2]: a
Out[2]: '\xe5\xa4\xa9\xe6\xb6\xaf'
In [3]: f.write(a)
```

运行结果：

```
In [7]: cat mtianyan.txt
天涯
```

我们可以用`codecs`模块提供方法创建制定编码格式文件.

```python
import codecs
f = codecs.open('test.txt','w','utf-8')
f.encoding
```

`open(fname,mode,encoding,errors,buffering):`使用指定编码格式打开文件

## linux文件系统

**所有**设备都可以看为文件:
- 包括磁盘(ext2,ext4)文件,NFS文件系统，各种外设

![mark](http://upload-images.jianshu.io/upload_images/1779926-ae7f5b6240b621f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`虚拟文件系统`为文件提供`文件节点`

每一个外设在内核中都有一个对应的**驱动程序**。通过**系统调用**对文件节点进行访问。把所有外设作为文件处理。

![mark](http://upload-images.jianshu.io/upload_images/1779926-80ac236f69c604eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在操作系统之上的Python解释器,Python程序跑在解释器中。

![mark](http://upload-images.jianshu.io/upload_images/1779926-f391fab5d67427af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用`open()`会访问`文件节点`。设备节点对应虚拟文件系统的方法，这个文件系统与驱动绑定。
驱动会操作硬件设备。


## os模块对文件和目录进行操作

使用os模块来打开文件：


`os.open(filename, flag [,mode])`打开文件
  	
    - os.O_CREAT:创建文件
    - os.O_RDONLY:只读方式打开
    - os.O_WRONLY:只写方式打开
    - os.O_RDWR:读写方式打开

`os.read(fd, buffersize)`:读取文件，返回值为读取的内容
`os.write(fd, string)`:写入文件 ,返回值是写入数据的大小
`os.lseek(fd, pos, how)`: 文件指针操作
`os.close(fd)`:关闭文件


`umask`获取系统的默认权限。我的是`0022`

```python
In [14]:fd = os.open('mtianyan.txt',os.O_CREAT | os.O_RDWR)
In [15]: n = os.write(fd,"mtianyan")
In [16]: n
Out[16]: 8
In [17]: os.lseek(fd,0,os.SEEK_SET)
Out[17]: 0
In [18]: os.read(fd,5)
Out[18]: 'mtian'
Out[19]: os.close()
```
使用os是为了跨平台。
### os方法
| os方法 | 说明|
| ------------- | ------------- |
|os.access(path, mode)| 判断该文件权限：F_OK存在,权限:R_OK ,W_OK, X_OK |
|os.listdir(path) | 返回当path路径下所有文件名组成的列表 |
|os.remove(path) | 删除文件 |
|os.rename(old, new) | 修改文件或者目录名 |
|os.mkdir(path[, mode]) | 创建目录 |
|os.makedirs(path[, mode]) | 创建多级目录 |
|os.removedirs(path) | 删除多级目录 |
|os.rmdir(path)| 删除目录(目录必须空目录) |


```
os.access('mtianyan,txt',os.R_OK)
os.listdir('./')
os.rename('test/','test1')
```
### os.path方法
| os.path方法| 说明 |
| ------------- | ------------- |
| os.path.exists(path) | 当前路径是否存在,也可以判断是否有该文件 |
| os.path.isdir(s)| 是否是一个目录 |
| os.path.isfile(path) |是否是一个文件|
| os.path.getsize(filename) | 返回文件大小，返回目录文件大小 |
| os.path.dirname(p) | 返回路径的目录|
| os.path.basename(p)| 返回路径的文件名 |


```
os.path.exists('./mtianyan.txt')
# True
os.path.isdir('./'')
# True
os.path.isdir('mtianyan.txt')
# False
os.path.isfile('mtianyan.txt')
# True
```

# 文件练习

使用Python来管理`ini`文件：实现`查询`，`添加`，`删除`，`保存`。

- 掌握文件的基本操作
- 认识`ini`文件
- 了解`ConfigPaser`

## ini配置文件格式：

```
节： [session]
参数(键=值)  name=value

[port]
	port1 = 800
	port2 = 900
```

定位带`port`这个节，然后找到。
## ConfigParser
mtianyan.txt内容:
```
[userinfo]
name = mtianyan
pwd = abc

[study]
python = 15
java_base = 20
```
两个概念：`sections(大的分类)`和`option小的选项`
```python
import ConfigParser
cfg = ConfigParser.ConfigParser()

help(cfg.read)

cfg.read('mtianyan.txt')
# ['mtianyan.txt']
cfg.sections() #返回节点值：userinfo，study
# ['userinfo', 'study']
for se in cfg.sections():
	print se
	print cfg.items(se)
#遍历出整个文件
```
遍历结果如下

```python
userinfo
[('name', 'johntian'), ('pwd', 'abc')]
study
[('python_base', '15'), ('python_junior', '20'), ('linux_base_', '15')]
```
```python
cfg.set('userinfo', 'pwd', '1234567')     #修改键值
cg.set('userinfo','email','1147727180.com')//增加键值

cfg.remove_option('user info','email')  //删除键值

```

## 文件操作练习

```python
import os
import ConfigParser

'''
1: dump ini
2: del section
3: del item
4: modify item
5: add section
6: save modify

'''


class student_info(object):

    def __init__(self, recordfile):
        self.logfile = recordfile
        self.cfg = ConfigParser.ConfigParser()

    def cfg_load(self):
        self.cfg.read(self.logfile)

    def cfg_dump(self):
        se_list = self.cfg.sections()
        print "**********************>"
        for se in se_list:
            print se
            print self.cfg.items(se)
            print "<*********************"

    def delete_item(self, section, key):
        self.cfg.remove_option(section, key)

    def delete_section(self, sescion):
        self.cfg.remove_option(section)

    def add_section(self, section):
        self.cfg.add_section(section)

    def set_item(self, section, key, value):
        self.cfg.set(section, key, value)

    def save(self):
        fp = open(self.logfile, 'w')
        self.cfg.write(fp)
        fp.close()

if __name__ == '__main__':
    info = student_info('mtianyan.txt')
    info.cfg_load()
    info.cfg_dump()
    info.add_section('userinfo')
    info.set_item('userinfo', 'pwd', 'abc')
    info.cfg_dump()
    info.add_section('login')
    info.set_item('login', '2018-0106', '20')
    info.cfg_dump()
    info.save()
```


运行结果：

```
**********************>
**********************>
userinfo
[('pwd', 'abc')]
<*********************
**********************>
userinfo
[('pwd', 'abc')]
<*********************
login
[('2018-0106', '20')]
<*********************
[Finished in 0.3s]
```






