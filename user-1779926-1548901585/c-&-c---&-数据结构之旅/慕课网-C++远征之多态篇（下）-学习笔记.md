###RTTI（运行时类型识别）
Run-Time Type Identification

- typeid < - > dynamic_cast

例子：

```c
class Flyable
{
public:
	virtual void takeoff() = 0;//起飞
	virtual void land() = 0; //降落
};

class Bird:public Flyable
{
public:
	void foraging(){}
	virtual void takeoff(){}
	virtual void land(){}
private:
	//...
};

class Plane:public Flyable
{
public:
	void carry(){}
	virtual void takeoff(){}
	virtual void land(){}
};
```

使用时：

```c
void doSomething(Flyable *obj)
{
	obj ->takeoff();
	//如果是bird，则觅食
	//如果是plane，则运输
	obj -> land();
}
```

如果对指针能进行判断。然后根据传入指针不同调用不同方法。

```c
void doSomething(Flyable *obj)
{
	obj ->takeoff();
	cout << typeid(*obj).name() <<endl;
	if(typeid(*obj) == typeid(Bird))
	{
		Bird *bird = dynamic_cast<Bird *>(obj);
		//尖括号内是我们想要转化成的类型。
		bird -> foraging();
	}
	obj -> land();
}
```
总结:

dynamic_cast注意事项：
- 只能应用于指针和引用的转换
- 要转换的类型中必须包含虚函数
- 转换成功返回子类的地址，失败返回NULL

typeid注意事项：
- `type_id`返回一个`type_info`对象的引用
- 如果想通过基类的指针获得派生类的数据类型，基类必须带有虚函数
- 只能获取对象的实际类型.(不能判断当前指针)

![type_info](http://upload-images.jianshu.io/upload_images/1779926-59fc44787beaecb2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`name()` & 运算符重载

####RTTI代码示例

![要求](http://upload-images.jianshu.io/upload_images/1779926-d931e97e42eae89c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
#ifndef FLYABLE_H
#define FLYABLE_H
class Flyable
{
public:
	virtual void takeoff() = 0;//起飞
	virtual void land() = 0; //降落
};
#endif

#ifndef PLANE_H
#define PLANE_H

#include <string>
#include "Flyable.h"
using namespace std;

class Plane :public Flyable
{
public:
	void carry();
	virtual void takeoff();
	virtual void land();
};


#endif


#include <iostream>
#include "Plane.h"
using namespace std;

void Plane::carry()
{
	cout << "Plane::carry()" << endl;
}
void Plane::takeoff()
{
	cout << "Plane::takeoff()" << endl;
}
void Plane::land()
{
	cout << "Plane::land()" << endl;
}


#ifndef BIRD_H
#define BIRD_H
#include "Flyable.h"
#include <string>
using namespace std;
class Bird :public Flyable
{
public:
	void foraging();
	virtual void takeoff();
	virtual void land();
};

#endif // !BIRD_H

#include <iostream>
#include "Bird.h"
using namespace std;

void Bird::foraging()
{
	cout << "Bird::foraging()" << endl;
}
void Bird::takeoff()
{
	cout << " Bird::takeoff()" << endl;
}
void Bird::land()
{
	cout << "  Bird::land()" << endl;
}
```

main.cpp1:

```c
#include <iostream>
#include "Bird.h"
#include "Plane.h"
using namespace std;
#include <stdlib.h>

void doSomething(Flyable *obj)
{
	cout << typeid(*obj).name() << endl;
	obj->takeoff();
	if (typeid(*obj) == typeid(Bird))
	{
		Bird *bird = dynamic_cast<Bird *>(obj);
			bird->foraging();
	}
	if (typeid(*obj) == typeid(Plane))
	{
		Plane *plane = dynamic_cast<Plane *>(obj);
		plane->carry();
	}

	obj->land();
}

int main()
{
	Bird b;
	doSomething(&b);

	system("pause");
	return 0;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-995fe35ad2541b45.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 传入的是bird。所以执行了bird分支下的觅食。
- 当传入是plane时。执行carry。

![plane-carry](http://upload-images.jianshu.io/upload_images/1779926-7e15012f9c7e1b92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####代码说明typeid .dynamic_cast注意事项

```c
int main()
{
	int i =0;
	cout <<typeid(i).name() << endl; 
}
```

输出为int。打印出数据类型。

```c
int main()
{
	Flyable *p = new Bird();
	cout << typeid(p).name() << endl;
	cout << typeid(*p).name() << endl;
	system("pause");
	return 0;
}
```
![p类型为Flyable *,*p类型为bird对象](http://upload-images.jianshu.io/upload_images/1779926-aac3ec79d7556d09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

将Flyable.h的两个纯虚函数改为普通的。

```c
#ifndef FLYABLE_H
#define FLYABLE_H
class Flyable
{
public:
	void takeoff(){ };//起飞
	void land() {}; //降落
};
#endif
```

bird.h虚函数去掉。

```c
#ifndef BIRD_H
#define BIRD_H
#include "Flyable.h"
#include <string>
using namespace std;
class Bird :public Flyable
{
public:
	void foraging();
	void takeoff();
	void land();
};

#endif // !BIRD_H
```

 bird 和flyable变成普通的继承。

 ```c
 int main()
{
	// Flyable *p = new Bird();
	// Bird *b = dynamic_cast<Bird *>p;
	// //“dynamic_cast”:“Flyable”不是多态类型
	// 
	
	system("pause");
	return 0;
}
 ```

对于dynamic_cast.转换类型还是被转类型都要求有虚函数。

```c
int main()
{
	Flyable p;
	Bird b = dynamic_cast<Bird>p;
	//“dynamic_cast”:“Flyable”不是多态类型

	system("pause");
	return 0;

```
- 只能应用于指针和引用的转换

####练习题

- 继承关系不是RTTI的充分条件，只是必要条件，所以存在继承关系的类不一定可以用RTTI技术
- RTTI的含义是运行时类型识别
- RTTI技术可以通过父类指针识别其所指向对象的真实数据类型
- 运行时类型别必须建立在虚函数的基础上，否则无需RTTI技术

###巩固练习
>定义一个能够移动（Movable）类，要求含有纯虚函数move
定义一个公交车（Bus）类，继承Movable类，并实现函数move，定义函数carry
定义一个坦克（Tank）类，继承Movable类，并实现函数move，定义函数shot。
定义函数doSomething（Movable *obj）,根据s指向对象的类型调用相应的函数。

实例化公交车类和坦克类，将对象传入到doSomething函数中，调用相应函数

```c
#include <iostream>
#include <stdlib.h>
#include <string>
#include <typeinfo>
using namespace std;

/**
 * 定义移动类：Movable
 * 纯虚函数：move
 */
class Movable
{
public:
    virtual void move() = 0;
};

/**
 * 定义公交车类：Bus
 * 公有继承移动类
 * 特有方法carry
 */
class Bus : public Movable
{
public:
    virtual void move()
    {
        cout << "Bus -- move" << endl;
    }
    
    void carry()
    {
        cout << "Bus -- carry" << endl;
    }
};

/**
 * 定义坦克类：Tank
 * 公有继承移动类
 * 特有方法fire
 */
class Tank :public Movable
{
public:
    virtual void move()
    {
        cout << "Tank -- move" << endl;
    }

    void fire()
    {
        cout << "Tank -- fire" << endl;
    }
};

/**
 * 定义函数doSomething含参数
 * 使用dynamic_cast转换类型
 */
void doSomething(Movable *obj)
{
    obj->move();

    if(typeid(*obj) == typeid(Bus))
    {
        Bus *bus = dynamic_cast<Bus *>(obj);
        bus->carry();
    }

    if(typeid(*obj) == typeid(Tank))
    {
        Tank *tank = dynamic_cast<Tank *>(obj);
        tank->fire();
    }
}

int main(void)
{
    Bus b;
    Tank t;
    doSomething(&b);
    doSomething(&t);
    return 0;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-f7fb6e333c82960d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####异常处理
>- 异常：程序运行期出现的错误。
- 异常处理：对有可能发生异常的地方做预见性的安排

网线- 内存不足。

关键字：

- `try...catch...` :尝试  捕获
- throw 抛出异常

思想：

主逻辑(try)与异常处理逻辑(catch)分开


![异常处理](http://upload-images.jianshu.io/upload_images/1779926-37b398d5fde91b9d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

三个函数f1,f2,f3.用f2调用f1，f3调用f2.
当f1出现异常会往上抛。如果f2可以处理就可以处理完成。
如果不能处理，会继续进行异常的传播。f3捕获并处理。

```c
void fun1()
{
	throw 1;
}

int main(){
	try {
		fun1();
	}catch(int)//throw的是1，所以用int捕获
	{
		//.....
	}
	return 0;
}

try{
	fun1();
}
catch(int)
{}
catch(double)
{}
catch)(...)//捕获所有的异常
{}
```

一个try可以有多个catch。不同异常做不同处理。

- 抛出值，捕获数据类型。
- 下面我们来做捕获值。

```c
char getChar(const string& aStr,const int aIndex)
{
	if (aIndex > aStr.size())
	{
		throw string("ivalid index!");
	}
	return aStr[aIndex];
}

string str("hello world");
cahr ch;
try{
	ch = getChar(str,100);//这句抛异常，下句不会运行
	cout << ch << endl;
}catch(string& aval){
	cout << aval << endl;
}

```
常见的异常：

- 数组下标越界
- 除数为0
- 内存不足

异常处理与多态的关系：

![异常处理&多态](http://upload-images.jianshu.io/upload_images/1779926-724ae02b97dee067.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

定义一个接口exception。多个子类来继承该类。那么我们可以通过父类对象捕获不同子类对象的异常。

```c
void fun1()
{
	throw new SizeErr();
}

void fun2()
{
	throw new MemoryErr();
}

try{
	fun1();
}catch(Exception &e)
{
	e.xxx();
}

try{
	fun2();
}catch(Exception &e)
{
	e.xxx();
}
```

通过父类的引用，调用相应的子类处理函数。

####异常处理代码示例
![要求](http://upload-images.jianshu.io/upload_images/1779926-f7110d557371d9b7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
#include <iostream>
#include <stdlib.h>
#include "IndexException.h"
using namespace std;
void test()
{
	throw 0.1;
}
int main(void)
{
	try
	{
		test();
	}
	catch (double)
	{
		cout << "exception" << endl;
	}
	system("pause");
	return 0;
}
```

- 1 对应 int
- 1.0 对应 double

```c
catch (double &e)
	{
		cout << e << endl;
	}
```

可以打印出抛出来的异常值：如0.1

```c
#ifndef EXCEPTION_H
#define EXCEPTION_H
class Exception
{
public:
	virtual void printException();
	virtual ~Exception() {};


};
#endif 


#include "Exception.h"
#include <iostream>
using namespace std;

void Exception::printException()
{
	cout << " Exception::printException()" << endl;
}


#ifndef INDEX_EXCEPTION_H
#define INDEX_EXCEPTION_H

#include "Exception.h"
class IndexException:public Exception
{
public:
	virtual void printException();

};
#endif


#include "IndexException.h"
#include <iostream>
using namespace std;

void IndexException::printException()
{
	cout << "提示：下标越界" << endl;
}


#include <iostream>
#include <stdlib.h>
#include "IndexException.h"
using namespace std;
void test()
{
	throw IndexException();
}
int main(void)
{
	try
	{
		test();
	}
	catch (IndexException &e)
	{
		e.printException();
	}
	system("pause");
	return 0;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-7a99e84043bccc83.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
int main(void)
{
	try
	{
		test();
	}
	catch (Exception &e)
	{
		e.printException();
	}
	system("pause");
	return 0;
}
```
依然打印出数组的提示。父类的引用可以使用到子类的处理函数。

```c
int main(void)
{
	try
	{
		test();
	}
	catch (...)
	{
		cout << "error" << endl;
	}
	system("pause");
	return 0;
}
```
通过...可以捕获到所有异常。

###练习题

- 在C++中异常处理通常使用try...catch...语法结构。
- 一个try语句可以对应一个或多个catch语句，但不能没有catch语句
- C++中使用throw抛出异常，通过catch捕获异常

####巩固练习
>函数division的两个参数为dividend（被除数）和divisor（除数）
要求用户输入除数和被除数，并作为参数传递给division函数
如果除数为0，则抛出异常，并被捕获，将异常的内容显示到屏幕上

```c
#include <iostream>
#include <string>
#include <stdlib.h>
using namespace std;

/**
 * 定义函数division
 * 参数整型dividend、整型divisor
 */
int division(int dividend, int divisor)
{
    if(0 == divisor)
	{
        // 抛出异常，字符串“除数不能为0”
		throw string("除数不能为0");
	}
	else
	{
		return dividend / divisor;
	}
}

int main(void)
{
	int d1 = 0;
	int d2 = 0;
	int r = 0;
	cin >> d1;
	cin >> d2;
    // 使用try...catch...捕获异常
	try{
	    r = division(d1,d2);
	    cout << r << endl;
	}catch(string &str){
	    cout << str <<endl;
	}

	return 0;
}
```

运行结果：

除数不能为0.
