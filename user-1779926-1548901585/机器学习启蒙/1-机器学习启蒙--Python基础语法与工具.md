github: https://github.com/mtianyan/graphLabStartedML

## 机器学习正在改变世界

以前的机器学习观点

![mark](http://myphoto.mtianyan.cn/blog/180130/3H4aeACk8l.png?imageslim)

我为什么学习机器学习？机器人：人工智能应用。

![mark](http://myphoto.mtianyan.cn/blog/180130/41DABGb105.png?imageslim)

亚马逊零售推荐，Google广告。电影推荐，音乐推荐，社交推荐。

### 机器学习管道

数据通过机器学习算法管道实现智能。


## 机器学习实例：预测房价

![mark](http://myphoto.mtianyan.cn/blog/180130/h05jljb5Ij.png?imageslim)

>使用房屋特征与售价关系进行回归。

 
## 示例2 情感分析

![mark](http://myphoto.mtianyan.cn/blog/180130/2c72GbeHEJ.png?imageslim)

通过点评文本分析情感正负。

## 示例3 文件检索

数据 -> 聚类 -> 智能

分析文章潜在结构，贴出标签。

## 商品推荐

![mark](http://myphoto.mtianyan.cn/blog/180130/i6A6mB020C.png?imageslim)

通过历史购买记录行为，推荐商品。有可能也会用到别的客户使用的产品。
商品矩阵中方块代表用户真正购买的商品.

![mark](http://myphoto.mtianyan.cn/blog/180130/AFKl46fb21.png?imageslim)

矩阵分解。

## 根据图像的推荐

![mark](http://myphoto.mtianyan.cn/blog/180130/af6fiA09aA.png?imageslim)

将图片输入之后找到邻近样本。深度学习：神经网络。

## 本门课使用的工具

案例实现研究方法 ： Python Ipython Notebook GraphLabCreate

构建机器学习的小应用程序。

为什么选择Python？ 包多，可扩展，可部署。

SFrame & GraphLab Create(高度可扩展，华盛顿大学)

Graphlab 免费一年。处理核心机器学习概念。

## 安装环境

- 安装python Ipython Notebook GraphLab Create
- 启动IPython Notebook
- 在python中编写变量，函数和循环
- 在python中使用sframe执行基本数据操作

## 本门课的内容

一大串的方法。

这个课程从实际案例入手，从实际案例来引入模型和算法。

思考我们面对特定问题，如情感分析。

https://turi.com/

创建一个全新的ipython notebook。

```
cd 工程目录
jupyter notebook
```

- 点击右上角new可以创建全新的notebook。选择Python。可以新建一个全新的
notebook。

- 双击名字可以进行修改名字。

- 菜单可以进行保存文件，复制文件等很多功能。


点击一个单元输出

```
print "hello world"
```

`shift + enter` 进行运行输出。

ipython notebook 中你可以很好的包含文字与组织文字。

- 方法一：点击cell中celltype选择markdown

- 普通的回车提供多行单元格。

- `shift + enter` 进行markdown输出。

## Python基本语法

变量不需要显示声明，不需要指定类型

光标点进单元格里面之后按`esc + m`可以变成markdown格子
