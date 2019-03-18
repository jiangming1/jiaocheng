## 搭建开发环境

![mark](http://myphoto.mtianyan.cn/blog/180410/13LaGbf5IL.png?imageslim)

先学习小例子，然后最后学习3d赛车的例子。

《windows Linux macos 三平台安装openai 的gym 和 universe》

基本要用到的python的图形库

![mark](http://myphoto.mtianyan.cn/blog/180410/Jli7LhGcD6.png?imageslim)

Tkinter 

>python内置的标准GUI(图形用户界面) 库

无需安装，有python就可以导入Tkinter 来使用

https://wiki.python.org/moin/TkInter

pyglet

gym 使用 pyglet 搭建的模拟环境，安装过 Gym 没有必要再安装

```python
pip install pyglet
```

https://bitbucket.org/pyglet/pyglet/wiki/Home

安装pycharm

```python
sudo snap install pycharm-community --classic
```

## 什么是强化学习？

强化学习从1960年代就开始研究，但成名甚晚。

2013 论文 Playing Atari with Deep Reinforcement Learning

https://www.cs.toronto.edu/~vmnih/docs/dqn.pdf

用深度强化学习来玩Atari游戏。DeepMind团队

https://arxiv.org/abs/1312.5602?spm=a2c4e.11153940.blogcont221611.29.72801f465o9PhE&file=1312.5602

David Silver 网上有他关于强化学习的教程。

>因为这篇论文，Google用四亿英镑买下了DeepMind公司

DeepMind这家英国伦敦的公司加入Google后，开发AlphaGo

先用自己的经历来打比方

![mark](http://myphoto.mtianyan.cn/blog/180410/14ciLJ55Le.png?imageslim)

去公司的路上有一个井盖，会发出难闻的臭味，每次都要憋着气或者避开走。

根据环境做出我自己的策略。

![mark](http://myphoto.mtianyan.cn/blog/180410/5lmJeldAJG.png?imageslim)

给机器人奖励，以使它完成强化学习

![mark](http://myphoto.mtianyan.cn/blog/180410/h2DD390f7H.png?imageslim)

狗狗如果做对了事情，就给他肉吃。生物的反射。

### 机器人走迷宫

![mark](http://myphoto.mtianyan.cn/blog/180410/H408Fei117.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180410/c8f4BHieI3.png?imageslim)

从起点开始走。宝藏+1 炸弹-1

- 宝藏和炸弹的 Reward 分别是 1 和 -1

![mark](http://myphoto.mtianyan.cn/blog/180410/0HIacDGL7A.png?imageslim)

通过reward机制来指引机器人怎么走？

- 拿到宝藏最少要5步: 方案1

![mark](http://myphoto.mtianyan.cn/blog/180410/EaeIG9f0Jl.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180410/mEhAakl4A3.png?imageslim)

### 强化学习

Reinforcement Learning: Reinforce 是 加固 增强 强化之意。

- 有种"自强不息" 的感觉: 把机器人丢到一个环境里，让它自己学习。

促进它学习进步的因素只有一个: reward(奖励) 类似打分

![mark](http://myphoto.mtianyan.cn/blog/180410/8gDH80Kea3.png?imageslim)

小女孩你让她自己学自行车。骑得不好就会跌倒就会痛。能够保持平衡，她就知道这是好的。与环境互动，有一个反馈。

- 一句话概述强化学习

>在环境中试错(Trial and Error) 来学习最优策略 (Optimal policy)

try 的名词。 

- 最优策略

>最优策略(Optimal Policy) 是拿到最多的累积奖励(Reward)

### 理想(确定的) vs 马可夫(随机的)

![mark](http://myphoto.mtianyan.cn/blog/180411/5L5JBk54Hg.png?imageslim)

向上走，就一定会确定的向上走。而实际当中它一般会随机的走。

- Markov Decision Process (马可夫决策过程)

>俄罗斯数学家 安德烈.马可夫 Andrey Markov(1856-1922)

强化学习任务 通常通过 马可夫决策过程(MDP) 来描述

马可夫决策过程可以表示为:

>M = < S, A, P(s'|a,s)，R，y

### 一些术语

- Agent: 代理/智能体。 被抽象出来感知周围环境的单元。

>小机器人，或者小女孩

- Action(A): 动作/行动 Agent可以做出动作，会影响环境。

- Environment: 环境 Agent 所在的环境, 会对Action做出反馈。

- State(S) 状态 Agent 感知到的环境 / Agent 的状态

- Transition(P) 过渡/转变 P(s'|a,s) 或 T(s,a,s')

前面的p括号，表示在s的状态下，实施了动作a，到达s'的状态概率是多少？

- 伽马: Gamma 折扣因子 [0,1] 之间。时间越久远对奖励贡献的衰减

![mark](http://myphoto.mtianyan.cn/blog/180411/h5mmhF6J37.png?imageslim)

- Reward(R)： 到达一个State(状态)后获得的奖励。1 -1 0 标量值

- Policy: 策略 我们要学习到的目标, 对state做出相应的action。 从state到action的映射

- Optimal policy: 最优策略 使奖励最大化的策略

### Transition: 过渡/转变

![mark](http://myphoto.mtianyan.cn/blog/180411/35GGL9aI20.png?imageslim)

初始的状态为s， 在s状态实施动作a 。此时的过渡 T(s,a,s')

经过过渡之后，状态会到达s'

### MDP 的简单图解

![mark](http://myphoto.mtianyan.cn/blog/180411/9Kj5mE0dmh.png?imageslim)

也可以描述为强化学习的简单图解。环境会返回一个state以及一个reward的奖励。

### MDP随时间推移的图解

![mark](http://myphoto.mtianyan.cn/blog/180411/m8jbDeg1a1.png?imageslim)

Rt+1是下一时刻的奖励，St+1是下一个时刻的状态

![mark](http://myphoto.mtianyan.cn/blog/180411/5g16648HHk.png?imageslim)

使用玩游戏的例子来说明。游戏的主角像一个代理。分数反馈奖励，返回主角当前所在环境的状态。

- 其实我们每天都在进行“强化学习”

![mark](http://myphoto.mtianyan.cn/blog/180411/a8GhAC2l5j.png?imageslim)

- 决战朋友圈的“最优策略”

>慢慢地，我们学会了如何去发别人更喜欢看(更多赞)的内容

![mark](http://myphoto.mtianyan.cn/blog/180411/Fi1JE8cBL1.png?imageslim)

### 监督学习，非监督学习 和强化学习

- 监督学习(supervised Learning)： y=f(x) 映射/逼近

根据标签，找到输入和输出之间的一个关系。

- 非监督学习(unSupervised Learning) f(x) 聚类/描述

- 强化学习(reinforcement Learning) y=f(x) 策略/做决定

强化学习(RL) 与监督学习(SL) 的区别

- 强化学习是通过“试错” (Trial and Error)来学习: 需要有效探索

强化学习没有监督学习的标明: 输入/输出的标签。 奖励可能延迟。

![mark](http://myphoto.mtianyan.cn/blog/180411/g7kh2dkL4a.png?imageslim)

下围棋。我走了7,7 会给出下一步游戏的状态和奖励。这个奖励可能会有延时。

不会给你的每一步都去进行reward的打分。最终才会提供你是正是负。

监督学习，会有一个input，围棋走一步，会有老师给你一个标签，告诉你7,7 这步是不够好的，应该走的是5,5 这步。

- Model-Free RL

![mark](http://myphoto.mtianyan.cn/blog/180411/jem85gHmff.png?imageslim)

不基于模型的强化学习。直接在环境中进行学习，不需要对于环境进行抽象。

不需要对地球进行建模等工作。

- Model-Based RL

![mark](http://myphoto.mtianyan.cn/blog/180411/dKmFCIi9C2.png?imageslim)

基于模型的强化学习。地球被建模。

### 基于策略和基于价值

- 基于策略: Policy-Based 根据环境，直接在策略空间里选择

- 基于价值: Value-Based 学习价值函数， 根据价值高低选择策略

结合版: Actor - Critic (演员-评论家) 略类似生成对抗网络GAN

生成网络从噪声去生成图片。评判网络去评判生成的这张图片与实际图片是否类似。
彼此学习一起提升。

### Actor-Critic 演员-评论家

演员根据策略做出动作，评论家根据演员的动作打分

![mark](http://myphoto.mtianyan.cn/blog/180411/Hc5LlgJJl1.png?imageslim)

结合了基于策略和基于价值两种

![mark](http://myphoto.mtianyan.cn/blog/180411/48KbihEGED.png?imageslim)

### AlphaGo的策略网络和价值网络

![mark](http://myphoto.mtianyan.cn/blog/180411/6Ida3Jla6g.png?imageslim)

基于策略走一步，根据走的步打分值。

![mark](http://myphoto.mtianyan.cn/blog/180411/81ffck927l.png?imageslim)

有无模型 基于模型还是基于价值

### 强化学习是个大家族

![mark](http://myphoto.mtianyan.cn/blog/180411/CHdDFH62Gm.png?imageslim)

- 在线(On -Policy) 学习 和离线(Off- Policy) 学习

>在线学习: 自己亲自玩游戏，边玩边学习

离线学习: 可以自己玩游戏/看着别人玩游戏。能从经验/记忆中学习。

![mark](http://myphoto.mtianyan.cn/blog/180411/JLFJaKkG5k.png?imageslim)

强化学习分类。

- 回合更新和单步更新

>回合更新: Monte Carlo 需要等待游戏回合结束才能更新策略(蒙特卡罗)

>单步更新：Temporal Difference 游戏回合的每一步都可以更新策略

![mark](http://myphoto.mtianyan.cn/blog/180411/DCfaE8Hllk.png?imageslim)

单步更新，比回合更新更有优势。
