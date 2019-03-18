## 磨皮美白

上节课的+固定值，我们还可以变为乘以一个系数。

```python
for i in range(0,height):
    for j in range(0,width):
        (b,g,r) = img[i,j]
        bb = int(b*1.3)+10
        gg = int(g*1.2)+15

        if bb>255:
            bb = 255
        if gg>255:
            gg = 255
```

只修改b和g的值，然后分别加上系数。

```python
# p = p+40
# 改进: p = p*1.2+40

# g+r P*piexl = new
import cv2
import numpy as np
img = cv2.imread('image0.jpg',1)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]
cv2.imshow('src',img)
dst = np.zeros((height,width,3),np.uint8)
for i in range(0,height):
    for j in range(0,width):
        (b,g,r) = img[i,j]
        bb = int(b*1.3)+10
        gg = int(g*1.2)+15

        if bb>255:
            bb = 255
        if gg>255:
            gg = 255

        dst[i,j] = (bb,gg,r)
cv2.imshow('dst',dst)
cv2.waitKey(0)
```
![mark](http://myphoto.mtianyan.cn/blog/180326/l10289Lf97.png?imageslim)

可以看到这次就没有蒙上白色

### 磨皮美白(双边滤波)

```python
# 双边滤波
import cv2
img = cv2.imread('1.jpg',1)
cv2.imshow('src',img)

# 双边滤波过滤器: 到时候讲解
dst = cv2.bilateralFilter(img,15,35,35)
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/8IKGFaC79k.png?imageslim)

可以看到已经有很大效果了

## 高斯均值滤波

高斯滤波使用opencv api形式。均值滤波使用源码

滤波与我们的边缘检测有点类似，就是用一个滤波核与我们的图像进行卷积运算。

高斯滤波，既然要进行滤波说明原来图片有问题。

```python
import cv2
import numpy as np
img = cv2.imread('image11.jpg',1)
cv2.imshow('src',img)
# 这些参数在讲解高斯核时介绍
dst = cv2.GaussianBlur(img,(5,5),1.5)
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/5jhm8kgEAc.png?imageslim)

可以看到原图片中有很多小点，这些点都是一些椒盐噪声。

去除这些点，使用高斯滤波

基本上没有模糊的点了，但是图片相比较于原图变模糊了。

### 均值滤波

定义一个`6*6`的模板,它里面的值全为1.

使用这个全为1的数据，乘以一个(6,6) 矩阵中所有的图像数据，乘完之后除以36得到均值，将这个均值替换到中心的这个元素。实现均值滤波。

```python
# 均值 6*6 1 。 * 【6*6】/36 = mean -》P
import cv2
import numpy as np
# 读取带噪声的图片
img = cv2.imread('image11.jpg',1)
cv2.imshow('src',img)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]

# 目标图片
dst = np.zeros((height,width,3),np.uint8)

# 遍历所有点。(6,6)
for i in range(3,height-3):
    for j in range(3,width-3):
        # 统计模板中6,6的矩阵元素和。
        sum_b = int(0)
        sum_g = int(0)
        sum_r = int(0)
        # (6,6)
        for m in range(-3,3): # -3 -2 -1 0 1 2 共6个值
            for n in range(-3,3):
                # 读取6,6这个小块中的每一个数据累加
                (b,g,r) = img[i+m,j+n]
                sum_b = sum_b+int(b)
                sum_g = sum_g+int(g)
                sum_r = sum_r+int(r)
        # bgr 获取均值
        b = np.uint8(sum_b/36)
        g = np.uint8(sum_g/36)
        r = np.uint8(sum_r/36)
        dst[i,j] = (b,g,r)
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

这跟毛玻璃搞得是一回事啊。

![mark](http://myphoto.mtianyan.cn/blog/180326/IDJmEEHcLA.png?imageslim)

## 中值滤波

```python
# 中值滤波 取中间值代替原来像素值过程 
# 3*3模板 九个像素进行排序，排序之后中间值替换原来像素值

import cv2
import numpy as np
# 读取噪声图片
img = cv2.imread('image11.jpg',1)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]
# 灰度化
img = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
cv2.imshow('src',img)
# 最终生成结果
dst = np.zeros((height,width,3),np.uint8)

# 数组，这九个元素。大小为9
collect = np.zeros(9,np.uint8)

# 两层for循环遍历每一个点
for i in range(1,height-1):
    for j in range(1,width-1):
        
        # 描述即将装取数据的下标
        k = 0
        # (3,3) 从-1开始到2
        for m in range(-1,2): # -1 0 1
            for n in range(-1,2):
                # 获取当前灰度值
                gray = img[i+m,j+n]
                # 下标就是我们的k
                collect[k] = gray
                k = k+1
        #  0 1 2 3 4 5 6 7 8
        #  1
        # 拿一个元素和其他元素进行比较
        # 如果这个元素最大或者最小都放到最后面
        # 完成大小排序
        for k in range(0,9):
            # 第一个元素
            p1 = collect[k]
            for t in range(k+1,9):
                # 如果p1小于比较值
                if p1<collect[t]:
                    # 设置中间值，完成数据交换
                    mid = collect[t]
                    collect[t] = p1
                    p1 = mid
        # 取中间值
        dst[i,j] = collect[4]
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/eCk637Fg8h.png?imageslim)

把旁边的去掉了，反倒本来没啥麻子的全是麻子

## 总结

图片美化

- 直方图 & 直方图均衡化 & 亮度增强
- 磨皮美白 & 图片滤波 & 高斯滤波

重点复习，直方图均匀化和图像滤波的相关知识

- 直方图: 本质统计每个像素灰度出现的概率 x: 0 - 255 p(0-1)
- 在原有基础上将概率离散化

1. 统计每个颜色出现的概率
2. 累计概率 最后的几个概率接近1
3. 根据累计概率，进行0-255映射表的创建

```
公式: newp = 255 * p
```

- 计算新的像素值。

YUV方式不改变颜色。

我们的滤波核就是通过高斯滤波函数来生成的。

滤波的本质是

![mark](http://myphoto.mtianyan.cn/blog/180326/7JKcFDA03B.png?imageslim)

用当前窗口中的像素点与我们的滤波核进行相乘。

实质； 矩阵对应点相乘并求和

左侧是图片，右侧是我们的高斯滤波核。

我们想计算中心点新的像素。就用(3,3)图像乘以(3,3)的滤波核

```
7*1
4*2
1*1
```
完成9个像素的对应相乘，完成之后再乘以1/16

为什么是16，因为我们滤波核中所有数加起来是16.平均。

滤波核的特点，越往中间值越大。

![mark](http://myphoto.mtianyan.cn/blog/180326/1mcH74IagA.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180326/9h16bIdB32.png?imageslim)

这也是二维高斯的特点。

双边滤波实际是由一个高斯核和r距离核组成。

api实现。

g + r 两者加权形成一个共同的核。

当前这个核p 乘以像素 与像素进行卷积可以得到新的像素。

像素乘以滤波核。像素与滤波核的乘法就是我们的卷积运算。






