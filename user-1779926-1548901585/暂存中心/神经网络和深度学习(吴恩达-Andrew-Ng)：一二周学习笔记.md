**机器学习:**
机器学习研究的是计算机怎样模拟人类的学习行为，以获取新的知识或技能，并重新组织已有的知识结构使之不断改善自身。简单的说，就是计算机从数据中学习规律和模式，以应用在新数据上做预测的任务。

### （一）深度学习概念

深度学习指的是训练神经网络，有时候规模很大。
#### linear regression 线性回归

回归函数，例如在最简单的房价预测中，我们有几套房屋的面积以及最后的价格，根据这些数据来预测另外的面积的房屋的价格，根据回归预测，在以房屋面积为输入x，输出为价格的坐标轴上，做一条直线最符合这几个点的函数，将它作为根据面积预测价格的根据，这条线就是回归线，对应回归函数。当然也应该有限制条件，价格总不能为0，所以直线不合适，在最初的阶段，应该是为0的横线，这样的经过修改的更符合实际的就可以作为判断依据了。这是一个最简单的神经网络了，只经过了一层也就是一个神经元，从输入面积，经过神经元到输出价格。


![线性回归-回归线](http://upload-images.jianshu.io/upload_images/1779926-5c127cb18da04456.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在很多神经网络的文献中，都会看到这样的函数，函数一开始是0，然后就是一条直线，这个函数就被称为ReLU函数：修正线性单元函数(**rectified linear unit**)。“修正”指的是取不小于0的值，这是一个单神经元网络，规模很小的神经网络，大一点的神经网络是把这些单个神经元堆叠起来形成的，形成一个更大的神经网络。

>假设现在预测房价不只是考虑大小，还有卧室数量，邮编，以及富裕程度，根据这是个条件来预测价格呢，那么就会形成一个更大的神经网络，如图：

![房价预测神经网络](http://upload-images.jianshu.io/upload_images/1779926-81f027027160fbca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在图中，x为4个特征输入，y为输出结果房价，中间的为隐藏层，其中第一层挨着x输入的为输入层，每个神经元都与4个输入特征有联系，把这些独立的神经单元堆叠起来，简单的预测器（神经元）形成一个更大的。神经网络的一部分神奇之处在于，当你实现了它之后，你要做的只是输入x，就能得到输出，不管训练集有多大，所有的中间过程，都会自己完成。要做的就是，这有四个输入的神经网络，输入的特征可能是面积，大小等等，已知这些输入的特征，神经网络的工作就是预测对应的价格。输入层的连接数最高，因为每个输入都连接到了中间的每个圆圈。神经网络只要你给足够多的数据关于x和y的数据，给到足够多的训练数据，神经网络非常擅长于计算从x到y的精准映射函数。

神经网络给了输入以及输出的训练数据，是一种**监督学习**。

几乎所有由神经网络创造的经济价值，都基于其中一种机器学习，我们称之为监督学习(supervisor learning)。在监督学习中，输入x，习得一个函数，映射到输出y。这里有一些其他例子,这些例子中神经网络效果超群。

也许通过深度学习获利最大的就是**在线广告**，给网站输入广告信息，网站会考虑是否给你看这个广告，有时还需要输入一些用户信息。神经网络在预测你是否会点击这个广告方面，已经表现的很好，通过向你展示，向用户展示最有可能点开的广告。

**计算机视觉**，你输入一个图像，然后想输出一个指数，可以是从1到1000来表明这些照片是1000个不同的图像中的某一个，可以用来给照片打标签。

**语音识别**，你可以把一段音频输入神经网络，可以输出文本。**机器翻译**进步也很大，输入英语句子，直接输出一个中文句子。在无人驾驶技术中，你输入一副图像，汽车前方的一个快照，还有一些雷达信息，基于这个，训练过的神经网络能告诉你路上其他汽车的位置，这是**无人驾驶系统**的关键组件。要机智的选择x和y，才能解决特定问题，然后把这个监督学习过的组件嵌入到更大型的系统中，比如无人驾驶。

可以看出稍微不同的神经网络应用到不同的地方，都行之有效。**图像领域**里：我们经常用的是**卷积神经网络(cnn)**。对于**序列数据**，例如音频中含有时间成分，音频是随着时间播放的，所以音频很自然的被表示为以为时间序列。对于序列数据，经常使用**循环神经网络（RNN）- recurrent neural network**。

**语言**最自然的表示方式也是序列数据，语言，汉语，英语，单词和字母都是按序列出现的，更复杂的RNN经常会用于这些应用。对于更复杂的无人驾驶，输入一张快照，需要使用卷积神经网络架构去处理，雷达信息更不一样，可能需要更复杂的混合的神经网络结构。

#### 结构化数据和非结构化数据
所以，为了更好的说明标准的CNN和RNN结构，所以在文献中，你可以看到标准神经网络和卷积神经网络，卷积网络通常用于图像处理。循环神经网络很好的处理一维时间序列数据，其中包含时间成分。机器学习还可能运用于结构化数据和非结构化数据。

- 结构化数据：数据的数据库，比如在房价预测中，你可能有一个数据库或者数据列，告诉你房间的大小和卧室的数量，这就是结构化数据，意味着**每个特征都有着清晰的定义**。
- 非结构化数据：比如音频，原始音频，图像。你要表示图像或文本中的内容，这里的特征，可能是图像中的像素值，或者文本中的每个单词。**非结构化数据相对于结构化数据，计算机理解起来更难**。

#### 神经网络应用
神经网络的发展，就是让计算机更好的能理解非结构化数据。语音识别，图像识别，自然语言文本处理。神经网络在非结构化数据上的成功，主要是媒体领域。神经网络在很多短期经济价值的创造是基于结构化数据的，比如更好的广告系统，更好的获利建议，有更好的能力去处理很多公司拥有的海量数据库，并且这些数据有准确的预测未来的能力。

>监督学习神经网络应用领域:

![监督学习神经网络应用领域](http://upload-images.jianshu.io/upload_images/1779926-53489903370cce17.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>三种神经网络：标准神经网络   卷积神经网络   循环神经网络

![三种神经网络：标准神经网络   卷积神经网络   循环神经网络](http://upload-images.jianshu.io/upload_images/1779926-fd9b0c248f2f1b90.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 神经网络流行原因
![神经网络发展的原因](http://upload-images.jianshu.io/upload_images/1779926-4fc03112c13b303c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图: 在前面数据量很小的时候，算法之间性能差异不是很明显。到了大数据段，神经网络的作用就很明显了。

训练出一个很好的神经网络，重要的条件是：
- 规模大的数据量m（横轴）
- 以及很好的计算训练能力。

![规模促进了机器学习](http://upload-images.jianshu.io/upload_images/1779926-d648f46edf7a784d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

关于机器学习发展，重要的原因，**海量数据，计算速度，算法改进**。

关于算法改进，许多算法方面的创新都是为了让神经网络运行的更快，举一个例子，神经网络方面一个巨大的突破是，从**sigmoid**函数转换到这样的**ReLU**函数，使用sigmoid函数机器学习的问题是，在这个函数后部分，sigmoid函数的斜率梯度会接近0，所以学习会变得非常缓慢，因为用梯度下降法时，梯度接近0时，参数会变化的很慢，学习也会变的很慢，而通过改变激活函数，神经网络用ReLU函数(修正线性单元函数)，它的梯度对于所有为正值的输入输出都是1，因此梯度不会逐渐趋近于0。为这里的梯度，这条线的斜率，在这左边是0，我们发现，只需将sigmoid函数转换为ReLU函数便能够使得"梯度下降法"运行的更快，这就是一个例子关于算法创新。其目的就是**增加计算速度**。

### （二）神经网络基础
#### 2.1 二分分类
当你要构建一个神经网络，有些技巧是相当重要的。例如，m个样本的训练集，你可能会习惯性的去用一个for循环来遍历这m个样本，但事实上，实现一个神经网络，如果你要遍历整个数据集，并不需要直接使用for循环。还有就是，神经网络的计算过程中中，通常有一个正向过程，或者**正向传播步骤**，接着会有一个反向过程，也叫**反向传播步骤**。

**（1）二分分类案例：**
使用logistic回归来阐述，以便能更好的理解。
logistic回归是一个用于二分分类的算法。例如举个例子，输入一张图像，我们要输入y，是猫(1)或者不为猫(0)。计算机保存一张图片，要保存三个独立矩阵，分别对应图片中的红绿蓝三个颜色通道，如果输入图片是`64*64`像素的，就有三个`64*64`的矩阵,分别对应图片中的红绿蓝三个像素的亮度。为了方便表示，这里用三个小矩阵`5*4`的来表示，要把这些像素亮度值放进一个特征向量中，就要把这些像素值都提出来，放入一个特征向量x。为了把这些像素值取出来放入特征向量，就要像下面一样**定义一个特征向量x**以表示这张图片，我们把所有的像素值都取出来，把三层矩阵上的**所有数都放入一个向量**中，最后得到一个**很长的特征向量**，把图片中所有的红绿蓝像素强度值都列出来。

如果图片是`64*64`的，那么向量x的总维度为`64*64*3`，因为这是三个矩阵的元素数量，乘出来结果为12288,我们用**Nx=12288来表示输入特征向量x的维度**，有时候为了简洁，我们直接用小写的n，来表示特征向量的维度。

![案例，判断图片是否有猫](http://upload-images.jianshu.io/upload_images/1779926-5e221cd4ec5ea523.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![输入特征x的表示](http://upload-images.jianshu.io/upload_images/1779926-17c574efe016e86d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在**二分分类**问题中，目标就是训练出一个分类器，它以图片的特征向量x作为输入，预计输出的结果标签y是1还是0，也就是说，预测图片中**是否有猫**。

**（2）相关符号：**
后面课程中需要用到的符号。
用一对(x,y)来表示一个独立的样本，x是Nx维的特征向量，标签y: 值为1或0。训练集由m个训练样本构成，`（x^(1),y^(1)）`表示样本一的输入和输出，`（x^(2),y^(2)）`表示样本二的输入和输出，`（x^(m),y^(m)`）表示最后一个样本m的输入和输出，`{（x^(1),y^(1)）....（x^(m),y^(m)）}`这些一起就表示整个**训练集**。m表示整个训练集的样本数，有时候为了强调这是训练样本的个数，可以写作`m=m_train`，当说到测试集时，可以用m_test来表示测试集的样本数。最后用更紧凑的符号表示训练集，定义一个矩阵，用大写X来表示，它由训练集中的x1，x2这些组成，像这样写成矩阵的列。每个训练集`x^(1)..x^(m)`则分别为这个矩阵的1到m列，所以这个矩阵有m列，m是训练集的样本数，这个矩阵的高度记为Nx。要注意的是，有时候X的定义，训练数据作为行向量堆叠，而不是这样的列向量堆叠。但是**构建神经网络时，用列向量堆叠这个约定形式，会让构建过程简单的多**。

总结一下，X是一个`Nx*m`的矩阵，当用python实现时，会看到`X.shape`，这是一条python命令，用来输出矩阵的维度，即`（nx，m）`，表示X是一个`nx*m`的矩阵，这就是如何将训练样本，即输入x用矩阵表示，那输出标签y呢，同样为了简单的构建一个神经网络，将y标签也放入列中，`Y = [y^(1),y^(2),...y^(m)]`,这里的Y是一个`1*m`的矩阵，同样的，在python里面，Y.shape等于（1，m）。后面的课程中你会发现，好的习惯符号能够将不同训练样本的数据联系起来，这里说的数据，不仅有x和y还会有其他的量。将不同的训练样本数据取出来，放到不同的列上，就像刚才处理x和y一样。在logistic回归和神经网络，要用到的符号就是这些了。

![符号表示](http://upload-images.jianshu.io/upload_images/1779926-9157d23a44721b36.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 2.2 logistc回归
这是一个学习算法，用在监督学习中，输出y标签是0或1时，这是一个二分分类的问题。已知输入特征向量是x，可能是一张图，你希望把识别出这是不是猫图，你需要一个算法，可以给出一个预测值，y hat（y帽子），就是你对y的预测。更正式的说，你希望y hat是一个概率，当输入特征满足条件时，y就是1，所以换句话说，如果x是图片，例如之前判读图片是否有猫的例子，你希望y是一张图片是猫图的概率，x是一个nx维的向量，已知logistic回归的参数是w，也是一个nx维向量，而b就是一个实数，所以已知输入x和参数w和b，如何计算y hat。
也许可以尝试，y = w^T * x + b, 一个输入x的线性函数，事实上，如果你做线性回归，就是这么算的，但这并不是一个非常好的二元分类算法，因为你希望y hat 是y=1的概率，而不是计算y的值，所以y hat应该介于0到1之间，但实际上很难实现，因为`w^T*x+b`可能比1大得多，甚至是负值，这样的概率是没有意义的。所以在logistc回归中，我们把**输出变成y hat等于sigmoid函数作用到这个量y上**，这就是sigmoid函数的图形，横轴是Z，函数是从0到1的光滑函数，与数轴的交点在0.5处，这就是sigmoid(z)的图形，用z来表示w^T * x + b这个量。

![sigmoid函数公式](http://upload-images.jianshu.io/upload_images/1779926-b421211683345106.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![sigmoid函数图像](http://upload-images.jianshu.io/upload_images/1779926-1063a67429d50d6e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

要注意的是，如果z非常大，那么e^(-z)就很接近0，那么sigmoid(z)就是1/（1+某个接近0的量），所以这就接近1。相反如果z非常小，或者是非常大的负数，那么sigmoid(z)就很接近于0。 

所以当你要实现logistic回归时，你要做的是学习参数w和b，这样y hat 就变成了比较好的估计。对y=1的比较好的估计。当我们对神经网络编程时，我们通常会把w和b分开，这里b对应一个**拦截器**，在其他课程中，可能看到过不同的表示。在一些符号约定中，定义一个额外的特征向量x0并且等于1，所以出现x就是R^(nx+1)维向量，然后将y hat定义为`σ(θ^T*X)`，在这种符号定义中，出现了一个向量参数θ,这个θ是由向量[θ0，θ1，θ2...θnx]组成的列向量。所以θ0就是扮演的b的角色，这是一个实数，而θ1直到θnx的作用和w一样。**事实上，当你实现你的神经网络时，将b和w看成独立的参数可能更好。**

![logistic回归模型与参数，以及sigmoid函数](http://upload-images.jianshu.io/upload_images/1779926-16370652616728eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![参考资料1](http://upload-images.jianshu.io/upload_images/1779926-0f9d2835f960e0d6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![参考资料2](http://upload-images.jianshu.io/upload_images/1779926-56dad4813fdec20f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2.3 logistc回归损失函数
为了训练logistic回归模型的参数w和b，需要定义一个**成本函数**。为了让模型通过学习调整参数要给一个m个样本的训练集，通过训练集，找到参数b和w，来得到你的输出，在训练集上得到预测值，是预测值y hat(i) 更接近于训练集得到的y^(i)。
为了让上面的方程更详细一点，需要说明上面这里定义的y hat（即y头上有个^这个符号），是对一个训练样本x来说的，当然是对每个训练样本，我们使用这些带有圆角的符号方便引用说明，来区分样本。你的训练样本(i),对应的预测值为通过sigmoid函数获得的y hat^(i),通过函数作用到`W^T*x(i)+b`得到的，所以符号约定就是这个上标(i)来指明数据。

**loss function**： 损失函数，也叫作误差函数，他们可以用来衡量算法的运行情况，你可以定义损失为y hat和真的类标y的差平方的一半，但通常在logistic回归中，大家都不这么做，**因为当你学习这些参数时候，会发现优化问题会变成非凸的，最后会得到很多个局部最优解**，所以梯度下降法(gradient descent)可能找不到全局最优值。我们通过定义损失函数L来衡量你的预测输出值y hat和y的实际值有多接近。误差平方看起来似乎是一个合理的选择，但是用这个的话，梯度下降法就不会很有效。在logistic回归中，我们会定义一个不同的损失函数，它有着与误差平方相似的作用，这会给我们一个凸的优化问题，就很容易去做优化。在logistic回归中，我们用到的损失函数如下：

![logstic回归中用到的损失函数L](http://upload-images.jianshu.io/upload_images/1779926-e882c1aec328d248.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

直观地看看为什么这个损失函数能起作用，假设我们使用误差平方越小越好，对于这个logistic回归的损失函数，同样的我们也让它尽可能的小。

![损失函数L](http://upload-images.jianshu.io/upload_images/1779926-2b38fe95c77bb7e6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

-  当y=1时，我们要使y hat尽可能大，但这是由sigmoid函数估计出来的值，不会超过1，所以要尽可能接近1
- 当y=0时，要使y hat尽可能小，但是不会小于0，所以尽可能接近0。

损失函数是在单个训练样本中定义的，它衡量了在单个训练样本中的表现。接下来要定义一个**成本函数cost function，它衡量的是在全体训练样本上的表现**，这个成本函数J根据之前得到的两个参数w和b，J(w,b)
即所有训练样本的损失函数和。而y hat是用一组特定的参数w，b通过logistc回归算法得出的预测输出值。从而成本函数把L带入得到以下形式：
![成本函数J(w,b)最终形式](http://upload-images.jianshu.io/upload_images/1779926-0d188e9eb4014435.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

损失函数只适用于像这样的单个训练样本，而成本函数基于参数的总成本，所以在训练logistic回归模型时，我们要找到合适的参数w和b，让这里的成本函数J尽可能的小。
综上所述，可以得到logistic回归可以被看成一个非常小的神经网络。

#### 2.4 梯度下降法
如何使用梯度下降法来训练或学习训练集上的参数w和b，成本函数只能用来说明你选择的w和b作用效果好不好，最后误差大不大，参数w和b在训练集上的效果，但是我们不知道一个w和b的关系，如何取值，发现一组不好后如何确定下一组取值，这就需要梯度下降法来研究如何训练w和b使得成本函数最小。

![梯度下降法](http://upload-images.jianshu.io/upload_images/1779926-84f59d890c78579a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个图中的横轴表示空间参数w和b，在实践中，w可以是更高维的，但为了方便绘图，我们让w是一个实数，b也是一个实数，成本函数J(w,b)是在水平轴上w和b上的曲面，曲面的高度J(w,b)在某一点的值，我们想要做的就是找到这样的w和b使其对应的成本函数J值，是最小值。
可以看出，成本函数J是一个凸函数，和非凸的函数不一样，非凸函数有很多的局部最优，因此，我们使用为凸函数的J(w，b)，**凸函数的这个性质是我们为啥使用这个特定的成本函数J做logistic回归一个很重要的原因**。为了找到更好的参数值，我们要做的就是用某初始值，初始化w和b。对于logistic回归而言，几乎是任意的初始化方法都有效，通常用0来初始化，但人们一般不这么做，但是因为函数是凸的，无论在哪里初始化，都应该到达到同一点，或大致相同的点。梯度下降法所做的就是，从初始点开始，朝最抖的下坡方向走一步，在梯度下降一步后，也许就会停在碗的最低端，因为它试着沿着最快下降的方向往下走，这是梯度下降的一次迭代。两次迭代可能就到了最低点，或者需要更多次，隐藏在图上的曲线中，很有希望收敛到这个全局最优解，或接近全局最优解，这张图片阐述了梯度下降法。

![梯度下降法中参数更新规则](http://upload-images.jianshu.io/upload_images/1779926-e2180c68d3bc4947.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为了更好的说明如何学习w和b，我们将图像的横轴只有w，变成二维坐标轴，图像如左图，右图的公式代表w如何变化的公式。
**α表示学习率，学习率可以控制每一次迭代，或者梯度下降法中的步长**，后面会讨论，如何选择学习率α；其次d(J(w))/dw这个数是导数(derivative)，这就是对参数w的更新或者说变化量。

当我们开始编写代码，来实现梯度下降，我们会使用到代码中变量名的约定，dw用来表示导数，作为导数的变量名，那么`w:=w-α*dw `（:=代表变化取值），现在我们确保梯度下降法中更新是有用的。

记住导数的定义，是函数在这个点的斜率。对于一开始就很大的参数w来说，每更新一次就会向左移动，向最小值点更靠近，同样的，假设w很小，在最小值的左边，那么斜率为负值，每次迭代就是w加上一个数，也会逐步的向最小值的w0靠近。

我们想知道，用目前参数的情况下函数的斜率朝下降速度最快的方向走。我们知道，为了让成本函数J走下坡路，下一步更新的方向在哪。当前J(w)的梯度下降法只有参数w，在logistic回归中，你的成本函数是一个含有w和b的函数，在这种情况下，**梯度下降的内循环就是这里的这个东西**，你必须重复的计算，通过`w:=w-α*dw【d(j(w,b)/dw)】`更新w，通过`b:=b-α*d(J(w,b))/db`更新b。这两个公式是实际更新w和b时所作的操作。当然这里的符号d微分也可以是偏导数花哨的α，表示的是函数在w方向的斜率是多小，当函数有两个以上的变量时，应该使用偏导数符号，计算函数关于其中一个变量的在对应点所对应的斜率。

#### 2.5 导数
#### 2.6 更多关于导数的例子
这两节主要就是关于微积分，导数的内容，通过举例f(x)=3x, f(x)=x^2, f(x)=x^3来说明函数上某一点的导数其实就是在该点的斜率，直线上点的斜率处处相等，但是对于其他非直线的函数，每个点的斜率即导数可能都不相同。
对于f(x)=x^2, f(x)=2x, 当x=2时，f(2)=4, 将x向后移动一点点，比如x=2.001，对应的f(x)=8.004002 约为8.004，发现f(x)增量与x的增量相比，结果为`8.004/2.001=4=2*2`，这里f(x)我们只是近似的等于4倍，实际上，按照导数的定义，是在x点出，增加一个无穷小量，0.001的增量很明显不能表示无穷小，因此在该点处的切线的斜率就为2x。


#### 2.7 计算图
可以说，一个神经网络的计算都是按照前向或者反向传播过程来实现的，首先计算出，神经网络的输出，紧接着进行一个反向传播过程，后者我们用来计算出对应的梯度或者导数，以下流程图解释了，为什么要用这样的方式实现。
为了阐明这个计算过程，举一个比logistc回归更加简单的、不那么正式的神经网络的例子。我们尝试计算函数J,它是关于三个变量a,b,c的函数
`J(a,b,c) = 3(a+b*c)`
计算这个函数，实际上有三个不同的步骤：
1. 计算`b*c`，存在在变量u中，`u=b*c`
2. 计算a+u，存在变量v中，v=a+u
3. 最后输出J，J=`3*v`
我们可以把这个计算过程划成如下的流程图：

![`J(a,b,c)=3(a+b*c)` 计算流程图](http://upload-images.jianshu.io/upload_images/1779926-8bd10dd5a908ef05.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这种流程图用起来很方便，有不同的或者一些特殊的输出变量时，比如我们想要优化的J。在logistc回归中，J是想要最小化的成本函数，可以看出，通过一个从左到右的过程，你可以计算出J的值，计算导数就是一个从右到左的过程，刚好与从左到右传播的过程相反。

#### 2.8 计算图的导数计算
在使用计算图计算出J的值后，我们研究如果通过计算图计算出函数J的导数，在上个例子中，要计算J对v的导数，怎么做？
在反向传播术中，我们看到如果你想计算最后输出变量的导数，使用你最关心的变量对v的导数，dJ/dv=3, 那么我们就做完了一步反向传播。
那么关于dJ/da呢，也就是说改变a，对J的数值有什么影响呢
```
a=5时，v为11，J为33
a=5.001时，v为11.001,J为33.003
```
那么增加a，如果你把这个5换成某个新值，那么a的改变就会传播到流程图的最右，所以J最后是33.001。所以J的增量是3乘以a的增量，意味着这个导数是3。换句话说，如果你改变了a，那么就改变了v，通过改变v，又会改变J，所以J值的净变化量，当你提升这个值，当你把a值提高一点点，这就是J的净变化量。关于a影响v，v影响J，这在微积分里叫做链式法则，所以
```
dJ/da = dJ/dv * dv/da
```
这样我们就完成了另一步反向传播。


介绍一个新的符号约定，当你编程实现反向传播时，通常会有一个最终输出值是你要关心的，最终的输出变量，你真正想要关心的或者说优化的。在例子中，最终的输出变量是J，就是流程图的最后一个符号，所以有很多的计算尝试计算输出变量的导数，所以d输出变量对于某个变量的导数，我们就用d var命名。因为在python代码里，可以用d(finalvar)/dvar,例如dJ/dvar，但是在这个反向传播过程中，我们都是在对最终变量求它的导数，因此就用dvar来表示这个整体，所以在编程时候，我们就用d var来表示J关于对代码中各种中间量的导数。

#### 2.9 logistc回归中的梯度下降法
在本节我们将讨论如何计算偏导数来实现logistic回归的梯度下降法，它的核心关键点，其中有几个重要的公式用来实现logistc回归的梯度下降法，将使用导数流程图来计算梯度。使用计算图来计算logistc回归的梯度有点大材小用了。

回想一下logistc回归的公式，y hat或者说a的定义是预测出来的值，是logostic回归的输出；y是样本的基本真值即标签值，并且a=sigmoid(z),而z=w^T+b，现在只考虑单个样本的情况，关于该样本的损失函数如第三个公式，L(a,y)是关于y和y hat的函数，实质是w和b。现在写出该样本的偏导数流程图如图2。
![logistc回归关于单个样本的公式集 图1](http://upload-images.jianshu.io/upload_images/5355764-4b073db21781c395.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

假设样本只有两个，特征值x1，x2，为了计算z，我们需要输入参数w1,w2，b，还有样本特征值x1，x2，这几个都是用来计算z的，`z=x1*w1+x2*w2+b`，接下来再计算a即y hat，a=sigmoid(z),最后计算L(a,y)。
![logistc回归中的计算图 图2](http://upload-images.jianshu.io/upload_images/5355764-fe59468351b32e7a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因此我们在logistc回归中，需要做的就是变换参数w和b的值来最小化损失函数，在前面我们已经经过前向传播步骤在单个训练样本上，计算损失函数，接下来讨论，如何向后传播来计算偏导数，其实就是根据链式求导法则，把每一个变量的偏导数求出来。
```
偏导数求出来如下：
da = dL/da = -y/a + (1-y)/(1-a)
dz = dL/dz = dL/da * da/dz  = a - y
这里需要说明，关于 a=sigmoid(z),如何求da/dz，按正常求导法则得到，求导结果为 e^(-z) * 1/(1+e^(-z)), 发现与原始函数相同，将a带入，因此最后结果，关于sigmid函数求导结果为a(1-a),即da/dz=a(1-a)

向后传播的最后一步，就是看w和b怎么变化
dw1 = dL/dw1 = dL/dz * dz/dw1 = x1 * dz
dw2 = dL/dw2 = dL/dz * dz/dw2 = x2 * dz
db = dz
```

![单个样本logistc回归反向传播手写图](http://upload-images.jianshu.io/upload_images/5355764-c3d296089b684f78.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


因此这就是关于单个样本的梯度下降法，你所需要做的就是这些事情，使用这个公式计算dz，dw1，dw2，db，然后更新w1为 `w1=w1-dw1*α（学习率）`，类似的，更新w2，更新b为`b=b-db*α`, 这就是单个样本实例的一次梯度更新步骤。

现在你知道了怎么计算导数，并且实现了单个训练样本的logistc回归的梯度下降法，但是训练logistc回归模型，不仅仅只有一个训练样本，而是有m个训练样本的整个训练集。下一节将讲这些想法如何应用到整个训练样本集中，而不仅仅只是单个样本。

#### 2.10 m个样本的梯度下降
在之前的视频中，已经看到如何计算导数和把梯度下降法应用到logistc回归的一个训练样本上，现在我们想要把他应用在m个训练样本上 。首先，时刻记住有关于成本函数J(w,b)的定义，它是这样的一个平均值，m个样本的损失求和的平均值。


![成本函数 & 输出预测值y冒](http://upload-images.jianshu.io/upload_images/1779926-00d1633e151a3024.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![上述公式说明](http://upload-images.jianshu.io/upload_images/1779926-4e93b93761ca313d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![全局梯度值是样本梯度值的平均数](http://upload-images.jianshu.io/upload_images/1779926-790f10ed32940d69.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![总算法](http://upload-images.jianshu.io/upload_images/1779926-e6589cf679a9015e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![总算法-2](http://upload-images.jianshu.io/upload_images/1779926-6c8ed5e63685ae75.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这里就是循环的结束了，最终对所有的m个样本都进行了这个计算，还需要除以m，因为我们计算平均值。


![没有上标-全局 & 有上标-单个样本](http://upload-images.jianshu.io/upload_images/1779926-a9c4a5d62fc79e20.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![梯度下降更新参数](http://upload-images.jianshu.io/upload_images/1779926-32a58ce1adae4fc9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![logistc回归 导数计算](http://upload-images.jianshu.io/upload_images/5355764-7d38266b40fa5ed5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这些公式只应用了一次梯度下降法，因此你需要重复以上的内容很多次，以应用多次梯度下降，因为整个过程执行一次，说明在梯度下降J图中，参数得到了一次更新，但是要到达最低点，还要继续更新，而更新的内容来自于，将更新后的带回原公式中进行计算，z的值，a的值都会改变，此时J的值也会更新，直到最后参数不再更新，J到达最低点。

虽然细节看起来有点复杂，但是在编程的过程中，思路都会变得很清晰，但它表明计算中有两个缺点: 
- 当应用在这里的时候,就是说引用此方法到logistc回归你需要编写两个for循环，第一个for循环是遍历m个训练样本的小循环，第二个for循环是遍历所有特征的for循环。


![for循环](http://upload-images.jianshu.io/upload_images/1779926-aa0cee50bb9b25da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当你应用深度学习算法，你会发现在代码中显示的使用for循环会使算法的效率很低，同时在深度学习领域，会有越来越大的数据集，所以能够应用你的算法完全不显示for循环的话是很有用的，可以帮你处理更大的数据集，有一门**向量化技术**帮助你的代码，摆脱这些显示的for循环。向量化技术有时用来加速运算，但有时候也未必能够，但是在深度学习时代，用向量化来摆脱for循环已经变得相当重要，因为我们开始处理越来越大的数据集，你的代码需要变得非高效。接下来将了解向量化技术，使得在logistc回归中应用梯度下降法而不需要for循环。

####2.11 向量化vectorization
什么是向量化：


![非向量化方法](http://upload-images.jianshu.io/upload_images/1779926-ac35442c65f1d18c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以这是一个非向量的实现方法，你会发现这是真的很慢，作为对比，


![向量化方法](http://upload-images.jianshu.io/upload_images/1779926-755410836b3fe7f5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
# _*_ coding: utf-8 _*_
import numpy as np
a = np.array([1,2,3,4])
print a

# 完成向量化的例子 引用time模块是为了计算两次不同的操作花费了多少时间
import time
#它能创建一个数组a，通过rand函数随机得到
#用随机值创建了一个百万维度的数组,b为另外一个百万大小的数组
a = np.random.rand(1000000)
b = np.random.rand(1000000)

#现在tic记录一下当前时间
tic = time.time()
c=np.dot(a,b)
toc = time.time()
print c
print ('这是向量化的版本'+str(1000*(toc-tic))+"ms")

c=0
tic = time.time()
for i in range(1000000):
    c+=a[i]*b[i]
toc = time.time()
print c
print ('这是for loop的版本'+str(1000*(toc-tic))+"ms")

#可以发现向量化和非向量化计算出来的结果是一样的，但是最终花费的时间却相差很大
```
![向量化效果截图：](http://upload-images.jianshu.io/upload_images/5355764-1349cb926f3a05a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当你正在实现深度学习算法，使用向量化技术真的可以更快得到结果，向量化之后，运行速度会大幅提升，你可能听说过，可扩展深度学习实现是在GPU上做的，**GPU也叫图像处理单元**(graphics processing unit)。我们普通写的代码都是在cpu上运行的，cpu和gpu都有并行化的指令，有时候叫做SIMD指令，意思是指单指令流多数据流，这个词的意思是如果你使用了这样的内置函数，如np.function或者其他能让你显示去掉for循环的函数，这样python的numpy能够充分利用并行化去更快的计算，这点对gpu和cpu上面计算都是成立的。Gpu更加擅长SIMD计算，但是cpu事实上也不是太差，只是没有gpu那么擅长。
经验法则是：**只要有其他可能，就不要显式使用for循环。**

####2.12 向量化vectorization的一些其他例子


![例子](http://upload-images.jianshu.io/upload_images/1779926-5e3efcf7dbdb9a8f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

非向量化版计算：
```python
u = np.zeros((n,1))    
np.zeros这个函数意思是构造相应数据类型的数据，这里是一个n维一列的向量，用0填充
for i in ...
    for j in ...
        u[i] += A[i][j]*v[j]
#这是一个双重for循环，对指标i和j
#而向量化实现：
u = np.dot(A,v)
```
（2）假设你内存中已经有一个向量v，如果你想做**指数运算**，作用到向量v的每一个元素，那么非向量的做法: 先初始化一个全0向量u，然后再用for循环，对v中每一个元素做指数运算再放入u中，一次计算一个元素。但事实上numpy模块中有很多内置函数，可以完成运算，只需要调用一个函数，`u=np.exp(v)`则完成了。

Numpy库有很多向量值函数，例如`np.log(v)`会逐个元素计算log值，`np.Abs(v)`会计算绝对值，`np.maximum(v,0)`计算所有元素中的最大值，求出v中所有元素和0相比的最大值，`v**2`就是计算v中每个元素的平方，`1/v`就是每个元素求倒数等等。

所以每当你想写一个for循环时，应该看看可不可以调用numpy，用**内置函数**计算，而不是用for循环，
接下来看看如何把这些技巧应用到logistc回归梯度下降算法实现中来，看看是否可以去掉两个for循环中的一个.

![logistc回归，两个循环简化为一个循环过程](http://upload-images.jianshu.io/upload_images/5355764-ceade39b946cb5c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![向量化处理方法](http://upload-images.jianshu.io/upload_images/1779926-83d2074ce713d1ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####2.13 向量化logistc回归

这一节我们将谈及向量化是如何实现在logistc回归上面的，这样就能同时处理整个训练集来实现梯度下降法的一步迭代，针对整个训练集的一步迭代不需要使用任何显式for循环。


![省掉外部m个样本的循环](http://upload-images.jianshu.io/upload_images/1779926-29e41cac4021ab14.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![m个样本计算预测值](http://upload-images.jianshu.io/upload_images/5355764-b97176b76e214fab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![训练输入矩阵](http://upload-images.jianshu.io/upload_images/1779926-8428c7fc21ff956f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



![z为行向量，每个值为z(i)](http://upload-images.jianshu.io/upload_images/1779926-4bb95c427e495a30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

X是把所有的训练样本堆叠起来得到的，一个挨着一个，横向堆叠，Z则是一个一维行向量，使用每个计算出来的横向排在一起，最后为了计算Z，直接使用numpy的指令：


![计算z的指令](http://upload-images.jianshu.io/upload_images/1779926-1f6574f32ffd5165.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在这里b是一个实数，或者说是一个`1*1`的矩阵，就是一个普通的实数，但是当向量加上这个实数时，pyhton会自动的把b这个实数扩展成一个`1*m`的向量，在**python中这叫做广播**。


![激活函数将z转换为A](http://upload-images.jianshu.io/upload_images/1779926-a026ac59f912d22a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

总的来说，不需要for循环就可以从m个训练样本一次性计算出所有样本的z值和a预测值，只需要运行两行代码就可以高效计算出结果，以上就是正向传播一步迭代的向量化实现，同时处理m个训练样本，接下来你会发现使用向量化也可以高效的计算反向传播过程，计算出梯度。

####2.14 向量化logistc回归的梯度输出

如何使用向量化计算m个训练数据的梯度，注意是同时计算，最后得到一个非常高效的logistc回归的实现。
在计算梯度时，我们需要计算：


![dz的计算](http://upload-images.jianshu.io/upload_images/1779926-c3765182c8842337.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![dz的向量化计算](http://upload-images.jianshu.io/upload_images/1779926-3b1c3ac5d55c343a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![特征分量使用转换为向量](http://upload-images.jianshu.io/upload_images/1779926-93e9b0a950ab9fec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![python下·实现](http://upload-images.jianshu.io/upload_images/1779926-87bf7ec1d5aecbb2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![使用向量计算](http://upload-images.jianshu.io/upload_images/1779926-a7ff1a65d95d55b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在回顾之前的计算logistc回归的整个过程，没有向量化非常低效，而没有使用任何for循环，最后代码计算只需要几步就完成了：


![没有for循环的代码完成](http://upload-images.jianshu.io/upload_images/1779926-7abc403f69ec8403.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```python
dz = dL/dz = dL/da * da/dz  = a - y
向后传播的最后一步，就是看w和b怎么变化
dw1 = dL/dw1 = dL/dz * dz/dw1 = x1 * dz
db = dz
```
这就完成了正向传播和反向传播，确实实现了对所有样本进行预测和求导，而且没有使用任何一个for循环，然后梯度下降更新参数：

![梯度下降更新参数](http://upload-images.jianshu.io/upload_images/1779926-8d609245cf46e03f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

有了这些我们就实现了logistc回归的**梯度下降一次迭代**，虽然说过尽量不要使用for循环，但是如果想要实现多次迭代，仍然需要使用for循环，在上面整个一次迭代过程中加上循环次数，**应该没有方式能把这个for循环去掉**。
高度向量化的非常高效的logistc回归的梯度下降法。

####2.15 python中的广播

广播是一种手段，可以让你的python代码段执行的更快，我们将继续深入研究python中的广播是如何实际运作的。

```python
# _*_ coding: utf-8 _*_
import numpy as np

A = np.array([[56.0,0.0,4.4,68.0],
              [1.2,104.0,52.0,8.0],
              [1.8,135.0,99.0,0.9]])
print A

cal = A.sum(axis=0)
# axis=0意味着竖直相加，水平轴是axis=1
print cal

cal2 = cal.reshape(2,2)

print cal2

percentage = 100* A/cal.reshape(1,4)
#使用矩阵A除以这个1*4的矩阵，然后得到了百分比矩阵，
print percentage

#这个例子就是一个广播的例子.
#在求percentage时，用3*4的矩阵除以一个1*4的矩阵，这里在技术上使用reshape是多余的.
#但是通常使用reshape来确保参与运算的是我们想要的形状的矩阵。
#这里是o(1)操作，成本很低。所以不要害怕使用reshape命令来确保你的矩阵形状是你想要的。
#这种运算是怎么执行的？3*4矩阵是如何除1*4矩阵的呢。
```

运行结果：

```
C:\Anaconda2\python.exe C:/Users/mtianyan/Desktop/PyCode/haha/non_fortest.py
[[  56.     0.     4.4   68. ]
 [   1.2  104.    52.     8. ]
 [   1.8  135.    99.     0.9]]
[  59.   239.   155.4   76.9]
[[  59.   239. ]
 [ 155.4   76.9]]
[[ 94.91525424   0.           2.83140283  88.42652796]
 [  2.03389831  43.51464435  33.46203346  10.40312094]
 [  3.05084746  56.48535565  63.70656371   1.17035111]]
[[ 1  4  9]
 [ 4 10 18]
 [ 7 16 27]]

Process finished with exit code 0
```
关于广播，一个(m,n)矩阵和一个(1,n)矩阵相加，首先pyhton会把(1,n)的矩阵**复制m次**，变成(m,n)矩阵，然后两个矩阵相加。同理，若一个(m,n)与一个(m,1),则会复制n次列向量。

广播通用规则，对于一个(m,n)矩阵，加减乘除一个(1,n)的矩阵，后者都会复制m次变成(m,n)矩阵，同理，若为(m,1)的矩阵，则会复制n次列。**注意这里的加减乘除都是逐个元素对应操作，不是根据矩阵的乘法原则。** 还有另一种广播操作，就是一维向量与实数的操作，同理。以上是在神经网络中用到的广播形式，还有更多参看numpy文档。

####2.16 关于python/numpy向量的说明

Python让你能够使用广播运算，一般来说，pyhton numpy程序语言给你提供了很高的灵活性，这算是一门编程语言的优势，同时也是劣势，优势是因为它让语言的表现力更强，语言的灵活性很大，也就是说，你可以用一行代码完成很多运算，弱点就是因为广播和这么大的灵活性，有时可能会引入非常细微的错误，非常奇怪的bug，如果你不熟悉所有复杂的广播运作方式，比如你想用列向量把它加到一个横向量上，你可能会预计它会报错，说维度不匹配，或者类型错误之类的，但事实上你会得到一个行向量和一个列向量求和后的矩阵。

Python的这些奇怪的效果有其内在逻辑，但是如果你不熟悉的话，可能会产生很多奇怪的难以调试的错误。

```
# _*_ coding: utf-8 _*_
import numpy as np

a = np.random.randn(5)
#生成5个随机的高斯变量，存储在数组a中，
#这创建了一个数据结构，a.shape=（5，）就可以查看到这个数据结构，
#叫做秩为1的数组，这个数据结构与行向量和列向量并不一样
#
print "============秩为1数组========"
print a
print a.shape
#这就是所谓的pyhton秩为1的数组，它既不是行向量也不是列向量
print a.T
print np.dot(a,a.T)

print "============标准列向量========"
a=np.random.randn(5,1)
print a
print a.T
print np.dot(a,a.T)

```
![秩为1数组和列向量区别：运行结果](http://upload-images.jianshu.io/upload_images/5355764-d8f220a8f2c660fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当你进行编程联系时，或者实现神经网络的logistc回归时，就**不要使用这些秩为1的数组**，相反每次创建数组时，你要把它定义成**列向量**，或者变成一个**行向量**，那么你向量的行为就更容易理解一些。代码如下：
```python
a = np.random.randn(5)   #创建了一个秩为1的数组，不使用这个
a = np.random.randn(5,1) #列向量，5行1列 a.shape = (5,1)
a = np.random.randn(1,5) #创建一个行向量，5行1列
```
另外，当你在代码中做了很多，不清楚一个向量具体的维度是多少，经常使用assert（）这样一个声明，确保这是一个向量，这些assert()语句执行起来很快，也可以看成是代码的文档。最后，如果你因为一些原因，得到了秩为1 的数组，你可以使用reshape命令，a=a.reshape转化成一个向量。那么它的行为更好预测，就是列向量或者行向量的行为。

有时候会看到一些很难调试的错误，都来自秩为1的反直觉行为，通过消除代码中秩为1的数组，可以让代码更简单。所以在代码中，尽量使用n*1矩阵，基本上是列向量，或1*n矩阵，基本是行向量，随意插入assert（）声明，要仔细检查你的矩阵和数组的维度。不要害怕调用reshape来保证你的数组或向量是你想要的维度。


####2.17 jupyter notebook的基本使用

`shift + enter` 运行代码


####2.18 logistc回归损失函数解释
这节中，将给出关于成本函数的简洁的证明。
公式集合：


![公式集合](http://upload-images.jianshu.io/upload_images/1779926-4aad05ab52cbfb21.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![证明过程-1](http://upload-images.jianshu.io/upload_images/1779926-2d4aca103a25604c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![证明过程-2](http://upload-images.jianshu.io/upload_images/1779926-dfe631254c96d0d5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####第一章的补充内容：
一点是，如果你想达到很高的性能水平，有两个条件，一个是需要训练一个规模足够大的神经网络以发挥数据规模量巨大的优点，另外要达到横轴的右端位置，需要很多的数据，因此我们经常说，规模一直在推动深度学习的进步，我们需要有一个有很多隐藏单元的神经网络。有许多的参数，许多的连接，而且还有数据规模，事实上，要在神经网络上获得更好的表现，最可靠的手段要么是训练一个更大的神经网络，要么投入更多的数据。但这只是在一定程度上，因为你的数据达到一定程度，神经网络规模太大，需要的训练时间太久。

####第二章作业笔记：

#####1.4标准化行
另一个在深度学习和机器学习的技巧是**标准化数据**，因为梯度下降收敛的更快在标准化之后，这使得有更好的性能。

![标准化](http://upload-images.jianshu.io/upload_images/1779926-58c7471760697316.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#####1.4广播以及softmax函数
**sigmoid函数只能分两类**，而**softmax能分多类**，softmax是sigmoid的扩展。
Sigmoid函数对传入的z进行运算，最后得到为0或者为1的概率。Softmax的算法公式为

![softmax函数](http://upload-images.jianshu.io/upload_images/5355764-8989ec248cd3408b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![softmax函数公式详细](http://upload-images.jianshu.io/upload_images/5355764-3b6e75f382cc3118.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

numpy.dot()有两种意思：
- 参数为两个列表时，为求两个列表的点积，即对应相乘再加和
- 参数为向量与矩阵或者矩阵时，则是做矩阵的乘法
- 参数一个为m*n的矩阵，一个为n个元素的列表时，就结果为矩阵的每一行对应乘以列表，最后加和，每一行都是这样，最后得到一个m行的列向量。

```python
import numpy as np
x1 = np.array([
    [9, 2, 5],
    [7, 5, 0]])
x2 = [1,2,3]
toc = np.dot(x1,x2)
print (str(toc))
```
运行结果：[28 17]

关于python的数组，列表，和矩阵之间的联系：

![列表转换](http://upload-images.jianshu.io/upload_images/1779926-95d946180c8d68b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```
x = [1,2,3,4]
x = np.array(x)
print (x.shape)
#结果则为：(4,)
但是此时np.dot(x,x)结果仍然是对应元素的乘积加和
```
`abs()`与`np.abs()`区别：这两个函数的意思都是取绝对值，但是作用的对象不一样.
- abs（）可以对实数取绝对值，数组也可以，但是对向量就不可以.
- 而np.abs（）还可以对列表进行处理。







