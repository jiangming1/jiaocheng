实际案例:
>1. 某班学生期末考试成绩，语文，数学，英语分布存储在三个列表当中
    同时迭代三个列表，计算每个学生的总分
2. 某年级有四个班，某次考试每班英语成绩分布存储在4个列表中，依次迭代每个列表，统计全年级成绩高于90分的人

解决方案:
> 并行，采用内置函数zip，它能将多个长度一样的可迭代对象合并，每次迭代返回一个元组
串行，采用标准库里的itertools.chain，它能将多个可迭代对象链接

普通做法:
```python

from random import randint
math = [randint(60,100) for _ in xrange(40)]
english = [randint(60,100) for _ in xrange(40)]

for i in xrange(len(math)):
    print math[i] + english[i]

```
zip实现并行迭代
```python
from random import randint
math = [randint(60,100) for _ in xrange(40)]
english = [randint(60,100) for _ in xrange(40)]
# zip 

print zip([1,2,3],['a','b','c'],[7,8,9])
# out:[(1, 'a', 7), (2, 'b', 8), (3, 'c', 9)]

for m,e in zip(math,english):
    print m+e
```
itertools.chain实现多个可迭代对象的链接
```python
from random import randint
from itertools import chain
c1 = [randint(60,100) for _ in xrange(40)]
c2 = [randint(60,100) for _ in xrange(42)]
count = 0
for s in chain(c1,c2):
    if s > 90:
        count +=1
print count
```
