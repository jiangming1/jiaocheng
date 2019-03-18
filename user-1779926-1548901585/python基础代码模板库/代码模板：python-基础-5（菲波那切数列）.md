选自python高效开发实战。
```python
# -*- coding: utf-8 -*-
#!/usr/bin/env python
## linux系统告诉系统python解释器位置


import time

def fbis(num):
    result=[0,1]
    for i in range(num-2):
# 切片，负数为取倒数第几个值：如result[-1]为取倒数第一个值
        result.append(result[-2]+result[-1])
    return result

# enumerate() 将数组或列表组成一个带索引的序列
# time.sleep(1) 让程序暂停一秒
def main():
    result = fbis(10)
    fobj = open('result.txt', 'w+')
    for i, num in enumerate(result):
        print u"第 %d 个数是: %d" % (i, num)
        fobj.write("%d"%num)
        time.sleep(1)
    fobj.close()
'''
 此处if用于判断代码是被其他模块导入还是被直接执行，如果是直接执行，则调用main（）
__name__为python的只读内置变量，在模块访问该变量时，
其随着模块的被调用方法的不同而有不同值；
当该模块被直接执行调用时，__name__的值为__main__;
当该模块被其他模块用import语句调用时，该值为当前模块名
另一个常用的内置变量为__class__,在类内部使用时其内容为当前类名。
'''   
if __name__ == '__main__':
    main()

```
