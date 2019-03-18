## 编写DCGAN的判别模型

新建一个文件network.py 用来定义我们的生成器和判别器的模型

```python
# -*- coding: UTF-8 -*-

"""
DCGAN 深层卷积的生成对抗网络
"""

import keras as tf.keras
```

首先定义判别器模型

有一个卷积的判别器和一个反卷积的生成器。卷积的判别器判断我们生成的样本和真实的样本是否匹配。

反卷积的生成器用于从一些随机的噪声数据。矩阵一样的数据生成我们的假样本。

### 生成器(Generator) 基本原理

![mark](http://myphoto.mtianyan.cn/blog/180410/kdF92441fE.png?imageslim)

由简单到复杂地反向生成。

### 判别器(Discriminator) 基本原理

![mark](http://myphoto.mtianyan.cn/blog/180410/g4K29kBlcg.png?imageslim)

逐层深入 “抽丝剥茧” 地 “理解” 一张图片或其他事物

![mark](http://myphoto.mtianyan.cn/blog/180410/D8jmlHA9BB.png?imageslim)

我们之前使用过的卷积神经网络是这样的。我们判别器模型就类似于这个卷积神经网络的图。

输入一张图片一层一层抽丝剥茧地提取元素。我们先写判别器，因为它是一个正向的传播网络。而DCGAN里面的生成器却是一个有点类似反向的卷积神经网络的。

从噪声去生成最后的图片。

### 我们先定义判别器的模型

```python
# 定义判别器模型
def discriminator_model():
	# sequential，线性堆叠器。逐层添加网络
    model = tf.keras.models.Sequential()

    # 添加一层卷积: 
    model.add(tf.keras.layers.Conv2D(
        64,  # 64 个过滤器，输出的深度（depth）是 64
        (5, 5),  # 过滤器在二维的大小是（5 * 5）
        padding='same',  # same 表示输出的大小不变，因此需要在外围补零2圈
        input_shape=(64, 64, 3)  # 输入形状 [64, 64, 3]。3 表示 RGB 三原色
    ))
    model.add(tf.keras.layers.Activation("tanh"))  # 添加 Tanh 激活层
    model.add(tf.keras.layers.MaxPool2D(pool_size=(2, 2)))  # 池化层
    model.add(tf.keras.layers.Conv2D(128, (5, 5)))
    model.add(tf.keras.layers.Activation("tanh"))
    model.add(tf.keras.layers.MaxPool2D(pool_size=(2, 2)))
    model.add(tf.keras.layers.Conv2D(128, (5, 5)))
    model.add(tf.keras.layers.Activation("tanh"))
    model.add(tf.keras.layers.MaxPool2D(pool_size=(2, 2)))
    model.add(tf.keras.layers.Flatten())  # 扁平化
    model.add(tf.keras.layers.Dense(1024))  # 1024 个神经元的全连接层
    model.add(tf.keras.layers.Activation("tanh"))
    model.add(tf.keras.layers.Dense(1))  # 1 个神经元的全连接层
    model.add(tf.keras.layers.Activation("sigmoid"))  # 添加 Sigmoid 激活层

    return model
```

定义判别器之后再去定义生成器，就有点像把模型倒转一下的操作。sequential像一个汉堡包的模子。我们可以通过add方法去添加层。

keras.layer中的层都可以被添加进来。conv2d

https://www.tensorflow.org/versions/master/api_docs/python/tf/keras/layers/Conv2D

64个过滤器对于图像进行64次扫描。每扫描一次输出的深度就增加一层。

(5,5) 过滤器在二维平面的高和宽，扫描的区域

第三个参数是是否补padding。 补0，same表示输出的大小不变，因此需要在外围补零两圈。

输入形状 64 64 3 彩色图片

tanh激活层，池化层(maxpooling)，宽高各缩小2 (亚采样)

128个过滤器 过滤器大小。

全连接层输出维度1024
