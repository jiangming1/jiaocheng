![Linux下读写文件.png](http://upload-images.jianshu.io/upload_images/1779926-4134807ab6a314dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
f=open("hello.py"): 默认read是进行只读方式打开文件
type(f): 查看f对象的类型为文件对象，查看文件对象有什么方法可以f. table键补齐进行查看
c=f.read(): 使用read()方法读取文件，并且把内容保存到c中
c: 把文件内容打印出来
f.write("test"): 文件中写入东西，报错，说文件没有提供写权限。
```

![python文件打开方式](http://upload-images.jianshu.io/upload_images/1779926-9bf9c2dca4f7999b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
'r':使用只读方式打开文件必须存在
'w':只写方式打开，文件若不存在则创建文件，文件存在则会清空文件内容
'a': 追加方式打开，文件不存在则创建文件，文件存在则写入内容就是以追加的方式
'r+'/'w+': 读写方式打开
'a+'：追加和读写方式打开
```
![测试文件打开1.png](http://upload-images.jianshu.io/upload_images/1779926-121acbdb2fbf245e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![测试文件打开方式2.png](http://upload-images.jianshu.io/upload_images/1779926-180708be08c00183.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 关于r+，和w+ 经过测试发现，r+同时有读写权限，只不过写进去的内容从文件内容头部开始进行了替换，w+，以w+的读写方式打开，读取文件文件内容为空，说明以w+方式有读写权限，但还是会把内容清空。

![w+测试（文件中原本有内容）.png](http://upload-images.jianshu.io/upload_images/1779926-85226f9ebcb3cbbd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

< 'rb','wb','ab','rb+','wb+','ab+'：二进制打开，和普通打开基本是一样的，二进制打开意义，比如读取一张图片的eff信息，长宽拍摄日期等，普通方式是以文本的方式打开的，读取的时候不能获取正确的内容，当以二进制的方式读时，就可以根据eff头信息读取到正确的内容。

#### 文件操作之文件读取
文件读取方式：
read([size])
readline([size]):读取一行
readlines([size]):读取完文件（错误，是读取完一个buff个字节），返回每一行所组成的列表
iter：因此有另外读完文件的方法，使用迭代器读取文件。

![readline()读取文件操作.png](http://upload-images.jianshu.io/upload_images/1779926-eacd69524a4ea1d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> f.readline()读取文件操作，当行的大小小于读取的size，则返回整行，若行的大小大于要读取的size，则返回size个字节，并且是在前一次读取的基础上返回下面的size个字节。注意，读取文件内容必须先打开文件。

![readlines测试](http://upload-images.jianshu.io/upload_images/1779926-7ba17f67fb41b918.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> readlines()测试发现，传入size为1时将整个文件都读入了，可以使用help(f.readlines)查看这个属性的作用，可以发现，这个方法中，size为可选参数，一旦设置了这个参数，就是读取一个buff的内容，io操作的这个默认buff为8192个字节大小左右。


![使用迭代器遍历文件内容.png](http://upload-images.jianshu.io/upload_images/1779926-b95f5ddbb1caa338.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> 使用迭代器遍历文件内容，打开文件以后，直接iter(f)将文件转化为迭代内容，再直接使用for循环遍历即可。测试结果得到16行正确。
使用迭代器迭代文件的好处，实质把文件放入了迭代器，并不是把文件的所有内容都放到内存中，每使用next方法去获取一行的时候，才会自动读取一行。所以可以在不消耗大量内存下完成对整个文件的遍历。


#### python操作之文件写入
文件读取方式：
1. write(str): 把字符串直接写入文件
2. writelines(sequence_of_strings)： 写入多行到文件，参数为课迭代的对象，如list，但是writelines将迭代对象写入，不会自动换行，可以使用将迭代的字符串转化为带换行符的字符串，再写入。这种方式写‘w’方式打开的文件是在后面追加内容，内部参数必须为字符串序列，因此元组或者list内部必须为‘字符串’。

![写缓存机制](http://upload-images.jianshu.io/upload_images/1779926-5dc64f19da756496.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
> 写缓存机制，在write之后立即查看文件，发现内容还没有被写入。需要主动调用close或者flush，写的内容才会被记录到磁盘文件上。

python写文件的过程以及存在的问题：
在写文件f.write()时，python解释器解释它，调用系统的写函式，在内核中有文件缓冲buff，当我们调用close，把文件关闭时，才会系统调用，内核才会把缓冲的内容写到磁盘文件上。因此出现了“写问题”，即写入的和磁盘上不一致的问题。解决办法：主动调用，写缓存同步到磁盘； 写入数据量大于或等于写缓存，写缓存同步，但末尾不够满缓冲的还是需要手动关闭。


#### python操作之文件关闭
文件需要关闭的原因：
1. 将写缓存同步到磁盘
2. linux系统中每个进程打开文件的个数是有限的
3. 如果打开文件数超过了限制，再打开文件就会失败 

如何查看一个进程的最大打开文件限制：这里查看python进程
```
首先要进入到ipython环境，然后再退出，注意这里的退出不是exit关闭进程，而是使用crtl+z停止。在初始环境中输入ps命令，查看进程号，再根据进程号查看这个进程的属性。cat /proc/进程号/limits 就可以看到这个进程Max open files的数量了。
```
![查看进程属性操作](http://upload-images.jianshu.io/upload_images/1779926-900a92da955611d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



```
# 模拟打开最大文件数，最后超出范围，打开失败
list_f = []
for i in  range(1025):
	list_f.append(open('imooc.txt','w'))
	print "%d : %d" % (i.list_f[i].fileno)

#fileno是当前进程打开的文件数，每打开一个文件就会加1，关闭了就会减1。使用list_f存放文件对象，虽然打开的是同一个文件，但是存放在列表里的是1024个不同的文件对象。所以不可以用下面的方法测试：

for i in range(1025):
	f = open('imooc.txt','w')
	print "%d : %d" % (i.list_f[i].fileno)
#这里一直都是f对象，并且每次循环后都会把上一个f关闭，因此fileno一直在加1减1
```

#### python文件之文件指针
python写入和读取问题：
1. 写入文件后，必须重新打开文件才能读取写入内容
2. 读取文件后，无法重新读取读过的内容

文件读取过程中文件的定位：
需要办法自有操作文件指针，文件指针需要归为到最开始才能重新读

![文件写入和读取指针移动过程](http://upload-images.jianshu.io/upload_images/1779926-f9b17f882612333b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

python文件指针操作：
seek(offset[,whence]):移动文件指针
	offset：偏移量，可以是负数，相对于某个位置的偏移量
	whence：偏移相对位置，有三种，`os.SEEK_SET`：相对文件起始位置，0； `os.SEEK_CUR`：相对文件当前位置,1；
	`os.SEEK_END`: 相对文件结尾位置，2。设置偏移量时注意不要超出文件范围，会报错。
f.tell():返回文件当前偏移，是个数字0,1,2之类的


### 文件对象属性+标准文件+文件命令行参数+文件编码格式
文件属性：
file.fileno():linux所对应的文件描述符，是个数字，表示当前进程打开的第多少个文件数
file.mode: 文件打开权限
file.encoding：文件编码格式
file.closed: 检查文件是否关闭 true or false

标准文件：
文件标准输入： sys.stdin(是一个文件对象) fileno为0 只读
文件标准输出： sys.stdout    fileno为1  只写
文件标准错误： sys.stderr    fileno为2  只写
每当我们新打开一个终端，就会相应的创建三个文件，分别就是这三个标准文件。

文件命令行参数：
执行程序时，可以根据参数的不同，完成不同的功能。
sys模块提供`sys.argv`属性，通过该属性可以得到命令行参数
`sys.argv`：字符串组成的列表
 
 ```
 # arg.py
 import sys
 if __name__ == '__main__':
 		print len(sys.argv):
 		for arg in sys.argv:
 			print arg

 #再python命令行中运行arg.py文件，后面可以跟上任意参数。
 #这些参数代表的具体功能可以自己的定义。运行结果如下：
 #运行时后面的参数会自动传入sys.argv属性中，是一个列表。
 ```

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-7de3c0740ccaa9e1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

编码格式：
如果在文件中写入`u'慕课网'`的字符串，会报错，说ASCII文件不能有unicode编码的字符串，解决办法：
1. 把unicode转化为utf-8。`a=unicode.encode(u'慕课网',utf-8)`。unicode中一个中文字符占两个字节，在utf-8中占三个字节。将转换后的写入就是就是中文字符了。
2. 创建utf-8或其他指定编码格式文件：	
	使用codecs模块提供方法创建指定编码格式文件
	`open(fname, mode, encoding, errors, buffering)`

```
import codecs
f = codecs.open('test.txt','w','utf-8')
f.encoding
#utf-8,此时向文件中写入中文字符就是没问题的
```

#### Linux文件系统简介
在linux系统中，所有的设备都可以看成是文件，文件包括：磁盘（ext2，ext4）文件，NFS文件系统，各种外设（sd卡，USB设备）等。
那linux如何来管理外设，为应用层提供统一接口？
每个外设在内核中都有自己的一个驱动程序，接口都是不统一的。所以管理很麻烦，所以内核就在底层做了抽象的工作，把所有的外设统一的封装成虚拟文件系统，生成一个文件节点，这些文件有统一的操作模式，open，read，write等，这些接口都可以通过系统调用的方式对这些节点进行访问，就可以把所有的外设使用统一的文件形式进行管理。
![linux文件系统示意图](http://upload-images.jianshu.io/upload_images/1779926-94bc8249aacfbc23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![python操作文件流程](http://upload-images.jianshu.io/upload_images/1779926-02c576da87737980.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

