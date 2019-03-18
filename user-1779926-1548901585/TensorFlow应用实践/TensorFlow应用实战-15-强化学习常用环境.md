## 使用TensorFlow开发会开赛车的AI

- 进行游戏领域
- 第一个我们使用了 RNN 和 LSTM 开发作曲智能应用。
- 深度卷积的生成对抗网络，帮我们开发能够制图的AI


DeepMind 的 AlphaGo 围棋人工智能

AlphaGo是deepmind团队开发的，后来deepmind被谷歌收购到旗下，收购的价格是4亿英镑。

到了Google，由之前的torch转到以TensorFlow为后端开发。

新旧两个版本。

https://deepmind.com/research/alphago/

战胜很多人类棋手。

![mark](http://myphoto.mtianyan.cn/blog/180410/20BgAa9jL1.png?imageslim)

AlphaGo zero 强很多，训练4天之后，完胜之前版本。

https://deepmind.com/blog/alphago-zero-learning-scratch/

从零开始学习，没有被给予很多的资料，而是自己与自己博弈，从零开始学起。

### AlphaGo 主要算法

![mark](http://myphoto.mtianyan.cn/blog/180410/88fEhgk0gc.png?imageslim)

AlphaGo 战胜的著名人类棋手

![mark](http://myphoto.mtianyan.cn/blog/180410/bj5k27eale.png?imageslim)

AlphaZero 通用的强化学习

![mark](http://myphoto.mtianyan.cn/blog/180410/6imHgE83hK.png?imageslim)

OpenAi 非盈利人工智能项目

openai的创始人 埃隆马斯克 (Elon Musk) 现实版的钢铁侠 特斯拉ceo 火箭项目

openAi 的一些强化学习应用

![mark](http://myphoto.mtianyan.cn/blog/180410/D5k1hl4g33.png?imageslim)

- openAi的dota2 1v1 人工智能应用

https://blog.openai.com/dota-2/

- openai的开源项目 gym

开发和对比一些强化学习的算法。

https://github.com/openai/gym

- 基于gym项目开源了universe 宇宙

通用的深度学习环境模拟。 游戏模拟。邮件发送。

- udacity 开源的自动驾驶汽车项目

https://www.udacity.com/self-driving-car

github上开源了一个自动汽车驾驶的模拟器

基于unity开发的一个模拟器。

### 案例三的主要算法

强化学习 reinforcement Learning

![mark](http://myphoto.mtianyan.cn/blog/180410/i93Jf8keD1.png?imageslim)

openai开源的universe环境可以模拟的赛车程序

撞碎红色的车，避开紫色的车，不要撞到边边。

## 强化学习的经典实验环境

强化学习也需要一个统一的环境

就像我们cnn的时候 mnist等数据集。

- 强化学习经典实验场景: CartPole Mountain Car PacMan

![mark](http://myphoto.mtianyan.cn/blog/180410/g5IL4LCBb9.png?imageslim)

左右移动节点，保持平衡。

![mark](http://myphoto.mtianyan.cn/blog/180410/2C8K873ihb.png?imageslim)

把小车越过旗杆上山。

![mark](http://myphoto.mtianyan.cn/blog/180410/g72Ji5I8Fg.png?imageslim)

吃豆人。

![mark](http://myphoto.mtianyan.cn/blog/180410/fm91GID5bD.png?imageslim)

中国人实现的DeepLearningFlappyBird

https://github.com/yenchenlin/DeepLearningFlappyBird

- 大家自己实现的实验环境的差别会影响结果的比较。

- 统一的实验环境有利于论文的发表于研究

MuJoco

- Multi-Joint dynamics with Contact

"有接触的多关节动力学" 用于机器人 生物力学 动画 等需要快速精确仿真领域的物理引擎。

http://www.mujoco.org/

被广泛使用在各种强化学习， 不管是Google 还是 openai 还是中国的集智

下载对应的物理引擎。

30天试用版。学生免费。

Google团队，蜘蛛人。跨越障碍。 

https://github.com/openai/mujoco-py

RoboSchool: 免费版的MUJoco

- openai开源的机器人模拟软件，不止包含MUjoCO环境
- 基于OPenAI Gym

https://github.com/openai/roboschool

### DeepMind Control Suite and Package

- 谷歌DeepMind 发布的强化学习环境
- 由python编写，并由MujoCo物理引擎驱动

dm_control

https://github.com/deepmind/dm_control

类似的多关节运行。

- OpenAI Gym 

>openai 开发的强化学习工具包 

http://gym.openai.com/

- Elon Musk 希望人人都能了解 人工智能 以预防 "AI灭世"

集大成者；包含非常多学习环境，很多其他实验环境都是基于Gym

https://github.com/openai/gym

https://github.com/openai/gym/tree/master/gym/envs

看到里面有很多游戏实现环境，

http://gym.openai.com/envs/CartPole-v0/

允许我们实现算法然后打分。

envs 经典游戏环境。

### RL lab

伯克利大学和 openai 的学者开发的强化学习算法框架

- 与 openai gym兼容

https://github.com/rll/rllab

### DeepMind Lab

DeepMind 发布的 3d强化学习平台

- 最初是DeepMind 内部使用 之后开源了

https://github.com/deepmind/lab

模拟一些走迷宫之类的3d实验环境。

### PySC2 (StarCraft II 星际争霸)

- DeepMind 和 Blizzard(暴雪) 合作推出的 StarCraft ll 研究平台

- DeepMind 用 AlphaGo 征服围棋后， 继续征服很难的星际争霸 

https://github.com/deepmind/pysc2

DeepMind官网有专门的一篇文章

https://zhuanlan.zhihu.com/p/28434323

https://zhuanlan.zhihu.com/p/29246185?group_id=890682069733232640

https://deepmind.com/blog/deepmind-and-blizzard-open-starcraft-ii-ai-research-environment/

模拟一个agent收集资源等

- OpenAI Baselines

>基于 OpenAI Gym 和 TensorFlow 的实现

实现了经典的(深度) 强化学习算法

https://github.com/openai/baselines

高质量的强化学习算法实现。它使用的框架也是TensorFlow，是比较好的学习例子。

- VizDoom

毁灭战士(Doom) 游戏的强化学习环境 有举办竞赛

Doom 游戏是第一人称射击游戏的开拓者之一，还有同名电影。

https://github.com/mwydmuch/ViZDoom

毁灭战士的ai研究平台，研究强化学习。有一些演示视频。

- TensorFlow Models

TensorFlow Models(模型) 中提供的强化学习算法集

位于TensorFlow 源代码集中

https://github.com/tensorflow/models/tree/master/research

其中有gan，有很多强化学习的例子

https://github.com/tensorflow/models/tree/master/research/pcl_rl

rl是强化学习的缩写。

- TensorFlow Agents

基于TensorFlow的并行强化学习算法

容易开发并行强化学习算法

官网在github下的TensorFlow 

https://github.com/tensorflow/agents

- ELF

Facebook推出的游戏研究平台

可以让多个游戏实例并行执行，任何C++接口的游戏都可以接入

https://github.com/facebookresearch/ELF

- Coach 

Intel 收购的 Nervana 公司(还开发了Neon) 推出的强化学习框架

https://github.com/NervanaSystems/neon

neon是一个有名的深度学习框架，可以使用python进行调用。

coach 方便地实现并行算法，充分利用cpu gpu多核

https://github.com/NervanaSystems/coach

- Unity Machine Learning Agents

基于Unity 3d 引擎的强化学习框架 可以模拟3d 强化学习

与Unity 3d(pokemon Go的开发工具) 结合，未来大有可为。

https://unity3d.com/cn/machine-learning

https://github.com/Unity-Technologies/ml-agents

带你开发类似Pokemon Go的AR游戏 基于unity3d引擎

- OpenAi universe

openai 开发的开源人工智能软件平台 1000 多个环境

可以让一个程序变为openai gym 环境，将程序封装成docker 容器

https://github.com/openai/universe

模拟很多的游戏场景

案例三的主要实验环境

![mark](http://myphoto.mtianyan.cn/blog/180410/h676A12CHg.png?imageslim)
