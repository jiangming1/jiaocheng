我们已经学习了图像的特效，几何变换，图形绘制。

### 机器学习是什么？

机器学习的方式来达成某种功能

使用场景: 人脸检测，车辆识别，安防监控

深度学习: 

机器学习 = 训练样本 + 特征 + 分类器

深度学习 = 海量的训练样本 + 人工神经网络

对于机器学习来说，需要一个明确的特征进行提取。对于深度学习样本需求更多的数量。

区分ios和安卓系统？

>机器学习可以进行特征提取，ios 三个字母，Android 七个字母。这就是一种特征上的差别

深度学习: 神经网络训练自己抽取特征，有可能把更多特征抽取出来。

>我们并不知道深度学习的神经网络抽取了什么样的特征，进行最终判别。

而机器学习中特征非常的明显。

- Haar特征(人脸检测) & Hog特征是我们做行人检测，车辆检测，物体检测。

单词的个数 num判决 分类器

Adaboost分类器:

![mark](http://myphoto.mtianyan.cn/blog/180327/1fLiLbDAEh.png?imageslim)

SVM支持向量机:

![mark](http://myphoto.mtianyan.cn/blog/180327/jabjGGg485.png?imageslim)

完成特征提取之后如何进行判决

1. 样本准备
2. 获取机器学习的特征
3. 用分类器对于机器学习的特征进行分类。

- 视频与图片的分解合成(样本收集)

Haar特征:

![mark](http://myphoto.mtianyan.cn/blog/180327/dh78A5fFHH.png?imageslim)

- Haar特征主要用于人脸识别上，它在人脸识别上的概率非常的高。

>haar特征由一系列的模板组成，有基础类型，核心类型和所有类型。

以x2的haar特征为例:

![mark](http://myphoto.mtianyan.cn/blog/180327/7D8J5E3KLl.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180327/G9kk5a4FmE.png?imageslim)

>将这个模板放到我们图片上的任意一个位置。

- 当前的特征 = 白色部分 - 黑色部分
- 派生出的两个公式

![mark](http://myphoto.mtianyan.cn/blog/180327/hjll0f8iiD.png?imageslim)

四个指针相加减。

我们并不知道人脸位于我们图像的哪个位置，所以我们需要使用haar特征进行遍历

![mark](http://myphoto.mtianyan.cn/blog/180327/e3A43c6l4e.png?imageslim)

从上到下，从左到右遍历整个图像。

###  积分图进行特征的快速计算。

![mark](http://myphoto.mtianyan.cn/blog/180327/8IHKCDdJ2l.png?imageslim)

### Adaboost分类器

![mark](http://myphoto.mtianyan.cn/blog/180327/6EK45lhGJd.png?imageslim)

- 强分类器 & 弱分类器 & 若干特征节点

- 样本 & 特征 & 分类器

>haar特征 + Adaboost 实现人脸识别

haar 由一系列模板组成，adboost分类器分为三级(强分类器，弱分类器，node节点)

Hog特征 + svm小狮子识别

![mark](http://myphoto.mtianyan.cn/blog/180327/884cB0flBj.png?imageslim)

### Hog特征计算

![mark](http://myphoto.mtianyan.cn/blog/180327/5GgKH4HC6G.png?imageslim)

一系列的窗体: 蓝色矩形框，红色block模块，绿色的cell模块。

我们要计算cell模块中每一个像素的梯度。我们还要计算它的浮值和方向。
根据浮值与方向进行直方图的统计最后得到hog特征。

hog在进行梯度计算的时候同样有一个模板，加上SVM，实现小狮子识别

最后预测或检验特征是否有效。

## 视频分解图片

如何使用视频分解图片。

1. 加载视频 2. info信息 3. parse方法解析视频 4. imshow展示
5. 保存imread

```python
# 视频分解图片
# 1 load 2 info 3 parse 4 imshow imwrite
import cv2
# 可以通过摄像头，也可以通过本地文件
cap = cv2.VideoCapture("1.mp4") # 获取一个视频打开cap 参数1 file name(可选路径)
isOpened = cap.isOpened # 判断是否打开
print(isOpened)
fps = cap.get(cv2.CAP_PROP_FPS) # 帧率(每秒多少张图片)
width = int(cap.get(cv2.CAP_PROP_FRAME_WIDTH))# w h
height = int(cap.get(cv2.CAP_PROP_FRAME_HEIGHT)) # 高度信息
print(fps,width,height)
# i记录保存了多少张
i = 0
while(isOpened):
    # 读取十张照片
    if i == 10:
        break
    else:
        i = i+1
    (flag,frame) = cap.read()# 读取每一张(帧) 返回: flag(是否成功) frame(图片内容) 
    fileName = 'image'+str(i)+'.jpg'
    print(fileName)
    # 如果读取成功了，保存图片
    if flag == True:
        # 质量控制: 100表明质量最高。
        cv2.imwrite(fileName,frame,[cv2.IMWRITE_JPEG_QUALITY,100])
print('end!')

# 人脸识别的连续帧数为15帧
```

![mark](http://myphoto.mtianyan.cn/blog/180327/2fbkegHIiI.png?imageslim)

## 图片合成视频

```python
import cv2
img = cv2.imread('image1.jpg')
imgInfo = img.shape
# 宽度和高度信息
size = (imgInfo[1],imgInfo[0])
print(size)

# windows下使用DIVX
fourcc = cv2.VideoWriter_fourcc(*'DIVX')
# VideoWriter 参数1: 写入对象 参数
videoWrite = cv2.VideoWriter('pic2video.avi',fourcc,5,size,True)
# 写入对象 1 file name
# 2 可用编码器 3 帧率 4 size
for i in range(1,11):
    fileName = 'image'+str(i)+'.jpg'
    img = cv2.imread(fileName)
    videoWrite.write(img) # 写入方法 1 jpg data
print('end!')
```

这里注意报错:

```
OpenCV: FFMPEG: tag 0xffffffff/'' is not found (format 'mp4 / MP4 (MPEG-4 Part 14)')'
```

这个报错是因为windows下只支持DIVX

警告:

```
OpenCV: FFMPEG: tag 0x58564944/'DIVX' is not supported with codec id 13 and format 'mp4 / MP4 (MPEG-4 Part 14)'
```

不影响生成mp4的视频，但是因为DIVX原生支持的是avi格式。生成avi格式无警告。

