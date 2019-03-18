# 什么是Q-learning

Q是Quality的首字母，表示"质量/优劣"，表示给它打一个分。

在某些状态下做某个动作，会给他一个Q的价值。

learning就是学习的意思。基于质量，评判做出选择。

- Q learning 是基于价值(Value-Based) 的学习
- Q learning 是离线(off-play) 学习 基于过去的记忆学习。

![mark](http://myphoto.mtianyan.cn/blog/180411/0bH92JH2Hl.png?imageslim)

炸弹奖励是-1 宝石奖励为1。

### Q learning 是基于价值的: State-Action

对于state-action对做出一个打分。

![mark](http://myphoto.mtianyan.cn/blog/180411/lchc10g22d.png?imageslim)

总共有12个状态，s1到s12.对于每一个状态会有四个动作。对于每个状态下的每个动作会有一个Q的值。

游戏中不断去跳转Q表中的值，学到最佳策略。

算法:

![mark](http://myphoto.mtianyan.cn/blog/180411/AHIlmbGI5K.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180411/4h2dgEbL8I.png?imageslim)

中文的算法解释。之后我们会基于这个中文算法描述来写我们的代码。

流程图：

![mark](http://myphoto.mtianyan.cn/blog/180411/em4a0HKBga.png?imageslim)

基于表格的Q learning 局限性

- 对于状态(State) 非常多的(例如围棋) 无能无力

- 神经网络最会记住N多的参数，解决了Q Learing 的表格的局限。

结合深度学习(深度神经网络) 和 Q learning 推出了 DQL/DQN

深度Q learning 或者 深度的Q 网络

![mark](http://myphoto.mtianyan.cn/blog/180411/kcfj1bhG3e.png?imageslim)

>深度Q learning (DQL/DQN)

使用深度神经网络实现Q-learning的方法。克服了表格的局限

## Q-learning 实现机器人走迷宫

红色代表两个炸弹。蓝色宝藏。结束之后打印出Q表

### 实现步骤

- 游戏环境 机器人大脑 游戏主程序

1. 实现游戏的环境

![mark](http://myphoto.mtianyan.cn/blog/180411/beCiL2c6If.png?imageslim)

迷宫的地图是这样的，左下角有个起点。-1 是炸弹 +1 是宝藏。

用Tkinter 来模拟gym的环境构建。我们用到的方法名和用法都和gym是类似的。

完成的迷宫地图如下

![mark](http://myphoto.mtianyan.cn/blog/180411/Ia48BFJ1K7.png?imageslim)

编写我们的env.py

```python
# -*- coding: UTF-8 -*-

"""
Q Learning 例子的 Maze（迷宫） 环境

黄色圆形 :   机器人
红色方形 :   炸弹     [reward = -1]
绿色方形 :   宝藏     [reward = +1]
其他方格 :   平地     [reward = 0]
"""

import sys
import time

import numpy as np

# Python2 和 Python3 中 Tkinter 的名称不一样
if sys.version_info.major == 2:
    import Tkinter as tk
else:
    import tkinter as tk
```

python2与python3下的Tkinter的不同引入处理。

```python
WIDTH = 4   # 迷宫的宽度
HEIGHT = 3  # 迷宫的高度
UNIT = 40   # 每个方块的大小（像素值）
```

构建一个class继承TK

```python
# 迷宫 类
class Maze(tk.Tk, object):
    def __init__(self):
        super(Maze, self).__init__()
        self.action_space = ['u', 'd', 'l', 'r']  # 上，下，左，右 四个 action（动作）
        self.n_actions = len(self.action_space)   # action 的数目
        self.title('Q Learning')
        self.geometry('{0}x{1}'.format(WIDTH * UNIT, HEIGHT * UNIT))  # Tkinter 的几何形状
        self.build_maze()
```

定义一个init的构造方法。调用父类的初始化方法。

定义它的动作空间: action_space 上下左右四个。 up down

action的数目。 title: 生成的游戏窗口的title

geometry 几何形状。第一维是一宽度乘以每个单元的像素值，第二维是高乘以每个单元像素值。

调用 build Maze方法


```python
 # 创建迷宫
    def build_maze(self):
        # 创建画布 Canvas.白色背景，宽高。
        self.canvas = tk.Canvas(self, bg='white',
                                width=WIDTH * UNIT,
                                height=HEIGHT * UNIT)

        # 绘制横纵方格线。创建线条。
        for c in range(0, WIDTH * UNIT, UNIT):
            x0, y0, x1, y1 = c, 0, c, HEIGHT * UNIT
            self.canvas.create_line(x0, y0, x1, y1)
        for r in range(0, HEIGHT * UNIT, UNIT):
            x0, y0, x1, y1 = 0, r, WIDTH * UNIT, r
            self.canvas.create_line(x0, y0, x1, y1)

        # 零点（左上角） 往右是x增长的方向。往左是y增长的方向。
        # 因为每个方格是40像素，20,20是中心位置。
        origin = np.array([20, 20])

        # 创建我们的探索者 机器人（robot）
        robot_center = origin + np.array([0, UNIT * 2])
        # 创建椭圆，指定起始位置。填充颜色
        self.robot = self.canvas.create_oval(
            robot_center[0] - 15, robot_center[1] - 15,
            robot_center[0] + 15, robot_center[1] + 15,
            fill='yellow')

        # 炸弹 1
        bomb1_center = origin + UNIT
        self.bomb1 = self.canvas.create_rectangle(
            bomb1_center[0] - 15, bomb1_center[1] - 15,
            bomb1_center[0] + 15, bomb1_center[1] + 15,
            fill='red')

        # 炸弹 2
        bomb2_center = origin + np.array([UNIT * 3, UNIT])
        self.bomb2 = self.canvas.create_rectangle(
            bomb2_center[0] - 15, bomb2_center[1] - 15,
            bomb2_center[0] + 15, bomb2_center[1] + 15,
            fill='red')

        # 宝藏
        treasure_center = origin + np.array([UNIT * 3, 0])
        self.treasure = self.canvas.create_rectangle(
            treasure_center[0] - 15, treasure_center[1] - 15,
            treasure_center[0] + 15, treasure_center[1] + 15,
            fill='green')

        # 设置好上面配置的场景
        self.canvas.pack()
```

reset方法表示游戏重新开始，机器人回到左下角

```python
 # 重置（游戏重新开始，将机器人放到左下角）
    def reset(self):
        self.update()
        time.sleep(0.5)
        self.canvas.delete(self.robot)  # 删去机器人
        origin = np.array([20, 20])
        robot_center = origin + np.array([0, UNIT * 2])
        # 重新创建机器人
        self.robot = self.canvas.create_oval(
            robot_center[0] - 15, robot_center[1] - 15,
            robot_center[0] + 15, robot_center[1] + 15,
            fill='yellow')
        # 返回 观测（observation）
        return self.canvas.coords(self.robot)
```

使用update方法更新一下游戏环境。使用coords返回一个观测值。

走一步(机器人实施一个action)

```python
 # 走一步（机器人实施 action）
    def step(self, action):
    	# s表示一个state状态值
        s = self.canvas.coords(self.robot)
        # 基准动作
        base_action = np.array([0, 0])
        if action == 0:     # 上
            if s[1] > UNIT:
                base_action[1] -= UNIT
        elif action == 1:   # 下
            if s[1] < (HEIGHT - 1) * UNIT:
                base_action[1] += UNIT
        elif action == 2:   # 右
            if s[0] < (WIDTH - 1) * UNIT:
                base_action[0] += UNIT
        elif action == 3:   # 左
            if s[0] > UNIT:
                base_action[0] -= UNIT

        # 移动机器人，移动到baseation横向纵向坐标值
        self.canvas.move(self.robot, base_action[0], base_action[1])

        # 取得下一个 state
        s_ = self.canvas.coords(self.robot)

        # 奖励机制。
        if s_ == self.canvas.coords(self.treasure):
            reward = 1  # 找到宝藏，奖励为 1
            done = True
            s_ = 'terminal'   # 终止
            print("找到宝藏，好棒!")
        elif s_ == self.canvas.coords(self.bomb1):
            reward = -1  # 踩到炸弹1，奖励为 -1
            done = True
            s_ = 'terminal'   # 终止
            print("炸弹 1 爆炸...")
        elif s_ == self.canvas.coords(self.bomb2):
            reward = -1  # 踩到炸弹2，奖励为 -1
            done = True
            s_ = 'terminal'   # 终止
            print("炸弹 2 爆炸...")
        else:
            reward = 0   # 其他格子，没有奖励
            done = False

        return s_, reward, done
```

```python
    # 调用 Tkinter 的 update 方法
    def render(self):
        time.sleep(0.1)
        self.update()
```

调用Tkinter的update方法。0.1秒去走一步。

## 实现Q learning(机器人的大脑)

Q learning的 Q表

![mark](http://myphoto.mtianyan.cn/blog/180411/lIH7jl2L4G.png?imageslim)

每一行是一个状态，s1 到 s4
每一列是在这个状态下可以采取的行动。

Q learning的算法

![mark](http://myphoto.mtianyan.cn/blog/180411/bA1B64BCkH.png?imageslim)

我们例子的Q表: 有12个格子,4个动作

![mark](http://myphoto.mtianyan.cn/blog/180411/Fbc1elbAa3.png?imageslim)

对应的Q值。

中文的Q learning算法伪代码

![mark](http://myphoto.mtianyan.cn/blog/180411/DILJIkBLb8.png?imageslim)

首先会随机的初始化Q表中的值。对于每一个回合做一个循环。

循环中首先:

- 初始化初始位置的状态。

![mark](http://myphoto.mtianyan.cn/blog/180411/36a63bd9da.png?imageslim)

新的Q表中的Q(s,a)更新规则如上。

- 著名的贝尔曼方程

![mark](http://myphoto.mtianyan.cn/blog/180411/b3e5L0i16m.png?imageslim)

e-Greedy 贪婪算法: 持续探索(Exploration)

- 贪婪度 e: e-Greedy 算法可以预防更好的选择一直没有被探索到。
- 1-e 的概率 选择Q表中state位置值最大的action
- e的概率 随机选取Q表中state位置的action

![mark](http://myphoto.mtianyan.cn/blog/180411/EljEj6F0hm.png?imageslim)

https://www.zhihu.com/question/26408259?sort=created

http://mnemstudio.org/path-finding-q-learning-tutorial.htm

## 代码编写

```python
# -*- coding: UTF-8 -*-

"""
Q Learning 算法。做决策的部分，相当于机器人的大脑
"""

import numpy as np
import pandas as pd


class QLearning:
    def __init__(self, actions, learning_rate=0.01, discount_factor=0.9, e_greedy=0.1):
        self.actions = actions        # action 列表
        self.lr = learning_rate       # 学习速率
        self.gamma = discount_factor  # 折扣因子
        self.epsilon = e_greedy       # 贪婪度
        # 列是action，上下左右四种。
        self.q_table = pd.DataFrame(columns=self.actions, dtype=np.float32)  # Q 表

    # 检测 q_table 中有没有这个 state
    # 如果还没有当前 state, 那我们就插入一组全 0 数据, 作为这个 state 的所有 action 的初始值
    def check_state_exist(self, state):
    	# state对应每一行，如果不在Q表中。
        if state not in self.q_table.index:
            # 插入一组全 0 数据，上下左右，四个动作，创建四个零
            self.q_table = self.q_table.append(
                pd.Series(
                    [0] * len(self.actions),
                    index=self.q_table.columns,
                    name=state,
                )
            )

    # 根据 state 来选择 action
    def choose_action(self, state):
        self.check_state_exist(state)  # 检测此 state 是否在 q_table 中存在
        # 选行为，用 Epsilon Greedy 贪婪方法
        if np.random.uniform() < self.epsilon:
            # 随机选择 action
            action = np.random.choice(self.actions)
        else:  # 选择 Q 值最高的 action
            state_action = self.q_table.loc[state, :]
            # 同一个 state, 可能会有多个相同的 Q action 值, 所以我们乱序一下
            state_action = state_action.reindex(np.random.permutation(state_action.index))
            # 每一行中取到Q值最大的那个
            action = state_action.idxmax()
        return action

    # 学习。更新 Q 表中的值
    def learn(self, s, a, r, s_):
    	# s_是下一个状态
        self.check_state_exist(s_)  # 检测 q_table 中是否存在 s_

        q_predict = self.q_table.loc[s, a]  # 根据 Q 表得到的 估计（predict）值

        # q_target 是现实值
        if s_ != 'terminal':  # 下个 state 不是 终止符
            q_target = r + self.gamma * self.q_table.loc[s_, :].max()
        else:
            q_target = r  # 下个 state 是 终止符

        # 更新 Q 表中 state-action 的值
        self.q_table.loc[s, a] += self.lr * (q_target - q_predict)
```

## 编写游戏主程序

play.py

```python
# -*- coding: UTF-8 -*-

"""
游戏的主程序，调用机器人的 Q learning 决策大脑 和 Maze 环境
"""

from env import Maze
from q_learning import QLearning


def update():
    for episode in range(100):
        # 初始化 state（状态）
        state = env.reset()

        step_count = 0  # 记录走过的步数

        while True:
            # 更新可视化环境
            env.render()

            # RL 大脑根据 state 挑选 action
            action = RL.choose_action(str(state))

            # 探索者在环境中实施这个 action, 并得到环境返回的下一个 state, reward 和 done (是否是踩到炸弹或者找到宝藏)
            state_, reward, done = env.step(action)

            step_count += 1  # 增加步数

            # 机器人大脑从这个过渡（transition） (state, action, reward, state_) 中学习
            RL.learn(str(state), action, reward, str(state_))

            # 机器人移动到下一个 state
            state = state_

            # 如果踩到炸弹或者找到宝藏, 这回合就结束了
            if done:
                print("回合 {} 结束. 总步数 : {}\n".format(episode+1, step_count))
                break

    # 结束游戏并关闭窗口
    print('游戏结束')
    env.destroy()


if __name__ == "__main__":
    # 创建环境 env 和 RL
    env = Maze()
    RL = QLearning(actions=list(range(env.n_actions)))

    # 开始可视化环境
    env.after(100, update)
    env.mainloop()

    print('\nQ 表:')
    print(RL.q_table)
```

![mark](http://myphoto.mtianyan.cn/blog/180413/H3m91H2Chg.png?imageslim)


![mark](http://myphoto.mtianyan.cn/blog/180413/7lb34FCC2B.png?imageslim)

## DeepQlearning实现: 迷宫游戏

我们通过之前的Qlearning 知道了算法和代码

Qlearning 局限: 不能表示很多的状态和Q值。下围棋这个例子

![mark](http://myphoto.mtianyan.cn/blog/180413/m9lD1d3gjJ.png?imageslim)

可以表示很多参数

最终效果:

![mark](http://myphoto.mtianyan.cn/blog/180413/a0L5CHaHcG.png?imageslim)

实现步骤:

- 机器人大脑
- 游戏环境
- 游戏主程序。

DeepQlearing 有Q-learning的优势

- Q-learning 是 off-Policy (离线学习 可以学习过往经验或记忆)

- Q-learning 可以单步更新，比回合更新更有效率

![mark](http://myphoto.mtianyan.cn/blog/180413/A3g1aC8bbi.png?imageslim)

经验回放(去学习过往的经验或记忆)

单步更新，更新网络参数

![mark](http://myphoto.mtianyan.cn/blog/180413/A5k909Aa98.png?imageslim)

记忆库存储过往记忆。

估计神经网络 现实实际 神经网络 

![mark](http://myphoto.mtianyan.cn/blog/180413/C5Hlj51E9E.png?imageslim)

```python
# -*- coding: UTF-8 -*-

"""
Deep Q Learning 算法。做决策的部分，相当于机器人的大脑
"""

import numpy as np
import tensorflow as tf

# 伪随机数。为了复现结果
np.random.seed(1)
tf.set_random_seed(1)


class DeepQLearning:
    def __init__(
            self,
            n_actions,
            n_features,
            learning_rate=0.01,
            discount_factor=0.9,
            e_greedy=0.1,
            replace_target_iter=300,
            memory_size=500,
            batch_size=32,
            output_graph=False,  # 是否存储 TensorBoard 日志
    ):
        self.n_actions = n_actions    # action 的数目
        self.n_features = n_features  # state/observation 里的特征数目
        self.lr = learning_rate       # 学习速率
        self.gamma = discount_factor  # 折扣因子
        self.epsilon = e_greedy       # 贪婪度 Epsilon Greedy
        self.replace_target_iter = replace_target_iter  # 每多少个迭代替换一下 target 网络的参数
        self.memory_size = memory_size  # 记忆上限
        self.batch_size = batch_size  # 随机选取记忆片段的大小

        # 学习次数 (用于判断是否更换 Q_target_net 参数)
        self.learning_steps = 0

        # 初始化全 0 记忆 [s, a, r, s_]
        self.memory = np.zeros((self.memory_size, n_features * 2 + 2))

        # 构建神经网络
        self.construct_network()

        # 提取 Q_target_net 和 Q_eval_net 的参数
        t_params = tf.get_collection(tf.GraphKeys.GLOBAL_VARIABLES, scope='Q_target_net')
        e_params = tf.get_collection(tf.GraphKeys.GLOBAL_VARIABLES, scope='Q_eval_net')
        
        # 用 Q_eval_net 参数来替换 Q_target_net 参数
        with tf.variable_scope('target_replacement'):
            self.target_replace_op = [tf.assign(t, e) for t, e in zip(t_params, e_params)]

        self.sess = tf.Session()

        if output_graph:
            # 输出 TensorBoard 日志文件
            tf.summary.FileWriter("logs", self.sess.graph)

        # 初始化全局变量
        self.sess.run(tf.global_variables_initializer())

    '''
    构建两个神经网络（Q_eval_net 和 Q_target_net）。
    固定住一个神经网络 (Q_target_net) 的参数（所谓 Fixed Q target）。
    Q_target_net 相当于 Q_eval_net 的一个历史版本, 拥有 Q_eval_net 之前的一组参数。
    这组参数被固定一段时间, 然后再被 Q_eval_net 的新参数所替换。
    Q_eval_net 的参数是不断在被提升的
    '''
    def construct_network(self):
        # 输入数据 [s, a, r, s_]
        with tf.variable_scope('input'):
            self.s = tf.placeholder(tf.float32, [None, self.n_features], name='s')  # State
            self.a = tf.placeholder(tf.int32, [None, ], name='a')  # Action
            self.r = tf.placeholder(tf.float32, [None, ], name='r')  # Reward
            self.s_ = tf.placeholder(tf.float32, [None, self.n_features], name='s_')  # 下一个 State

        # 权重和偏差
        w_initializer, b_initializer = tf.random_normal_initializer(0., 0.3), tf.constant_initializer(0.1)

        # 创建 Q_eval 神经网络, 适时更新参数
        with tf.variable_scope('Q_eval_net'):
            e1 = tf.layers.dense(self.s, 20, tf.nn.relu, kernel_initializer=w_initializer,
                                 bias_initializer=b_initializer, name='e1')
            self.q_eval = tf.layers.dense(e1, self.n_actions, kernel_initializer=w_initializer,
                                          bias_initializer=b_initializer, name='e2')

        # 创建 Q_target 神经网络, 提供 target Q
        with tf.variable_scope('Q_target_net'):
            t1 = tf.layers.dense(self.s_, 20, tf.nn.relu, kernel_initializer=w_initializer,
                                 bias_initializer=b_initializer, name='t1')
            self.q_next = tf.layers.dense(t1, self.n_actions, kernel_initializer=w_initializer,
                                          bias_initializer=b_initializer, name='t2')

        # 在 Q_target_net 中，计算下一个状态 s_j_next 的真实 Q 值
        with tf.variable_scope('Q_target'):
            q_target = self.r + self.gamma * tf.reduce_max(self.q_next, axis=1)
            # tf.stop_gradient 使 q_target 不参与梯度计算的操作
            self.q_target = tf.stop_gradient(q_target)

        # 在 Q_eval_net 中，计算状态 s_j 的估计 Q 值
        with tf.variable_scope('Q_eval'):
            a_indices = tf.stack([tf.range(tf.shape(self.a)[0], dtype=tf.int32), self.a], axis=1)
            # tf.gather_nd 用 indices 定义的形状来对 params 进行切片
            self.q_eval_by_a = tf.gather_nd(params=self.q_eval, indices=a_indices)
        
        # 计算真实值和估计值的误差（loss）
        with tf.variable_scope('loss'):
            self.loss = tf.reduce_mean(tf.squared_difference(self.q_target, self.q_eval_by_a, name='error'))

        # 梯度下降法优化参数
        with tf.variable_scope('train'):
            self.train_op = tf.train.RMSPropOptimizer(self.lr).minimize(self.loss)

    # 在记忆中存储和更新 transition（转换）样本 [s, a, r, s_]
    def store_transition(self, s, a, r, s_):
        if not hasattr(self, 'memory_count'):
            self.memory_count = 0
        transition = np.hstack((s, [a, r], s_))
        # 记忆总大小是固定的。如果超出总大小, 旧记忆就被新记忆替换
        index = self.memory_count % self.memory_size
        self.memory[index, :] = transition
        self.memory_count += 1

    # 根据 state 来选 action
    def choose_action(self, state):
        # 统一 state 的形状
        state = state[np.newaxis, :]

        if np.random.uniform() < self.epsilon:
            # 随机选择
            action = np.random.randint(0, self.n_actions)
        else:
            # 让 Q_eval_net 神经网络生成所有 action 的值, 并选择值最大的 action
            actions_value = self.sess.run(self.q_eval, feed_dict={self.s: state})
            action = np.argmax(actions_value)

        return action

    # 学习
    def learn(self):
        # 是否替换 Q_target_net 参数
        if self.learning_steps % self.replace_target_iter == 0:
            self.sess.run(self.target_replace_op)
            print('\n替换现实网络的参数...\n')

        # 从记忆中随机抽取 batch_size 长度的记忆片段
        if self.memory_count > self.memory_size:
            sample_index = np.random.choice(self.memory_size, size=self.batch_size)
        else:
            sample_index = np.random.choice(self.memory_count, size=self.batch_size)
        batch_memory = self.memory[sample_index, :]

        # 训练 Q_eval_net
        _, _ = self.sess.run(
            [self.train_op, self.loss],
            feed_dict={
                self.s: batch_memory[:, :self.n_features],
                self.a: batch_memory[:, self.n_features],
                self.r: batch_memory[:, self.n_features + 1],
                self.s_: batch_memory[:, -self.n_features:],
            })

        self.learning_steps += 1
```

## Policy Gradient 实现 Gym游戏

实现步骤: 机器人大脑 & 游戏主程序

什么是Policy Gradient

- Policy-Based 方法: 与Value-Based(如Q-learning DQN)不同

策略上做一个梯度下降

Policy Gradient 跳过 Value 阶段，根据概率来输出具体的Action

输出的Action可以是一个连续的值，Value-Based输出是不连续的。

