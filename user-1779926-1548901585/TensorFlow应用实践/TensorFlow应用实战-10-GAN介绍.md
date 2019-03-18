## 用TensorFlow开发会ps的AI

生成图片或补全图片

### 背景和知识点简介

Adobe公司在Photoshop中加入人工智能等功能。

图片补全修复。

GAN 以及 由它发展而来的DC

GAN: 生成对抗网络

Generative Adversarial Networks

DCGAN: 深度卷积生成对抗网络

Deep Convolutional Generative Adversarial Networks

对于图片进行补全，或者其他的功用。

### DCGAN的论文

https://arxiv.org/abs/1511.06434

UNSUPERVISED REPRESENTATION LEARNING WITH DEEP CONVOLUTIONAL GENERATIVE ADVERSARIAL NETWORKS

非监督学习

这篇论文有16页。基础的架构也用到了卷积神经网络。结合了生成式对抗的网络的相关技术。最终可以生成一些图片。

结合几张图片生成新的图片。微笑的女人，紧张的女人，紧张的男人

微笑的女人 - 紧张的女人 + 紧张的男人 = 微笑的男人

对于图片元素的提取以及删减。

生成图片样例

![mark](http://myphoto.mtianyan.cn/blog/180409/7b1I9GaLc2.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180409/hFIFb8l44b.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180409/gLah43320a.png?imageslim)

生成卡通人物或者人脸。

## 配置开发环境

软件和素材

- TensorFlow: tf.keras(TensorFlow对keras的一个上层实现)

可以更加方便的去调用TensorFlow底层的api，让我们的模型更加清晰

- PIL python imaging Library (安装pillow)

```python
pip install pillow
```

下载大量的图片

实验中我们用到的图片是(64,64)的，也可以调整网络模型的参数使它成为(32,32)的
或者更大

CIFAR-10 需要使用pickle来处理的

https://www.cs.toronto.edu/~kriz/cifar.html

香港中文大学 celeba 人脸数据

http://mmlab.ie.cuhk.edu.hk/projects/CelebA.html

百度云盘和Google drive都可以下载

kaggle中的datasets下载

https://www.kaggle.com/datasets

图片识别的比赛。

https://www.kaggle.com/alxmamaev/flowers-recognition/data

https://cs.nyu.edu/~roweis/data.html

Olivetti Faces (64,64)的图片

网上可以下载到卡通人物图片，Pokémon GO，香港中文大学人脸

下载下来转成我们想要的格式。用于模型的训练。

## 什么是GAN？

GAN 释义

- generative Adversarial Network: 生成对抗网络

- 让两个神经网络以相互博弈的方式进行学习
- 一个生成(generator) 网络 与一个判别(Discriminator) 网络

想象一下场景: 给女友拍照

- 女友说: 我要这种效果

![mark](http://myphoto.mtianyan.cn/blog/180409/5k5B59L27a.png?imageslim)

可是实际的效果是这样:

![mark](http://myphoto.mtianyan.cn/blog/180409/mLhc0iCKA7.png?imageslim)

女友说: 我还没转身你就拍啦。

女友说: 你就不能去学学xxx的抓拍吗？

女友又说我要这种效果:

![mark](http://myphoto.mtianyan.cn/blog/180409/48fGdfEkE8.png?imageslim)

可是你竟然拍出来这样的效果

![mark](http://myphoto.mtianyan.cn/blog/180409/liGI91c3EG.png?imageslim)

女友说: 你就不能去学学xxx的后期么？

没办法，女朋友的指令大于一切。

经过不断学习和失败, 最终。。。

![mark](http://myphoto.mtianyan.cn/blog/180409/j0h2i8dC1l.png?imageslim)

拍出了高大上的照片，女友说很棒我拿去发朋友圈了。

### GAN的基本原理

- 男友；Generator(生成网络) 负责从随机生成数据(这里是照片)

- 女友: Discriminator (判别网络) 找出和真实数据之间的区别

直到判别网络无法判断数据是生成的还是真实的为止

无法判断这个照片是你拍的还是专业的人拍的

![mark](http://myphoto.mtianyan.cn/blog/180409/fAlDeCl1KD.png?imageslim)

给GAN一些真实的样本，大写的G表示生成器。大写的D表示判别器。

我给判别器一些真实样本，同时给它一些生成器从潜在空间加些噪声(具有随机性)的
数据，经过生成器，生成假的样本。

真实的样本和假冒的样本会一起经过判别器，判别器给出判断。

我们从上帝视角去看判别器判别的是否正确，根据我们上帝视角的判断去调教判别器和生成器

不只提升生成器，两个都会一起提升。

男友的拍照能力 和 女友的审美都会提升

![mark](http://myphoto.mtianyan.cn/blog/180409/Hel9AHkjGE.png?imageslim)

生成的数据与真实数据如果重合就停止

![mark](http://myphoto.mtianyan.cn/blog/180409/F1IFAKhdJd.png?imageslim)

### GAN的相关信息

- lan Goodfellow 在2014年提出，他曾是 Yoshua Bengio的学生

深度学习三巨头 Google brain openai

Yan LeCun 称赞 GAN 是"过去十年机器学习领域最有意思的想法"

可以生成图像，视频，模型。有不少的变体: DCGAN WGAN等
