 #学会装饰器，Python更进阶
函数作用域到闭包到装饰器讲解，及闭包和装饰器的运用。

- [√] 慕课网Meshare_huang老师: python进阶

<!--more-->
![mark](http://upload-images.jianshu.io/upload_images/1779926-bc50fdb696061d4b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

# 函数作用域

介绍 Python 的函数作用域，了解函数作用域 `LEGB` 间关系。

主要内容:

- 函数作用域LEGB
- 闭包理解与使用
- 装饰器

`LEGB`: `L>E>G>B`

- `L: local` 函数内部作用域
- `E: enclosing` 函数内部与内嵌函数之间(主要是内置函数对我们函数变量的一个引用，称之为闭包)
- `G: global` 全局作用域: 我们所定义的全局变量。
- `B: build-in` 内置作用域: Python解释器默认导入的一些变量。

`build-in`比如：`tuple`，`list`，`元组`等。

知识点： `LEGB`原则: 首先从`函数内部作用域`查找，然后去`enclosing作用域`中去查找,然后依次是`全局` 和`内置`。

例子(使用Python3.4版本 + sublimeText)：

```python
passline = 60          #passline 是全局变量（global）
def func(val):
	if val >= passline:
		print ('pass')
	else:
		print ('failed')

func(89)
```
运行结果:

```
pass
```

分析：

- 当我们定义一个函数时，会引入一个作用域：`L: local`.
- 当我们对于`func`函数进行调用时，`val`就是我们的一个本地变量。
- 在函数内部并没有定义`passline` 的值。这个时候回去全局变量找查找。如果全局没有还会继续向上查找`B: build-in`

当总分变为150.我们的`passline`应该设为`90`,如果我们不想修改全局的`passline`,
我们可以在函数内部定义新的passline。因为`L>G`,所以会以我们自己函数内部的`local`域为准。

实现代码:

```python
passline = 60          #passline 是全局变量（global）
def func(val):
	passline = 90      #这里的passline是函数内部作用域(local)
	if val >= passline:
		print ('pass')
	else:
		print ('failed')

func(89)
```

运行结果：

```
failed
```

Python解释器查找顺序为`L-->E-->G-->B`,如果已经找到，就不会找更上层。

如果我们需要拿到两个分数中的更大值。

实现代码:

```python
def Max(val1,val2):
	return max(val1,val2)

print (Max(90,100))
```

运行结果：

```
100
```

`Max`函数内部引用了一个内置函数方法`max`.这个内置方法在`Max`函数中以及整个文件中都没有定义。

这个`max`存在于我们的`build-in`.Python解释器在运行时会自动导入内置的方法。比如`list,tuple`

函数内部的函数产生`enclosing `：

实现代码:

```python
passline = 60          #passline 是全局变量（global）
def func(val):
	passline = 90      #这里的passline是函数内部作用域(local)
	if val >= passline:
		print ('pass')
	else:
		print ('failed')
	def in_func():
		print (val)
	# 调用方式1
	in_func()
	# 调用方式2：将in_func()返回。这样我们就可以在外部调用。
func(89)
```

运行结果：

```
failed
89
```

`val`变量的查找过程:  `print (val)` 中val的查找过程。

- `in_func()`内部并没有定义这个`val`的值。也就是`local作用域`中没有这个值.
- 下一步我们就会去`enclosing `作用域查找。也就是我们的`func(val)`中引入的有val变量。
- 找到传入的`val`值`89`


# 什么是闭包

介绍什么是闭包，为什么使用闭包，闭包作用

## 装饰器之闭包1

`closure`：内部函数对`enclosig`作用域的变量进行引用

>概念：如果在一个内部函数里，对在`外部作用域（但不是在全局作用域）`也就是`enclosig`作用域的变量进行引用，那么内部函数就被认为是`闭包（closure）`

函数实质与属性

- 函数是一个对象
- 函数执行完之后内部变量回收(如果我们中间产生一个变量，这个变量返回那么他不会被回收: 因为他的引用计数还补为0)
- 作为一个对象函数拥有自己的属性(闭包函数的特殊属性)
- 函数返回值

正常的调用参考上一章代码。

```python
passline = 60          #passline 是全局变量（global）
def func(val):
	passline = 90     
	if val >= passline:
		print ('pass')
	else:
		print ('failed')
	def in_func():
		print (val)
	in_func()	
	return in_func # in_func是func内部的一个函数对象。
f = func(89) #使用f来接收返回值
f() #infunc
```
运行结果：

```
failed
89
89
```
`func`执行完成之后，他的`val`值就会消失。但是我们再次调用`f()`
因为`infunc`的引用计数还没有归零。所以会一直保留。

- 当我们这时候运行`f()`，`val`值是哪来的呢？

```python
print (f.__closure__)
```
运行结果
```
(<cell at 0x0000000005236C48: int object at 0x00000000052169F8>,)
```
这里面有一个`int object` 地址为: `0x00000000052169F8`

添加一行查看`传入func`的`val`的`id`值(`%x` 表示使用`16进制`打印。`id()`可打印出`val`的`id`) 	

```
def func(val):
	print ('%x'%id(val))
```

![mark](http://upload-images.jianshu.io/upload_images/1779926-2f087ed1677ef7ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

可以看出`value`的`id`值和`__closure__`中的那个`int object`的值一样。

如果我引用了外部`enclosing`的值。会将该值保存在`函数的属性`中。
当我们调用`f()`时并没有去代码中查找。而是去函数的属性(`Local域`)中查找.

可以理解为在`in_func`定义的时候，函数属性中会添加`(val,)`
这个属性的值是一个元组。是不能变得。

总分从`100`到`150`涉及到passline的取值问题。

```python
passline = 60  #100      
def func(val):
	passline = 90 # 150
```

最常用的解决方案是定义两个函数分别处理:

实现代码:

```python
def func_150(val):
	passline = 90 # 150    
	if val >= passline:
		print ('pass')
	else:
		print ('failed')
def func_100(val):
	passline = 60 # 150    
	if val >= passline:
		print ('pass')
	else:
		print ('failed')
func_100(89) 
func_150(89)
```

运行结果：

```
pass
failed
```

上面两个函数在处理逻辑上基本一致，如果后期对于打印出来的信息要做修改，就得修改两遍。

如要为print添加数值的显示。

```python
def func_150(val):
    print ('%d pass' %val)
def func_100(val):
    print ('%d pass' %val)
```

所有的改动都得做两遍。（这里想起了c++的模板）

进阶版修改:

```python
def  set_passline(passline): #passline = 60
	def cmp(val): #cmp 的__closure__属性中加入passline
		if val >= passline:
			print ('%x'%id(passline))
			print ('pass')
		else:
			print ('%x'%id(passline))
			print ('failed')
	return cmp

f_100 = set_passline(60)
f_150 = set_passline(90)
print (type(f_100)) # f_100就是一个函数对象。__closure__属性中存放着passline

print (f_100.__closure__)
f_100(89)
print (f_150.__closure__)
f_150(89)
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-e6843299924ad905.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

理解：闭包就是`内部函数(cmp)`对于`外层函数(set_passline)`变量(`passline`)的使用（也就是对`enclosing作用域变量`的使用），会将我们使用的这个变量(`passline`)放到我们的`__closure__`这个属性中。当我们内部函数处理时会直接对于这个属性值进行使用。

闭包的作用：

- 封装
- 代码复用

## 装饰器之闭包2

上节我们接触到的`enclosing域`中的变量`passline`是一个整数，我们可不可以把它换成一个函数对象呢。

废话，当然可以。

例子： 求一组数据的总分和平均分

```python
def my_sum(*arg):
    return sum(arg)


def my_average(*arg):
    return sum(arg) / len(arg)


print(my_sum(1, 2, 3, 4, 5))
print(my_average(1, 2, 3, 4, 5))
```
运行结果：

```
15
3
```

- 此时如果我们的`my_average`需要加上一个对于传入参数不为0的判断。

```
print(my_average())
```

因为传入为空会报`除0`错误：

```
ZeroDivisionError: integer division or modulo by zero
```
- 而我们如果想要给`my_sum`传一个字符串进去。

```python
print(my_sum(1,2,3,4,5,'6'))
```

会报错不支持`int`和`str`相加:

```
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

说明我们的函数写的不够健全。我们还需要对于函数的参数进行判断。

1. 判断参数有没有长度。也就是不能为空。
2. 对于参数的类型进行判断，限制为只是`int型`

普通版实现代码:

```python

def my_sum(*arg):
    if len(arg) == 0:
        return 0
        for val in arg:
            if not isinstance(val, int):
                return 0
        return sum(arg)


def my_average(*arg):
    if len(arg) == 0:
        return 0
        for val in arg:
            if not isinstance(val, int):
                return 0
    return sum(arg) / len(arg)


print(my_sum(1, 2, 3, 4, 5))
print(my_average(1, 2, 3, 4, 5))
print(my_sum(1, 2, 3, 4, 5, '6'))
```

运行结果：

```
None
3
None
```

可以看出两部分代码都有重合，我们使用进阶方法，使用闭包方式完成。

```python
def my_sum(*arg):
    print ('in mysum arg=', arg)
    return sum(arg)


def my_average(*arg):
    print ('in my_average arg=', arg)
    return sum(arg) / len(arg)


def dec(func):
    def in_dec(*arg):  # my_sum -> __closure__
        print ('in dec arg=', arg)
        if len(arg) == 0:
            return 0
        for val in arg:
            if not isinstance(val, int):
                return 0
        return func(*arg)
    return in_dec


# dec() return indec -> my_sum;
# mysum = in_dec(*arg);
my_sum = dec(my_sum)
my_average = dec(my_average)

print(my_sum(1, 2, 3, 4, 5))
print(my_sum(1, 2, 3, 4, 5, '6'))
# print(my_average(1, 2, 3, 4, 5))
# print(my_average())
```

运行结果：

```
('in dec arg=', (1, 2, 3, 4, 5))
('in mysum arg=', (1, 2, 3, 4, 5))
15
('in dec arg=', (1, 2, 3, 4, 5, '6'))
0
```


我们把原有的重复的逻辑操作放进了我们的`in_dec`中。
`def dec(func):`中`func`是我们传入的一个参数。因此我们调用这个函数时。我们可以指定它做什么

此时我们想要让`func()`对于`arg`进行处理。所以`return func(*arg)`

函数名可以进行重新赋值：`my_sum = dec(my_sum)`

- 第一步我们调用的是`dec(func)`,调用之后将`my_sum`传了进去。因为在`in_dec`中我们对他进行了使用。所以我们的`in_dec`就是一个闭包。
	- 这个时候`my_sum`就已经作为`in_dec`的一个`__closure__` 属性被保存。那么在`in_dec`内部就可以直接使用`my_sum`
- 第二步`my_sum = dec(my_sum)`时。`my_sum`将保存`dec()`被调用后的返回值也就是`in_dec(*arg)`对象。

具体执行函数： 

- 第一步是调用的`in_dec`函数
- 第二步是调用的`func`也就是`my_sum`

这里所有参数的处理都是`in_dec`处理的。所以当第二个直接返回`0`时，`my_sum`直接没有被调用。

```python
# dec() return indec -> my_sum;
# mysum = in_dec(*arg);
```

# Python装饰器

python装饰器

	- 装饰器用来装饰函数
	- 返回一个函数对象
	- 被装饰函数标识符指定返回的函数对象(A被装饰了，再用A接收被装饰后返会的新对象)被装饰的函数去哪了？
	- 语法糖 @deco

`my_sum = dec(my_sum)`执行过程：

- `dec(my_sum)`将`my_sum`作为一个参数传给`dec`函数.
- `dec`函数内部有一个内置的函数`in_dec`
- 内置函数作为返回值重新赋给了`my_sum`

理解：装饰器就是对于闭包的一个使用。Python提供了语法糖`@`

实现代码:

```python
def dec(func):
    print "call dec"

    def in_dec(*arg):  # my_sum
        print ('in dec arg=', arg)
        if len(arg) == 0:
            return 0
        for val in arg:
            if not isinstance(val, int):
                return 0
        return func(*arg)
    return in_dec


@dec
def my_sum(*arg):
    print ('in mysum arg=', arg)
    return sum(arg)
```

运行结果：

```
call dec
```

并没有显式的调用任何方法，但是打印出了`call dec`
因为`@dec`就相当于`my_sum = dec(my_sum)`已经进行了调用。此时的`my_sum`已经是装饰后的函数`in_dec`了。

```python
print (my_sum(1, 2, 3, 4, 5))
```
运行结果：

```
15
```

`@dec`就相当于`my_sum = dec(my_sum)`，这是python解释器支持的语法糖。

实现代码:

```python
def dec(func):
    print "call dec"

    def in_dec(*arg):  # my_sum

        print('in dec arg=', arg)
        if len(arg) == 0:
            return 0
        for val in arg:
            if not isinstance(val, int):
                return 0
        return func(*arg)
    print('return in_dec')
    return in_dec


@dec
def my_sum(*arg):  # my_sum = in_dec
    print('in mysum arg=', arg)
    return sum(arg)


print(my_sum(1, 2, 3, 4, 5))

```

运行结果：

```
call dec
return in_dec
('in dec arg=', (1, 2, 3, 4, 5))
('in mysum arg=', (1, 2, 3, 4, 5))
15
```
装饰器就是对于我们的函数进行了功能的丰富。内部继续调用具体函数，
将新函数返回，并覆盖原函数变量。实质就是对于闭包的使用。
`my_sum`当做`enclosing`域的变量。被内置函数`in_dec`所使用。

实现代码:

```python
def deco(func):
    def in_deco():
        print('in decp')
        func()
    print('call deco')


@deco
def bar():
    print('in bar')

print (type(bar))
```

运行结果：

```
call deco
<type 'NoneType'>
```

因为我们在外层函数`deco`中返回的是`None`

实现代码:

```python
def deco(func):
    def in_deco():
        print('in decp')
        func()
    print('call deco')
    return in_deco


@deco
def bar():
    print('in bar')


print(type(bar))
bar()
```

运行结果：

```
call deco
<type 'function'>
in decp
in bar
```
给被装饰函数加上参数：第二个装饰器。

```python
@deco
def bar(x, y):
    print('in bar', x + y)
```

我们如果给被装饰函数加上了参数。那么也要对要返回的内置函数`in_deco`加上参数。
否则报错：

```
TypeError: bar() takes exactly 2 arguments (0 given)
```

实现代码:

```python
def deco(func):
    def in_deco(x, y):
        print('in decp')
        func(x, y)
    print('call deco')
    return in_deco


@deco
def bar(x, y):
    print('in bar', x + y)


print(type(bar))
bar(1, 2)
```

运行结果：

```
call deco
<type 'function'>
in decp
('in bar', 3)
```

要同时对于`in_deco(x, y)`和`func(x, y)`都加上参数。

假设我们是Python解释器：

- 我们看到了`@deco`我们将会调用`deco()`然后将`bar`也就是被装饰函数作为参数传入。

```
deco(bar) -> indeco
bar -> in_deco  #(enclosing作用域)变量保存在in_deco的`__closure__`使用。
bar() in_deco() 重新调用自己属性中的被装饰函数
```

- 调用之后`deco`会返回一个`in_deco`的函数对象。
- 现在根本没地方选的。只能存在原来的`bar`中。因此`bar`已经变成了`in_deco`
- 而`in_deco`中调用的`func(x,y)`是存放在自己的`bar.__closure__`中了。

```python
def deco(func):
    def in_deco(x, y):
        print('%x' % id(func))
        print(in_deco.__closure__)
        print('in deco')
        func(x, y)
    print('call deco')
    return in_deco


@deco
def bar(x, y):
    print('%x' % id(bar))


bar(1, 2)
```
运行结果：

```
call deco
5491ba8
(<cell at 0x0000000004F66F78: function object at 0x0000000005491BA8>, <cell at 0x000000000546EA38: function object at 0x0000000005491C18>)
in deco
5491c18
```

可以看出`in_deco.__closure__`已经将`原始bar函数`进行了保存。

这里就有一个问题了？可以看出里面保存着两个变量。(第一个是一个函数对象`func`，第二个便是我们的`原始bar`)







 












#
分解来说，包含下面3个条件：
1) 需要函数嵌套, 就是一个函数里面再写一个函数.
2) 外部函数需要返回一个内部函数的引
3) 外部函数中有一些局部变量, 并且, 这些局部变量在内部函数中有使用
一些概念:
1)自由变量: 外部函数中定义的局部变量, 并且在内部函数中被使用
2) 闭包: 那个使用了自由变量并被返回的内部函数就称为闭包

#支持闭包的语言有这样的特性：
1)函数是一阶值（First-class value），即函数可以作为另一个函数的返回值或参数，还可以作为一个变量的值
2)函数可以嵌套定义，即在一个函数内部可以定义另一个函数

```python
#代码示例（点开编辑查看）
#coding:utf-8
#闭包：实现封装，实现代码复用
def set_passline(passline):   #passline = 60
    def cmp(val):             #cmp() -> 闭包
        if val >= passline:
            print('pass')
        else:
            print("failed")
    return cmp
f_100 = set_passline(60)   #f_100是函数，也即返回的函数cmp
f_150 = set_passline(90)
f_100(89)
f_150(89)
```
封装和代码的复用




闭包会保留来自外围作用域变量的信息。
Python 中函数对象都拥有一个 __closure__ 属性。
__closure__ 对象返回一个由 cell 对象组成的元组，cell 对象记录了定义在外围作用域的变量信息。
对于那些不是闭包的函数对象来说，__closure__ 属性值为 None


函数调用顺序：（）
```
#1、dec函数返回in_dec -> my_sum
#2、my_sum = in_dec(*arg)
print(my_sum(1,2,3,4,5))
print(my_sum(1,2,3,4,5,'6'))
```
打印结果：
```
in_dec(*arg)= (1, 2, 3, 4, 5)
my_sum
15
in_dec(*arg)= (1, 2, 3, 4, 5, '6')
0
```

