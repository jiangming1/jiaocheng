## TensorFlow以及TensorFlow的应用

支持深度学习的框架。torch caffe TensorFlow

### 简介

- 使用图(Graph)来表示计算任务
- 图中的节点被称为op(operation)
- 一个op获取0个或多个tensor，执行计算，产生0个或多个tensor
- 在会话(session)上下文中执行图
- 使用张量(tensor)来表示数据
- 通过变量(variable)来维护状态
- 使用feed和fetch可以为任意的操作赋值或从中获取数据。

### Tensor

![mark](http://myphoto.mtianyan.cn/blog/180404/iljId7AG0B.png?imageslim)

tensorflow代表数据的流动

### 具体例子

```python
# 定义常量op
a = tf.constant(2)
b = tf.constant(3)

# 使用seesion 启动默认图
with tf.Session() as sess:
    print("a=2, b=3")
    print("常量相加: %i" % sess.run(a + b))
    print("常量相乘: %i" % sess.run(a * b))
```

必须在session中执行才会运行。

```python
# 定义连个变量op占位符
a = tf.placeholder(tf.int16)
b = tf.placeholder(tf.int16)

# 定义2个op操作 加法 乘法
add = tf.add(a, b)
mul = tf.multiply(a, b)

with tf.Session() as sess:
    print("加法：%i" % sess.run(add, feed_dict={a: 2, b: 3}))
    print("乘法：%i" % sess.run(mul, feed_dict={a: 2, b: 3}))
```

### TensorFlow中矩阵运算

```python
# 1x2 矩阵常量op
matrix1 = tf.constant([[3., 3.]])

# 2x1 矩阵常量op
matrix2 = tf.constant([[2.], [2.]])

# 矩阵乘op
product = tf.matmul(matrix1, matrix2)

with tf.Session() as sess:
    result = sess.run(product)
    print(type(result))
    print(result)
```

运行结果:

```
<class 'numpy.ndarray'>
[[12.]]
```

## 如何选择好的框架

- 性能方面
- 社区活跃
- 语言

函数，计算模型模块。底层实现语言。

torch caffe TensorFlow

相同情况下torch运算相对快一些。

活跃度说明有更多人在使用这个项目，贡献代码，提交bug。找到解决方案。

不管什么语言实现的框架，现在都支持python的接口。

TensorFlow 社区活跃

![mark](http://myphoto.mtianyan.cn/blog/180404/DCCEigBFFH.png?imageslim)

TensorFlow在Stack Overflow上。语言上python2和3都可以。

TensorFlow对于多gpu，分布式支持好。充分利用多个工作站，多块显卡。

适合工程应用。

## 多环境与集群的支持

支持cpu 也支持gpu 设置可以运行在安卓

一颗cpu gpu的多个核心。一台机器的多gpu也支持

2016.4 Google发布了分布式的TensorFlow，能够支持在几百万台机器上进行并行的训练。

底层是由高性能的RPC库支持的。分布式的TensorFlow集群由一系列的任务组成。
这些任务的执行是在TensorFlow中进行图的计算。

每个任务会关联到一个TensorFlow的服务，这个服务就是用于创建TensorFlow会话，以及执行图计算的。

TensorFlow集群中也可以划分成一个或者多个的作业，每个作业包含一个或多个任务。
在一个TensorFlow集群中，通常一个任务是运行在一台机器上的，如果该机器有多块gpu，就可以把这个任务分配在该机器上运行多个任务。

由应用程序来控制，这多个任务在哪个gpu上运行。通常我们的深度学习模型是数据并行化的。

TensorFlow任务采用相同的训练模型在不同的小批量数据集上进行训练，然后在参数服务器上来更新模型的共享参数。

TensorFlow支持同步训练和异步训练两种训练模式。异步训练就是指在TensorFlow上每个节点的任务，它是作为独立的训练方式。不需要执行任何的操作。

![mark](http://myphoto.mtianyan.cn/blog/180404/imiC5102a1.png?imageslim)

如上图，每个任务在每个节点上都是单独进行训练的，不需要协调工作。

同步训练的方式；

![mark](http://myphoto.mtianyan.cn/blog/180404/h3K4FGmG3H.png?imageslim)

同步训练方式每个节点的任务需要读取共享数据，并且执行并行化的梯度计算。
然后将所有的共享数据进行合并。

具体过程: 初始化一个模型的矩阵w 把一个批次的训练样本平均的分成若干等份。

图中就是分成三等份。分别给三个节点进行计算，然后进行正向传播。

在正向传播之后会得到若干个不同的梯度。在反向更新时，需要把这几个的他p传送到一起。求出平均梯度值。用平均梯度值来更新。

理论上来讲，可以在多台服务器上的多个gpu上共同运行。

你的网络不是足够大的话，那么这种并行计算的方式就不太经济。

因为参数在服务器之间的网络交互是需要时间的。传输时间反倒很常。

三台提高反倒不是很明显。(数据量和网络复杂度不够高)

## TensorFlow线性回归实现

```python
import tensorflow as tf
import numpy
import matplotlib.pyplot as plt
from numpy import random
```

设置我们的学习率等参数

```python
# 训练参数
learning_rate = 0.01
training_epochs = 1000
display_step = 50 # 每隔50步打印一下训练数据
```

总共有多少个训练数据

```python
# 训练数据
train_X = numpy.asarray([3.3, 4.4, 5.5, 6.71, 6.93, 4.168, 9.779, 6.182, 7.59, 2.167,
                         7.042, 10.791, 5.313, 7.997, 5.654, 9.27, 3.1])
train_Y = numpy.asarray([1.7, 2.76, 2.09, 3.19, 1.694, 1.573, 3.366, 2.596, 2.53, 1.221,
                         2.827, 3.465, 1.65, 2.904, 2.42, 2.94, 1.3])
# 总共有多少个训练数据
n_samples = train_X.shape[0]
```

定义两个占位符

```python
# 定义两个变量op占位符,设置为浮点类型
X = tf.placeholder("float")
Y = tf.placeholder("float")
```

初始化w和b

```python
# 初始化模型里所有的w和b
W = tf.Variable(random.random(), name="weight")
b = tf.Variable(random.random(), name="bias")
```

构造线性模型 & 均方误差

```python
# 构造线性模型
pred = tf.add(tf.multiply(X, W), b)

# 均方误差
cost = tf.reduce_sum(tf.pow(pred - Y, 2)) / (2 * n_samples)
```

reduce_sum是对每一项累加求和。

```python
# 'x' is [[1, 1, 1]
#         [1, 1, 1]]
# tf.reduce_sum(x) ==> 6
# tf.reduce_sum(x, 0) ==> [2, 2, 2]
# tf.reduce_sum(x, 1) ==> [3, 3]
```

对于每一列进行相加，对于每一行进行相加。

- 使用梯度下降算法来优化我们的模型：

```python
# 梯度下降
optimizer = tf.train.GradientDescentOptimizer(learning_rate).minimize(cost)
```

传入学习率，目的是最小化cost

TensorFlow里面规定在使用variable创建变量之后，在程序运行之前必须初始化变量。

```python
# 初始化所有变量
init = tf.global_variables_initializer()
```

以上都只是描述了是怎么计算的，并没有真正的计算。

```python
# 使用session 启用默认图
with tf.Session() as sess:
    sess.run(init)

    # 训练开始
    for epoch in range(training_epochs):
        for (x, y) in zip(train_X, train_Y):
            sess.run(optimizer, feed_dict={X: train_X, Y: train_Y})
```

zip同时循环两个列表。TensorFlow会从optimizer主动顺腾摸瓜向上寻找的。

我们只需要运行最后的op, x和y的初始化feed_dict 填充数据

当前运行到了哪一轮，当前的损失值，当前的w当前的b。

```python
        # 每个一个epoch打印一下结果
        if (epoch + 1) % display_step == 0:
            c = sess.run(cost, feed_dict={X: train_X, Y: train_Y})
            print("Epoch:", '%04d' % (epoch + 1), "cost=", "{:.9f}".format(c), \
                  "W=", sess.run(W), "b=", sess.run(b))
```

训练完毕之后的损失值

```python
    print("Optimization Finished!")
    training_cost = sess.run(cost, feed_dict={X: train_X, Y: train_Y})
    print("Training cost=", training_cost, "W=", sess.run(W), "b=", sess.run(b), '\n')
```

### 画图。

```python
# 图画
    plt.plot(train_X, train_Y, 'ro', label='Original data')
    plt.plot(train_X, sess.run(W) * train_X + sess.run(b), label='Fitted line')
    plt.legend()
    plt.show()
    plt.savefig('linear_train.png')

    # 测试数据
    test_X = numpy.asarray([6.83, 4.668, 8.9, 7.91, 5.7, 8.7, 3.1, 2.1])
    test_Y = numpy.asarray([1.84, 2.273, 3.2, 2.831, 2.92, 3.24, 1.35, 1.03])

    print("Testing... (Mean square loss Comparison)")
    testing_cost = sess.run(
        tf.reduce_sum(tf.pow(pred - Y, 2)) / (2 * test_X.shape[0]),
        feed_dict={X: test_X, Y: test_Y})  # same function as cost above
    print("Testing cost=", testing_cost)
    print("Absolute mean square loss difference:", abs(
        training_cost - testing_cost))

    plt.plot(test_X, test_Y, 'bo', label='Testing data')
    plt.plot(train_X, sess.run(W) * train_X + sess.run(b), label='Fitted line')
    plt.legend()
    plt.show()
    plt.savefig('linear_test.png')
```

![mark](http://myphoto.mtianyan.cn/blog/180404/I1g57dc94i.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180404/0hCgH4GAga.png?imageslim)

解决使用 plt.savefig 保存图片时一片空白

```
savefig要在show之前。或者:

 fig = plt.gcf()
    plt.show()
    fig1.savefig('tessstttyyy.png', dpi=100)
```

gcf获取当前图片。


