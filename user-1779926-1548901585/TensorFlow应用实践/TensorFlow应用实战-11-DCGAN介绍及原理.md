## 什么是DCGAN

DCGAN是GAN的一个变体。

Deep Convolutional GAN

深度卷积生成对抗网络。

生成模型和判别模型都运用了深度卷积 神经网络的生成对抗网络

Gan里面生成模型和判别模型也是用到了神经网络。

### 卷积神经网络的基本原理

![mark](http://myphoto.mtianyan.cn/blog/180410/I7HlbGdjAB.png?imageslim)

给一个输入图片 汽车

经过卷积层提取它的一些特征信息。池化层进行亚采样，再经过卷积层池化层。
将信息逐层深入的去解析。

由复杂图像最终得到的是线条一类的东西。

![mark](http://myphoto.mtianyan.cn/blog/180410/GL5BIamb6d.png?imageslim)

逐层深入 "抽丝剥茧" 地 "理解" 一张图片或其他事物

类比一下我们小时候画画

### 绘画的顺序

![mark](http://myphoto.mtianyan.cn/blog/180410/L8ChKmmhI1.png?imageslim)

绘画的顺序与我们卷积神经网络的顺序是正好相反的。

从简单到复杂直到最后的成品。点线条到一幅完整的画。

### “反” 卷积神经网络的基本原理

![mark](http://myphoto.mtianyan.cn/blog/180410/4B3afFai3K.png?imageslim)

由简单到复杂地反向生成。

我们的DCGAN也是用到这个原理

https://arxiv.org/abs/1511.06434

用深度卷积神经网络来做的非监督学习

论文中将DCGAN的基础结构画了出来。

![mark](http://myphoto.mtianyan.cn/blog/180410/mmJ51fD806.png?imageslim)

用到的反卷积的网络结构就是这样一个。从简单的输入一些随机的数据。

逐层递增的形式越来越复杂，到最终生成一个完整的。

卷积神经网络是由复杂到简单，这里是由简单到复杂

卷积神经网络用于从图形里面抽取特征用来做到识图，识别等。

DCGAN的发明者想到能不能把卷积神经网络反一反，用于生成图像。

- Generator(生成网络) 利用了类似反卷积的神经网络模型
- Discriminator(判别网络) 利用了卷积的神经网络模型

判别网络要去读生成网络生成出来的东西，去识别鉴别。

用随机的噪声向量作为输入

![mark](http://myphoto.mtianyan.cn/blog/180410/gKEJDa2gG3.png?imageslim)
