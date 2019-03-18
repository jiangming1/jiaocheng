##3 循环控制

循环控制主要包括三种：    `pass` 、 `continue` 、 `break` 。
1. pass 表示什么也不做，只是占一行代码的位置；
2. continue 表示立即退出本轮循环，继续执行后续轮循环；
3. break 表示立即推出循环，后续循环也不再执行。
```python
#pass例子
for x in xrange(0, 10):
    if x == 5:
        pass
    else:
        print x
#continue例子
for x in xrange(0, 10):
    if x == 5:
        continue
    print x

#上面两个例子的结果都是将5跳过，打印出了除五以外的其他数字。

#break例子
for x in xrange(0, 10):
    if x == 5:
        break
    print x

#直接跳出循环。所以只打印到4.
```
