### hog特征的维度

haar特征是一个具体的值，白-黑

我们的hog特征是一个向量，就会有一个维度的问题: 必须完全描述一个obj的所有信息

维度 = 每个windows窗体中有多少个block,cell个数 (105,4,9)=3780

### 梯度方向大小

必须以像素为单位，每个像素都有一个梯度，所有的像素共同构成hog特征

windows窗体下的所有像素。

运算量很大，hog的特征模板 -> haar类似

它的模板分两种: 水平和竖直

```
[1,0,-1] [[1],[0],[-1]]
水平方向上，左中右三个数分别与模板相乘
a = p1*1+p2*0+p3*(-1) = 相邻像素之差
b = 上下像素之差
f = 根号下（a方+b方）
angle = arctan（a/b）
```

## bin的投影

block中有36个bin，105个block。所以维度3780

hog特征维度的计算要基于windows窗体，一个窗体可以描述一个obj的所有信息。

hog特征也是描述一个对象完整的描述信息

梯度是根据像素来计算的，每一水平和竖直两个方向的模板。用像素和两个模板进行卷积运算，得到相邻像素之差和上下像素之差

使用根号下(a方+b方)得到浮值，使用arctan(a/b)得到角度

### bin的投影依赖于梯度

bin的范围是0-360 9bin bin 0-40

bin1 0-20 180-200 水平方向想左，还是水平方向向右，都认为在同一个bin

某一个像素ij f a=10

位于0-20度之间。位于二者之间的中心位置，认为它投影在bin1上。

a 190 度，也可以认为投影在bin1上。

25度。会被分解到bin1 和bin2上

```
f1 = f*f(夹角)
f2 = f*f(夹角) 夹角0-1之间
```

嵌入端移植。计算量太大。

## hog特征的最后一个问题

如何计算整体的hog特征。

cell的复用

3780维向量。 来源于 win(block cell bin)

一个cell 分为9份。 bin0-bin8

```
# cell0 cell3 bin0-bin8
# cell0: bin0 bin1 。。。bin8
# cell1: bin0 bin1 。。。bin8
# cell2: bin0 bin1 。。。bin8
# cell3: bin0 bin1 。。。bin8
```

假设我们有一个像素ij，投影在了cell0上。
计算出梯度bin0=f0

```
#ij cell0 bin0=《f0，
#i+1 j cell0 bin0 = f1
#ij。。。。
# sumbin0（f0+f1.。）= bin0
# 权重累加
#ij bin0 bin1 

cell复用

# block 4个cell
# 【0】【1】【2】【3】
# cell0 bin0-bin9 
# cellx0 cellx2 cellx4
# cellx0:ij-》bin bin+1 只对当前cell0起作用
# cellx2：ij -》 cell2 cell3 -》bin bin+1 bin bin+1
# cellx4：ij

# 【cell 9】【4cell】【105】 = 3780
```

### 判决

svm 线性分类器

训练之后也会得到一个3780维向量

hog特征 与 svm得到的向量 相乘 得到一个值

这个值 与我们的阈值进行比较；

大于就是目标 小于就是非目标。

## hog特征总结

block滑动不能能超出windows边缘。

windows包含所有信息。

0-20 180-200 都属于一个bin

中心位置就是一个bin 不然就要进行两个bin的合成运算

4个cell分为三组。

## Hog+SVM小狮子识别

准备样本 进行训练 test预测

```python
# 1 样本 2 训练 3 test 预测
# 1 样本
# 1.1 pos 正样本 包含所检测目标 neg 不包含obj 图片大小均为64*128
# 1.2 如何获取样本 1 网络 2 公司内部 3 自己收集
# 一个好的样本 远胜过一个 复杂的神经网络 （K w）（M）
# 1.1 网络公司 样本：1张图 1元  贵
# 1.2 网络 爬虫 自己爬 
# 1.3 公司： 很多年积累（mobileeye ADAS 99%） 红外图像 
# 1.4 自己收集 视频 100秒 30 = 3000
# 正样本：尽可能的多样  环境 干扰
# 820 pos neg 1931 1:2 1:3
# name
```

其中的比如视频分解图片，缩放大小,图片质量控制，图片裁剪等都在前面讲过了

## 训练 

正(820)负(1931)样本要在1:2 或1:3之间

名字按一定规则命名。

```python
# 训练
# 1 参数声明 2 hog 3 svm 4 computer hog 5 label 6 train 7 pred 8 draw
import cv2
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
# 1 par
# 正样本个数
PosNum = 820
# 负样本个数
NegNum = 1931
# 窗体大小
winSize = (64,128)
# block块大小
blockSize = (16,16) # 105 = （64-16) 是需要滑动的距离 / 步长 = block数 再+1
# (128-16)/8 +1 纵向需要滑动的block数
# 步长
blockStride = (8,8) # 4 cell
# cell大小
cellSize = (8,8)
# bin个数9个
nBin = 9 # 9 bin 3780个维度

# 2 hog create hog 1 win 2 block 3 blockStride 4 cell 5 bin
hog = cv2.HOGDescriptor(winSize,blockSize,blockStride,cellSize,nBin)

# 3 svm分类器创建
svm = cv2.ml.SVM_create()

# 4 computer hog
featureNum = int(((128-16)/8+1)*((64-16)/8+1)*4*9) # 3780
# 特征数组
featureArray = np.zeros(((PosNum+NegNum),featureNum),np.float32)
# 标签数组
labelArray = np.zeros(((PosNum+NegNum),1),np.int32)

# svm 监督学习 样本 标签 svm 学习使用 image hog特征(对于svm来说真正的样本)。  
for i in range(0,PosNum):
    fileName = 'pos/'+str(i+1)+'.jpg'
    img = cv2.imread(fileName)
    # 传入图片数据 和windows窗口步长
    hist = hog.compute(img,(8,8)) # 3780维
    for j in range(0,featureNum):
        featureArray[i,j] = hist[j]
    # 作用: featureArray hog [1,:] hog1 [2,:]hog2 
    labelArray[i,0] = 1
    # 正样本 label 1
    
for i in range(0,NegNum):
    fileName = 'neg/'+str(i+1)+'.jpg'
    img = cv2.imread(fileName)
    hist = hog.compute(img,(8,8))# 3780
    for j in range(0,featureNum):
        featureArray[i+PosNum,j] = hist[j]
    labelArray[i+PosNum,0] = -1
    # 负样本 label -1

# svm属性设置
svm.setType(cv2.ml.SVM_C_SVC)
svm.setKernel(cv2.ml.SVM_LINEAR)
svm.setC(0.01)

# 6 train
ret = svm.train(featureArray,cv2.ml.ROW_SAMPLE,labelArray)

# 7 myHog ：《-myDetect
# myDetect-《resultArray  rho
# myHog-》detectMultiScale

# 7 检测  核心：create Hog -》 myDetect—》array-》
# resultArray-》resultArray = -1*alphaArray*supportVArray
# rho-》svm-〉svm.train

# 一行一列
alpha = np.zeros((1),np.float32)
rho = svm.getDecisionFunction(0,alpha)
print(rho)
print(alpha)
alphaArray = np.zeros((1,1),np.float32)
# 支持向量
supportVArray = np.zeros((1,featureNum),np.float32)
resultArray = np.zeros((1,featureNum),np.float32)
alphaArray[0,0] = alpha
resultArray = -1*alphaArray*supportVArray

# detect
myDetect = np.zeros((3781),np.float32)
for i in range(0,3780):
    myDetect[i] = resultArray[0,i]
myDetect[3780] = rho[0]

# rho svm （判决）
# 构建hog
myHog = cv2.HOGDescriptor()
myHog.setSVMDetector(myDetect)

# load图片
imageSrc = cv2.imread('Test2.jpg',1)

# (8,8) win 
objs = myHog.detectMultiScale(imageSrc,0,(8,8),(32,32),1.05,2)

# xy wh 三维 最后一维
x = int(objs[0][0][0])
y = int(objs[0][0][1])
w = int(objs[0][0][2])
h = int(objs[0][0][3])

# 绘制展示
cv2.rectangle(imageSrc,(x,y),(x+w,y+h),(255,0,0),2)
cv2.imshow('dst',imageSrc)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180328/EkGcja7CFc.png?imageslim)

## 代码解析

从后往前

```python
# 绘制展示
# 图片，起始位置，终止位置，颜色，线条宽度
cv2.rectangle(imageSrc,(x,y),(x+w,y+h),(255,0,0),2)
# 图片展示
cv2.imshow('dst',imageSrc)
# 程序等待
cv2.waitKey(0)
```

```python
# load待检测图片
imageSrc = cv2.imread('Test2.jpg',1)

# detectMultiScale实现对目标检测 (8,8) win 
# 图片内容  (8,8)windows步长  1.05缩放系数 32窗体大小
# 核心是我们的myhog对象
objs = myHog.detectMultiScale(imageSrc,0,(8,8),(32,32),1.05,2)

# 包含x y 宽高
# xy wh 三维信息 参数在最后一维
x = int(objs[0][0][0])
y = int(objs[0][0][1])
w = int(objs[0][0][2])
h = int(objs[0][0][3])
```


myhog对象是我们的核心

我们的核心是创建我们的myhog myhog的创建由下面代码

```python
# detect
myDetect = np.zeros((3781),np.float32)
for i in range(0,3780):
    myDetect[i] = resultArray[0,i]
myDetect[3780] = rho[0]

# rho svm （判决）
# 构建hog
myHog = cv2.HOGDescriptor()
myHog.setSVMDetector(myDetect)
```

cv2.HOGDescriptor()方法创建myhog，通过setSVMDetector，将当前的detect属性传递进去。

myDetect 来源于哪里呢？

```python
myDetect = np.zeros((3781),np.float32)
```

实际上它就是一个数组。看看它的内容来自哪里

mydetect这个array的内容来源于: result array 和 rho

核心: result array 和 rho如何计算

```
rho = svm.getDecisionFunction(0,alpha)
```

rho是svm得到的hog的描述信息，会在最后判决累加的时候起作用。

而svm来源于哪里？

```python
ret = svm.train(featureArray,cv2.ml.ROW_SAMPLE,labelArray)
```

```
resultArray-》resultArray = -1*alphaArray*supportVArray
```

来自支持向量的个数，当成一个参数。

result_array是3780 rho一维

```
myHog ：《-myDetect myDetect-《 resultArray(公式)  rho
使用: myHog-》detectMultiScale
```

## 机器学习小结

![mark](http://myphoto.mtianyan.cn/blog/180328/gjcgjihIlj.png?imageslim)

准备样本，特征:haar特征，hog特征。分类器: Adaboost分类器

Hog+svm小狮子检测 haar+Adaboost 人脸检测

样本准备: 买 收集爬虫 视频分解

图片命名规则

haar特征用于人脸识别。积分图。

强分类器(判决) 弱分类器(产生强的输入) 节点

hog特征，模块划分。图片 窗体 block cell bin bin中的角度

hog特征维度 梯度计算: 相邻像素之差 bin的投影，cell的复用。

目标检测。

svm支持向量机身高体重的实现。

hog计算特征。myhog。
