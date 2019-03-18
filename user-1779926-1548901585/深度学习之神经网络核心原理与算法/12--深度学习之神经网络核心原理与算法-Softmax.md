## softmax

输出层的激励函数softmax

![mark](http://myphoto.mtianyan.cn/blog/180404/cfJD5b5D0L.png?imageslim)

前面我们已经接触了一种激励函数，sigmoid函数。含义: 似然度

https://www.zhihu.com/question/35322351/answer/67193153

全连接层和输出层相连接，输出分类结果。从样子上看和普通的全连接层没有什么区别。
但是激励函数的形式却发生了很大的改变。

最后一层作为预测分类的输出节点。每一个节点代表一个分类。七个节点分成七类。

任何一个节点的激励函数都是:

![mark](http://myphoto.mtianyan.cn/blog/180404/jfbDia3AjL.png?imageslim)

下标i是节点的下标次序, 而Zi就等于Wi x X +B

最后一层有这样一个特性，sigmoid(z)的加和等于1

![mark](http://myphoto.mtianyan.cn/blog/180404/45El6jGGjG.png?imageslim)


每个节点上输出的值都是小于1的，代表它从属于这个分类的概率。

### softmax的训练过程

![mark](http://myphoto.mtianyan.cn/blog/180404/DCKIGFKG2E.png?imageslim)

onehot

训练时我们可以把图片和它对应的标签放入网络中进行训练。

一张待训练的图片输出时产生的结果(经过softmax):

![mark](http://myphoto.mtianyan.cn/blog/180404/cDHjE7GAmk.png?imageslim)

每个维度就是样本在该分类下的概率。

1000个节点就可以做1000中样本的分类。

## softmax层代码实现

```python
class SoftmaxLayer(object):
    def __init__(self, n_in, n_out, p_dropout=0.0):
        self.n_in = n_in
        self.n_out = n_out
        self.p_dropout = p_dropout
        self.w = theano.shared(
            np.zeros((n_in, n_out), dtype=theano.config.floatX),
            name='w', borrow=True)
        self.b = theano.shared(
            np.zeros((n_out,), dtype=theano.config.floatX),
            name='b', borrow=True)
        self.params = [self.w, self.b]
```

跟全连接网络一模一样。保存w和b到params列表。

实现set_input函数，它的三个参数和之前的全连接层也是一模一样的。

```python
 def set_inpt(self, inpt, inpt_dropout, mini_batch_size):
        self.inpt = inpt.reshape((mini_batch_size, self.n_in))
        self.output = softmax((1 - self.p_dropout) * T.dot(self.inpt, self.w) + self.b)
        self.y_out = T.argmax(self.output, axis=1)
        self.inpt_dropout = dropout_layer(
            inpt_dropout.reshape((mini_batch_size, self.n_in)), self.p_dropout)
        self.output_dropout = softmax(T.dot(self.inpt_dropout, self.w) + self.b)
```

使用theano提供的softmax函数。将wx+b的线性结果乘以一个我们需要保留的百分比。
经过激励函数softmax作用，成为我们的输出。

我们取output中的最大值，将十维化为一个实数。

如果使用了Dropout，我们需要过一个Dropout layer层。还是把input Dropout reshape一下

我们使用的cost函数是似然函数函数

```python
    def cost(self, net):
        "Return the log-likelihood cost."
        return -T.mean(T.log(self.output_dropout)[T.arange(net.y.shape[0]), net.y])
```

## 卷积神经网络手写数字识别

- 全连接
- 加入卷积池化层
- 使用Relu函数
- 加上Dropout

卷积神经网络图像识别效率比全连接神经网络高。

### 编码实现

```
import pickle
import gzip
```

这两个是用来读取保存在磁盘的数据的。

Python3下的修改:

```
TypeError: 'float' object cannot be interpreted as an integer”
```

将minibatch分数据集时的`/` 改为`//`

pickle加上encoding参数. 

### reluc函数

```
def ReLU(z): return T.maximum(0.0, z)
```

之前我们实现了softmax layer 和卷积池层layer 然后是全连接层layer

### 读取数据的函数

```python
# 读取MNIST data
def load_data_shared(filename="./data/mnist.pkl.gz"):
    f = gzip.open(filename, 'rb')
    training_data, validation_data, test_data = pickle.load(f,encoding='bytes')
    f.close()

    def shared(data):
        shared_x = theano.shared(
            np.asarray(data[0], dtype=theano.config.floatX), borrow=True)
        shared_y = theano.shared(
            np.asarray(data[1], dtype=theano.config.floatX), borrow=True)
        return shared_x, T.cast(shared_y, "int32")

    return [shared(training_data), shared(validation_data), shared(test_data)]
```

主要是把磁盘上的数据使用theano加载到gpu上计算。

gzip打开文件，pickle来读取我们的文件数据。返回三个值分别是训练，验证和测试

然后我们把训练，验证，测试分别使用shared方法进行计算。


### 如何使用？

```python
if __name__ == '__main__':
    training_data, validation_data, test_data = load_data_shared()
    mini_batch_size = 10
```

首先读入数据，然后定义minibatch大小。

实例化一个network的对象

```python
    net = Network([
        FullyConnectedLayer(n_in=784, n_out=100),
        SoftmaxLayer(n_in=100, n_out=10)], mini_batch_size)
```

定义一个全连接层，输入为(28,28) 784,输出位100.定义一个softmax层n_in是100，n_pout是10

使用随机梯度下降法进行训练

```python
    net.SGD(training_data, 60, mini_batch_size, 0.1, validation_data, test_data)
```
学习率0.1

![mark](http://myphoto.mtianyan.cn/blog/180404/elmel5B3hb.png?imageslim)

### 加入卷积池化层

看看准确率是否会提高。

```python
    # 加入卷积池化层
    net = Network([
        ConvPoolLayer(image_shape=(mini_batch_size, 1, 28, 28),
                      filter_shape=(20, 1, 5, 5),
                      poolsize=(2,2)),
        FullyConnectedLayer(n_in=20*12*12, n_out=100),
        SoftmaxLayer(n_in=100, n_out=10)], mini_batch_size)
```

> image_shape=(mini-batch的大小，输入的feature map个数, 图片高， 图片宽)

因为第一层，所以feature map个数就是1,图片的宽高。

>filter_shape=(filter个数，输入的feature map个数，filter高，filter宽)

过滤器个数为20个，输入的feature map因为在第一层所以也是1。过滤器(5,5)

池层的size是一个(2,2)的池层。

卷积池层之后我们使用一个全连接层: 总共会有20个feature map。featuremap大小(12,12)

>全连接层的n_in就是我们卷积层的输出。

SOftmax层的输入就是上一层全连接层的输出100.输出是10有十种分类。

![mark](http://myphoto.mtianyan.cn/blog/180404/bmHAmAbeh1.png?imageslim)

### 多加入一层卷积池化层

```python
#多加入一层卷积池化层
    net = Network([
        ConvPoolLayer(image_shape=(mini_batch_size, 1, 28, 28),
                      filter_shape=(20, 1, 5, 5),
                      poolsize=(2,2)),
        ConvPoolLayer(image_shape=(mini_batch_size, 20, 12, 12),
                      filter_shape=(40, 20, 5, 5),
                      poolsize=(2,2)),
        FullyConnectedLayer(n_in=40*4*4, n_out=100),
        SoftmaxLayer(n_in=100, n_out=10)], mini_batch_size)
```

>一个(28,28)的图经过20个(5,5)的过滤器。

池化层的(2,2)每四个点合成一个像素。所以能得到12,12.原本应该是(24,24)

而28如何到24: 28-5 = 23 + 开始停留的这一步。

正向推导:

- 我们的(20,12,12) 经过40个(5,5)的过滤器.

featuremap个数会变成40个。12-5 = 7 再加一就是8,8的。(40,8,8)
然后经过池化(2,2) 会变成(40,4,4)

![mark](http://myphoto.mtianyan.cn/blog/180404/dce7dLj6EF.png?imageslim)

### 使用relu激励函数替代sigmoid

```python
net = Network([
        ConvPoolLayer(image_shape=(mini_batch_size, 1, 28, 28),
            filter_shape=(20, 1, 5, 5),
            poolsize=(2,2),
            activation_fn=ReLU),
        ConvPoolLayer(image_shape=(mini_batch_size, 20, 12, 12),
            filter_shape=(40, 20, 5, 5),
            poolsize=(2,2),
            activation_fn=ReLU),
        FullyConnectedLayer(n_in=40*4*4, n_out=100, activation_fn=ReLU),
        SoftmaxLayer(n_in=100, n_out=10)], mini_batch_size)
```

![mark](http://myphoto.mtianyan.cn/blog/180404/iIb03LL3Km.png?imageslim)

在两个卷积池化和全连接层我们都显示的指定使用激励函数relu。

### 加上Dropout

```python
 #加上dropout
    net = Network([
        ConvPoolLayer(image_shape=(mini_batch_size, 1, 28, 28),
            filter_shape=(20, 1, 5, 5),
            poolsize=(2,2),
            activation_fn=ReLU),
        ConvPoolLayer(image_shape=(mini_batch_size, 20, 12, 12),
            filter_shape=(40, 20, 5, 5),
            poolsize=(2,2),
            activation_fn=ReLU),
        FullyConnectedLayer(n_in=40*4*4, n_out=100,
                            activation_fn=ReLU, p_dropout=0.5),
        SoftmaxLayer(n_in=100, n_out=10)], mini_batch_size)
```

全连接层添加参数, p_dropout保留一半输出结果。

![mark](http://myphoto.mtianyan.cn/blog/180405/9H1k3DH5H3.png?imageslim)

### 最终的结果对比

电脑太慢了，没跑完，先看下一章了。















































