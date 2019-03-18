## 章节介绍

实现一个刷脸识别

输入一张图片，找到图片中的人脸，识别这个是哪一个人。

案例代码简单 人工网络层级低。 隐层只有一层。

综合: 样本收集 + 图像预处理 + TensorFlow神经网络

- python爬虫
- opencv预处理
- TensorFlow神经网络 CNN

每个item都有code，避免侵权。python 方法。

- 视频-> FFmpeg opencv

图片预处理，刷脸功能，提取人脸(haar + Adaboost) 样本

cnn卷积神经网络。 yale开源识别库。

## 爬虫获取样本

```python
# 'https://class.imooc.com/?c=ios&mc_marking=286b51b2a8e40915ea9023c821882e74&mc_channel=L5
# 爬虫 1 理解爬虫原理 2 实现一个的图片爬虫
# 1 http 2 html 3 正则 过滤条件 4 其它
# 知识点多
# 1 url 2 html src 3 img 4 img url下载图片
import urllib
# import urllib3
import os
from bs4 import BeautifulSoup
# load url
html = urllib.request.urlopen('https://class.imooc.com/?c=ios&mc_marking=286b51b2a8e40915ea9023c821882e74&mc_channel=L5').read()
# parse url data 1 html 2 'html.parser' 3 'utf-8'
soup = BeautifulSoup(html,'html.parser',from_encoding='utf-8')
# img
images = soup.findAll('img')
print(images)
imageName = 0 
for image in images:
    link = image.get('src')
    print('link=',link)
    link = 'http:'+link
    fileFormat = link[-3:]
    if fileFormat == 'png' or fileFormat == 'jpg':
        fileSavePath = './img/'+str(imageName)+'.jpg'
        imageName = imageName +1 
        urllib.request.urlretrieve(link,fileSavePath)
```

## FFmpeg初识

```
# python 爬虫 # ffmpeg
# 样本采集车 -》路段-〉视频
# opencv 视频 本质-》ffmpeg
# 是什么？软件
# 文件格式 编解码 剪切 录制 提取 裁剪 复用
# 信息 ffmpeg -i 1.mp4
# 视频分解图片 ffmpeg -i 1.mp4 image%d.jpg
```

mac下安装ffmpeg

```
brew install ffmpeg
```

```
ffmpeg -i 1.mp4
```

![mark](http://myphoto.mtianyan.cn/blog/180329/Lb67LCG5j6.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180329/bIG4I4Bdai.png?imageslim)

分解图片

```
ffmpeg -i 1.mp4 image%d.jpg
```

![mark](http://myphoto.mtianyan.cn/blog/180329/7C24c4hA2h.png?imageslim)

除过命令行，我们还可以通过api调用的方式。

## opencv预处理

将检测到的人脸裁剪下来。

```python
# 1 load xml 2 load jpg 3 haar gray 4 detect 5 draw
import cv2
import numpy as np
# load xml 1 file name
face_xml = cv2.CascadeClassifier('haarcascade_frontalface_default.xml')
eye_xml = cv2.CascadeClassifier('haarcascade_eye.xml')
# load jpg
img = cv2.imread('face.jpg')
cv2.imshow('src',img)
# haar gray
gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
# detect faces 1 data 2 scale 3 5
faces = face_xml.detectMultiScale(gray,1.3,5)
print('face=',len(faces))
# draw
index = 0
for (x,y,w,h) in faces:
    cv2.rectangle(img,(x,y),(x+w,y+h),(255,0,0),2)
    roi_face = gray[y:y+h,x:x+w]
    roi_color = img[y:y+h,x:x+w]
    fileName = str(index)+'.jpg'
    cv2.imwrite(fileName,roi_color)
    index = index + 1
    # 1 gray
    eyes = eye_xml.detectMultiScale(roi_face)
    print('eye=',len(eyes))
    for (e_x,e_y,e_w,e_h) in eyes:
        cv2.rectangle(roi_color,(e_x,e_y),(e_x+e_w,e_y+e_h),(0,255,0),2)
cv2.imshow('dst',img)
cv2.waitKey(0)
```

## 某个人脸识别

数据yale加载进入， 准备label标签 组成训练数据

卷积神经网络 检测人脸。

```python
# 1 数据yale 2 准备train label-》train 
# 3 cnn 4 检测
import tensorflow as tf
import numpy as np
import scipy.io as sio

f = open('Yale_64x64.mat','rb')
mdict = sio.loadmat(f)

# fea gnd; key value
train_data = mdict['fea']
train_label = mdict['gnd']

# 实现数据的无序排列
train_data = np.random.permutation(train_data)
train_label = np.random.permutation(train_label)

test_data = train_data[0:64]
test_label = train_label[0:64]

# 设置随机种子
np.random.seed(100)
test_data = np.random.permutation(test_data)
np.random.seed(100)
test_label = np.random.permutation(test_label)
```

加载数据，打乱随机标签和label值的划分。

```python
# train [0-9] [10*N] [15*N]  onehot [0 0 1 0 0 0 0 0 0 0] -> 2
# traindata进行处理 / 255完成归一化
train_data = train_data.reshape(train_data.shape[0],64,64,1).astype(np.float32)/255
train_labels_new = np.zeros((165,15))# 165 image 15人
for i in range(0,165):
    j = int(train_label[i,0])-1 # 1-15 转换为 0-14 
    train_labels_new[i,j] = 1

test_data_input = test_data.reshape(test_data.shape[0],64,64,1).astype(np.float32)/255
test_labels_input = np.zeros((64,15))# 165 image 15
for i in range(0,64):
    j = int(test_label[i,0])-1 # 1-15 0-14 
    test_labels_input[i,j] = 1
```

完成训练测试数据的维度处理。

## cnn神经网络

tf.layer更简单

```python
# cnn acc  tf.nn tf.layer
data_input = tf.placeholder(tf.float32,[None,64,64,1])
label_input = tf.placeholder(tf.float32,[None,15])

layer1 = tf.layers.conv2d(inputs=data_input,filters=32,kernel_size=2,strides=1,padding='SAME',activation=tf.nn.relu)
layer1_pool = tf.layers.max_pooling2d(layer1,pool_size=2,strides=2)
layer2 = tf.reshape(layer1_pool,[-1,32*32*32])
layer2_relu = tf.layers.dense(layer2,1024,tf.nn.relu)
output = tf.layers.dense(layer2_relu,15)

loss = tf.losses.softmax_cross_entropy(onehot_labels=label_input,logits=output)
train = tf.train.GradientDescentOptimizer(0.01).minimize(loss)
accuracy = tf.metrics.accuracy(labels=tf.argmax(label_input,axis=1),predictions=tf.argmax(output,axis=1))[1]
```

run

```python
# run acc
init = tf.group(tf.global_variables_initializer(),tf.local_variables_initializer())
with tf.Session() as sess:
    sess.run(init)
    for i in range(0,200):
        train_data_input = np.array(train_data)
        train_label_input = np.array(train_labels_new)
        sess.run([train,loss],feed_dict={data_input:train_data_input,label_input:train_label_input})
        acc = sess.run(accuracy,feed_dict={data_input:test_data_input,label_input:test_labels_input})
        print('acc:%.2f',acc)
```

![mark](http://myphoto.mtianyan.cn/blog/180329/mcf8j9LbBH.png?imageslim)

## 本章小结

从样本收集到图片预处理。

FFmpeg opencv预处理只要人脸。

cnn训练

## 课程总结

- 计算机视觉入门
- opencv + TensorFlow

计算机视觉之TensorFlow: 手写数字识别
计算机视觉之TensorFlow: 刷脸识别

主要案例

![mark](http://myphoto.mtianyan.cn/blog/180329/d8eaia8Bka.png?imageslim)

搭建使用两层神经网络

![mark](http://myphoto.mtianyan.cn/blog/180329/fK2c3aBK81.png?imageslim)

特征 + 分类器

hog特征(行人检测) + 分类器

刷脸识别: 样本收集 + 预处理 + 神经网络搭建

- 基础知识 & 实战案例 & 论文分析+ 案例实战 + 算法优化

物体识别，位置。

![mark](http://myphoto.mtianyan.cn/blog/180329/KDb07BKiaK.png?imageslim)

图片像素级别语义分析。

