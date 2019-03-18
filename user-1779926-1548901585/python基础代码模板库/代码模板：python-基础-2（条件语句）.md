##2 条件语句
 在写代码的时候， 往往需要根据某些条件进行判断，并根据判断结果执行不同的分支代码。
### 2.1  if 语句 elif else
```python
c = 1
if c == 1:
	print 11111
elif c ==2:
	print 3333
else:
	print 2222
```
### 2.2 循环
#### 2.2.1 while 循环
while 循环的思想是，只要某一条件成立，就不断执行循环体里的代码，直到条件不再成立。
```python
c = 1
# 一定要记得在循环体里修改条件变量
# 否则可能导致死循环
while c < 10:
	print c
	c += 1
```
### 2.2.2 for循环
```python
for x in xrange(1,5):
	print x
```
###2.3 for循环遍历字典和列表
```python
a = {'k1': 1,'k2':2.1,'k3':"hello"}
b = [1,2.1,'hello']
c = (1,2,3)
```
#### 2.3.1 遍历列表b
```python
# 遍历列表，这里的i只是一个临时变量，取别的名称也行
for i in b:
	print i
```
#### 2.3.2遍历元组c
```python
for i in c:
	print i
```
#### 2.3.3 遍历字典a
```python
#1. 同时遍历key与value
for key,value in a.items():
	print key,value
#2. 遍历字典的全部key，这里的key也只是一个临时变量，名称不重要
for key in a.keys():
	print key
#3. 遍历字典的全部value，这里的value也只是一个临时变量，名称不重要
for value in a.values():
	print value
```
