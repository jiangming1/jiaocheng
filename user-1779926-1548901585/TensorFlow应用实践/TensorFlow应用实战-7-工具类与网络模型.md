### 编写转换midi到mp3的方法

```python
# -*- coding: UTF-8 -*-

import os
import subprocess

def convertMidi2Mp3():
    """
    将神经网络生成的 MIDI 文件转成 MP3 文件
    """
    input_file = 'output.mid'
    output_file = 'output.mp3'

    # 验证一下是否存在
    assert os.path.exists(input_file)

    print('Converting %s to MP3' % input_file)

    # 用 timidity 生成 mp3 文件(写一个命令行的命令)
    command = 'timidity {} -Ow -o - | ffmpeg -i - -acodec libmp3lame -ab 64k {}'.format(input_file, output_file)
    # 运行这个命令
    subprocess.call(command, shell=True)

    print('Converted. Generated file is %s' % output_file)
```

```python
if __name__ == '__main__':
    convertMidi2Mp3()
```

## Music21的使用和测试

### 主要的两个Music21的类

![mark](http://myphoto.mtianyan.cn/blog/180407/G0AjfiClH2.png?imageslim)

音符。

### 什么是Chord和弦

![mark](http://myphoto.mtianyan.cn/blog/180407/CD0c5bD00g.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180407/DkEJHheIF7.png?imageslim)

http://web.mit.edu/music21/doc/moduleReference/moduleNote.html

pitch name 就是我们看过的C4 d#等

http://web.mit.edu/music21/doc/usersGuide/usersGuide_07_chords.html

体验一个midi文件的构成。

新建一个test21.py

```python
# -*- coding: UTF-* -*-

from music21 import converter, instrument

def print_notes():
    # 读取 MIDI 文件, 输出 Stream 流类型
    stream = converter.parse("1.mid")

    # 获得所有乐器部分
    parts = instrument.partitionByInstrument(stream)

    if parts: # 如果有乐器部分，取第一个乐器部分
        notes = parts.parts[0].recurse()
    else:
        notes = midi.flat.notes

    # 打印出每一个元素
    for element in notes:
        print(str(element))

if __name__ == "__main__":
    print_notes()
```

![mark](http://myphoto.mtianyan.cn/blog/180407/Ff2D7F1c6E.png?imageslim)

node类和Chord类

![mark](http://myphoto.mtianyan.cn/blog/180407/GadHmHbbbH.png?imageslim)

钢琴类 rest休息符

## 编写整个神经网络模型

```python
# -*- coding: UTF-8 -*-

"""
RNN-LSTM 循环神经网络
"""

import tensorflow as tf
import keras

# 神经网络的模型
def network_model(inputs, num_pitch, weights_file=None):
    model = keras.models.Sequential()
    model.add(keras.layers.LSTM(
        512, # 输出的维度
        input_shape=(inputs.shape[1], inputs.shape[2]), # 输入的形状
        return_sequences=True # 返回 Sequences（序列）
    ))
    model.add(keras.layers.Dropout(0.3)) # 丢弃 30%
    model.add(keras.layers.LSTM(512, return_sequences=True))
    model.add(keras.layers.Dropout(0.3))
    model.add(keras.layers.LSTM(512))
    model.add(keras.layers.Dense(256)) # 256 个神经元的全连接层
    model.add(keras.layers.Dropout(0.3))
    model.add(keras.layers.Dense(num_pitch)) # 所有不重复的音调的数目
    model.add(keras.layers.Activation('softmax')) # Softmax 激活函数算概率
    # 交叉熵计算误差，使用 RMSProp 优化器
    model.compile(loss='categorical_crossentropy', optimizer='rmsprop')

    if weights_file is not None: # 如果是 生成 音乐时
        # 从 HDF5 文件中加载所有神经网络层的参数（Weights）
        model.load_weights(weights_file)

    return model
```
https://keras.io/models/sequential/

The Sequential model is a linear stack of layers.

线性堆叠的层级模型。官方样例代码:

```python
# Optionally, the first layer can receive an `input_shape` argument:
model = Sequential()
model.add(Dense(32, input_shape=(500,)))
# Afterwards, we do automatic shape inference:
model.add(Dense(32))
```

使用add方法添加神经网络不同的层。样例中添加一个dense全连接层。

https://www.tensorflow.org/versions/master/api_docs/python/tf/keras/layers/Dense

我们可以添加的层在tf.keras.layer中。

1.4版本中在tf.keras.models.Squential中。之后的版本移动到了tf.keras.Squential

模型输入，音符的数量，weights_file是我们的模型,参数文件。生成音乐的时候要指定文件.

```python
# 神经网络的模型
def network_model(inputs, num_pitch, weights_file=None):
    model = keras.models.Sequential()
```

创建了一个做汉堡的模子。在里面去填充它的各个层。

![mark](http://myphoto.mtianyan.cn/blog/180407/iB0Calme38.png?imageslim)


汉堡没制作出来之前你的脑海已经有这个形状了。一层一层添加面包什么的。

第一层的输出会变成第二层的输入，第二层的输出变成第三层的输入。以此类推。

添加第一层

## 构建的神经网络模型

![mark](http://myphoto.mtianyan.cn/blog/180407/hBeHkggCIL.png?imageslim)

- lstm 神经元512
- Dropout 丢弃一部分神经元 防止过拟合
- 第二个lstm 神经元512
- 第二个dropout依然丢弃30%
- 第三个lstm给全连接层(dense)，256个神经元。
- 丢弃30%之后，这个全连接层神经元个数等于我们的不同音调的个数。(num_pitch)
- softmax激活层 算概率(最大概率)

### 决定生成音符的判别依据: 百分比概率


![mark](http://myphoto.mtianyan.cn/blog/180409/gAGi6diiia.png?imageslim)

C是概率最高的音符，挑选C最为最终的输出。

![mark](http://myphoto.mtianyan.cn/blog/180409/2aiHJBHH6l.png?imageslim)

- 神经网络生成新 音符/和弦原理

我们给神经网络输入一个序列音符，经过神经网络模型。

![mark](http://myphoto.mtianyan.cn/blog/180409/4JeGgF6LKh.png?imageslim)

将其中的A丢弃掉，然后产生新的预测。

![mark](http://myphoto.mtianyan.cn/blog/180409/3A1lbGl9H4.png?imageslim)

>将其中的B丢弃掉，再去产生新的预测音符。

```python
model.add(keras.layers.LSTM(
        512, # Lstm层神经元的数目是512，也是它输出的维度
        input_shape=(inputs.shape[1], inputs.shape[2]), # 输入的形状
        return_sequences=True # 返回 Sequences（序列）
    ))
```

添加一个Lstm层。 第一个参数表示神经元的数目(也表示经过这一层之后输出的维度)。
第一层需要指定输入的形状。后面的lstm层就不需要指定了。

return_sequences 设置为true。默认是返回输出序列的最后一个输出。

指定为true会输出所有的输出序列。前两层要指定为true，将完整的序列输出给到下一层。第三层就不用了。

![mark](http://myphoto.mtianyan.cn/blog/180409/23Kf9hcl9k.png?imageslim)

在堆叠LSTM层时必须设置，最后一层 LSTM 不用设置

有两个维度，音符和音符对应的整型值。

添加第二层

```python
    model.add(keras.layers.Dropout(0.3)) # 丢弃 30%神经元，防止过拟合

```

第二个lstm层

```python
    model.add(keras.layers.LSTM(512, return_sequences=True))
```

第二个Dropout层

```python
    model.add(keras.layers.Dropout(0.3)) # 丢弃 30%
```

第三层让它只输出默认的false下的输出序列的最后一个

```python
    model.add(keras.layers.LSTM(512))
```

全连接层，神经元数目256个

```python
    model.add(keras.layers.Dense(256)) # 256 个神经元的全连接层
```

dropout层略

```python
model.add(keras.layers.Dense(num_pitch)) # 输出的数目等于所有不重复的音调的数目
```

类似于onehot编码。得到所有音符对应的预测概率，然后取最大的一个

![mark](http://myphoto.mtianyan.cn/blog/180409/aJh451f64L.png?imageslim)

激活层使用softmax函数

```python
    model.add(keras.layers.Activation('softmax')) # Softmax 激活函数算概率
```

最后对于sequential做一些配置,指定一些参数

```python
    # 交叉熵计算误差，使用 RMSProp 优化器
    model.compile(loss='categorical_crossentropy', optimizer='rmsprop')
```

损失函数使用categorical_crossentropy (交叉熵计算误差。) 优化器(rmsprop优化器)

循环神经网络来说比较优秀的Rmsprop优化器。

神经网络的结构已经写好了。

![mark](http://myphoto.mtianyan.cn/blog/180409/E3Ah7Kk3Kk.png?imageslim)

```python
    if weights_file is not None: # 如果是 生成 音乐时
        # 从 HDF5 文件中加载所有神经网络层的参数（Weights）
        model.load_weights(weights_file)

    return model
```

训练网络时不指定weights_file。训练和生成时都会用到这个model。

## 从训练文件中获取音符的方法

```python
from music21 import converter, instrument, note, chord,stream
# converter用于转换的，instrument处理乐器部分。
# 音符与和弦的类


def get_notes():
    """
    从 music_midi 目录中的所有 MIDI 文件里提取 note（音符）和 chord（和弦）
    Note 样例： A, B, A#, B#, G#, E, ...
    Chord 样例: [B4 E5 G#5], [C5 E5], ...
    因为 Chord 就是几个 Note 的集合，所以我们把它们简单地统称为“Note”
    """
    notes = []

    # glob : 匹配所有符合条件的文件，并以 List 的形式返回
    for file in glob.glob("music_midi/*.mid"):
    	# 生成流数据
        stream = converter.parse(file)

        # 获取所有乐器部分
        parts = instrument.partitionByInstrument(stream)

        if parts: # 如果有乐器部分， 取第一个乐器部分
            notes_to_parse = parts.parts[0].recurse()
        else:
            notes_to_parse = stream.flat.notes

        for element in notes_to_parse:
            # 如果是Note类型，那么取它的音调
            if isinstance(element, note.Note):
                # 格式例如： E6
                notes.append(str(element.pitch))
            # 如果是Chord类型，那么取它各个音调的序号
            elif isinstance(element, chord.Chord):
                # 转换后格式例如： 4.15.7
                notes.append('.'.join(str(n) for n in element.normalOrder))

    # 将数据写入 data/notes 文件
    with open('data/notes', 'wb') as filepath:
        pickle.dump(notes, filepath)

    return notes

```

![mark](http://myphoto.mtianyan.cn/blog/180409/DcE5m0B75g.png?imageslim)

对于每个音调有一个固定的数字与之对应

### 从预测得到音乐


```python
def create_music(prediction):
    """
    用神经网络'预测'的音乐数据来生成 MIDI 文件，再转成 MP3 文件
    """
    offset = 0   # 偏移 有音符和弦和空白，偏移空白值
    output_notes = []

    # 生成 Note（音符）或 Chord（和弦）对象
    for data in prediction:
        # 是 Chord。格式例如： 4.15.7 有可能只有一个整数
        if ('.' in data) or data.isdigit():
            notes_in_chord = data.split('.')
            notes = []
            for current_note in notes_in_chord:
                new_note = note.Note(int(current_note))
                new_note.storedInstrument = instrument.Piano() # 乐器用钢琴
                notes.append(new_note)
            new_chord = chord.Chord(notes)
            new_chord.offset = offset
            output_notes.append(new_chord)
        # 是 Note
        else:
            new_note = note.Note(data)
            new_note.offset = offset
            new_note.storedInstrument = instrument.Piano()
            output_notes.append(new_note)

        # 每次迭代都将偏移增加，这样才不会交叠覆盖
        offset += 0.5

    # 创建音乐流（Stram）
    midi_stream = stream.Stream(output_notes)

    # 写入 MIDI 文件
    midi_stream.write('midi', fp='output.mid')

    # 将生成的 MIDI 文件转换成 MP3
    convertMidi2Mp3()
```

我的博客即将搬运同步至腾讯云+社区，邀请大家一同入驻：https://cloud.tencent.com/developer/support-plan?invite_code=36y07bigmmo0w
