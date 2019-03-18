## 编写生成器模型

```python
# 定义生成器模型
# 从随机数来生成图片
def generator_model():
    model = tf.keras.models.Sequential()
    # 接收正态分布的随机数
    # 输入的维度是 100, 输出维度（神经元个数）是1024 的全连接层
    model.add(tf.keras.layers.Dense(input_dim=100, units=1024))
    model.add(tf.keras.layers.Activation("tanh"))
    model.add(tf.keras.layers.Dense(128 * 8 * 8))  # 8192 个神经元的全连接层
    model.add(tf.keras.layers.BatchNormalization())  # 批标准化
    model.add(tf.keras.layers.Activation("tanh"))
    model.add(tf.keras.layers.Reshape((8, 8, 128), input_shape=(128 * 8 * 8, )))  # 8 x 8 像素
    model.add(tf.keras.layers.UpSampling2D(size=(2, 2)))  # 16 x 16像素
    model.add(tf.keras.layers.Conv2D(128, (5, 5), padding="same"))
    model.add(tf.keras.layers.Activation("tanh"))
    model.add(tf.keras.layers.UpSampling2D(size=(2, 2)))  # 32 x 32像素
    model.add(tf.keras.layers.Conv2D(128, (5, 5), padding="same"))
    model.add(tf.keras.layers.Activation("tanh"))
    model.add(tf.keras.layers.UpSampling2D(size=(2, 2)))  # 64 x 64像素
    model.add(tf.keras.layers.Conv2D(3, (5, 5), padding="same"))
    model.add(tf.keras.layers.Activation("tanh"))

    return model
```

反卷积不是那么准确。

https://www.tensorflow.org/versions/master/api_docs/python/tf/keras/layers/BatchNormalization

将前一层激活函数标准化一下。对于激活函数应用一个转换，使得平均激活接近于0

应用保持平均激活接近0和激活标准偏差接近1的转换

经过这个步骤会变成8,8的图片

![mark](http://myphoto.mtianyan.cn/blog/180410/3DEKIEJIF8.png?imageslim)

UpSampling2D是池化的一个反操作，pooling = subsampling 亚采样 

upsampling 超采样。本来(8,8)像素的图片会变成16,16

再经过一个卷积层。激活层。向上采样。(32,32)

激活，之后再超采样。这里的卷积层 过滤器3 深度为3

## 编写训练神经网络的方法

我们的images目录中有许多(64,64)的图片。

构造一个 Sequential 对象，包含一个 生成器 和一个 判别器
>实现: 输入 -> 生成器 -> 判别器 -> 输出

https://medium.com/@awjuliani/generative-adversarial-networks-explained-with-a-classic-spongebob-squarepants-episode-54deab2fce39

![mark](http://myphoto.mtianyan.cn/blog/180410/gfK9JbB9EC.png?imageslim)

```python
def generator_containing_discriminator(generator, discriminator):
    model = tf.keras.models.Sequential()
    model.add(generator)
    discriminator.trainable = False  # 初始时 判别器 不可被训练
    model.add(discriminator)
    return model
```

定义一些超参数

```python
# Hyper parameter（超参数）
EPOCHS = 100
BATCH_SIZE = 128
LEARNING_RATE = 0.0002
BETA_1 = 0.5
```

每个训练轮中一次取batchsize大小。学习率。 
