3- TensorFlow介绍

一句话介绍:

>Google开源的基于数据流图的科学计算库，适用于机器学习

不局限于机器学习，但目前被大多用于机器学习等。

![mark](http://myphoto.mtianyan.cn/blog/180210/5F14aabELm.png?imageslim)

TensorFlow计算流图的概念图

Tensor在图中流动。

### TensorFlow的含义

拆字释义:

- Tensor 张量(tf中数据的表征)
- flow 流动

![mark](http://myphoto.mtianyan.cn/blog/180210/2c95eiL9f7.png?imageslim)

>张量在图中流动

TensorFlow的详细架构

![mark](http://myphoto.mtianyan.cn/blog/180210/hmg35c657E.png?imageslim)

TensorFlow基本架构

![mark](http://myphoto.mtianyan.cn/blog/180210/F5Dbf4c096.png?imageslim)

TensorFlow 大事记

![mark](http://myphoto.mtianyan.cn/blog/180210/I1aiFkfmLm.png?imageslim)

>deepmind团队之前用的torch。

![mark](http://myphoto.mtianyan.cn/blog/180210/iheigk5HA9.png?imageslim)

>底层api调用起来繁琐。

1.3版本加入了很多高层次的抽象api。调用底层api更方便，使用代码量更少。

![mark](http://myphoto.mtianyan.cn/blog/180210/gfD8dlB3gG.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180210/mDBli9iBEh.png?imageslim)

### TensorFlow的特点

灵活性: 只要可以将计算表示成数据流图，就可以使用TensorFlow

跨平台: Linux windows Android Ios

多语言: 上层语言 python c++ java Go

速度快: 包含了XLA这款强大的线性代数编译器

上手快: Keras,Estimiators 等等高层api

可移植: 代码几乎不加修改的可以移植到cpu gpu tpu等等

### TensorFlow的著名用途

![mark](http://myphoto.mtianyan.cn/blog/180210/dHKmJe5aJd.png?imageslim)

使用TensorFlow的公司

![mark](http://myphoto.mtianyan.cn/blog/180210/3I4f3KcBLK.png?imageslim)

小米 & 中兴 & 京东

## TensorFlow和其他机器学习库的对比

![mark](http://myphoto.mtianyan.cn/blog/180210/50Dhcf6Dam.png?imageslim)

theano 就是一个比较老的框架。

### 各个机器学习库的对比

![mark](http://myphoto.mtianyan.cn/blog/180210/I0HAbf0L76.png?imageslim)

DL4J java

torch(Lua) 和 pytorch(Facebook)

Caffe(2) 贾扬清由Google tf 跳槽到Facebook

Keras 底层必须由TensorFlow或theano的支持。

### 闲扯一下法国人对于世界的杰出贡献

![mark](http://myphoto.mtianyan.cn/blog/180210/gFDhA1cAiE.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180210/6723Gcamd6.png?imageslim)

### 各个库的网站对比

![mark](http://myphoto.mtianyan.cn/blog/180210/lEhEhbC8eK.png?imageslim)

文章: Tensorflow 与 主流深度学习框架对比

### 机器学习框架

TensorFlow的收藏数，fork数，issue数遥遥领先于其他。

skit learn 的pull request多于TensorFlow
 
contributor数多

### TensorFlow的优势：集大成者

- Google力捧，数据流图的设计理念和编程模式。社群广大，文档详尽。
- 功能强大的可视化 Tensor board 和 模拟环境 playground 

配置方便。在线模拟演示。

Epoch 整体完整运行次数 learning rate 

选择激励函数 正规化 L1 还是L2的选择。正规化率。

问题类型: 分类问题和回归问题

输入特征值的类型。噪音，增加隐层。点亮输入的参数。

点击左上角run，开始帮你训练。

点击可以看到当前的每个参数的权重。

- 移动端(ios 和 Android) 云端，服务器端 ，分布式，各种硬件。

### 总结

有些库略显不足，如Torch(lua语言) scikit-learn 不支持Gpu，不支持分布式

TensorFlow 总体来说很优异，虽然在某些方面速度和效率还不够。

推荐: MXNet PyTorch TensorFlow(scikit 和 keras) caffe2

## 如何学习TensorFlow？

官网: tensorflow.org

下面语言切换为中文。点击开始使用。

官方文档，如何安装。

develop 是提供教程为开发者使用。

Programmer 指南

Tutoials 教程

performance 如何提高性能

mobile 如何在手机等去开发。

一般常用python api。文档阅读api指南。

deploy 部署到平台。 如何在Hadoop部署

扩展: 体系结构

社区； 

版本:

云平台：

### 学习方式:

- 官网: 了解

- 视频 + 书籍 入门 + 知识点学习

- 实战: 技术

人工智能 - 数学知识 - 机器学习 - 深度学习

cs156 吴恩达课程 107节 机器学习基本课程

吴恩达 - 神经网络 和 深度学习课程

![mark](http://myphoto.mtianyan.cn/blog/180210/A6BeJK4eKe.png?imageslim)

可以穿插进行。

## TensorFlow前景

人工智能是大势所趋

TensorFlow : 人工智能框架的领军产品

Google出品，大多属精品。

![mark](http://myphoto.mtianyan.cn/blog/180210/KKDef4b79c.png?imageslim)












