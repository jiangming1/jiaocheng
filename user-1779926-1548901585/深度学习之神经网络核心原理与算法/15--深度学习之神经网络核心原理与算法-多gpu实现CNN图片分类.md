使用TensorFlow中的卷积神经网络CNN对于图片进行分类。

## 简介

### CIFAR-10

每张图片: (32,32) 六万张

![mark](http://myphoto.mtianyan.cn/blog/180407/hKJECd3cKB.png?imageslim)

十种分类 训练集: 五万张 测试集: 一万张
 
- 汽车 手机 鸟 猫 等。

图片 彩色 (32,32)

我们将要使用的卷积神经网络的网络结构:

![mark](http://myphoto.mtianyan.cn/blog/180407/HcgDaALH9b.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180407/BDK95lChE1.png?imageslim)

从下往上看: 卷积1 - 池层1 - 归一化1 - 卷积2 - 归一化2 - 池层2 - 全连接层3
- 全连接层4 - 输出层

每一层的详细解释如1图所示。

使用单gpu和多gpu的代码应该怎么来写。

## 单gpu版本的代码实现

https://github.com/tensorflow/models.git

models/tutorials/images/cifar10

tutorials/image/cifar10/cifar10_train.py 这个是单gpu的版本

tutorials/image/cifar10/cifar10_multi_gpu_train.py 这个是多gpu的版本

```python
if __name__ == '__main__':
  tf.app.run()
```

调用app.run运行代码

```python
def main(argv=None):  # pylint: disable=unused-argument
  cifar10.maybe_download_and_extract()
  if tf.gfile.Exists(FLAGS.train_dir):
    tf.gfile.DeleteRecursively(FLAGS.train_dir)
  tf.gfile.MakeDirs(FLAGS.train_dir)
  train()
```

默认情况下会运行我们定义的main函数。main函数中做了数据的下载，判断traindir是否存在，重新创建。

进入train函数,train函数的上面定义了一些参数

```
FLAGS = tf.app.flags.FLAGS

tf.app.flags.DEFINE_string('train_dir', '/tmp/cifar10_train',
                           """Directory where to write event logs """
                           """and checkpoint.""")
tf.app.flags.DEFINE_integer('max_steps', 1000000,
                            """Number of batches to run.""")
tf.app.flags.DEFINE_boolean('log_device_placement', False,
                            """Whether to log device placement.""")
tf.app.flags.DEFINE_integer('log_frequency', 10,
                            """How often to log results to the console.""")
```

训练的目录，最大的步数。在train中开始训练。

```python
def train():
  """Train CIFAR-10 for a number of steps."""
  with tf.Graph().as_default():
    global_step = tf.train.get_or_create_global_step()
```

在训练之前设置一下现在全局的步数。

```python
    with tf.device('/cpu:0'):
      images, labels = cifar10.distorted_inputs()

```

通过cifar10封装好的方法获取我们的图片内容和对应的标签。接下来跟我们做TensorFlow做mnist 一样的。使用inference方法传入images来创建网络。

过完网络后得到了网络的预测值。拿到预测结果和真实标签计算loss

```python
    # Calculate loss.
    loss = cifar10.loss(logits, labels)
```

这时候就可以开始训练

```python
    # Build a Graph that trains the model with one batch of examples and
    # updates the model parameters.
    train_op = cifar10.train(loss, global_step)
```

训练的时候放入loss和全局的步数，得到一个训练的优化op。

这里的训练和之后有一些区别。

```python
    with tf.train.MonitoredTrainingSession(
        checkpoint_dir=FLAGS.train_dir,
        hooks=[tf.train.StopAtStepHook(last_step=FLAGS.max_steps),
               tf.train.NanTensorHook(loss),
               _LoggerHook()],
        config=tf.ConfigProto(
            log_device_placement=FLAGS.log_device_placement)) as mon_sess:
      while not mon_sess.should_stop():
        mon_sess.run(train_op)

```

可以设置一个钩子函数，在运行之前和运行之中设置回调函数处理变量，输出你想要的信息。

设置checkpoint_dir, 设置一个钩子，然后设置了一个配置项。

```python
    class _LoggerHook(tf.train.SessionRunHook):
      """Logs loss and runtime."""

      def begin(self):
        self._step = -1
        self._start_time = time.time()

      def before_run(self, run_context):
        self._step += 1
        return tf.train.SessionRunArgs(loss)  # Asks for loss value.

      def after_run(self, run_context, run_values):
        if self._step % FLAGS.log_frequency == 0:
          current_time = time.time()
          duration = current_time - self._start_time
          self._start_time = current_time

          loss_value = run_values.results
          examples_per_sec = FLAGS.log_frequency * FLAGS.batch_size / duration
          sec_per_batch = float(duration / FLAGS.log_frequency)

          format_str = ('%s: step %d, loss = %.2f (%.1f examples/sec; %.3f '
                        'sec/batch)')
          print (format_str % (datetime.now(), self._step, loss_value,
                               examples_per_sec, sec_per_batch))
```


设置的回调函数有三个方法。在程序运行之前做一些操作，，每次run之前做一些操作。
run之后计算耗时，得到loss值。每隔多少step打印信息。

这些都是在钩子函数里来完成的。

下面我们来看一下cifar10里面封装的网络构建函数和损失值计算，以及训练优化。

cifar10.py:

```python
 Basic model parameters.
tf.app.flags.DEFINE_integer('batch_size', 128,
                            """Number of images to process in a batch.""")
tf.app.flags.DEFINE_string('data_dir', '/tmp/cifar10_data',
                           """Path to the CIFAR-10 data directory.""")
tf.app.flags.DEFINE_boolean('use_fp16', False,
                            """Train the model using fp16.""")
```

首先定义了一些变量。batch大小，数据目录，标志位。


```python
# Global constants describing the CIFAR-10 data set.
IMAGE_SIZE = cifar10_input.IMAGE_SIZE
NUM_CLASSES = cifar10_input.NUM_CLASSES
NUM_EXAMPLES_PER_EPOCH_FOR_TRAIN = cifar10_input.NUM_EXAMPLES_PER_EPOCH_FOR_TRAIN
NUM_EXAMPLES_PER_EPOCH_FOR_EVAL = cifar10_input.NUM_EXAMPLES_PER_EPOCH_FOR_EVAL
```

图片大小(32,32) 分类数目(10) 训练集和验证集的每个epoch数量

设置了一些训练时的超参数，每隔多少轮我们的learning_rate下降多少。

```python
# Constants describing the training process.
MOVING_AVERAGE_DECAY = 0.9999     # The decay to use for the moving average.
NUM_EPOCHS_PER_DECAY = 350.0      # Epochs after which learning rate decays.
LEARNING_RATE_DECAY_FACTOR = 0.1  # Learning rate decay factor.
INITIAL_LEARNING_RATE = 0.1       # Initial learning rate.
```

前三个参数都是learning_rate下降时，第四个是我们初始化的learning_rate

我们初始化的是0.1 随着网络的训练，每隔一定的步数这个learning_rate下降一点点。

我们开始时可以把learning_rate调大一点，使它下降快一点。当loss值下降到一定的水平。

我们的learning_rate会逐渐减小。

tower_name就是我们使用多gpu版本时为op加上前缀。

```python
TOWER_NAME = 'tower'

DATA_URL = 'https://www.cs.toronto.edu/~kriz/cifar-10-binary.tar.gz'
```

数据的下载地址。程序会帮我们直接下载。

把需要记录的数据用summary记录的封装函数

```python
def _activation_summary(x):
  """Helper to create summaries for activations.

  Creates a summary that provides a histogram of activations.
  Creates a summary that measures the sparsity of activations.

  Args:
    x: Tensor
  Returns:
    nothing
  """
  # Remove 'tower_[0-9]/' from the name in case this is a multi-GPU training
  # session. This helps the clarity of presentation on tensorboard.
  tensor_name = re.sub('%s_[0-9]*/' % TOWER_NAME, '', x.op.name)
  tf.summary.histogram(tensor_name + '/activations', x)
  tf.summary.scalar(tensor_name + '/sparsity',
                                       tf.nn.zero_fraction(x))
```

98行时创建一个在cpu上运行的变量。

```python
def _variable_on_cpu(name, shape, initializer):
  """Helper to create a Variable stored on CPU memory.

  Args:
    name: name of the variable
    shape: list of ints
    initializer: initializer for Variable

  Returns:
    Variable Tensor
  """
  with tf.device('/cpu:0'):
    dtype = tf.float16 if FLAGS.use_fp16 else tf.float32
    var = tf.get_variable(name, shape, initializer=initializer, dtype=dtype)
  return var
```

显式的指定了这个变量在cpu0上运行，指定了你需要创建的变量的数据类型。

```python
    var = tf.get_variable(name, shape, initializer=initializer, dtype=dtype)
```

使用get_variable创建一个变量。变量的名字，变量的shape，变量的初始化方法。

TensorFlow中有很多的变量初始化方法。正态分布等。这个函数主要的目的是在cpu0上创建变量。

```python
def _variable_with_weight_decay(name, shape, stddev, wd):
  """Helper to create an initialized Variable with weight decay.

  Note that the Variable is initialized with a truncated normal distribution.
  A weight decay is added only if one is specified.

  Args:
    name: name of the variable
    shape: list of ints
    stddev: standard deviation of a truncated Gaussian
    wd: add L2Loss weight decay multiplied by this float. If None, weight
        decay is not added for this Variable.

  Returns:
    Variable Tensor
  """
  dtype = tf.float16 if FLAGS.use_fp16 else tf.float32
  var = _variable_on_cpu(
      name,
      shape,
      tf.truncated_normal_initializer(stddev=stddev, dtype=dtype))
  if wd is not None:
    weight_decay = tf.multiply(tf.nn.l2_loss(var), wd, name='weight_loss')
    tf.add_to_collection('losses', weight_decay)
  return var
```

正则化，代码中使用的是L2的正则化。 变量的类型。

调用我们之前的在cpu创建变量的方法。这里使用的是截断的正态分布truncated_normal_initializer。输入数据类型，名称。数据的形状。

如果使用L2正则化。就是w的平方加和。TensorFlow已经提供了一个函数就是l2_loss

https://www.tensorflow.org/versions/master/api_docs/python/tf/nn/l2_loss

L2损失。

计算一个张量的L2范数的一半，没有sqrt：

```
output = sum(t ** 2) / 2
```

w的平方加和除以2。查看之前我们介绍正则化的时候的公式。

```python
def distorted_inputs():
  """Construct distorted input for CIFAR training using the Reader ops.

  Returns:
    images: Images. 4D tensor of [batch_size, IMAGE_SIZE, IMAGE_SIZE, 3] size.
    labels: Labels. 1D tensor of [batch_size] size.

  Raises:
    ValueError: If no data_dir
  """
  if not FLAGS.data_dir:
    raise ValueError('Please supply a data_dir')
  data_dir = os.path.join(FLAGS.data_dir, 'cifar-10-batches-bin')
  images, labels = cifar10_input.distorted_inputs(data_dir=data_dir,
                                                  batch_size=FLAGS.batch_size)
  if FLAGS.use_fp16:
    images = tf.cast(images, tf.float16)
    labels = tf.cast(labels, tf.float16)
  return images, labels
```

获取图片和每个图片对应的标签。如果是多gpu，会把每个batch的图片和标签获取出来，分散到多个gpu上。

单gpu版本就使用input函数来获取样本

```python
def inputs(eval_data):
  """Construct input for CIFAR evaluation using the Reader ops.

  Args:
    eval_data: bool, indicating if one should use the train or eval data set.

  Returns:
    images: Images. 4D tensor of [batch_size, IMAGE_SIZE, IMAGE_SIZE, 3] size.
    labels: Labels. 1D tensor of [batch_size] size.

  Raises:
    ValueError: If no data_dir
  """
  if not FLAGS.data_dir:
    raise ValueError('Please supply a data_dir')
  data_dir = os.path.join(FLAGS.data_dir, 'cifar-10-batches-bin')
  images, labels = cifar10_input.inputs(eval_data=eval_data,
                                        data_dir=data_dir,
                                        batch_size=FLAGS.batch_size)
  if FLAGS.use_fp16:
    images = tf.cast(images, tf.float16)
    labels = tf.cast(labels, tf.float16)
  return images, labels
```
## 网络的构建

```python
def inference(images):
  """Build the CIFAR-10 model.
```

卷积层1的创建

```python
  # conv1
  with tf.variable_scope('conv1') as scope:
    kernel = _variable_with_weight_decay('weights',
                                         shape=[5, 5, 3, 64],
                                         stddev=5e-2,
                                         wd=None)
    conv = tf.nn.conv2d(images, kernel, [1, 1, 1, 1], padding='SAME')
    biases = _variable_on_cpu('biases', [64], tf.constant_initializer(0.0))
    pre_activation = tf.nn.bias_add(conv, biases)
    conv1 = tf.nn.relu(pre_activation, name=scope.name)
    _activation_summary(conv1)
```

把代码块创建的所有变量前面再加一个前缀。

调用tf的卷积函数，我们自己实现神经网络时使用的theano。输入的图片，kernel(卷积的过滤器)  步长，然后padding

在cpu上创建了biases偏置。使用了常量的初始化方式。

卷积加上biases得到了预测值。将预测值过了一遍relu函数。

`_activation_summary`将第一层的信息保存至磁盘，以便于TensorBoard观察

池层:

把卷积层的输出结果放到池层，池层里面的过滤器形状，步长，padding，起一个别名

归一化方法:

对池层输出的结果做了归一化。

- 第二个卷积层:

- 第二层归一化 & 池层

- 过一个全连接层

- 将batch上的数据做了平铺

```python
 # local3
  with tf.variable_scope('local3') as scope:
    # Move everything into depth so we can perform a single matrix multiply.
    # 变成了一个一维的向量
    reshape = tf.reshape(pool2, [images.get_shape().as_list()[0], -1])
    dim = reshape.get_shape()[1].value
    # 创建了weights 和biases
    weights = _variable_with_weight_decay('weights', shape=[dim, 384],
                                          stddev=0.04, wd=0.004)
    biases = _variable_on_cpu('biases', [384], tf.constant_initializer(0.1))
    # wx+b线性部分，经过relu函数
    local3 = tf.nn.relu(tf.matmul(reshape, weights) + biases, name=scope.name)
    _activation_summary(local3)
```

接下来又过一个全连接层: 创建w和b 过线性过relu激励。 需要数据写入磁盘

最后一个是输出层

w b 线性部分，总共分类10类

```python
  with tf.variable_scope('softmax_linear') as scope:
    weights = _variable_with_weight_decay('weights', [192, NUM_CLASSES],
                                          stddev=1/192.0, wd=None)
    biases = _variable_on_cpu('biases', [NUM_CLASSES],
                              tf.constant_initializer(0.0))
    softmax_linear = tf.add(tf.matmul(local4, weights), biases, name=scope.name)
    _activation_summary(softmax_linear)

  return softmax_linear
```

过一个线性，直接得到十种分类每个类别上的概率。

### loss函数

```python
def loss(logits, labels):
  """Add L2Loss to all the trainable variables.

  Add summary for "Loss" and "Loss/avg".
  Args:
    logits: Logits from inference().
    labels: Labels from distorted_inputs or inputs(). 1-D tensor
            of shape [batch_size]

  Returns:
    Loss tensor of type float.
  """
  # Calculate the average cross entropy loss across the batch.
  labels = tf.cast(labels, tf.int64)
  cross_entropy = tf.nn.sparse_softmax_cross_entropy_with_logits(
      labels=labels, logits=logits, name='cross_entropy_per_example')
  cross_entropy_mean = tf.reduce_mean(cross_entropy, name='cross_entropy')
  tf.add_to_collection('losses', cross_entropy_mean)

  # The total loss is defined as the cross entropy loss plus all of the weight
  # decay terms (L2 loss).
  return tf.add_n(tf.get_collection('losses'), name='total_loss')
```

标签做一下类型转换。sparse_softmax_cross_entropy_with_logits方法。
得到了最终的损失值。将最终的损失值保存在了一个列表里面

### 凡是带有summary都是往磁盘写数据的

train函数

```python
lr = tf.train.exponential_decay(INITIAL_LEARNING_RATE,
                                  global_step,
                                  decay_steps,
                                  LEARNING_RATE_DECAY_FACTOR,
                                  staircase=True)
  tf.summary.scalar('learning_rate', lr)
```

exponential_decay函数做learningrate的指数下降得到每个epoch中有多少个batch。

![mark](http://myphoto.mtianyan.cn/blog/180408/gEkflHKIA9.png?imageslim)

利用下面这个公式变小。

TensorFlow帮你控制学习率的下降

```python
  # Compute gradients.
  with tf.control_dependencies([loss_averages_op]):
    opt = tf.train.GradientDescentOptimizer(lr)
    grads = opt.compute_gradients(total_loss)
```

使用梯度下降算法，输入learning_rate.不同的是直接显式的计算梯度。

介绍theano中也有一个计算梯度的函数。应用梯度。

maybe_download_and_extract函数作用是下载数据集。

什么时候停止:

```python
 with tf.train.MonitoredTrainingSession(
        checkpoint_dir=FLAGS.train_dir,
        hooks=[tf.train.StopAtStepHook(last_step=FLAGS.max_steps),
               tf.train.NanTensorHook(loss),
               _LoggerHook()],
        config=tf.ConfigProto(
            log_device_placement=FLAGS.log_device_placement)) as mon_sess:
      while not mon_sess.should_stop():
        mon_sess.run(train_op)
```

StopAtStepHook到达你设定的最大的步数的时候。调用should_stop停止

![mark](http://myphoto.mtianyan.cn/blog/180408/j3L0f6fIkL.png?imageslim)

## 多gpu版本代码实现

cifar10_multi_gpu_train.py

整个代码的区别与单gpu版本区别不大。首先还是

```python
if __name__ == '__main__':
  tf.app.run()
```

然后进入main函数,跟单gpu版本一模一样

train函数前面的代码也和之前是一样的。

```python
# Get images and labels for CIFAR-10.
    images, labels = cifar10.distorted_inputs()
    batch_queue = tf.contrib.slim.prefetch_queue.prefetch_queue(
          [images, labels], capacity=2 * FLAGS.num_gpus)
    # Calculate the gradients for each model tower.
    tower_grads = []
    with tf.variable_scope(tf.get_variable_scope()):
      for i in xrange(FLAGS.num_gpus):
        with tf.device('/gpu:%d' % i):
          with tf.name_scope('%s_%d' % (cifar10.TOWER_NAME, i)) as scope:
```

取有多少块gpu。然后循环每个gpu，把数据平均分到每个gpu上。

放在gpu上计算。把每个训练数据的batch分到多个gpu上。


```python
 image_batch, label_batch = batch_queue.dequeue()
            # Calculate the loss for one tower of the CIFAR model. This function
            # constructs the entire CIFAR model but shares the variables across
            # all towers.
            loss = tower_loss(scope, image_batch, label_batch)

            # Reuse variables for the next tower.
            tf.get_variable_scope().reuse_variables()

            # Retain the summaries from the final tower.
            summaries = tf.get_collection(tf.GraphKeys.SUMMARIES, scope)

            # Calculate the gradients for the batch of data on this CIFAR tower.
            grads = opt.compute_gradients(loss)

            # Keep track of the gradients across all towers.
            tower_grads.append(grads)
```

每个gpu上的网络其实完全是一样的。每个GPU上分别取计算各自的loss。计算每个gpu上的梯度。然后将梯度保存到列表里面

```python
    grads = average_gradients(tower_grads)
```

将每个gpu上的梯度计算一个平均梯度。

显式的计算梯度:

```python
 # Apply the gradients to adjust the shared variables.
    apply_gradient_op = opt.apply_gradients(grads, global_step=global_step)
```

把训练数据写入到磁盘

```python
    # Create a saver.
    saver = tf.train.Saver(tf.global_variables())
```

保存了一下模型

```python
 # Build an initialization operation to run below.
    init = tf.global_variables_initializer()

    # Start running operations on the Graph. allow_soft_placement must be set to
    # True to build towers on GPU, as some of the ops do not have GPU
    # implementations.
    sess = tf.Session(config=tf.ConfigProto(
        allow_soft_placement=True,
        log_device_placement=FLAGS.log_device_placement))
    sess.run(init)
```

初始化全局变量，调用session.run方法

```python
  for step in xrange(FLAGS.max_steps):
      start_time = time.time()
      _, loss_value = sess.run([train_op, loss])
      duration = time.time() - start_time
```


开始训练每一步，记录一下训练的耗时。每隔一定步数，打印记录到磁盘。

![mark](http://myphoto.mtianyan.cn/blog/180408/BjejFGdlHm.png?imageslim)

>代码如图: 初始化所有的w和b，这里假设有两块gpu。初始化好的w和b分别拷贝到了两块gpu上。
接着把数据一个batch的数据平均分配到了这两块gpu上。

然后在这两块gpu上去训练我们的模型。计算loss，计算梯度。可以看到这两块gpu上的模型以及初始化的w和b是一模一样的。

这两个gpu分别得到了一个梯度值和一个loss值, 又把这两块gpu的loss值和梯度值拷贝到了cpu中进行平均梯度的计算。

计算完平均梯度就来更新神经网络。

Wn = Wn-1 - 一塔(学习率) 乘以 梯度值

更新网络中的所有w和b。所有的w和b更新完之后又是同样的操作。又把更新好的w和b拷到gpu上，更新这两块gpu上的参数。然后又把一个batch平均分配给两个gpu。

计算loss计算梯度。然后循环。

多gpu版本, 阿里云等云服务商租两块gpu。

这个版本的代码没有gpu跑不起来。因为显式指定了GPU。

![mark](http://myphoto.mtianyan.cn/blog/180408/mj4G8hKcFD.png?imageslim)



















































