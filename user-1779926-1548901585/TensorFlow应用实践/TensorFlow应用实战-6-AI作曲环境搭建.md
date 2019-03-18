用TensorFlow开发会作曲的AI

## 背景和知识点介绍

人工智能的不断火热。

Google的Magenta(洋红色)项目

![mark](http://myphoto.mtianyan.cn/blog/180324/CijHEifAhj.png?imageslim)

https://magenta.tensorflow.org/


>Make Music and Art Using Machine Learning

项目的源代码在github, pip install就可以安装。

可以查看官方文档查看用法。

### 所用到的音乐存储格式

![mark](http://myphoto.mtianyan.cn/blog/180324/h0a1JHaK87.png?imageslim)

MP3包含音乐，midi有点像一个电子的乐谱，告诉电脑怎么演奏。

### 用到的神经网络技术

RNN & LSTM(基于RNN优化的一个长短期记忆) long short time memory

### 为什么用RNN(循环神经网络)?

- RNN 每一个输入与前面的输出建立起关联

- 能够很好的处理序列化的数据。前面的数据和后面的数据是有联系，影响的。

- 能以前面的序列化对象为基础，来"生成"新的序列化对象

### 生成句子(序列化)

我是中国人，我很爱我的祖国，我会说

序列中的每个词不可以颠倒顺序，是有次序的。

![mark](http://myphoto.mtianyan.cn/blog/180324/B2eihDaH5f.png?imageslim)

循环神经网络会帮我们填上答案。

生成音乐(也是序列化)

![mark](http://myphoto.mtianyan.cn/blog/180324/m19da385bb.png?imageslim)

它也可以基于前面的音乐，为我们生成新的音乐。

### 音乐基础知识

![mark](http://myphoto.mtianyan.cn/blog/180324/gK8LlblClG.png?imageslim)

音符。

一个八度: Octave

八度由12个半音组成。

#读作sharp，是音乐里的升音符号，在sharp前面的这个音去升高一个半音。

![mark](http://myphoto.mtianyan.cn/blog/180324/0b46f9Cf3K.png?imageslim)

c# 比 c高了一个半音。c#与d之间也是一个半音。c和d之间相差一个全音。之后每隔间隔是一个半音。

E 和 F 相当于mi和fa，他们两之间其实是一个半音，而不是一个全音。

最后的b和下一个的C又高半个音。

![mark](http://myphoto.mtianyan.cn/blog/180324/eh3fAB7il0.png?imageslim)

### 编程语言C#的含义其实是音符

c# : c升高半个音

音符表格

![mark](http://myphoto.mtianyan.cn/blog/180324/AEAdLb4ce6.png?imageslim)

midi格式是用这些数字来标志的。C4中央和A4都是调音的节点

0-127 表示不同音的音符。

http://midikits.net/midi_analyser/midi_note_numbers_for_octaves.htm

数字表示对应的音。

![mark](http://myphoto.mtianyan.cn/blog/180324/0I7EAgahFB.png?imageslim)

https://newt.phys.unsw.edu.au/jw/notes.html

标准的钢琴是88个键，不能表示全部的128个数据。

钢琴最低的音从A0(21)开始。a0对应白键，a#0对应黑键,B0对应白键23

![mark](http://myphoto.mtianyan.cn/blog/180324/Em5j7EEHj6.png?imageslim)

B0和c1之间是一个半音。

钢琴有七个八度音，`12*7` 一个八度音里面有12个半音。

88键的钢琴，有七个八度。加上首部的3个键，和尾部的1个键c8

```
12*7 + 3 +1 =88
```

曲子样例1 & 2

>还行，能听

### 局限性

![mark](http://myphoto.mtianyan.cn/blog/180324/mhA6GbdFed.png?imageslim)

## 音乐和数学的联系

![mark](http://myphoto.mtianyan.cn/blog/180324/bbCF4Kbi7J.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180324/L2GkFkG95a.png?imageslim)

生动的音乐和枯燥的数学真的有联系？

数学是科学的皇后。

>数学是大部分我们研究的学科的基础: 物理，密码学，天文学，化学。

- 计算机底层语言也是0,1这样的二进制(cpu里面晶体管的开关)

- 德国数学家高斯说: "数学是科学的皇后"

### 音乐的本质

- 音乐是由声音(Sound)组成的
- 声音是由重复的声波(Sound Wave)组成的
- 音符的音调有对应的频率，由Hz描述

http://www.paleotechnologist.net/?p=2253

![mark](http://myphoto.mtianyan.cn/blog/180324/7Jkia9A9I5.png?imageslim)

音符对应的Frequency(Hz)表示。最小，理想，最大。

### 音乐中的数学

- 在乐谱中经常能看到数学: 2/4 (以四分音符为一拍，每小节两拍)

- 音符之间的关系也由数学所表示

![mark](http://myphoto.mtianyan.cn/blog/180324/8mC1jddgl8.png?imageslim)

两个四分音符加起来就是一个二分音符。两个八分音符加起来是一个四分音符

全音符表示为几个音符相加之后的和。

- 节奏(Rhythm) 是音乐在时间上的组织。

### 毕达哥拉斯琴弦律

- 两个音弦长成简单整数比，同时或连续弹奏，发出和谐声音。

毕达哥拉斯 勾股定理

- 和谐的比例贯穿整个大自然的"音乐"

- 宇宙的星球间的距离成简单的整数比，运行时发出美妙的球体音乐

### 法国数学家傅里叶的证明

傅里叶变换

- 所有的乐声(不论是器乐还是声乐) 都能用数学表达式来表述

- 是一些简单的正弦周期函数的和

- 每种声音都有三种品质: 音调，音量和音色

### 黄金比例 和 斐波那契数列

- 有不少作曲家用黄金比例和斐波那契数列来帮助他们作曲

- 黄金比例: 0.618:1
- 斐波那契数列: 

![mark](http://myphoto.mtianyan.cn/blog/180324/AmeADCfJ16.png?imageslim)

开始是1,1+2等于3，数前后关系有黄金比例。

把音乐形式用我们的数学形式来表达，放进我们的循环神经网络，训练之后为我们生成新的音乐形式。

## 什么是midi文件

- Musical Instrument Digital Interface(乐器数字接口)

"数码乐谱" 二进制文件

基本结构: 文件头 + 数据描述

http://ericjknapp.com/blog/2014/03/28/midi-file-format/

![mark](http://myphoto.mtianyan.cn/blog/180324/lG7kjI3KIK.png?imageslim)

头部，后面是数据

- 现代音乐几乎都是MiDI加上音色库来制作合成的

### 什么是MIDI
- 20世纪80年代初为解决电声乐器之间的通信问题而提出。
- 记录的是音符，控制参数等指令。它指示设备要做什么及怎么做
- 电脑，合成器，声卡，电子乐器能相互控制，交换信息

![mark](http://myphoto.mtianyan.cn/blog/180324/eAIJg1jmc3.png?imageslim)

在midi设备的控制下连接电子吉他。电子琴等。

### MIDI和五线谱的区别

五线谱是拿给人演奏看的

MIDI是拿给电脑演奏看的

MIDI文件格式分析-理论篇

https://www.midifan.com/modulearticle-detailview-901.htm

我们会演示如何从midi中读取数据，然后存入我们的二进制文件。
再去给我们的神经网络调用训练。训练之后也会产生一个midi的文件

我们之后再把midi文件转成mp3文件，给大家播放试听。

MiDI包含的信息量一般来说远远超过五线谱

### MIDI的优势

小: MIDI文件一般十几kb，MP3文件一般十几MB 1MB=1024kb

记录的信息丰富: 音轨，声调，强度，音量，颤音，相位，节奏

提高制作音乐效率，降低成本，乐队耗资太大

示例MIDI文件。

MIDI是一个电子乐谱。一般不能直接播放。win可以直接播放。

车库乐队软件可以播放。

## 配置AI作曲的开发环境

TensorFlow

keras

```
sudo pip install keras
```

Music21(处理一些音乐格式的)

```
sudo pip install music21
```

H5Py用于存储二进制数据的

```
sudo pip install h5py
```

Ffmpeg(处理音乐声卡相关)

```
sudo apt install ffmpeg
```
Timidity(midi转成mp3)

```
sudo apt install timidity
```

http://web.mit.edu/music21/

麻省理工学院出品的软件包。

HDF5 python的一个接口。对于hdf5数据格式进行操作

ffmpeg 一个跨平台的音频，视频转换。

timidity是可以去播放midi的一个。

```
sudo pip install keras
sudo pip install music21
sudo pip install h5py

sudo apt install timidity
```
