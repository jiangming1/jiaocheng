## 编写训练的python文件

```python
# -*- coding: UTF-8 -*-

"""
训练 DCGAN
"""

import os
import glob
import numpy as np
from scipy import misc
import keras as tf.keras

from network import *


def train():

if __name__ == "__main__":
    train()
```

获取训练数据

```python
# 获取训练数据
    data = []
    for image in glob.glob("images/*"):
    	# 读取图片，返回一个数组对象
        image_data = misc.imread(image)  # imread 利用 PIL 来读取图片数据
        data.append(image_data)
    input_data = np.array(data)
```

将数据进行标准化

```python
 # 将数据标准化成 [-1, 1] 的取值, 这也是 Tanh 激活函数的输出范围
    input_data = (input_data.astype(np.float32) - 127.5) / 127.5
```

tanh的取值范围是-1 到 1

像素值最大255 减去一半127.5 再除以 127.5 被限制到-1到1之间。


构造生成器和判别器

```python
    # 构造 生成器 和 判别器
    g = generator_model()
    d = discriminator_model()
```

构建生成器和判别器组成的网络模型

```python
# 构建 生成器 和 判别器 组成的网络模型
    d_on_g = generator_containing_discriminator(g, d)
```

里面的参数传入g和d

优化器使用Adam optimizers

```python
 # 优化器用 Adam Optimizer
    g_optimizer = tf.keras.optimizers.Adam(lr=LEARNING_RATE, beta_1=BETA_1)
    d_optimizer = tf.keras.optimizers.Adam(lr=LEARNING_RATE, beta_1=BETA_1)
```

学习率是我们之前定义的学习率。beta_1 参数。

使用compile方法对于神经网络进行配置 生成器 和 判别器

```python
# 配置 生成器 和 判别器
    g.compile(loss="binary_crossentropy", optimizer=g_optimizer)
    d_on_g.compile(loss="binary_crossentropy", optimizer=g_optimizer)
    d.trainable = True
    d.compile(loss="binary_crossentropy", optimizer=d_optimizer)
```

交叉熵损失函数。固定住判别器去优化生成器。相反固定一方优化另一方。

开始训练

```python
# 开始训练
    for epoch in range(EPOCHS):
    	# 每经过一个batchsize大小训练一下
        for index in range(int(input_data.shape[0] / BATCH_SIZE)):
        	# 数据切片
            input_batch = input_data[index * BATCH_SIZE : (index + 1) * BATCH_SIZE]

            # 连续型均匀分布的随机数据（噪声）
            random_data = np.random.uniform(-1, 1, size=(BATCH_SIZE, 100))
            # 生成器 生成的图片数据
            generated_images = g.predict(random_data, verbose=0)
            # 首尾相连，输入自身以及产生的图片
            input_batch = np.concatenate((input_batch, generated_images))
            # 输出的数据要么是0 要么是 1。1就是通过检测，跟真实图片一致。
            output_batch = [1] * BATCH_SIZE + [0] * BATCH_SIZE

            # 训练 判别器，让它具备识别不合格生成图片的能力
            d_loss = d.train_on_batch(input_batch, output_batch)

            # 当训练 生成器 时，让 判别器 不可被训练
            d.trainable = False

            # 重新生成随机数据。很关键
            random_data = np.random.uniform(-1, 1, size=(BATCH_SIZE, 100))

            # 训练 生成器，并通过不可被训练的 判别器 去判别
            g_loss = d_on_g.train_on_batch(random_data, [1] * BATCH_SIZE)

            # 恢复 判别器 可被训练
            d.trainable = True

            # 打印损失
            print("Epoch {}, 第 {} 步, 生成器的损失: {:.3f}, 判别器的损失: {:.3f}".format(epoch, index, g_loss, d_loss))
```

保存生成器和判别器的参数

```python

        # 保存 生成器 和 判别器 的参数
        # 大家也可以设置保存时名称不同（比如后接 epoch 的数字），参数文件就不会被覆盖了
        if epoch % 10 == 9:
            g.save_weights("generator_weight", True)
            d.save_weights("discriminator_weight", True)
```

当我们训练完成，会生成一个generator_weight文件

它是一个h5py的文件。

```
pip install h5py
```

## 编写神经网络生成图片的方法

```python
# -*- coding: UTF-8 -*-

"""
用 DCGAN 的生成器模型 和 训练得到的生成器参数文件 来生成图片
"""

import numpy as np
from PIL import Image
import keras as tf.keras

from network import *


def generate():
    # 构造生成器
    g = generator_model()

    # 配置 生成器
    g.compile(loss="binary_crossentropy", optimizer=tf.keras.optimizers.Adam(lr=LEARNING_RATE, beta_1=BETA_1))

    # 加载训练好的 生成器 参数
    g.load_weights("generator_weight")

    # 连续型均匀分布的随机数据（噪声）
    random_data = np.random.uniform(-1, 1, size=(BATCH_SIZE, 100))

    # 用随机数据作为输入，生成器 生成图片数据
    images = g.predict(random_data, verbose=1)

    # 用生成的图片数据生成 PNG 图片
    for i in range(BATCH_SIZE):
    	# 将被限制到-1到1之间的数据进行还原
        image = images[i] * 127.5 + 127.5
        Image.fromarray(image.astype(np.uint8)).save("image-%s.png" % i)


if __name__ == "__main__":
    generate()

```

## 代码完成与测试模型

一个错误的个人使用，因为我的TensorFlow版本较老。keras并没有被集成进来。

我以为可以

```python
import keras as tf.keras
```

但是测试失败了，直接把全部的tf.keras全部替换为keras

新的风暴

```
throws OOM when allocating tensor with shape
```

又是穷人才会遇到的问题

将batch_size大小从128改为64可以正常训练

![mark](http://myphoto.mtianyan.cn/blog/180410/FBLfbJDed2.png?imageslim)

然后使用generator.py生成图片。

```python
 # 配置 生成器 和 判别器
    g.compile(loss="binary_crossentropy", optimizer=g_optimizer)
    d_on_g.compile(loss="binary_crossentropy", optimizer=g_optimizer)
    d.trainable = True
    d.compile(loss="binary_crossentropy", optimizer=d_optimizer)
```

让判别器先可以训练，再设置。我们训练生成器的随机数据不应该和训练整个dong
的一样，不然不够随机化。

基本都得训练好几个小时。
