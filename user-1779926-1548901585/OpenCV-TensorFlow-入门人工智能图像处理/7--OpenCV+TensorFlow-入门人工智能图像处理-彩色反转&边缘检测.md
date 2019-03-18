### 彩色图像的颜色反转

```python
# RGB 255-R=newR
# 0-255 255-当前
import cv2
import numpy as np
img = cv2.imread('image0.jpg',1)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]

# 目标图片的深度为3，彩色图片
dst = np.zeros((height,width,3),np.uint8)
for i in range(0,height):
    for j in range(0,width):
        # 获取原图片的bgr信息
        (b,g,r) = img[i,j]
        dst[i,j] = (255-b,255-g,255-r)
cv2.imshow('dst',dst)
cv2.waitKey(0)
```
![mark](http://myphoto.mtianyan.cn/blog/180326/B5c1HBJDbh.png?imageslim)

不管是灰度图像的颜色反转，还是彩色颜色反转。都是255减去当前值

## 马赛克效果

理解马赛克效果中的关键指标

马赛克效果的窗体范围。如: 从100行开始到300行结束，列从100列开始到200列结束。

![mark](http://myphoto.mtianyan.cn/blog/180326/b5K743BmBJ.png?imageslim)

每个马赛克窗体里面有小方块。我们采用`10*10`的方形。

- 马赛克实现原理

小方形里的颜色，随机选取一个，替换掉`10*10`这100个像素点的颜色

![mark](http://myphoto.mtianyan.cn/blog/180326/DalkCiA5IC.png?imageslim)

### 代码实现马赛克

```python
import cv2
import numpy as np
img = cv2.imread('image0.jpg',1)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]

# 遍历马赛克矩形范围内的所有点
for m in range(100,300):
    for n in range(100,200):
        # 选择一个pixel点 -> 替换掉 10*10
        # 每隔10个宽高，我们选取一个像素
        if m%10 == 0 and n%10 == 0:
            for i in range(0,10):
                for j in range(0,10):
                    # 获取一个像素点
                    (b,g,r) = img[m,n]
                    # 回填颜色
                    img[i+m,j+n] = (b,g,r)
cv2.imshow('dst',img)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/C7K9K9Id8F.png?imageslim)

## 毛玻璃特效

毛玻璃是用某个像素点周围随机的一个点来替换掉当前像素点

![mark](http://myphoto.mtianyan.cn/blog/180326/2CeF0L0FKi.png?imageslim)

### 代码实现

```python
import cv2
import numpy as np
import random
img = cv2.imread('image0.jpg',1)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]

# 目标效果: 先行后列 ，行高度，列宽度
dst = np.zeros((height,width,3),np.uint8)
# 定义一个范围，水平竖直都有可能
mm = 8


for m in range(0,height-mm):
    for n in range(0,width-mm):
        # 选取随机数 0-8 之间数据。
        # 如果产生为8，当前已经位于最后一个像素点，会超出范围
        index = int(random.random()*8)# 0-8
        (b,g,r) = img[m+index,n+index]
        dst[m,n] = (b,g,r)
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

最右边和最下边的黑色边框是。因为我们用毛玻璃遍历时均减去了当前范围值。

最边边没有操作。最原始黑色

## 图片融合

每种图片乘以一个比例系数直接相加的方式。

![mark](http://myphoto.mtianyan.cn/blog/180326/EEmEe58Hb1.png?imageslim)

融合时两张图片的大小必须保持一致

```python
# 公式: dst  = src1*a + src2*(1-a)
import cv2
import numpy as np
img0 = cv2.imread('image0.jpg',1)
img1 = cv2.imread('image1.jpg',1)
imgInfo = img0.shape
height = imgInfo[0]
width = imgInfo[1]

# ROI 感兴趣范围区域(高宽均为原来的一半)
roiH = int(height/2)
roiW = int(width/2)

# 行对应我们的高度，列对应我们的宽度
img0ROI = img0[0:roiH,0:roiW]
img1ROI = img1[0:roiH,0:roiW]

# dst 目标图片
dst = np.zeros((roiH,roiW,3),np.uint8)
# addWeighted 两张图片的权重相加
# 第一张图片，权重，第二张图片，权重
dst = cv2.addWeighted(img0ROI,0.5,img1ROI,0.5,0) # add src1*a+src2*(1-a)
# 参数1 src1 参数2 a 参数3 src2 参数4 1-a
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/li9JfGhed5.png?imageslim)

## 边缘检测

边缘检测做出来的效果有点类似于素描的效果

将通过api的形式，源码形式。介绍边缘检测

- 边缘检测的实质就是图像的卷积运算。

实现canny边缘检测步骤

1. 将彩色图像转换为灰度图像
2. 高斯滤波，可以去除一些噪声干扰
3. 调用Opencv的canny方法实现边缘检测

```python
import cv2
import numpy as np
img = cv2.imread('image0.jpg',1)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]
cv2.imshow('src',img)

# canny: 1. gray 2. 高斯 3.canny

# 灰度化
gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
# 高斯滤波
# 参数一: 灰度图像数据 参数二: 模板大小 参数三: sigmaX
imgG = cv2.GaussianBlur(gray,(3,3),0)
# 参数一: 图片数据 参数二: 两个阈值
dst = cv2.Canny(img,50,50) # 图片卷积之后的值 大于 th 就认为是边缘点
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/gkfIaFehhI.png?imageslim)

这是我们通过opencv的api方式实现了边缘检测

## 边缘检测算法原理

使用sobel算子，源码的形式实现边缘检测

1. 搞清楚，sobel算子的算法原理

涉及的几个概念: 1. 算子模板 2. 图片卷积 3. 阈值判决

- sobel的算子模板有水平和竖直方向上两种。
```
[1  2  1        [ 1 0 -1
 0  0  0         2 0 -2
-1 -2 -1 ]        1 0 -1 ]
```
(3,3)的矩阵,第一个矩阵第一行(1,2,1) 第三行(-1,-2,-1)

第二个矩阵竖直方向第一列(1,2,1)，第三列为(-1,-2,-1)

- 图片的卷积 和 矩阵运算不是一回事

矩阵是行列式相乘，卷积是这个算子方框中的每一个元素对应相乘之后再求和。

举个例子:

原来的图片 `[1 2 3 4]` 每个点是一个像素

当前的计算模板是 `[a b c d]`

卷积之后的结果(对应元素相乘):

```
a*1+b*2+c*3+d*4 = dst
```

这个运算得到的结果，就是我们所说的梯度。梯度有水平和竖直两种情况。

使用arctan计算梯度的方向

使用`sqrt(a*a+b*b)` 计算梯度的浮值 = (f>th)?边缘:非边缘

>这里的a，我们的第一个算子称为竖直方向上的算子，竖直方向的算子与图片卷积得到的dst 就是a

- b就是水平方向上的算子与图片卷积得到的dst

### 源码实现边缘检测

```python
import cv2
import numpy as np
import math
img = cv2.imread('image0.jpg',1)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]
cv2.imshow('src',img)
        
gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
# 最终的边缘检测图片
dst = np.zeros((height,width,1),np.uint8)

# 遍历图片中的每一个点
# 我们的算子模板在最外围时会溢出
for i in range(0,height-2):
    for j in range(0,width-2):
        # 计算水平方向和竖直方向的梯度
        # 竖直方向的梯度
        # 取原图片 gray[i,j] gray[i,j+1] gray[i,j+2] gray[i+2,j] gray[i+2,j+1] gray[i+2,j+2] 六个点。乘以模板中对应元素
        # 其实是取九个点，因为第二行为0.不用写出来
        gy = gray[i,j]*1+gray[i,j+1]*2+gray[i,j+2]*1-gray[i+2,j]*1-gray[i+2,j+1]*2-gray[i+2,j+2]*1
        gx = gray[i,j]+gray[i+1,j]*2+gray[i+2,j]-gray[i,j+2]-gray[i+1,j+2]*2-gray[i+2,j+2]
        # 计算梯度
        grad = math.sqrt(gx*gx+gy*gy)
        # 大于阈值
        if grad>50:
            dst[i,j] = 255
        else:
            dst[i,j] = 0
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/g6gClcj02f.png?imageslim)

同过我们自己的源码实现，我们同样实现了边缘检测

