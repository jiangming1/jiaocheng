## 编写训练神经网络的方法

![mark](http://myphoto.mtianyan.cn/blog/180409/i3aaI37iG7.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180409/3fc96JBfkg.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180409/FE957BALil.png?imageslim)

utils.py中有 

- convertMidi2Mp3 转换midi文件为mp3
- get_notes 从音乐中获取notes 分为两种一种是音符C4，一种是和弦4.15.7
- create_music 从预测的list中创建新的乐曲

我们还需要两个文件，一个用于我们的训练，一个用于生成。

```python
"""
==== 一些术语的概念 ====
# Batch size : 批次(样本)数目。一次迭代（Forword 运算（用于得到损失函数）以及 BackPropagation 运算（用于更新神经网络参数））所用的样本数目。Batch size 越大，所需的内存就越大
# Iteration : 迭代。每一次迭代更新一次权重（网络参数），每一次权重更新需要 Batch size 个数据进行 Forward 运算，再进行 BP 运算
# Epoch : 纪元/时代。所有的训练样本完成一次迭代

# 假如 : 训练集有 1000 个样本，Batch_size=10
# 那么 : 训练完整个样本集需要： 100 次 Iteration，1 个 Epoch
# 但一般我们都不止训练一个 Epoch
"""
```

前向传播，反向求导再更新参数。 epoch是指样本大小个完成一大轮。

```python
# -*- coding: UTF-8 -*-

"""
训练神经网络，将参数（Weight）存入 HDF5 文件
"""
```

一种文件格式

```python
import numpy as np
import tensorflow as tf
import keras

from utils import *
from network import *
```
![mark](http://myphoto.mtianyan.cn/blog/180409/31i0kmm96J.png?imageslim)

训练的时候会先有数据传进去。传进去生成一个预测音符，然后将音符序列往后挪动。
一直保持长度不变。

准备一些序列,numpitch 所有不同音符的数目，notes是getnotes获取的音符集合文件

```python
def prepare_sequences(notes, num_pitch):
    """
    为神经网络准备好供训练的序列
    """
    sequence_length = 100  # 每一个序列的长度

    # 得到所有不同的音调的名字。set去重
    pitch_names = sorted(set(item for item in notes))

    # 创建一个字典，用于映射 音调 和 整数: 整数才是我们实际传递给神经网络用的数据
    # picth - num 键值对 enumerate 枚举，会给第一个pitch 1 第二个 2
    pitch_to_int = dict((pitch, num) for num, pitch in enumerate(pitch_names))

    # 序列的输入
    network_input = []
    # 真实的output
    network_output = []

    # 创建输入序列，以及对应的输出序列: 0 最大长度-100 每次取100个 每隔1个
    for i in range(0, len(notes) - sequence_length, 1):
    	# 输入的序列切片。
        sequence_in = notes[i:i + sequence_length]
        # 本来的实际音符
        sequence_out = notes[i + sequence_length]
        # 取到音符的字符的对应数字。
        network_input.append([pitch_to_int[char] for char in sequence_in])
        # 将下一个真实实际音符也转换为数字
        network_output.append(pitch_to_int[sequence_out])

    # network_input的长度
    n_patterns = len(network_input)

    # 将输入的形状转换成 LSTM 模型可以接受的。要转换的数组，第二个参数是转换成的形状。长度n_patterns 高sequence_length 
    network_input = np.reshape(network_input, (n_patterns, sequence_length, 1))

    # 将输入标准化(归一化) 除以总的音调数目
    network_input = network_input / float(num_pitch)

    # 转换成 {0, 1} 组成的布尔矩阵，为了配合 categorical_crossentropy 误差算法使用
    network_output = keras.utils.to_categorical(network_output)

    return (network_input, network_output)

```

为什么要reshape是因为我们刚才使用for循环给network_input这个序列去添加了
n_patterns 这么多次的字典长度。 它的每个字典长度又有100.

我们使用了categorical_crossentropy计算交叉熵。

逻辑矩阵

https://zh.wikipedia.org/wiki/%E9%82%8F%E8%BC%AF%E7%9F%A9%E9%99%A3

```python
if __name__ == '__main__':
    train()
```

### 编写train

```python
# 训练神经网络
def train():
    notes = get_notes()

    # 得到所有不重复（因为用了set）的音调数目
    num_pitch = len(set(notes))

    # 生成输入与真实的输出
    network_input, network_output = prepare_sequences(notes, num_pitch)

    # 训练时不指定文件
    model = network_model(network_input, num_pitch)

    filepath = "weights-{epoch:02d}-{loss:.4f}.hdf5"
    # 第几轮 损失
    # 检查点: 保存文件。每一轮 epoch结束时保存模型的参数
    # 不怕训练过程中丢失模型参数，可以在我们对loss满意时随时停止训练。
    checkpoint = keras.callbacks.ModelCheckpoint(
        filepath, # 保存的文件路径
        monitor='loss', # 监视器 衡量哪个值
        verbose=0, # 冗余模式
        save_best_only=True, # 上一个最好的不会被覆盖，loss更好覆盖掉
        mode='min' # 损失最小
    )
    callbacks_list = [checkpoint]

    # 用fit方法来训练模型
    (network_input, network_output, epochs=66666, batch_size=64, callbacks=callbacks_list)
```

saver and restoring 保存和恢复

https://www.tensorflow.org/versions/master/api_docs/python/tf/keras/callbacks/ModelCheckpoint

生成音乐时我们选择loss最小的去生成音乐。

https://www.tensorflow.org/versions/master/api_docs/python/tf/keras/Sequential

的fit方法来训练模型。
