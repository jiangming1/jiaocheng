## 典型的CNN网络

### VGG16

![mark](http://myphoto.mtianyan.cn/blog/180404/if1hL8g2ig.png?imageslim)


卷积核，池化层，卷积网络使用的激励函数。

目前市面上比较新的网络，由于计算能力的提升变得越来越复杂。

不再出现典型的纯粹的全连接网络，卷积网络的独有特点，而是把他们进行了混搭。

16指其中有16个带有参数的网络层。

VGG16是一个完整的带有卷积层池化层和全连接层的网络。

VGG16是一个公开的模型，只描述数据进行处理的逻辑关系，与代码和语言没有关系。

一张图片从左侧进入模型，然后经过64个不同的(3,3)的卷积核，每次stride的步长是1.
生成64个小尺寸的图片。或者说64个feature map。然后再把这64个图片拼接在一起。
通过64个(3,3)的卷积核，生成后一层的feature map。然后经过maxpooling层来做池化。

最后这个maxpooling层的又被当做一张图片向后输入，通过128个(3,3)的卷积核进一步提取特征。

这个过程中卷积核的数量在逐步的上升，从64-128-256-512。每个卷积核提取过后的信息所占用空间也越来越小。这就是一个特征提取的过程。

还有一个一千个节点的softmax来做分类使用。分类中同样是把损失函数描述成各个卷积核中w的函数，然后进行凸优化，找到极值点。

这个过程与BP网络的训练过程大同小异。

VGG19 含有19个有参数的网络层。近年，越来越多的模型放入了多个卷积层，
能够在模型深度增加的过程中加快收敛速度，并且可以让网络有更好的泛化特征。

### AlexNet网络结构

![mark](http://myphoto.mtianyan.cn/blog/180404/B5F4G6dhKH.png?imageslim)

三个卷积层，两个池化层，两个全连接层，还有个1000个节点的softmax

### GoogleNet

![mark](http://myphoto.mtianyan.cn/blog/180404/Ec1G0cGkCe.png?imageslim)

深度已经达到了22层。和以前众多网络不一样的地方在于它里面引入了一个全新的结构叫做Inception(中文翻译，盗梦空间)

![mark](http://myphoto.mtianyan.cn/blog/180404/KLm3hF7e2a.png?imageslim)

前面输入过来的向量在这一层展开成了三个不同卷积核处理的并列结构。

这样可以在一定程度上加大下一层的输入信息量。其中(3,3)(5,5)的卷积层可以大大增加抽象能力.

Inception的引入大大的增加了网络的深度与宽度。使得网络的信息容纳能力变得更强。

使用inception结构的网络，往往比没有使用该结构的网络性能提高两到三倍。

![mark](http://myphoto.mtianyan.cn/blog/180404/ggLG4F1ED4.png?imageslim)

分类比赛中的成绩单。

## 图片识别

![mark](http://myphoto.mtianyan.cn/blog/180404/D0LaHg52kd.png?imageslim)

卷积神经网络在大样本下的分类体现出越来越好的效果。图片，音频，视频和大段的文字这些场景使用传统机器学习处理都不是很理想，而卷积神经网络就有得天独厚的能力。

因为卷积神经网络有卷积核这一法宝。人对于图片的认知，不会因为这张图片进行了缩放，而判断出该图片表示的是不同的事物，也不会因为一个人的肤色有了深浅的变化，或戴不戴眼镜，发型的变化而认不出来。


一个事物只要被我们认识了之后，只要它发生变化的程度不足以改变我们对它认知的判断。我们人就认为它是同一个事物。对于细微变化的免疫能力或者说不敏感性。

卷积网络在对输入特征进行提取的过程中，就是一个把高维向量映射成低维向量的过程。
也是一种有损压缩，这种压缩特点，就是卷积核在工作的过程中，会提供一种前一层输入向量(有可能是样本，有可能是前一层输出的feature map)到后一输出向量feature map的刺激能力。

而在卷积核滑动的过程中，我们发现有一个特性:

>个别向量值的变化对于刺激结果的影响是极为有限的。

这是一种用科学的方法通过量化的手段去表示敏感程度的过程。而且这个量化的程度是通过训练得到的。

![mark](http://myphoto.mtianyan.cn/blog/180404/jFJg61JFBb.png?imageslim)

当一张图像被卷积核之后的feature map 我们改变这张图的少量像素，无论改变颜色，线条，涂鸦等产生的feature mapB

在多层卷积核的扫描之后，这种差异已经非常的不敏感了。就像是被打了马赛克的图片，之前也许会有差异，但是被打了马赛克之后这些都会被抹去。

进而对后面层的网络产生近似的刺激。

两个观点:

- 少量的噪声，错误对于深度卷积神经网络的分类影响是很有限的。

>具有更强的容忍力。

- 由于卷积神经网络的这一特性，也使得它的泛化性更好。因为即使对象与训练样本库有一定差异，这种模糊化处理使得他们在较深的网络层有相似的刺激结果。

从原理上来看: 神经网络的工作原理实际是记忆一个大概的印象。而不像是在思考或者是推理。
你在训练样本中直白的告诉网络的样例，网络会很好的记下来，并且让它有一定的泛化性。

但是带有一些复杂的分析和判断能力，神经网络自身是不会有的。


