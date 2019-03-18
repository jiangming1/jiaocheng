之前我们在使用cnn做图片分类的时候使用了CIFAR-10数据集

## 其他框架对于CIFAR-10的图片分类是怎么做的

来与TensorFlow做对比。

Caffe Keras

### 安装

官方安装文档:

https://github.com/IraAI/caffe-gpu-installation

https://github.com/BVLC/caffe/tree/windows

windows下安装gpu加速版的caffe

![mark](http://myphoto.mtianyan.cn/blog/180408/0bLefKJbmb.png?imageslim)

使用的数据集依然是CIFAR-10，使用的也依然是卷积神经网络。查看有什么不同点和相同点。

十种分类。

### 准备数据

把图片转成leveldb格式，对图片做均值。

![mark](http://myphoto.mtianyan.cn/blog/180408/G9j2aCej0g.png?imageslim)

里面是一个官方给出来的例子，如何使用caffe 对cifar10做分类。

caffe-master/examples/cifar10/create_cifar10.sh

是一个bash脚本: 作用是转换cifar数据到 into leveldb format.


定义了两个目录变量和数据类型

```python
EXAMPLE=examples/cifar10
DATA=data/cifar10
DBTYPE=lmdb
```

删除指定目录下的文件

```python
echo "Creating $DBTYPE..."

rm -rf $EXAMPLE/cifar10_train_$DBTYPE $EXAMPLE/cifar10_test_$DBTYPE
```

调用了convert_cifar_data.bin 二进制文件。 指定参数，就可以下载，解压，格式化。

计算图片的均值。compute_image_mean 输入参数 backend: 数据类型。

计算均值，保存到mean.binaryproto文件中

- 将数据转换为lmdb格式，保存到磁盘中。
- 计算image文件的均值。保存。

这里用到的两个命令是c++编写的，也可以阅读源代码变成python脚本。

网上也有这个数据脚本的python实现代码

http://research.beenfrog.com/code/2015/05/04/write-leveldb-lmdb-using-python.html

就是根据c++代码改写的。这两个数据格式都是键值对的格式。

### 训练模型
caffe-master/examples/cifar10/train_quick.sh

```
#!/usr/bin/env sh
set -e

TOOLS=./build/tools
```

定义一个存放的目录

调用tools目录下的caffe 主程序。

```python
$TOOLS/caffe train \
  --solver=examples/cifar10/cifar10_quick_solver.prototxt $@
```

第一个参数指定训练，第二个参数指定模型

cifar10_quick_solver.prototxt

超参数和模型的结构定义与描述。

```python
# The train/test net protocol buffer definition
net: "examples/cifar10/cifar10_quick_train_test.prototxt"
```

网络结构:
caffe-master/examples/cifar10/cifar10_quick_train_test.prototxt

我们在使用TensorFlow时是使用python脚本定义的网络结构。有多少层什么的。

caffe使用一个描述文件来描述。

第二次的时候从第一次的模型快照处继续运行。

```python
# reduce learning rate by factor of 10 after 8 epochs
$TOOLS/caffe train \
  --solver=examples/cifar10/cifar10_quick_solver_lr1.prototxt \
  --snapshot=examples/cifar10/cifar10_quick_iter_4000.solverstate $@
```

两次的变化只是学习率不同。

### 模型的描述文件

![mark](http://myphoto.mtianyan.cn/blog/180408/fa6IHbjLII.png?imageslim)

- 训练参数，初始化参数: 网络描述

caffe中有方法可以将模型的描述文件打印成图表

caffe-master/examples/cifar10/cifar10_quick_train_test.prototxt

http://ethereon.github.io/netscope/#/editor

![mark](http://myphoto.mtianyan.cn/blog/180408/Ded48hlb6b.png?imageslim)

第一层是数据层。卷积层 池层 卷积层 池层 全连接 激励函数。 softmax

本地版本:

```
https://graphviz.gitlab.io/_pages/Download/Download_windows.html
# 下载Graphviz
pip install pydot
```

```
python draw_net.py C:\Users\mtian\PycharmProjects\NeuralNetworksGetStarted\caffe-master\examples\cifar10\cifar10_quick_train_test.prototxt ./cifar.png
```

运行的画图py文件，位于caffe python下。

三个参数: 1. 网络模型的ProtoTXT文件 2. 保存的图片路径 3. 从左到右。

>–rankdir=x , x 有四种选项，分别是LR, RL, TB, BT 。

画图报错:

```
Traceback (most recent call last):
  File "draw_net.py", line 9, in <module>
    import caffe.draw
  File "C:\software\caffe\python\caffe\draw.py", line 31, in <module>
    pydot_find_graphviz = pydot.graphviz.find_graphviz
AttributeError: module 'pydot_ng' has no attribute 'graphviz'
```

查看这个文件可以看到里面写着解决方案

```
pip install pydot-plus
try:
    # Try to load pydotplus
    import pydotplus as pydot
```

给这个描述文件起一个名字

```
name: "CIFAR10_quick"
```

定义每一层,名字，类型，top有两个一个是data 一个是label。数据层的top有两个

```
layer {
  name: "cifar"
  type: "Data"
  top: "data"
  top: "label"
```

![mark](http://myphoto.mtianyan.cn/blog/180408/kIFka5g7Ge.png?imageslim)

训练阶段调用这一层

```
  include {
    phase: TRAIN
  }
```

图片的均值文件所放的路径。

```
  transform_param {
    mean_file: "examples/cifar10/mean.binaryproto"
  }
```
图片的训练数据路径，batchsize的大小。训练数据存储的方式

```
  data_param {
    source: "examples/cifar10/cifar10_train_lmdb"
    batch_size: 100
    backend: LMDB
  }
```

接下来又定义了一层

```python
layer {
  name: "cifar"
  type: "Data"
  top: "data"
  top: "label"
  include {
    phase: TEST
  }
  transform_param {
    mean_file: "examples/cifar10/mean.binaryproto"
  }
  data_param {
    source: "examples/cifar10/cifar10_test_lmdb"
    batch_size: 100
    backend: LMDB
  }
}
```

跟上一层一模一样，只不过指定了阶段是TEST

测试集入口和训练集入口

卷积层

```
layer {
  name: "conv1"
  type: "Convolution"
  bottom: "data"
  top: "conv1"
  param {
    lr_mult: 1
  }
  param {
    lr_mult: 2
  }
  convolution_param {
    num_output: 32
    pad: 2
    kernel_size: 5
    stride: 1
    weight_filler {
      type: "gaussian"
      std: 0.0001
    }
    bias_filler {
      type: "constant"
    }
  }
}
```

起个名字，类型卷积层。bottom是data也就是上一层的名字。上一层的top对应下一层的bottom

top是conv1

![mark](http://myphoto.mtianyan.cn/blog/180408/hG2AEaBhHm.png?imageslim)

指定了一下w和b的学习率。以及卷积层的相关参数

- 总共有多少个output的feature map
- padding 过滤器大小 步长
- w的初始化方式: 高斯 方差
- b的初始化方式: 类型

池层

```
layer {
  name: "pool1"
  type: "Pooling"
  bottom: "conv1"
  top: "pool1"
  pooling_param {
    pool: MAX
    kernel_size: 3
    stride: 2
  }
}
```
类型池层。 bottom是conv1 top是pool1

![mark](http://myphoto.mtianyan.cn/blog/180408/a90klB5bIc.png?imageslim)

池层的参数: 取最大值的pool(或均值)

池层出来的结果过relu 激励函数。它的bottom和top是一个

![mark](http://myphoto.mtianyan.cn/blog/180408/mEDLed01dE.png?imageslim)

把数据过完relu之后又返回pool1.

定义第二个卷积层。relu层。池层。 卷积层。 全连接层

计算test上的准确率，计算loss值。使用的类型softmax

![mark](http://myphoto.mtianyan.cn/blog/180408/87hHJJ9hdE.png?imageslim)

caffe的官方网站上就介绍了这些data loss

数据layers 卷积层。可以看到input output

http://caffe.berkeleyvision.org/tutorial/

给你一个sample如何定义一个卷积。每个参数的意思。

Solver 定义你所使用的优化方式。

Loss 定义损失。

## keras是怎样做的

keras比theano 或者TensorFlow写代码简洁很多。

中文文档:

https://keras-cn.readthedocs.io/en/latest/

上手keras。源代码工程中。

7-3 keras-master/examples/cifar10_cnn.py

引入一些我们工程需要的包

```python
from __future__ import print_function
import keras
from keras.datasets import cifar10
from keras.preprocessing.image import ImageDataGenerator
from keras.models import Sequential
from keras.layers import Dense, Dropout, Activation, Flatten
from keras.layers import Conv2D, MaxPooling2D
import os
```

```python
batch_size = 32
num_classes = 10
epochs = 100
data_augmentation = True
num_predictions = 20
save_dir = os.path.join(os.getcwd(), 'saved_models')
model_name = 'keras_cifar10_trained_model.h5'
```

定义网络训练所需要的超参数

```python
# The data, split between train and test sets:
(x_train, y_train), (x_test, y_test) = cifar10.load_data()
```

读取数据分为训练数据，测试数据。

```python
model = Sequential()
model.add(Conv2D(32, (3, 3), padding='same',
                 input_shape=x_train.shape[1:]))
model.add(Activation('relu'))
model.add(Conv2D(32, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Conv2D(64, (3, 3), padding='same'))
model.add(Activation('relu'))
model.add(Conv2D(64, (3, 3)))
model.add(Activation('relu'))
model.add(MaxPooling2D(pool_size=(2, 2)))
model.add(Dropout(0.25))

model.add(Flatten())
model.add(Dense(512))
model.add(Activation('relu'))
model.add(Dropout(0.5))
model.add(Dense(num_classes))
model.add(Activation('softmax'))
```

我们要把网络结构层加入到sequential中

官方文档中有激励函数的说明。Flatten展开成1维的。

过最后一个全连接层的时候，总共有10类。

keras是一个高层次的封装。简单的网络结构。

```python
# initiate RMSprop optimizer
opt = keras.optimizers.rmsprop(lr=0.0001, decay=1e-6)
```

告诉我们的模型使用的是什么损失函数

```python
# Let's train the model using RMSprop
model.compile(loss='categorical_crossentropy',
              optimizer=opt,
              metrics=['accuracy'])
```

模型训练fit，以前我们是训练多少轮，每轮里面有多少个batch

```python
    model.fit(x_train, y_train,
              batch_size=batch_size,
              epochs=epochs,
              validation_data=(x_test, y_test),
              shuffle=True)
```

注明验证数据集是什么。是否需要打乱。

keras是基于theano和TensorFlow之上的。

更改后端。

keras代码简洁。caffe使用文件配置模型描述。

![mark](http://myphoto.mtianyan.cn/blog/180408/15hGh9dKJi.png?imageslim)

## 课程总结

神经元 激励函数 神经网络 神经网络中的梯度下降 前向传播 反向更新

随机梯度下降 简单版本神经网络 

提高神经网络的学习效率: 并行计算 梯度消失问题 归一化

参数初始化 正则化 学习率 Dropout 交叉熵

简单版本的前馈神经网络 增加代码提高学习效率

卷积神经网络 全连接神经网络对比 卷积核 卷积层(参数)

池化层(池化层参数) 典型的卷积神经网络 如何使用卷积神经网络做图片识别

softmax层 theano实现卷积神经网络

TensorFlow框架 线性回归 TensorBoard

手写数字识别 图片分类(cnn) 单gpu 多gpu

其他两个框架与TensorFlow对比。caffe keras

神经网络的算法和原理有初步的了解。

rnn神经网络原理 与 应用 游戏ai

卷积神经网络做图像风格变化。 人脸识别。

























