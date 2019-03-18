图片的几何变换

## 图片的几何变换章节介绍

- 图片位移 & 图片缩放
- 图片剪切 & 图片镜像
- 图片仿射变换

Hog + Svm 小狮子识别

计算机视觉的基础: 裁剪样本为64,128 等比例缩放

![mark](http://myphoto.mtianyan.cn/blog/180325/J5mHK4A2CE.png?imageslim)

### 仿射变换: 位移，旋转，缩放

![mark](http://myphoto.mtianyan.cn/blog/180325/kK484Kidid.png?imageslim)

算法完成。梯形到正方向的投影。

复杂案例的基础。

### 图片缩放

![mark](http://myphoto.mtianyan.cn/blog/180325/0JcfgeigBk.png?imageslim)

缩放，会从原理，api，源码。三个角度阐述。

- 缩放就是改变图片的宽高。

根据变化大小，会有放大和缩小两种。

根据当前宽高比是否变化，有等比例缩放和非等比例

![mark](http://myphoto.mtianyan.cn/blog/180325/f2HbdB8FLB.png?imageslim)

还介绍了图片的最近邻域差值和双线插值

### 图片剪切

思路就是拿到图片数据集中的一部分数据将数据进行剪切

![mark](http://myphoto.mtianyan.cn/blog/180325/bgCfaEA0A3.png?imageslim)

>本质还是对于矩阵的操作

### 图片位移

![mark](http://myphoto.mtianyan.cn/blog/180325/E0LaFH62Dg.png?imageslim)

利用矩阵完成图片的映射变化

当前图片向右移动一百个像素。原图片的x,y值产生新的x,y

### 图片镜像

实现步骤:

1. 创建一个更大的画板
2. 将一幅图像分别从前往后，从后往前绘制
3. 绘制中心分割线

![mark](http://myphoto.mtianyan.cn/blog/180325/9dAka19IBK.png?imageslim)

矩阵倒序矩阵。

### 仿射变换: 位移 旋转 缩放

![mark](http://myphoto.mtianyan.cn/blog/180325/JLCjE6iaCh.png?imageslim)

旋转，移位，缩放。

基本都是对图片仿射变换矩阵的操作。

>本质是对图片数据的矩阵操作

## 图片缩放1

缩放: 改变图片的宽度与高度

可以放大也可以缩小

opencv中的api resize


改变图片尺寸的步骤:

1. 加载图片
2. 获取图片信息
3. 调用resize
4. 检查最终结果

```python
import cv2
img = cv2.imread('image0.jpg',1)
imgInfo = img.shape
print(imgInfo)

# 图片的高度，宽度，颜色组成方式3指一个像素点由三个颜色通道组成
height = imgInfo[0]
width = imgInfo[1]
mode = imgInfo[2]

# 1 放大 缩小
# 2 等比例 非 原来图片2:3 更改之后还是2:3 
dstHeight = int(height*0.5)
dstWidth = int(width*0.5)
# 原始图片的宽高乘以一个共同的系数，等比例缩放。
# 原始图片的宽高乘以一个不同的系数，非等比例缩放。

# opencv提供了四种方式: 最近邻域插值 双线性插值 像素关系重采样 立方插值
# 默认使用第二种: 双线性插值

# 第一个参数:我们要缩放的图片 第二个参数: 要缩放成的目标图片大小
dst = cv2.resize(img,(dstWidth,dstHeight))
cv2.imshow('image',dst)
cv2.waitKey(0)
```

## 讲解最近邻域差值法和双线性插值算法原理

python源代码实现

### 最近临域插值 双线性插值 原理

原始src `10*20` 目标dst `5*10`

目标图像上的每个点都是来自原图像的。

dst<<<src
>目标图像的(1,2) 对应 原图像的(2,4)

已知目标图片dst的x值1 对应 原始图片src x值2 newX

```
newX = x*(src 行/目标 行)[即缩放比例] newX = 1*（10/5） = 2
newY = y*(src 列/目标 列)[即缩放比例] newY = 2*（20/10）= 4
12.3 = 12

```

>对于一个非整数的像素坐标，我们取最近的点，就叫最近邻域差值法

### 双线性插值

![mark](http://myphoto.mtianyan.cn/blog/180326/i8723JhF34.png?imageslim)

四个角分别表示当前像素点的坐标。目标dst的大小(5,10)

使用目标插值法得到的原图像点未(15.2,22.3)实际上我们的原图像上是不存在这个点的，如何用周围的四个点表示这个点。

>最近邻域插值是取最近的点，而双线性是通过穿过的四个点来合成该点。

![mark](http://myphoto.mtianyan.cn/blog/180326/gicImld428.png?imageslim)

水平方向上穿过A1A2，竖直方向上穿过B1B2

计算出A1，A2，B1，B2.通过这四个点进行合成。

x轴表明的是我们图片的列，y轴表明的是我们图片的行

A1的横坐标15.2，表明它距离上面点20%，距离下面点80%

- A1 = 20% 上+80%下 A2
- B1 = 30% 左+70%右 B2

>方法1 最终点  = A1 30% + A2 70%
>方法2 最终点  = B1 20% + B2 80%

实质：矩阵运算 

## 图片缩放最近领域插值和双线性代码

1. 获取图片info(高,宽,颜色组成方式) 
2. 新建空白模版
3. 重新计算xy坐标

```python
import cv2
import numpy as np
# 图片名称，彩色图片
img = cv2.imread('image0.jpg',1)

imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]

# 目标图片高度宽度
dstHeight = int(height/2)
dstWidth = int(width/2)

# 创建一个空白模板。第一个参数(高，宽，颜色组成) 第二个参数 数据类型: uint8是0-255
dstImage = np.zeros((dstHeight,dstWidth,3),np.uint8) # 0-255

# 计算目标图片坐标
for i in range(0,dstHeight): # 行
    for j in range(0,dstWidth): # 列
        # 乘1.0转换为浮点型
        iNew = int(i*(height*1.0/dstHeight))
        jNew = int(j*(width*1.0/dstWidth))
        dstImage[i,j] = img[iNew,jNew]
cv2.imshow('dst',dstImage)
cv2.waitKey(0)
# 三个角度: 1. opencv API resize 2.算法原理 3.源码 
```

## 图片剪切

![mark](http://myphoto.mtianyan.cn/blog/180326/5jDHCD48aB.png?imageslim)

要想完成图片的剪切，我们得先搞清图片的坐标系。

x轴描述的是宽度信息列信息，y轴描述的是宽度信息行信息。

蓝绿色的蒙板就是我们想要剪切的内容

x,y位置信息。

- 从x轴100 -> 200
- 从y轴100 -> 300

```python
import cv2
img = cv2.imread('image0.jpg',1)
imgInfo = img.shape
# 利用切片实现剪切
dst = img[100:200,100:300]
cv2.imshow('image',dst)
cv2.waitKey(0)
```
![mark](http://myphoto.mtianyan.cn/blog/180326/Lh1caI24al.png?imageslim)
