图像美化

- 案例1: 直方图
- 案例2: 直方图均衡化
- 案例3: 亮度增强
- 案例4: 磨皮美白
- 案例5: 图片滤波
- 案例6: 高斯滤波

颜色等级的直方图。

![mark](http://myphoto.mtianyan.cn/blog/180326/e933hm02A6.png?imageslim)

图片滤波

![mark](http://myphoto.mtianyan.cn/blog/180326/iEh5dc9bGf.png?imageslim)

这一章节所有的知识点都比较复杂，也就是难度更高一点。

适用性更强，使用案例常见。

直方图均衡化，磨皮美白算法。

![mark](http://myphoto.mtianyan.cn/blog/180326/ChLFgih219.png?imageslim)

Rgb三种颜色直方图，有横纵两个左边，横坐标描述0-255颜色等级(8位颜色)

纵坐标是每个颜色等级在整张图出现的概率(0-1)。

直方图均衡化？

>一幅图上RGb的直方图没有均匀分布，而是集中在了某一处。
这时可以用直方图均衡化，让原本聚合在一起的直方图尽可能的离散化。

有灰度图像的直方图和彩色图像的直方图两种。

![mark](http://myphoto.mtianyan.cn/blog/180326/cEJJLfk6c1.png?imageslim)

经过图片直方图均衡化，图片的亮度和清晰度给人的感觉就要好很多。

![mark](http://myphoto.mtianyan.cn/blog/180326/bHCgf60d8g.png?imageslim)

无论灰色还是彩色。

### 图像修复

因为某些原因图像造成了污损。可以采用这种方式进行修补

![mark](http://myphoto.mtianyan.cn/blog/180326/gfACdijElG.png?imageslim)

在图像修补时要准备一个蒙版的图片，左下角这张黑色图片。
这张图片要尽可能的描述受损区域。

### 亮度增强

最简单的图像美化

![mark](http://myphoto.mtianyan.cn/blog/180326/K6l0fFlkj3.png?imageslim)

当前的灰度值，全部加上一个灰度等级。或者将当前的灰度值乘以一个放大系数。

这个效果是不分差异的增加整个图片的亮度

### 磨皮美白

![mark](http://myphoto.mtianyan.cn/blog/180326/8d10eDf9GG.png?imageslim)

左图是未经过磨皮美白之前的，右图是经过美白后。这里采用一个双边过滤器实现。


### 图片滤波

- 高斯滤波，中值滤波，均值滤波

一维高斯:

![mark](http://myphoto.mtianyan.cn/blog/180326/fL7j820dib.png?imageslim)

高斯滤波将使用opencv api的形式

二维高斯:

![mark](http://myphoto.mtianyan.cn/blog/180326/4E05k4iF67.png?imageslim)

中值滤波使用源代码形式

### 图像卷积

![mark](http://myphoto.mtianyan.cn/blog/180326/8bB7gHIbdk.png?imageslim)

>实质是矩阵对应点相乘并求和。

## 彩色图片直方图

api调用

```python
import cv2
import numpy as np

# 定义一个图片直方图方法
def ImageHist(image,type):
    color = (255,255,255)
    windowName = 'Gray'
    if type == 31:
        color = (255,0,0)
        windowName = 'B Hist'
    elif type == 32:
        color = (0,255,0)
        windowName = 'G Hist'
    elif type == 33:
        color = (0,0,255)
        windowName = 'R Hist'
        
    # 调用opencv api完成hist方法
    # 参数1 image 注意加上[] 变成一个list 
    # 参数2 channels 通道 灰度直方图，下标[0] 
    # 参数3 mask None 蒙版
    # 参数4 histSize 绘制直方图个数 256  
    # 参数5 ranges 0-255
    hist = cv2.calcHist([image],[0],None,[256],[0.0,255.0])
    
    # 定义最大值，定义最小值，定义最小值下标，定义最大值下标。
    minV,maxV,minL,maxL = cv2.minMaxLoc(hist)
    # 创建画布
    histImg = np.zeros([256,256,3],np.uint8)
    # 高度遍历
    for h in range(256):
        # 归一化数据，获取每一个直方图的数据*256 除以最大值。值会被归到0-256之间
        intenNormal = int(hist[h]*256/maxV)
        # 1. 画布；2.起点 3. 256 - 归一化之后的值 
        cv2.line(histImg,(h,256),(h,256-intenNormal),color)
    cv2.imshow(windowName,histImg)
    return histImg
img = cv2.imread('image0.jpg',1)
# 将图片分层，RGB
channels = cv2.split(img)# RGB - R G B
for i in range(0,3):
    ImageHist(channels[i],31+i)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/6aI8fmjkfA.png?imageslim)

minMaxLoc求出最大值最小值，以便我们进行归一化。

## 直方图均衡化

### 灰度图片直方图均衡化

```python
#灰度 直方图均衡化
import cv2
import numpy as np
img = cv2.imread('image0.jpg',1)
# 灰度化
gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
cv2.imshow('src',gray)

# equalizeHist 灰度图直方图均匀化
dst = cv2.equalizeHist(gray)
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/468hje7mB1.png?imageslim)

### 彩色图片直方图均衡化

```python
# 彩色 直方图均衡化
import cv2
import numpy as np
img = cv2.imread('image0.jpg',1)
cv2.imshow('src',img)

# 对于单通道进行直方图均衡化
(b,g,r) = cv2.split(img)# 通道分解
bH = cv2.equalizeHist(b)
gH = cv2.equalizeHist(g)
rH = cv2.equalizeHist(r)

# merge合并三个通道
result = cv2.merge((bH,gH,rH))# 通道合成
cv2.imshow('dst',result)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/D08f49e1e6.png?imageslim)


>先分解通道，对于每个单一通道进行equalizeHist()之后，再将这些处理后的通道数据进行merge 合并。

### 实现YUV 直方图均衡化

```python
# YUV 直方图均衡化
import cv2
import numpy as np
img = cv2.imread('image0.jpg',1)

# 灰色图片转换为YCrCb(YUV)
imgYUV = cv2.cvtColor(img,cv2.COLOR_BGR2YCrCb)
cv2.imshow('src',img)

# 对图片进行分解，会得到一个三维的数组
channelYUV = cv2.split(imgYUV)
# 对第一维进行直方图均衡化
channelYUV[0] = cv2.equalizeHist(channelYUV[0])
# 再将这三个通道进行合并
channels = cv2.merge(channelYUV)

# 再将YUV转为BGR
result = cv2.cvtColor(channels,cv2.COLOR_YCrCb2BGR)
cv2.imshow('dst',result)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/glBf2D5dHL.png?imageslim)

## 图片修补

1. 生成坏图片

```python
import cv2 
import numpy as np
img = cv2.imread('image0.jpg',1)

# 画一条线从200画到300
for i in range(200,300):
    # 线宽三个像素
    img[i,200] = (255,255,255)
    img[i,200+1] = (255,255,255)
    img[i,200-1] = (255,255,255)
for i in range(150,250):
    img[250,i] = (255,255,255)
    img[250+1,i] = (255,255,255)
    img[250-1,i] = (255,255,255)
# 写入图片，破图
cv2.imwrite('damaged.jpg',img)
cv2.imshow('image',img)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/Ce74155Dbg.png?imageslim)

```python
# 1 坏图 2 array描述图片坏哪里了 3 inpaint
import cv2 
import numpy as np
# 读取破图
img = cv2.imread('damaged.jpg',1)
cv2.imshow('src',img)
# 获取图片信息
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]

# 定义蒙版
paint = np.zeros((height,width,1),np.uint8)

for i in range(200,300):
    # 改为单通道的
    paint[i,200] = 255
    paint[i,200+1] = 255
    paint[i,200-1] = 255
for i in range(150,250):
    paint[250,i] = 255
    paint[250+1,i] = 255
    paint[250-1,i] = 255
cv2.imshow('paint',paint)

# 1 src 2 mask
imgDst = cv2.inpaint(img,paint,3,cv2.INPAINT_TELEA)

cv2.imshow('image',imgDst)
cv2.waitKey(0)
```

>定义一个蒙版，并调用inpaint方法。

```
参数1 原图片，参数2 蒙版图片，参数3 inpaintRadius修复的半径
参数4 flag 哪种修复
```

![mark](http://myphoto.mtianyan.cn/blog/180326/bI2H3bI5C8.png?imageslim)
