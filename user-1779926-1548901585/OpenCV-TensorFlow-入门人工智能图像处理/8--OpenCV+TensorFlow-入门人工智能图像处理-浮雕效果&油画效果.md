## 浮雕效果

浮雕效果与边缘检测类似,也是计算梯度的一个过程

计算公式:

```
每一个新的像素值 newP = 相邻像素值之差 gray0-gray1 + 恒定值150
```

加上150是为了增强图片的浮雕灰度等级。

相邻像素相减，是为了突出灰度的突片。边缘特征。

### 代码实现

```python
import cv2
import numpy as np
img = cv2.imread('image0.jpg',1)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]
gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
# newP = gray0-gray1+150

# 定义灰度图片
dst = np.zeros((height,width,1),np.uint8)

# 遍历原图片的每一个点
for i in range(0,height):
    # 因为边界值时相邻像素会溢出
    for j in range(0,width-1):
        # 当前像素
        grayP0 = int(gray[i,j])
        # 下一个像素。我们宽度-1就是为了防止j+1越界
        grayP1 = int(gray[i,j+1])
        # 当前像素-下一个像素+150
        newP = grayP0-grayP1+150
        
        # 当前像素大于255.
        if newP > 255:
            newP = 255
        if newP < 0:
            newP = 0
        dst[i,j] = newP
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/aDKg5FgKA6.png?imageslim)

>150就是当前的灰度值。

边缘细节是我们的相邻像素之差

## 颜色风格

颜色的映射变换，实现各种颜色风格的效果。

这种颜色映射最简单的方法就是做一个很大的查找表。

根据原始图像的rgb值根据查找表查找一组新的rgb，用新的rgb代替原来像素值的过程。

如果不想做表，可以用一些简单的公式完成颜色映射。

利用公式的方法可能无法表示一些更为复杂的效果

>因为颜色效果越复杂，利用公式进行拟合的时候难度就越大。
甚至拟合的计算量远远超过查找表。

### 一个比较简单的颜色映射公式

蓝色性更强

```python
import cv2
import numpy as np
img = cv2.imread('image0.jpg',1)
cv2.imshow('src',img)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]
# rgb -> RGB new “蓝色增强”
# b = b*1.5
# g = g*1.3

# 目标彩色模板
dst = np.zeros((height,width,3),np.uint8)

# 遍历每个点
for i in range(0,height):
    for j in range(0,width):
        # 获取当前像素值值
        (b,g,r) = img[i,j]
        b = b*1.5
        g = g*1.3
        # 判断不要超过255
        if b>255:
            b = 255
        if g>255:
            g = 255
        # 为新图赋值
        dst[i,j]=(b,g,r)
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/Bdg3IeFId0.png?imageslim)

可以看到，当前的图片相比于原图片蓝色和绿色都增强了。

## 油画特效

油画特效实现步骤:

1. gray 灰度化
2. 将图片分割为若干个小方块。统计这些小方块中每个像素的灰度值。

```
7*7 10*10 的小方块。决定油画细腻程度
```
3. 将0-255，共256划分为几个等级，并将第二步的映射到等级

>如划分为4个段，每个段有64个等级。0-63 第一段；64-127第二段

举例: 这时有一个像素点.灰度值是10，那么他就在0-63这个点

4. 找到每个方块中灰度等级最多的所有像素. count 并求取这些像素的均值

5. 用统计出来的平均值，替代我们原来的像素值。

```python
import cv2
import numpy as np
img = cv2.imread('image00.jpg',1)
cv2.imshow('src',img)
imgInfo = img.shape
height = imgInfo[0]
width = imgInfo[1]

# 灰度化
gray = cv2.cvtColor(img,cv2.COLOR_BGR2GRAY)
# 新建空彩色图片模板
dst = np.zeros((height,width,3),np.uint8)

# 两层for循环遍历图片中的每个数据
for i in range(4,height-4):
    for j in range(4,width-4):
        # 我们想定义的灰度等级是8个，定义一个数组。
        # 装载8个像素的等级个数。
        array1 = np.zeros(8,np.uint8)
        
        # 要知道小方块的大小，(8,8)
        # 因为我们这里从-4开始，所以边界值要从4开始
        for m in range(-4,4):
            for n in range(-4,4):
                # 将灰度等级划分为8个段，每个段是32
                p1 = int(gray[i+m,j+n]/32)
                # p1是等级。这个p1的个数+1
                array1[p1] = array1[p1]+1
        currentMax = array1[0]
        # 记录是哪个段
        l = 0
        # 遍历这个列表，求最大值
        for k in range(0,8):
            if currentMax<array1[k]:
                currentMax = array1[k]
                l = k
        # 简化 均值
        for m in range(-4,4):
            for n in range(-4,4):
                # l是处于哪一个灰度段，32是它的灰度等级。
                # 小于等于下一个灰度段，保证处于范围之内
                if gray[i+m,j+n]>=(l*32) and gray[i+m,j+n]<=((l+1)*32):
                    (b,g,r) = img[i+m,j+n]
        dst[i,j] = (b,g,r)
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/IahGdL741f.png?imageslim)

右图油画之后的效果。

## 总结

![mark](http://myphoto.mtianyan.cn/blog/180326/B3fdBIm4E9.png?imageslim)

结合代码复习

灰度:

- imread + 0/1 灰度彩色

彩色图片是三维的。

- 使用opencv api的方法将灰度图片转换为彩色图片。

```
cv2.cvtColor方法
```

- 灰度图片实现的算法原理: RGB三者均值

>心理学公式。

- 算法优化: 浮点转定点，加减乘除尽可能转换为移位

乘以一个共同的值，比如4把浮点运算，转换为定点运算。

将乘法除法运算转换为移位运算

api 原理 源码实现 自己实现 算法优化

## 图像的绘制

![mark](http://myphoto.mtianyan.cn/blog/180326/jiAHKC3CD5.png?imageslim)

根据绘制的内容

把图像的绘制分为形状，文字，图片三种

![mark](http://myphoto.mtianyan.cn/blog/180326/E0le6LLCDk.png?imageslim)

形状就是线段，矩形，圆形，三角形，以及任意多边形

```python
import cv2
import numpy as np

# 图片宽高，颜色深度
newImageInfo = (500,500,3)
# 目标图片: 形状，数据类型
dst = np.zeros(newImageInfo,np.uint8)

# line方法
# 绘制线段 参数1 画在那张图上:dst 参数2 begin坐标 3 end坐标 4 color
cv2.line(dst,(100,100),(400,400),(0,0,255))
# 参数5 line w(宽度)
cv2.line(dst,(100,200),(400,200),(0,255,255),20)
# 参数6 line type(圆弧光滑了许多)
cv2.line(dst,(100,300),(400,300),(0,255,0),20,cv2.LINE_AA)

# 绘制三角形，三个边组成一个闭合图像。坐标首尾相接。
cv2.line(dst,(200,150),(50,250),(25,100,255))
cv2.line(dst,(50,250),(400,380),(25,100,255))
cv2.line(dst,(400,380),(200,150),(25,100,255))

cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/dEagKe40cK.png?imageslim)

## 矩形圆形绘制

```python
import cv2
import numpy as np
newImageInfo = (500,500,3)
dst = np.zeros(newImageInfo,np.uint8)


# 参数1 目标图片; 2 左上角; 3 右下角; 4 颜色 
# 参数5 内容是否fill(填充) if -1(填充)  if(>0) 该参数变为line宽度
cv2.rectangle(dst,(50,100),(200,300),(255,0,0),5)
# 参数2 圆心坐标center 参数3 r半径; 参数4 颜色 -1表示填充 >0表明线条宽度
cv2.circle(dst,(250,250),(50),(0,255,0),2)
# 参数2 椭圆心坐标center 
# 参数3 轴的长度(有两个轴，所以有两个值)
# 参数4 angle偏转角度 
# 参数5 begin角度 
# 参数6 end角度
# 参数7颜色，8内容是否填充
cv2.ellipse(dst,(256,256),(150,100),0,0,180,(255,255,0),-1)

# 任意多边形
points = np.array([[150,50],[140,140],[200,170],[250,250],[150,50]],np.int32)
# 五行两列
print(points.shape)
# reshape成5行一列，深度为2 维度转换
points = points.reshape((-1,1,2))
print(points.shape)
# 多边形绘制方法: 目标图片，2 点的集合，是否闭合，颜色
cv2.polylines(dst,[points],True,(0,255,255))
cv2.imshow('dst',dst)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/bAfIaC28C3.png?imageslim)

## 文字图片绘制

### 文字绘制

```python
import cv2 
import numpy as np
img = cv2.imread('image0.jpg',1)

# 定义字体
font = cv2.FONT_HERSHEY_SIMPLEX
# 方框: 左上，右下坐标。颜色，线宽
cv2.rectangle(img,(200,100),(500,400),(0,255,0),3)
# 1 dst 2 文字内容 3 坐标 4 字体 5 字体大小 6 color 7 粗细 8 line type
cv2.putText(img,'this is mtianyan text',(100,300),font,1,(200,100,255),2,cv2.LINE_AA)
cv2.imshow('src',img)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/DG7d04g9E2.png?imageslim)

### 图片绘制

```python
import cv2 
img = cv2.imread('image0.jpg',1)

# 定义贴图的大小
height = int(img.shape[0]*0.2)
width = int(img.shape[1]*0.2)

# 图片缩放
imgResize = cv2.resize(img,(width,height))

# 
for i in range(0,height):
    for j in range(0,width):
        # 写到图片的位置(200,350)
        img[i+200,j+350] = imgResize[i,j]

cv2.imshow('src',img)
cv2.waitKey(0)
```

![mark](http://myphoto.mtianyan.cn/blog/180326/40l858HEeA.png?imageslim)

>可以看到第二个图已经被贴在了向下200 向右350的位置。
