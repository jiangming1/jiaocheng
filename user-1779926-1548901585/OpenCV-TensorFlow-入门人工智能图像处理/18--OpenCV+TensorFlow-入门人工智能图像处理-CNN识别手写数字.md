## cnn卷积神经网络实现手写数字识别

```python
# 1 import 
import tensorflow as tf
import numpy as np
from tensorflow.examples.tutorials.mnist import input_data

# 2 load data
mnist = input_data.read_data_sets('MNIST_data',one_hot = True)

# 3 input
imageInput = tf.placeholder(tf.float32,[None,784]) # 28*28 
labeInput = tf.placeholder(tf.float32,[None,10]) # knn

# 4 data reshape 完成维度调整
# [None,784]->M*28*28*1  2D->4D  28*28 wh 1 channel 
imageInputReshape = tf.reshape(imageInput,[-1,28,28,1])

# 5 卷积 w0 : 卷积内核 5*5 out:32  in:1 
# 生成正态分布数据的维度，方差
w0 = tf.Variable(tf.truncated_normal([5,5,1,32],stddev = 0.1))
b0 = tf.Variable(tf.constant(0.1,shape=[32]))
```

## 卷积层 & 池化层实现

```python
# 6 # layer1：激励函数+卷积运算
# imageInputReshape : M*28*28*1  w0:5,5,1, 32  
layer1 = tf.nn.relu(tf.nn.conv2d(imageInputReshape,w0,strides=[1,1,1,1],padding='SAME')+b0)

# 输出大小M*28*28*32
# pool层 下采样 数据量减少很多M*28*28*32  对应除ksize => M*7*7*32
layer1_pool = tf.nn.max_pool(layer1,ksize=[1,4,4,1],strides=[1,4,4,1],padding='SAME')
# [1 2 3 4]->[4] 4来自于这四个数中最大值
```
padding参数决定卷积核是否可以停留边缘。

## 全连接层

激励函数 + 乘加运算 输出层

```python
# 7 layer2 out : 激励函数+乘加运算； 卷积: softmax(回归计算，计算输出值)（激励函数 + 乘加运算）
# [7*7*32,1024]
w1 = tf.Variable(tf.truncated_normal([7*7*32,1024],stddev=0.1))
b1 = tf.Variable(tf.constant(0.1,shape=[1024]))
# 维度转换，四维数据转换为2维的
h_reshape = tf.reshape(layer1_pool,[-1,7*7*32])# M*7*7*32 -> N*N1
```

```python
# [N*7*7*32]  [7*7*32,1024] = N*1024
h1 = tf.nn.relu(tf.matmul(h_reshape,w1)+b1)
```

softmax得到第二层的输出

## 7.1 softmax

```python
# 7.1 softMax
w2 = tf.Variable(tf.truncated_normal([1024,10],stddev=0.1))
b2 = tf.Variable(tf.constant(0.1,shape=[10]))
pred = tf.nn.softmax(tf.matmul(h1,w2)+b2)# N*1024  1024*10 = N*10
# 输出: N*10( 概率 )N1【0.1 0.2 0.4 0.1 0.2 。。。】
# label。        【0 0 0 0 1 0 0 0.。。】
loss0 = labeInput*tf.log(pred)
loss1 = 0

# 7.2 累加操作
for m in range(0,500):#  test 100
    for n in range(0,10):
        loss1 = loss1 - loss0[m,n]
loss = loss1/500
```

### 训练过程

```python
# 8 train
train = tf.train.GradientDescentOptimizer(0.01).minimize(loss)
```

## run代码

```python
# 9 run
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer())
    for i in range(100):
        images,labels = mnist.train.next_batch(500)
        sess.run(train,feed_dict={imageInput:images,labeInput:labels})
    ## 检测预测值 
        pred_test = sess.run(pred,feed_dict={imageInput:mnist.test.images,labeInput:labels})
        acc = tf.equal(tf.arg_max(pred_test,1),tf.arg_max(mnist.test.labels,1))
        acc_float = tf.reduce_mean(tf.cast(acc,tf.float32))
        acc_result = sess.run(acc_float,feed_dict={imageInput:mnist.test.images,labeInput:mnist.test.labels})
        print(acc_result)
```

## 总结cnn

输入层 隐层 输出层

```
#cnn : 1 卷积
# ABC 
# A: 激励函数+矩阵(乘法加法)
# A CNN :  pool（激励函数+矩阵(卷积 加法)）
# C：激励函数+矩阵 乘法加法（A-> B）
# C：激励函数+矩阵 乘法加法（A-> B） + softmax（矩阵 乘法加法）
# loss：tf.reduce_mean(tf.square(y-layer2))
# loss：code

```

## 本章总结

数字识别案例:

- 样本介绍
- KNN最近邻域
- CNN卷积神经网络

样本准备。好样本胜过复杂模型。

图片 28,28 onhot编码。 灰度图像

### knn本质

维度变化。本质:测试与训练，找到最接近的图片。

knn距离计算。维度变化。

找到k个最近距离，找到距离最近k个中最多的。
