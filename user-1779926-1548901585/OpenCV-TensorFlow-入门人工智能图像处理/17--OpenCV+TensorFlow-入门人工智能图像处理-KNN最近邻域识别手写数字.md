- 样本介绍
- KNN最近邻域
- CNN卷积神经网络

传统的分类算法和人工神经网络

网络的搭建，每一层向量维度的变化。

cnn: 目标判决

每一层的维度变化。

![mark](http://myphoto.mtianyan.cn/blog/180328/jE9g0jgl1F.png?imageslim)

```
# 1 重要
# 2 KNN CNN 2种方式
# 3 样本: MNIST_data
# 4 旧瓶装新酒 ：数字识别的不同
# 4.1 网络的搭建 4.2 每一层结构输入输出 4.3 先原理 后代码 
```

## 样本准备

http://yann.lecun.com/exdb/mnist/

![mark](http://myphoto.mtianyan.cn/blog/180328/dKCCB90h2G.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180328/j8HgbHb481.png?imageslim)

灰度图像，只有一个通道。

0表示白色。数字·代表有灰度值。

标签

![mark](http://myphoto.mtianyan.cn/blog/180328/EJ09DcfFD8.png?imageslim)

十位，one-hot编码描述五

## knn识别

一张待检测的图片与样本图片进行比较，找到k个与我们的图片相似的。

而我们取其中相似度最大的。

10个图片由8个都描述了1。我们就预测它是1

```python
import tensorflow as tf
import numpy as np
import random 
from tensorflow.examples.tutorials.mnist import input_data

# load data 2 one_hot : 1 0000 1 fileName 
mnist = input_data.read_data_sets('MNIST_data',one_hot=True)

# 属性设置
# 训练多少张图片
trainNum = 55000
# 测试图片张数
testNum = 10000
# 训练500张
trainSize = 500
# 测试5张
testSize = 5
k = 4
```

数据装载到mnist中

## 数据分解

```python
# 获取4个最接近的
k = 4

# data 分解 

# 1 trainSize   2 范围: 0-trainNum 3 是否可以重复: replace=False
trainIndex = np.random.choice(trainNum,trainSize,replace=False)
testIndex = np.random.choice(testNum,testSize,replace=False)

# 获取训练数据
trainData = mnist.train.images[trainIndex]# 训练图片
trainLabel = mnist.train.labels[trainIndex]# 训练标签
testData = mnist.test.images[testIndex] # 测试图片
testLabel = mnist.test.labels[testIndex] # 测试标签
```

数据分解，取其中一部分为训练测试数据

## 训练数据和测试数据维度

```python
# 28*28 = 784
print('trainData.shape=',trainData.shape)# 500*784 1 图片个数 2 784?
print('trainLabel.shape=',trainLabel.shape)# 500*10
print('testData.shape=',testData.shape)# 5*784
print('testLabel.shape=',testLabel.shape)# 5*10
print('testLabel=',testLabel)# 4 :testData [0]  3:testData[1] 6 
```

![mark](http://myphoto.mtianyan.cn/blog/180328/7hEDBeGag5.png?imageslim)

>行数表示图片的个数，784表示有784个像素。

10列是因为进行了onehot编码。

### 定义tf的输入输出

```python
# 定义tf的输入输出
# tf input  784->一个完整的image
trainDataInput = tf.placeholder(shape=[None,784],dtype=tf.float32)
trainLabelInput = tf.placeholder(shape=[None,10],dtype=tf.float32)
testDataInput = tf.placeholder(shape=[None,784],dtype=tf.float32)
testLabelInput = tf.placeholder(shape=[None,10],dtype=tf.float32)
```
## 使用knn的距离计算

扩展维度，进行减法求差值，并进行累加。

简单公式

```python
#knn distance 5*784. 经过转换: 5*1*784
# 目的，计算数据。
# 测试5张 训练500张 每张784维 (3D) 行数表示训练数据，列数表示测试数据 第三个维度表示测试之差
# 2500组合*784
f1 = tf.expand_dims(testDataInput,1) # 维度扩展
f2 = tf.subtract(trainDataInput,f1)  # 减法 784 sum(784)
# 计算差距(距离有可能是负数所以取绝对值) reduction_indices在第二个维度进行累加
f3 = tf.reduce_sum(tf.abs(f2),reduction_indices=2)# 完成数据累加 784 abs
# 5*500 某一个点表示距离之差
```

## 使用TensorFlow运行代码

完成knn中距离的计算

```python
with tf.Session() as sess:
    # f1 <- testData 5张图片
    p1 = sess.run(f1,feed_dict={testDataInput:testData[0:5]})
    print('p1=',p1.shape)# p1= (5, 1, 784)
    # 训练数据与测试数据的差值
    p2 = sess.run(f2,feed_dict={trainDataInput:trainData,testDataInput:testData[0:5]})
    print('p2=',p2.shape)# p2= (5, 500, 784) (1,100)点
    # p3五行五百列差值累加
    p3 = sess.run(f3,feed_dict={trainDataInput:trainData,testDataInput:testData[0:5]})
    print('p3=',p3.shape)# p3= (5, 500)
    print('p3[0,0]=',p3[0,0]) # 130.451 knn distance p3[0,0]= 155.812
```

## 使用knn 找到最近的k个照片

```python
f4 = tf.negative(f3)# 取反

p4 = sess.run(f4,feed_dict={trainDataInput:trainData,testDataInput:testData[0:5]})
    print('p4=',p4.shape)
    print('p4[0,0]',p4[0,0])
```

155 变成了-155

```python
f5,f6 = tf.nn.top_k(f4,k=4) # 选取f4 最大的四个值
```

f3选取的是距离最小的四个值。值的内容与下标。

## 检验f5 f6运行

```python
 p5,p6 = sess.run((f5,f6),feed_dict={trainDataInput:trainData,testDataInput:testData[0:5]})
    # p5= (5, 4) 每一张测试图片（5张）分别对应4张最近训练图片
    # p6= (5, 4)
    print('p5=',p5.shape)
    print('p6=',p6.shape)
    print('p5[0,0]',p5[0])
    print('p6[0,0]',p6[0])# p6 index
```

![mark](http://myphoto.mtianyan.cn/blog/180328/F2FaB499Ka.png?imageslim)


可以看出p6是我们的下标。

k个最近的图片 - 解析图片的内容 - 使用图片label标签

```python
# f3 最小的四个值
# f6 index->trainLabelInput
f7 = tf.gather(trainLabelInput,f6)

    p7 = sess.run(f7,feed_dict={trainDataInput:trainData,testDataInput:testData[0:5],trainLabelInput:trainLabel})
    print('p7=',p7.shape)# p7= (5, 4, 10)
    print('p7[]',p7)
```

![mark](http://myphoto.mtianyan.cn/blog/180328/f6H6gLH1Ch.png?imageslim)

## 获取最终的数字

测试图片与训练图片的距离，k个最近图片

```python
f8 = tf.reduce_sum(f7,reduction_indices=1)
# tf.argmax 选取在某一个维度上最大的值 index
f9 = tf.argmax(f8,dimension=1)
# f9 -> test5 image -> 5 num

 p8 = sess.run(f8,feed_dict={trainDataInput:trainData,testDataInput:testData[0:5],trainLabelInput:trainLabel})
    print('p8=',p8.shape)
    print('p8[]=',p8)
    
    p9 = sess.run(f9,feed_dict={trainDataInput:trainData,testDataInput:testData[0:5],trainLabelInput:trainLabel})
    print('p9=',p9.shape)
    print('p9[]=',p9)
```

## 验证数字对不对

```python
    p10 = np.argmax(testLabel[0:5],axis=1)
    print('p10[]=',p10)
j = 0
for i in range(0,5):
    if p10[i] == p9[i]:
        j = j+1
print('ac=',j*100/5)
```

![mark](http://myphoto.mtianyan.cn/blog/180328/jidEhgfFmf.png?imageslim)

## 总结

```
# 1 load Data  1.1 随机数 1.2 4组 训练 测试 （图片 和 标签）
# 2 knn test train distance 维度扩展 5*500 = 2500 784=28*28
# 3 knn k个最近的图片5 500 1-》500train （4）
# 4 k个最近的图片-> parse centent label
# 5 label -> 数字 p9 测试图片-》数据
# 6 检测概率统计
```

k值可以修改。测试训练数据可以进行修改。
