>实际案例：
足球联赛，对于每轮球员的进球进行统计:
第一轮:{'梅西':2,'本泽马':1,'C罗':3}
第二轮:{'梅西':4,'格里兹曼':1,'C罗':3}
第三轮:{'梅西':2,'贝尔':1,'C罗':3}
统计出前N轮，每场比赛都有进球的球员。

###普通方法:
```python
from random import randint, sample
# 随机取样出3到5个
persons = sample('abcdefg',randint(3,6))

dic1 = {k:randint(0,5) for k in persons}
dic2 = {k:randint(0,5) for k in persons}
dic3 = {k:randint(0,5) for k in persons}

res = set()
# 遍历dic1的key。
# 如果dic2与dic3中也有这个key，将其保存进res
for k in dic1:
    if k in dic2 and k in dic3:
        res.add(k)
print res
```
###解决方案
>1. 利用字典的viewskeys()方法，得到一个字典keys的集合
2. 利用map函数，得到所有字典的keys的集合
3. 使用reduce函数，取所有字典的keys的集合的交集

#### 利用字典的viewskeys()方法，得到一个字典keys的集合
并做`&`交集运算
```python
print type(dic1.viewkeys())

print dic1.viewkeys() & dic2.viewkeys() & dic3.viewkeys()
```
#### 利用map函数，得到所有字典的keys的集合解决n轮比较
```python
worklist =[dic1,dic2,dic3]
print map(dict.viewkeys,worklist)

print reduce(lambda a,b:a & b,map(dict.viewkeys,worklist))
```
