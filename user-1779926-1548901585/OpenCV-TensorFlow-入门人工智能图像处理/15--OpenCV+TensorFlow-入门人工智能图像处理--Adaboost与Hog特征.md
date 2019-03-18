## Adaboost的训练步骤，以及训练好之后的xml文件结构

1. 初始化数据权值分布

苹果 苹果 苹果 香蕉

0.1 0.1 0.1 0.1 (第一次权值都是相等的)

2. 遍历阈值 p

计算出误差概率，找到最小的minP t 3. 计算出G1（x）4. 权值分布 update

更新权重分布: 0.2 0.2 0.2 0.7

我们使用的opencv可执行文件如exe

训练终止条件。

### opencv xml文件结构

![mark](http://myphoto.mtianyan.cn/blog/180327/17B3DccFLl.png?imageslim)

boost表明是一个boost分类器

特征类型使用haar模板。还有hog特征等

![mark](http://myphoto.mtianyan.cn/blog/180327/E25jlCe7gC.png?imageslim)

弱分类器最大个数，强分类器最大个数

之后就是我们的Adaboost分类器的所有参数。

解析的步骤opencv会帮我们做的

## haar特征+Adaboost人脸识别

```python
# 1 load xml 2 load jpg 3 haar gray 4 (检测)detect 5 draw(绘制方框)
import cv2
import numpy as np

# 加载load xml 参数1 file name
face_xml = cv2.CascadeClassifier('haarcascade_frontalface_default.xml')
eye_xml = cv2.CascadeClassifier('haarcascade_eye.xml')

# load jpg
img = cv2.imread('face.jpg')
cv2.imshow('src',img)

# haar gray
gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)

# detect faces 1 data 2 scale 3 5
# 检测人脸: 1 灰度图片数据 2 缩放系数 3 目标大小
faces = face_xml.detectMultiScale(gray,1.3,5)
# 人脸个数
print('face=',len(faces))

# draw方框
for (x,y,w,h) in faces:
    # 起始坐标。起始坐标。
    cv2.rectangle(img,(x,y),(x+w,y+h),(255,0,0),2)
    # 我们已经找到的人脸区域
    roi_face = gray[y:y+h,x:x+w]
    roi_color = img[y:y+h,x:x+w]
    # 参数1 gray
    eyes = eye_xml.detectMultiScale(roi_face)
    print('eye=',len(eyes))
    for (e_x,e_y,e_w,e_h) in eyes:
        cv2.rectangle(roi_color,(e_x,e_y),(e_x+e_w,e_y+e_h),(0,255,0),2)
cv2.imshow('dst',img)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180327/88mJG82DFa.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180327/EFhihL3c16.png?imageslim)

## hog + svm小狮子识别

特征 + 分类器

如何对于数据进行训练？

### 什么是svm支持向量机？

![mark](http://myphoto.mtianyan.cn/blog/180327/BCA6DDJiGG.png?imageslim)

svm的本质仍然是一个分类器，任意多个线分割能分割开。

svm本质: 寻求一个最优的超平面 完成分类

若干个直线。svm 核: 线性的

### 身高体重的分类。

使用svm找一个最优的超平面。完成男生女生身高体重分类问题，训练，预测。

```python
# 1 思想 分类器 
# 2 如何？ 寻求一个最优的超平面 分类
# 3 核：line
# 4 数据：样本 
# 5 训练  SVM_create  train predict
# svm本质 寻求一个最优的超平面 分类
# svm 核: line
# 身高体重 训练 预测 
import cv2
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

#1 准备data：男生身高数据，女生身高数据
# 女生
rand1 = np.array([[155,48],[159,50],[164,53],[168,56],[172,60]])
# 男生
rand2 = np.array([[152,53],[156,55],[160,56],[172,64],[176,65]])

# 2 label 0女 1男
label = np.array([[0],[0],[0],[0],[0],[1],[1],[1],[1],[1]])

# 3 data
data = np.vstack((rand1,rand2))
data = np.array(data,dtype='float32')

# svm 对于数据的要求: 所有的数据都要有label
# [155,48] -- 0 女生 [152,53] ---1  男生
# 监督学习 0 负样本 1 正样本

# 4 训练

# 创建svm
svm = cv2.ml.SVM_create() # ml  机器学习模块 SVM_create() 创建SVM
# 设置属性
svm.setType(cv2.ml.SVM_C_SVC) # svm type
svm.setKernel(cv2.ml.SVM_LINEAR) # line 线性分类器
svm.setC(0.01)
# 训练
result = svm.train(data,cv2.ml.ROW_SAMPLE,label)
# 预测
pt_data = np.vstack([[167,55],[162,57]]) # 0 女生 1男生
pt_data = np.array(pt_data,dtype='float32')
print(pt_data)
(par1,par2) = svm.predict(pt_data)
print(par2)
```

![mark](http://myphoto.mtianyan.cn/blog/180327/0g2Cd9I00j.png?imageslim)

学习svm时要理清其本质还是在解决分类问题。寻求一个最优超平面

svm 支持很多核: 我们主要用的是线性核

数据准备: 正负样本两种，正负样本不一定数目相等，但是必须要有一个唯一的label

训练: 创建svm 设置内核。使用train完成训练。

使用predict完成预测。

## hog特征

什么是hog特征

- 什么是hog？

>特征: 某个特定区域的像素进行某种特定运算得到的结果

haar特征是直接经过模板计算的结果: 我们称之为haar特征。

步骤:  

1. 模块划分 
2. 计算梯度和方向 模版 
3. 使用梯度和方向进行bin投影 
4. 计算每个模块的hog特征

### 模块划分

![mark](http://myphoto.mtianyan.cn/blog/180327/2gCimdc32d.png?imageslim)

整个蓝色方框是我们图片的大小，我们还有一个windows窗口。窗口我们也设置为当前大小。

![mark](http://myphoto.mtianyan.cn/blog/180327/IBcKHFGJLc.png?imageslim)

- image（ppt蓝色区域） windows窗体（蓝色） block模块（红色） cell （绿色）

每个概念都有一个size

关系: image > win > block >cell

滑动窗口概念

block-step  win-step 

>cell-bin

一般情况下: 窗体是特征计算的最顶层单元(一个窗口正常情况要能覆盖车的全部信息) 
必须包含一个目标obj的所有描述信息

```
1. win-size 50*100 20*50 推荐大小64*128
2. block(红)  小于win大小  win-size 宽高是block整数倍
当窗体大小为64*128时，通常的block为16*16
3. block-step block会在窗体中从上到下，从左到右进行遍历。
block每次滑动多少个像素。8*8

计算block-cout(滑动多少个block)= （（64-16）/8+1）*（（128-16）/8+1）= 
105 block

水平竖直两个方向滑动步长。

5. cell size 8*8 
6. block = ?cell 16*16 = 2*2 = > 4cell  cell1-cell4 不可滑动
7. bin?
```

## hog计算

车辆检测，可以根据windows窗体计算，判决这是车辆还是不是车辆。

可以计算出一个windows窗体下有多少个block。

block 下 cell 是不可滑动的。

### cell bin 梯度 运算

每个像素都有一个梯度: 大小(浮值) 方向

当前的bin 与方向有关

0-360度，以40度为一个bin进行划分，可以分出九个bin。

一个bin = 40度

cell和bin的关系。一个cell必须完整的包含360度的信息，多以就必须让cell包含九个bin

一个cell中对应9个bin

![mark](http://myphoto.mtianyan.cn/blog/180327/je88mgjB7l.png?imageslim)
