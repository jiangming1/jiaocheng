图片特效及线段文字的绘制

- 特效1: 灰度处理

![mark](http://myphoto.mtianyan.cn/blog/180326/2jEG916cCg.png?imageslim)

>完成彩色图片灰度化。彩色图片有三个颜色通道RGB 
灰度图片也是三通道的话，RGB值相等。
单通道的灰度图片的值，需要经过RGB值进行计算。

图中两个公式: 一个是取均值，一个是根据公式

- 特效2: 底板效果

![mark](http://myphoto.mtianyan.cn/blog/180326/DDG6AhIhbA.png?imageslim)

当前像素取反得到的。当然底板也分灰度底板和彩色底板。

![mark](http://myphoto.mtianyan.cn/blog/180326/cAkei5D54m.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180326/lj9a7aBfJf.png?imageslim)

- 特效3: 马赛克

![mark](http://myphoto.mtianyan.cn/blog/180326/6E9iKK5EL4.png?imageslim)


>思路将马赛克的地方划分成一个小方框。把方框中的像素用其中的某一个颜色进行取代

![mark](http://myphoto.mtianyan.cn/blog/180326/hF6Jb148IC.png?imageslim)

- 特效4: 毛玻璃特效

![mark](http://myphoto.mtianyan.cn/blog/180326/9DAH8Dk75J.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180326/mGGH8eh322.png?imageslim)

毛玻璃是把方框中的所有像素，每一个像素都用一个随机的来替代，替代完之后每一个像素仍然是不一样的。

- 特效5: 图像融合

![mark](http://myphoto.mtianyan.cn/blog/180326/5CCjLD4dDi.png?imageslim)

>实现两张图片相互融合成为一张图片

- 特效6: 图片蓝色

![mark](http://myphoto.mtianyan.cn/blog/180326/Jk53GB5gbf.png?imageslim)

颜色映射，给出一个RGB的颜色映射表或映射公式。

- 特效7: 边缘检测

![mark](http://myphoto.mtianyan.cn/blog/180326/Ahl3AiJDLJ.png?imageslim)

保留了整个图像的细节信息, 将图像进行二值化之后可以看到右边

右边图所有的细节被保留。光滑的地方都被删除掉了

- 特效8: 浮雕效果

![mark](http://myphoto.mtianyan.cn/blog/180326/l0bl2f1gbc.png?imageslim)

边缘检测的基础上多添加了一个底板。形成浮雕效果。

>讲解调优算法和思想

介绍一些图像滤镜的相关算法，掌握相机滤镜的相关知识。

如何实现这个滤镜，掌握这些原理就很重要了。

- 目的二: 三方面 api 原理 源码

### 形状绘制

![mark](http://myphoto.mtianyan.cn/blog/180326/Cj0JFi57BD.png?imageslim)

椭圆圆形正方形等任意形状的绘制。

以及文字与图片的绘制

![mark](http://myphoto.mtianyan.cn/blog/180326/3KkI3JFh6E.png?imageslim)

### 图片美化章节会介绍的

- 直方图

![mark](http://myphoto.mtianyan.cn/blog/180326/ljFEB7hicj.png?imageslim)

统计出行概率。

图像处理的基础。灰度特效平常使用的比较少。行人检测，人脸检测都要用到灰度特效。

为后面的复杂内容打下基础。

## 图片灰度处理

imread

方法1: imread() 指定颜色模式为0 灰度图

```python
#imread 
#方法1 imread 
import cv2
# 0设置为灰度图片
img0 = cv2.imread('image0.jpg',0)
# 1设置为彩色图片
img1 = cv2.imread('image0.jpg',1)

# 宽高，深度信息为1
print(img0.shape)

# 宽高，深度信息为3
print(img1.shape)

cv2.imshow('src',img0)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/HG766kbDl8.png?imageslim)

### 方法2 cvtColor方法

```python
import cv2
img = cv2.imread('image0.jpg',1)

# cvtColor方法: 图片，转换模式: BGR转换为灰度(颜色空间转换)
dst = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)# 颜色空间转换 1 data 2 BGR gray
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

## 灰度处理算法原理，源码编写

>问题来了: 灰色图像的Rgb值和彩色图像的rgb值到底有什么区别

灰色图像，如果也是三通道的话。R = G = B gray

我们可以将当前rgb 三者的均值当做灰度图像的值

方法3:

```python
import cv2
import numpy as np
img = cv2.imread('image0.jpg',1)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]
# RGB R=G=B = gray  (R+G+B)/3

# 生成空白模板，形状为高宽，深度3. 数据类型
dst = np.zeros((height,width,3),np.uint8)
for i in range(0,height):
    for j in range(0,width):
        # 读取原始图片的bgr
        (b,g,r) = img[i,j]
        # 因为我们读取出来的b g r 是uint8的，计算有可能溢出
        gray = (int(b)+int(g)+int(r))/3
        # 目标图片的每个点，等于当前rgb的均值
        dst[i,j] = np.uint8(gray)
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

>灰度图像三通道，RGB三者取均值形式

### 方法4  `gray = r*0.299+g*0.587+b*0.114`
​
>心理学灰度计算公式

```python
import cv2
import numpy as np
img = cv2.imread('image0.jpg',1)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]
dst = np.zeros((height,width,3),np.uint8)
for i in range(0,height):
    for j in range(0,width):
        (b,g,r) = img[i,j]
        b = int(b)
        g = int(g)
        r = int(r)
        gray = r*0.299+g*0.587+b*0.114
        dst[i,j] = np.uint8(gray)
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

## 如何快速简单计算灰度值。(算法优化)

1.  灰度是本章中最重要的知识点 
2.  它是很多图像识别的基础 
3.  图像算法的实时性(算法优化)

优化的基本原则:
    
>- 定点 快于 浮点
- 加减 快于 乘除法；移位操作比乘除法更要快些

> 心理学公式: `r*0.299+g*0.587+b*0.114`

优化这个公式。

我们乘以4来优化误差比较大。
我们可以通过乘以128 512 1024等更大的数进行优化。

- 浮点转整型，整型转移位运算

```python
import cv2
import numpy as np
img = cv2.imread('image0.jpg',1)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]
# RGB R=G=B = gray  (R+G+B)/3
dst = np.zeros((height,width,3),np.uint8)
for i in range(0,height):
    for j in range(0,width):
        (b,g,r) = img[i,j]
        b = int(b)
        g = int(g)
        r = int(r)
        # 浮点运算转为定点运算。有误差但是可以用。
        
        # gray = (r*1+g*2+b*1)/4
        
        # 乘以1全部省略掉，乘以2相当于放大两倍。
        # 左移一位扩大2
        # 右移两位除以4
        gray = (r+(g<<1)+b)>>2
        dst[i,j] = np.uint8(gray)
cv2.imshow('dst',dst)
cv2.waitKey(0)
```
