## 图片移位

1. opencvAPI实现 
2. 算法原理
3. 源代码实现


```python
import cv2
import numpy as np

# 读图
img = cv2.imread('image0.jpg',1)
# 展示图
cv2.imshow('src',img)

# 获取图片信息
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]

# 设置一个偏移矩阵 两行三列数据
matShift = np.float32([[1,0,100],[0,1,200]])# 2*3
# 目标图片warpAffine，完成矩阵的映射。
# 参数1: 原图片 参数2: 移位矩阵 参数3: 当前图片的高宽
dst = cv2.warpAffine(img,matShift,(height,width)) # 1 data 2 matshift 3 info
# 移位 矩阵运算

cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/F329KDac7c.png?imageslim)

原始图片和移位后图片

## 图片位移的算法原理

- 分析api实现原理
- 分析像素移动实现原理

偏移矩阵

```
[1,0,100],[0,1,200]  拆分为两个矩阵 2行*2列 2行*1列

[[1,0],[0,1]]  2*2   A(矩阵的前两个元素)
[[100],[200]]  2*1   B(矩阵的第三个元素)
```

完成矩阵的运算

输入的xy定义为C

```
计算公式: A*C+B = [[1*x+0*y],[0*x+1*y]]+[[100],[200]]
= [[x+100],[y+200]]

效果为: 沿着x轴向右了100，沿着y轴向下了200

A（2,2） 乘以 C(2,1) 会变成一个(2,1) 然后(2,1)加(2,1)还是(2,1)

>得到新的x,y.描述当前移位之后的矩阵
```

结果: (10,20)->(110,220)

对于矩阵来讲，想当于每个x加上100，y加上200

## 通过源代码实现图片的位移

```python
import cv2
import numpy as np
# 读图
img = cv2.imread('image0.jpg',1)
# 展示图
cv2.imshow('src',img)
# 获取图片信息
imgInfo = img.shape
# 定义一个空模板，大小和原始图片一模一样大
dst = np.zeros(img.shape,np.uint8)

height = imgInfo[0]
width = imgInfo[1]

for i in range(0,height):
    # 宽右移100像素
    for j in range(0,width-100):
        # 目标图像i值不变，j值来自原图像加100
        dst[i,j+100]=img[i,j]
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/71660c4j44.png?imageslim)

## 图片镜像

完成图片的翻转

沿着红线，每个坐标的x不变，y轴沿着红线从图片最低端实现翻转

现在的y轴坐标等于 整个图片高度的两倍减去(y-1)

实现步骤:

1. 创建一个足够大的"画板" 宽度与原来保持一致，宽度是原来的两倍
2. 将一副图像分别从前向后，从后向前绘制
3. 绘制中心分割线

```python
import cv2
import numpy as np
img = cv2.imread('image0.jpg',1)
cv2.imshow('src',img)
imgInfo = img.shape
# 旧图片的info信息
height = imgInfo[0]
width = imgInfo[1]
deep = imgInfo[2]

# 新图片的info信息
newImgInfo = (height*2,width,deep)
# 创建空白模板
dst = np.zeros(newImgInfo,np.uint8)# uint8

for i in range(0,height):
    for j in range(0,width):
        # 绘制上半部分
        dst[i,j] = img[i,j]
        # 下半部分: x值不变, y值变为 = 2*h - y -1
        dst[height*2-i-1,j] = img[i,j]
# 添加分割线
for i in range(0,width):
    dst[height,i] = (0,0,255)# BGR
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/A52ili0BEE.png?imageslim)

## 利用移位公式进行图片缩放

定义一个两行三列的矩阵`[[A1 A2 B1],[A3 A4 B2]]`

拆分矩阵: 拆分成两个(2,2)和一个(2,1)矩阵

`[[A1 A2],[A3 A4]]  [[B1],[B2]]`

计算新的x和y值:

- `newX = A1*x + A2*y+ B1`
- `newY = A3*x + A4*y+ B2`


实现缩放功能
- `x->x*0.5 y->y*0.5`

将矩阵中的A1 A4设置为0.5, B1,B2设置为0 就等价于缩放xy 0.5

- `newX = 0.5*x`
- `newY = 0.5*x`

```python
import cv2
import numpy as np
img = cv2.imread('image0.jpg',1)
cv2.imshow('src',img)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]

# 缩放矩阵（2,3）
matScale = np.float32([[0.5,0,0],[0,0.5,0]])

# 仿射方法 原始数据，缩放矩阵，最终生成图片的宽高信息
dst = cv2.warpAffine(img,matScale,(int(width/2),int(height/2)))

cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/A1IKeC1jH2.png?imageslim)

## 图片的仿射变换

仿射变换就是将图像上每个像素点按照一定的规律映射到新的位置上

求解新的x和新的y的过程。和我们之前讲的矩阵变换非常的像。

但是仿射变换，有时候没有很明确的公式，而是1通过图片上三个点的拉伸映射出新的x值，新的y值。

![mark](http://myphoto.mtianyan.cn/blog/180326/IaFCBfBcA6.png?imageslim)

通过对于图片三个顶点的拉伸，实现仿射变换

为什么是三个点: 三个点可以唯一的确定一个平面

### 实现功能

```python
import cv2
import numpy as np
img = cv2.imread('image0.jpg',1)
cv2.imshow('src',img)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]

# src 3->dst 3  原图上(左上角 左下角 右上角) 转换到新图片上三个点
# 原图三个点
matSrc = np.float32([[0,0],[0,height-1],[width-1,0]])
# 新图上的位置
matDst = np.float32([[50,50],[300,height-200],[width-300,100]])
# 组合: 定义一个仿射变换矩阵。
# getAffineTransform获得仿射变换矩阵。参数一:原图的点，参数二: 新图片的点
matAffine = cv2.getAffineTransform(matSrc,matDst) # mat 1 src 2 dst

# 使用仿射变换 第一个参数: 图片信息 第二个: 仿射变换矩阵 第三个：宽高信息
dst = cv2.warpAffine(img,matAffine,(width,height))
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/1KlLIl2Kmm.png?imageslim)

## 图片旋转

前面我们分别通过源代码，opencvapi形式，矩阵加减乘除操作形式。

几何变换中的图片旋转

```python
import cv2
import numpy as np
# 图片载入
img = cv2.imread('image0.jpg',1)
# 展示
cv2.imshow('src',img)
# 图片信息获取
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]

# 定义一个旋转矩阵 2*3 
# getRotationMatrix2D 获取旋转矩阵
matRotate = cv2.getRotationMatrix2D((height*0.5,width*0.5),45,0.5)
# mat rotate 参数1 旋转center点 2 旋转的angle(角度) 3 scale(缩放系数)

# 为什么一个矩阵旋转的列子，要进行缩放0.5设置让它不超出边界

# 100*100 25 四个角会超出。
dst = cv2.warpAffine(img,matRotate,(height,width))
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/kJhF0H5D7g.png?imageslim)

讲过仿射变换，图片完成了45度的旋转

如果设置缩放系数为1，超出边界位置不显示。

## 总结

- 图片缩放
- 剪切
- 位移
- 镜像
- 旋转
- 仿射变换: 位移 旋转 缩放

- 如何使用opencv的api
- 算法原理
- 自己编写源代码实现

图片缩放:

1. 通过opencv api方式`cv2.resize()`
2. 图片缩放的最近邻域插值和双线性插值(算法原理)
3. 自己编写最近邻域插值源码

几何变换的实质就是矩阵运算，给矩阵不同的系数得到不同的功能。
