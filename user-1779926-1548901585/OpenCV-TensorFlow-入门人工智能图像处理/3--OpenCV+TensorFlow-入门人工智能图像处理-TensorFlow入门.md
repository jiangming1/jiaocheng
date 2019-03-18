## tensorflow基础入门

思考一个问题:

如何刚好学习TensorFlow

- 类比为一门开发语言，学会语法，api的调用， 原理性掌握。

语言的要素:

>基础数据类型 运算符 流程 字典 数组

```python
import tensorflow as tf

# 常量，指定数据类型
data1 = tf.constant(2,dtype=tf.int32)

# 变量，指定变量名
data2 = tf.Variable(10,name='var')


print(data1)
print(data2)
```

![mark](http://myphoto.mtianyan.cn/blog/180325/4BjemCD574.png?imageslim)

并没有打印出数值，而是描述信息。

TensorFlow中所有的操作都得在会话中被执行。

```python
import tensorflow as tf

# 常量，指定数据类型
data1 = tf.constant(2,dtype=tf.int32)

# 变量，指定变量名
data2 = tf.Variable(10,name='var')


print(data1)
print(data2)

'''
sess = tf.Session()
print(sess.run(data1))

# 这里可以正确打印出data1

init = tf.global_variables_initializer()
sess.run(init)

# 但是当我们仍要用这个sess打印data2时会报错
# 对于变量数值，我们要进行初始化。
print(sess.run(data2))
sess.close()
# 本质 tf = tensor + 计算图
# tensor 数据
# op 
# graphs 数据操作
# session
'''
init = tf.global_variables_initializer()
sess = tf.Session()
with sess:
    sess.run(init)
    print(sess.run(data1))
    print(sess.run(data2))
```

![mark](http://myphoto.mtianyan.cn/blog/180325/8lG6lmmlf6.png?imageslim)

## TensorFlow运算实质

![mark](http://myphoto.mtianyan.cn/blog/180325/1IlJlkjcJi.png?imageslim)

tensorflow 由张量和计算图两部分构成。

常量变量都是张量。张量表示的就是数据。

op是operation。 常见的四则运算。赋值，两个tensor之间的加法。

op + tensor 构成了 graphs

```python
# 本质 tf = tensor + 计算图
# tensor 数据
# op 
# graphs 数据操作
# session
```

所有的操作都要在session中被run

分析tf代码:

- 先分析tensor张量有哪些。
- 再分析session中的计算图有哪些
- 这两个方面掌握清楚就好理解代码了

本质是数据 + 计算图

TensorFlow中所有的变量都必须1初始化之后才能进行。init操作实质仍然是一个计算图，
要使用session.tun(init)

直接使用完session 我们还需要close掉

- 或者使用with进行关闭

view linenumber 看到行号

## 常量与变量的四则运算

```python
import tensorflow as tf

# 两个常量
data1 = tf.constant(6)
data2 = tf.constant(2)

# 定义一个加法操作
dataAdd = tf.add(data1,data2)
# 定义一个乘法操作
dataMul = tf.multiply(data1,data2)
# 定义一个减法操作
dataSub = tf.subtract(data1,data2)
# 定义一个除法操作
dataDiv = tf.divide(data1,data2)

# 使用sess创建。with不需要close
with tf.Session() as sess:
    print(sess.run(dataAdd))
    print(sess.run(dataMul))
    print(sess.run(dataSub))
    print(sess.run(dataDiv))
print('end!')
```

![mark](http://myphoto.mtianyan.cn/blog/180325/h48CKa3e3A.png?imageslim)

```python
import tensorflow as tf
data1 = tf.constant(6)
# data2变量
data2 = tf.Variable(2)
dataAdd = tf.add(data1,data2)

# 完成当前的数据拷贝: 把当前dataAdd的结果追加到data2中
dataCopy = tf.assign(data2,dataAdd)# dataAdd ->data2

dataMul = tf.multiply(data1,data2)
dataSub = tf.subtract(data1,data2)
dataDiv = tf.divide(data1,data2)
init = tf.global_variables_initializer()
with tf.Session() as sess:
    sess.run(init)
    print(sess.run(dataAdd))
    print(sess.run(dataMul))
    print(sess.run(dataSub))
    print(sess.run(dataDiv))
    
    # 将6和2相加的结果放到data2中
    print('sess.run(dataCopy)',sess.run(dataCopy))#8->data2
    
    # 除过run方法还可以使用.eval()直接输出。eval方法相当于下面这句话
    print('dataCopy.eval()',dataCopy.eval())#8+6->14->data2 = 14
    
    # 获取默认的session，执行run操作
    print('tf.get_default_session()',tf.get_default_session().run(dataCopy))
print('end!')
```

运行dataCopy.eval()时，其中的data2已经是8了，然后加上常量6.输出14

而再次运行时data2已经变成了14.

计算图运行的两种方法:

- sess.run
- op.eval()

## 矩阵基础

前面的数据我们都是初始化的时候就定义好了，然而有时我们用到的数据是
需要在运算时实时插入的。

### placeholder

```python
import tensorflow as tf
data1 = tf.placeholder(tf.float32)
data2 = tf.placeholder(tf.float32)
dataAdd = tf.add(data1,data2)
with tf.Session() as sess:
    # 使用feed_dict为placeholder回填数据
    print(sess.run(dataAdd,feed_dict={data1:6,data2:2}))
    # 1：op dataAdd 2： data (feed_dict = {参数1:6,参数2:2})
print('end!')
```

### 矩阵运算

- 类比 数组 M行N列(二维数组) 
- 外层[] 内层嵌套[]  
- 每个内层中括号表示:[里面 列数据]   
- 每个内层[]整体作为一行 行数

```python
# [[6,6]] 一行两列的矩阵
import tensorflow as tf
# 一行两列矩阵
data1 = tf.constant([[6,6]])
# 两行一列矩阵
data2 = tf.constant([[2],
                     [2]])
# 一行两列
data3 = tf.constant([[3,3]])
# 三行两列
data4 = tf.constant([[1,2],
                     [3,4],
                     [5,6]])

print(data4.shape)# 打印矩阵的维度

with tf.Session() as sess:
    print(sess.run(data4)) # 打印整体
    print(sess.run(data4[0]))# 打印某一行
    print(sess.run(data4[:,0]))# M*N的矩阵，第一位是行，第二位是列。
    # 打印某一列，使用:表示所有行
    
    print(sess.run(data4[0,1]))# 1 1  下标从0开始算。但是shape是从1开始算的
```

## 矩阵运算2

一种矩阵加法，和两种矩阵乘法

矩阵在进行加法的时候，必须要保证两个矩阵的行列相等。

我们两个(2,3)的矩阵相加，相加之后仍然是一个(2,3)的矩阵

![mark](http://myphoto.mtianyan.cn/blog/180325/6bIJ2ID6dJ.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180325/ggbfced1B3.png?imageslim)

两个(2,2)的矩阵相加可以得到新矩阵

![mark](http://myphoto.mtianyan.cn/blog/180325/mADamfBLJ1.png?imageslim)

A+E 是由第一个矩阵中第一行第一列的值，加上第二个矩阵中第一行第一列的值。

以此类推，对应元素的加法

### 矩阵的乘法

![mark](http://myphoto.mtianyan.cn/blog/180325/E4FFCH3DIE.png?imageslim)

第一个矩阵的列数，必须等于第二个矩阵的行数，矩阵才可以进行相乘。

(m,k) 与 (k,n) 相乘可以乘出一个(m,n)的

左侧矩阵的第一行的对应元素乘以右侧矩阵的第一列的对应元素，构成新矩阵中(1,1)位置的元素。

>这里我们就可以看出为啥要左边的列，等于右边的行了，不然左边这第一行的元素个数(列数) 去右侧的矩阵中找不到对应的元素

### 代码实现

```python
import tensorflow as tf
data1 = tf.constant([[6,6]])
data2 = tf.constant([[2],
                     [2]])
data3 = tf.constant([[3,3]])
data4 = tf.constant([[1,2],
                     [3,4],
                     [5,6]])

# 矩阵相乘
matMul = tf.matmul(data1,data2)

# 直接使用简单乘法，对应元素相乘
matMul2 = tf.multiply(data1,data2)

# 矩阵相加
matAdd = tf.add(data1,data3)

with tf.Session() as sess:
    print(sess.run(matMul))# data1 (1,2) 乘以 data2(2,1) 得到结果(1,1)
    print(sess.run(matAdd))# 1行2列 加 1行2列 还是一行两列
    print(sess.run(matMul2))# 1x2 2x1 = 2x2 这里涉及到python的广播机制
    # [6,6] 和 [[2].[2]] 都会被补全成一个2x2的矩阵。然后对应元素相乘
    
    # 使用print一次打印多个内容
    print(sess.run([matMul,matAdd]))
```

## 特殊矩阵初始化

```python
import tensorflow as tf

# 两行三列零矩阵
mat0 = tf.constant([[0,0,0],[0,0,0]])

# 两行三列零矩阵
mat1 = tf.zeros([2,3])

# 三行两列一矩阵
mat2 = tf.ones([3,2])

# 两行三列填充矩阵(15)
mat3 = tf.fill([2,3],15)
with tf.Session() as sess:
    print(sess.run(mat0))
    print('************')
    print(sess.run(mat1))
    print('************')
    print(sess.run(mat2))
    print('************')
    print(sess.run(mat3))
```

![mark](http://myphoto.mtianyan.cn/blog/180325/Cam4Hd6dDB.png?imageslim)

### 一些更为特殊的矩阵

```python
import tensorflow as tf
# 三行一列的矩阵
mat1 = tf.constant([[2],[3],[4]])
# 与mat1具有相同的维度，但mat2内容为0
mat2 = tf.zeros_like(mat1)

# 将0-2之间数据分成相等的10份，中间有10个数据，要填11
mat3 = tf.linspace(0.0,2.0,11)

# 随机数矩阵，2行三列，数字为-1到2之间的随机数
mat4 = tf.random_uniform([2,3],-1,2)


with tf.Session() as sess:
    print(sess.run(mat2))
    print('************')
    print(sess.run(mat3))
    print('************')
    print(sess.run(mat4))
```

![mark](http://myphoto.mtianyan.cn/blog/180325/7dkF1j1jgK.png?imageslim)

## tf中numpy创建矩阵

类比数据库的学习CURD增删改查

```python
# CURD
import numpy as np

# 一维数组
data1 = np.array([1,2,3,4,5])
print(data1)
print('***************')

# 两行两列数组
data2 = np.array([[1,2],
                  [3,4]])
print(data2)
print('***************')

# 矩阵的维度
print(data1.shape,data2.shape)
print('***************')

# 初始化特殊矩阵 zeros ones
print(np.zeros([2,3]),np.ones([2,2]))
print('***************')

# 改查: 修改第二行第一列数据
data2[1,0] = 5
print(data2)
# 查询第二行第二列数据
print(data2[1,1])
print('***************')

# 基本运算:加减乘除

# 两行三列的全一矩阵
data3 = np.ones([2,3])

# 简单乘除加法
print(data3*2) # 对应每个元素乘2
print(data3/3) # 对应元素除以3
print(data3+2) # 所有元素加2
print('***************')

# 矩阵 +*

# 两行三列矩阵
data4 = np.array([[1,2,3],[4,5,6]])

# 矩阵对应位置元素相加
print(data3+data4)

# 矩阵对应位置元素相乘
print(data3*data4)
```

![mark](http://myphoto.mtianyan.cn/blog/180325/89ciee6hab.png?imageslim)

## Matplotlib绘图模块

使用numpy准备数据，Matplotlib进行可视化绘图

```python
import numpy as np
import matplotlib.pyplot as plt
# 设置在浏览器内显示
%matplotlib inline
# x轴坐标
x = np.array([1,2,3,4,5,6,7,8])
# y轴坐标
y = np.array([3,5,7,6,2,6,10,15])

# 绘制折线图，第一个参数x轴坐标，第二个参数y轴坐标。第三个参数颜色
plt.plot(x,y,'r')# 折线 1 x 2 y 3 color
plt.show()
# 第四个参数: 线条宽度
plt.plot(x,y,'g',lw=10)# 4 line w
# 折线 饼状 柱状

x = np.array([1,2,3,4,5,6,7,8])
y = np.array([13,25,17,36,21,16,10,15])

# 柱状图: color 是颜色， alpha 透明度
# 每个柱状图的占用比例
plt.bar(x,y,0.2,alpha=1,color='b')# 5 color 4 透明度 3 0.9
plt.show()
```

![mark](http://myphoto.mtianyan.cn/blog/180325/F1F8GcKED6.png?imageslim)

## 综合性demo

神经网络逼近股票收盘价格

结合TensorFlow实现一个综合性的例子

实现股票数据日线的绘制，并且用人工神经网络实现股票收盘价格的拟合

![mark](http://myphoto.mtianyan.cn/blog/180325/l7meEI6DaK.png?imageslim)

其中红色表示当天的股票是上涨的

蓝色表示股票是下降的。9点半开盘，下午三点收盘。

上涨代表: 收盘大于开盘价格。下跌相反

最低价会低于收盘价或开盘价。

### 数据加载和图表的绘制

```python
import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline

# 准备数据15组数据。(15天)
date = np.linspace(1,15,15)

# 每天的收盘价格
endPrice = np.array([2511.90,2538.26,2510.68,2591.66,2732.98,2701.69,2701.29,2678.67,2726.50,2681.50,2739.17,2715.07,2823.58,2864.90,2919.08]
)

# 每天的开盘价格
beginPrice = np.array([2438.71,2500.88,2534.95,2512.52,2594.04,2743.26,2697.47,2695.24,2678.23,2722.13,2674.93,2744.13,2717.46,2832.73,2877.40])

# 验证日期
print(date)

plt.figure()

for i in range(0,15):
    # 绘制 一个柱状图
    # x是一个一维数组两个值，对应两个y收盘开盘。
    dateOne = np.zeros([2])
    dateOne[0] = i;
    dateOne[1] = i;
    
    # 开盘价格和收盘价格
    priceOne = np.zeros([2])
    priceOne[0] = beginPrice[i]
    priceOne[1] = endPrice[i]
    
    # 绘制每一根柱状图，上涨红色，下降绿色
    if endPrice[i]>beginPrice[i]:
        # 日期和开收盘价格
        plt.plot(dateOne,priceOne,'r',lw=8)
    else:
        plt.plot(dateOne,priceOne,'g',lw=8)
    
plt.show()
```

![mark](http://myphoto.mtianyan.cn/blog/180325/f15jbmbh7d.png?imageslim)

## 实现最简单的人工神经网络

![mark](http://myphoto.mtianyan.cn/blog/180325/9K09JJdI81.png?imageslim)

输入: 15行一列的矩阵。

隐藏层矩阵 1行10列

输出层矩阵 15行一列

![mark](http://myphoto.mtianyan.cn/blog/180325/1ahgfKac5B.png?imageslim)

输入: 天数 输出: 每天股价

![mark](http://myphoto.mtianyan.cn/blog/180325/l2b9B4dgcJ.png?imageslim)

输入层与隐藏层进行连接

![mark](http://myphoto.mtianyan.cn/blog/180325/FBkIi3HAIa.png?imageslim)

输入矩阵A 乘以权重矩阵w1 + 偏置矩阵 构成了B

构成了我们的中间层矩阵。

矩阵B与第三层如何连接

```python
B*w2 + b2 = C
```

c就是我们的输出矩阵。

### 维度的变化

![mark](http://myphoto.mtianyan.cn/blog/180325/3cD9GHIF9g.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180325/H3eD4d74F4.png?imageslim)

w1维度: 一行十列
b1维度: 一行十列

w2维度: 十行一列
b2维度: 十五行一列

![mark](http://myphoto.mtianyan.cn/blog/180325/A3HhAE8K3F.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180325/0mbbD6563d.png?imageslim)

>加上b1, 会进行广播机制。`1*10`的b1会被扩展成15行10列。也就是每一行一样。
然后矩阵对应每个元素相加。

![mark](http://myphoto.mtianyan.cn/blog/180325/mIAKBLDDDD.png?imageslim)

神经网络内部如何进行计算和工作？

![mark](http://myphoto.mtianyan.cn/blog/180325/cm6Fc7e9Jm.png?imageslim)

给参数一些简单的初始值

计算出一个C值。而我们的真实股价2400 2511 

- 第二次循环"梯度下降法" 目的减少111

给出一个新的参数组合。

for循环的终止条件: 训练多少次之后，或者差异符合我们的要求

循环终止会输出最后的w1 w2 b1 b2

知道了 这些参数。我们就知道预测新的股价

## 代码实现

```
import tensorflow as tf
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
date = np.linspace(1,15,15)
endPrice = np.array([2511.90,2538.26,2510.68,2591.66,2732.98,2701.69,2701.29,2678.67,2726.50,2681.50,2739.17,2715.07,2823.58,2864.90,2919.08]
)
beginPrice = np.array([2438.71,2500.88,2534.95,2512.52,2594.04,2743.26,2697.47,2695.24,2678.23,2722.13,2674.93,2744.13,2717.46,2832.73,2877.40])
print(date)
plt.figure()
for i in range(0,15):
    # 1 柱状图
    dateOne = np.zeros([2])
    dateOne[0] = i;
    dateOne[1] = i;
    priceOne = np.zeros([2])
    priceOne[0] = beginPrice[i]
    priceOne[1] = endPrice[i]
    if endPrice[i]>beginPrice[i]:
        plt.plot(dateOne,priceOne,'r',lw=8)
    else:
        plt.plot(dateOne,priceOne,'g',lw=8)
# 输入的日期
dateNormal = np.zeros([15,1])
# 输出的价格
priceNormal = np.zeros([15,1])

# 进行归一化处理
for i in range(0,15):
    # 日期最大14
    dateNormal[i,0] = i/14.0;
    # 价格不会超出3000
    priceNormal[i,0] = endPrice[i]/3000.0;
    
# 一个n行一列的占位符
x = tf.placeholder(tf.float32,[None,1])
# 一个n行一列的占位符
y = tf.placeholder(tf.float32,[None,1])

# 隐藏层B
# w1是会被修改的。所以定义一个变量。
# 初值随机，一行十列，0到1之间
w1 = tf.Variable(tf.random_uniform([1,10],0,1))

# 偏置矩阵一行十列
b1 = tf.Variable(tf.zeros([1,10]))

# wb1也就是隐藏层的输出值是x乘以w1加上b1
wb1 = tf.matmul(x,w1)+b1
# 激励函数增加非线性元素。
layer1 = tf.nn.relu(wb1) # 激励函数

# 输出层
w2 = tf.Variable(tf.random_uniform([10,1],0,1))
b2 = tf.Variable(tf.zeros([15,1]))

wb2 = tf.matmul(layer1,w2)+b2
layer2 = tf.nn.relu(wb2) #完成简单映射

# 损失函数(真实值-预测值)的平方。开放出来的结果再进行一个均值
loss = tf.reduce_mean(tf.square(y-layer2))#y 真实 layer2 计算

# 梯度下降法，梯度下降法。参数为学习率，目标是最小化loss
train_step = tf.train.GradientDescentOptimizer(0.1).minimize(loss)

with tf.Session() as sess:
    # 初始化所有的全局变量
    sess.run(tf.global_variables_initializer())
    # 程序终止: for循环次数
    for i in range(0,10000):
        # x，y值填入归一化之后的x，y
        sess.run(train_step,feed_dict={x:dateNormal,y:priceNormal})
    
    # 进行预测验证
    # w1w2 b1b2  A + wb -->layer2 得到新的预测值
    pred = sess.run(layer2,feed_dict={x:dateNormal})
    predPrice = np.zeros([15,1])
    # 还原当前价格。（反归一化）
    for i in range(0,15):
        predPrice[i,0]=(pred*3000)[i,0]
    plt.plot(date,predPrice,'b',lw=1)
plt.show()
```

![mark](http://myphoto.mtianyan.cn/blog/180325/G57A9dKeC1.png?imageslim)










