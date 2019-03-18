###实际案例:
>很多应用程序都自带一个浏览用户的历史记录的功能
例如:
- 浏览器历史记录
- 视频播放历史记录
- shell查看用户输入过的命令
>现在我们制作了一个简单的猜数字大小的小游戏
添加历史记录功能，显示用户最近猜过的数字如何实现？

###解决方案:
>使用容量为n的队列来存储历史记录
- 使用标准库collections中的deque，他是一个双端循环队列
- 程序退出前，可以使用pickie将队列对象存入文件，再次运行时程序将其导入

####猜数小游戏:
```python
from random import randint

Num = randint(0,100)

def guess(k):
    if k == Num:
        print "success"
        return True
    elif k > Num:
        print "the number is bigger"
    else:
        print "the number is smaller"
    return False

while True:
    line = raw_input("please input a number(1~100)")
    # line是一个字符串，通过isdigit()方法来看这个字符串是不是一个数字
    if line.isdigit():
        k = int(line)
        if guess(k):
            break
```
####队列小例子:
```python
# 使用队列

from collections import deque

# deque传入两个参数:1.初始值;2.队列长度
q = deque([],5)

# append方法从右部添加
q.append(1)
q.append(2)
q.append(3)
q.append(4)
q.append(5)
q.append(6)

print q
```
####加上了历史记录的猜数字小游戏:
```python
from collections import deque
from random import randint

Num = randint(0,100)
history = deque([], 5)

def guess(k):
    if k == Num:
        print "success"
        return True
    elif k > Num:
        print "the number is bigger"
    else:
        print "the number is smaller"
    return False

while True:
    line = raw_input("please input a number(1~100):")
    if line.isdigit():
        k = int(line)
        history.append(k)
        if guess(k):
            break
    elif line == 'history' or line == 'h?':
        print list(history)
    else:
        print 'input error'
```
####将对象存储进文件进行持久化
```python
import pickle
from collections import deque
q = deque([3,4,5,6,7])
# dump()方法传入两个参数: 1.一个是要保存的对象；2.一个可写的文件对象
pickle.dump(q,open('history','w'))
q2 = pickle.load(open('history'))
print q2
```
####cmd乱码修改
>chcp 936 #utf-8输出
