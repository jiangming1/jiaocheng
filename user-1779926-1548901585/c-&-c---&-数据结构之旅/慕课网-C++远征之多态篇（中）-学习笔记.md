##c++远征之多态篇
###纯虚函数 & 抽象类
例子：

```c
class Shape
{
public:
	virtual double calcArea()//虚函数
	{return 0;}
	virtual double calcPerimeter() = 0;//纯虚函数
}
```

纯虚函数：

- 没有函数体
- 直接等于0


![普通虚函数有值，纯虚函数直接为0](http://upload-images.jianshu.io/upload_images/1779926-793446e7f0708a61.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 在虚函数表中直接写为0，

- 包含纯虚函数的类，就是抽象类。上面含有纯虚函数的shape类就是一个抽象类。

- 纯虚函数无法调用，所以抽象类无法实例化对象

```c
class Person
{
public:
	Person(string name);
	virtual void work() =0;
	virtual void printInfo() =0;
};
class Worker: public Person
{
public:
	Worker(string name)
	virtual void work() = 0;
	virtual void printInfo() { cout << m_strName <<endl;}
private:
	string m_strName;
};
class Dustman: public Worker
{
public:
	Worker(string name)
	virtual void work() {cout << "扫地"};
	virtual void printInfo() { cout << m_strName <<endl;}
private:
	string m_strName;
};
```

- 抽象类的子类也有可能是抽象类。抽象类的子类只有把抽象类当中的所有纯虚函数都做了实现，子类才可以实例化对象。
- 上面代码中work只把子类的两个实现了一个。只有dustman
才能实例化对象。

###抽象类代码示例

![抽象类代码](http://upload-images.jianshu.io/upload_images/1779926-ede6475900dcfac5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


如果Worker没有实现work。则不可以实例化work。
当Worker的子类dustman实现了work。就可以实例化dustman。

代码：

```c
#ifndef PERSON_H//假如没有定义
#define PERSON_H//定义

#include <string>
using namespace std;

class Person
{
public:
	Person(string name);
	virtual ~Person() {};
	virtual void work() =0;
private:
	string m_strName;
};

#endif //结束符

//
#include "Person.h"


Person::Person(string name)
{
	m_strName = name;

}
//
#include <string>
using namespace std;
#include "Person.h"
class Worker:public Person
{
public:
	Worker(string name,int  age);
	//virtual void work();
	virtual ~Worker() {};
private:
	int  m_iAge;

};

//
#include "Worker.h"
#include <iostream>
using namespace std;
Worker::Worker(string name,int age):Person(name)
{
	m_iAge = age;
}

//void Worker::work()
//{
//	cout << "work()" << endl;
//}

#ifndef DUSTMAN_H
#define DUSTMAN_H
#include "Worker.h"
class Dustman :public Worker
{
public:
	Dustman(string name, int age);
	virtual void work();
};
#endif 

#include "Dustman.h"
#include <iostream>
using namespace std;

Dustman::Dustman(string name, int age) :Worker(name, age)
{

}
void Dustman::work() {
	cout << "扫地" << endl;
}

//main.cpp
//
#include <iostream>
#include "Person.h"
#include <stdlib.h>
#include "Dustman.h"
int main()
{
	//Person person("张三");//“Person”: 不能实例化抽象类
	//Worker worker("zhangsan", 17);// “Worker”: 不能实例化抽象类
	Dustman dustman("zhangsan", 20);

	
	system("pause");
	return 0;
}
```
> 一个抽象类之所以叫抽象类，是因为它里面有一个或以上的纯虚函数，纯虚函数的写法是virtual 函数返回类型 函数名（）=0 ，纯虚函数里面不用写任何代码。
当我们定义了一个纯虚函数，他同样会在虚函数表中出现，如图calcPerimeter ptr就是纯虚函数的指针，他的只是0（意思就是他没有指向代码区，不会实现任何方法）。他这样的目的是为了让子类在继承他的时候，再实现他的方法。

>例如circle继承了shape，circle为了可以计算周长，定义了一个叫calcPerimeter的方法，因此把他父类shape的纯虚函数calcPerimeter覆盖了，这样就可以通过子类circle来计算周长。

####练习

- 只有函数声明没有函数定义,直接等于0的虚函数是纯虚函数。
- 含有纯虚函数的类叫做抽象类。
- 不可以使用含有纯虚函数的类实例化对象。
- 抽象类的子类也可以是抽象类。

####单元练习
>定义一个动物（animal）类，要求含有虚函数eat和纯虚函数move以及数据成员m_strName，并定义构造函数和虚析构函数
定义一个狗（Dog）类，要求公有继承动物类，定义构造函数和虚析构函数，并实现自己的eat和move函数

通过动物类实例化狗类，调用狗类当中的成员函数

```c
#include <iostream>
#include <stdlib.h>
#include <string>
using namespace std;

/**
 * 定义动物类：Animal
 * 虚函数：eat()
 * 纯虚函数：move()
 * 数据成员：m_strName
 */
class Animal
{
public:
    // 默认构造函数
    Animal(){};
    // 含参构造函数
	Animal(string name){m_strName = name; cout << "Animal" << endl;}
    // 虚析构函数
	virtual ~Animal(){cout << "~Animal" << endl;}
    // 虚成员函数
	virtual void eat(){cout << "Animal--" << m_strName << "-- eat" << endl;}
    // 纯虚函数
	virtual void move() = 0;
public:
    // 数据成员
	string m_strName;
};

/**
 * 定义狗类：Dog
 * 公有继承动物类
 * 虚成员函数：eat()、move()
 */
class Dog: public Animal
{
public:
    // 默认构造函数
    Dog(){};
    // 含参构造函数
	Dog(string name){m_strName = name; cout << "Dog" << endl;}
    // 虚析构函数
	virtual ~Dog(){cout << "~Dog" << endl;}
    // 虚成员函数eat()
	virtual void eat(){cout << "Dog--" << m_strName << " -- eat" << endl;}
    // 虚成员函数move()
	virtual void move(){cout << "Dog--" << m_strName << " -- move" << endl;}
public:
    // 数据成员
	string m_strName;
};

int main(void)
{
    // 通过动物类实例化狗类
    Animal *p = new Dog("狗类");
    // 调用成员函数
	p ->eat();
    p ->move();
    // 释放内存
	delete p;
	p = NULL;
    
	return 0;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-10efbfe50dc8b42e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因为狗实现了动物类的所有纯虚构函数，所以它可以被实例化。因为父类的eat和move都是虚函数。所以子类的纯虚函数表覆盖了父类的方法。因为是虚析构函数所以父类指针销毁。子类的也一起没了。

**如果不是分.h和.cpp文件写，记得在默认构造函数加上`{}`**

###接口类
- 含有纯虚函数的类叫抽象类；仅仅含有纯虚函数的类叫接口类
- 接口类没有数据成员，所有成员函数都是纯虚函数

```c
class Shape
{
public:
	virtual double calcArea() = 0;//计算面积
	virtual double calcPerimeter() = 0;//计算周长
}
```

上述shape类是一个接口类。

接口类表达的是一种能力或协议：

```c
class Flyable
{
public:
	virtual void takeoff() = 0;//起飞
	virtual void land() = 0; //降落

}
```

飞行能力.

```c
class Bird:public Flyable
{
public:
	virtual void takeoff(){}
	virtual void land(){}
private:
	//...
}
```
鸟要实例化就得实现起飞和降落。

如果我们在使用的时候有这样一个函数。函数需要传入的指针是能飞的。鸟继承自父类flyable。is-a关系。

飞行比赛

```c
class flyMatch(Flyable *a,Flyable *b)
{
	//...
	a->takeoff();
	b->takeoff();
	a->land;
	b->land;

}
```
如果你要参加飞行比赛就得会飞。你要会飞就得实现这两个函数。相当于一种协议。

同样道理射击能力

```c
class CanShot
{
public:
	virtual void aim() = 0;//瞄准
	virtual void reload() =0;//装弹
}
```
飞机多继承飞行能力和射击之后。变成战斗机。
它要实例化就得实现下面四个函数。

```c
class Plane:public Flyable,public CanShot
{
	virtual void takeoff(){}
	virtual void land(){}
	virtual void aim(){}
	virtual void reload(){}
}
//传入两个plane。plane is a canshot
void fight(CanShot *a,CanShot *b)
{
	a -> aim();
	b -> aim();
	a -> reload();
	b -> reload();
}
```
复杂情况：

```c
class Plane:public Flyable
{
	//...
	virtual void takeoff(){}
	virtual void land(){}
}

class FighterJet:public Plane,public CanShot
{
	virtual void aim(){}
	virtual void reload(){}
}
```

这种继承情况下plane不是一个接口类。而canshot是一个接口类

```c
void airBattle(FighterJet *a,FighterJet *b)
{
	//调用flyable中约定的函数
	//调用canshot中约定的函数
}
```

###接口类代码示例

![要求](http://upload-images.jianshu.io/upload_images/1779926-5f2a7812c79031cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

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

//
#ifndef PLANE_H
#define PLANE_H
#include "Flyable.h"
#include <string>
using namespace std;
class Plane :public Flyable
{
public:
	Plane(string code);
	virtual void takeoff();
	virtual void land();
	void printCode();
private:
	string m_strCode;
};
#endif

//
#include "Plane.h"
#include <iostream>
using namespace std;
Plane::Plane(string code)
{
	m_strCode = code;
}
void Plane::takeoff()
{
	cout << "plane - takeoff" << endl;

}
void Plane::land()
{
	cout << "plane - land" << endl;

}
void Plane::printCode()
{
	cout << m_strCode << endl;
}

//
//
#ifndef FIGHTERPLANE_H
#define FIGHTERPLANE_H

#include "Plane.h"
class FighterPlane:public Plane
{
public:
	FighterPlane(string code);
	virtual void takeoff();
	//因为plane已经实现过了，所以它可实现也可也不
	virtual void land();
};
#endif 

//
#include <iostream>
#include "FighterPlane.h"
using namespace std;

FighterPlane::FighterPlane(string code) :Plane(code)
{

}
void FighterPlane::takeoff()
{
	cout << "FighterPlane -- takeoff" <<endl;

}
void FighterPlane::land()
{
	cout << "FighterPlane -- land" << endl;

}

//
//
#include <iostream>
using namespace std;
#include <stdlib.h>
#include "FighterPlane.h"

void flyMatch(Flyable *f1,Flyable *f2)
{
	f1->takeoff();
	f1->land();
	f2->takeoff();
	f2->land();

}
int main(void)
{
	Plane p1("001");
	Plane p2("002");
	p1.printCode();
	p2.printCode();

	flyMatch(&p1,&p2);

	system("pause");
	return 0;
}

```

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-797fe5baecd1bc22.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看出飞机可以作为参数传入flymatch。限制了传入参数的对象类型。可以在函数体中调用接口类的方法。

```c
int main(void)
{
	FighterPlane p1("001");
	FighterPlane p2("002");
	p1.printCode();
	p2.printCode();

	flyMatch(&p1,&p2);

	system("pause");
	return 0;
}
```
![战斗机运行结果](http://upload-images.jianshu.io/upload_images/1779926-bcfcbab161d4c182.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>让战斗机继承flyable和plane。飞机不再继承flyable。

此时它就既可以当做flyable传入，也可以传入plane。

```c
#include <iostream>
using namespace std;
#include <stdlib.h>
#include "FighterPlane.h"

void flyMatch(Plane *f1,Plane *f2)
{
	f1->printCode();
	f2->printCode();

}

int main(void)
{
	FighterPlane p1("001");
	FighterPlane p2("002");

	flyMatch(&p1,&p2);

	system("pause");
	return 0;
}

//
class FighterPlane:public Plane,public Flyable
{
}
class Plane
{

}
```

可以看出同时继承两个。既可以当做flyable传入，也可以传入plane。

- 接口类中仅有纯虚函数，不能含有其他函数，也不能含有数据成员。
- 可以使用接口类指针指向其子类对象，并调用子类对象中实现的接口类中的纯虚函数。
- 一个类可以继承一个接口类，也可以继承多个接口类。
- 一个类继承接口类的同时也可以继承非接口类。

###巩固练习
>定义一个能够射击（CanShut）类，要求含有纯虚函数aim和reload
定义一个枪（Gun）类，继承CanShut类，并实现函数aim和reload。
定义函数Hunting（CanShut *s）,调用s指向对象的函数。

在函数中传入Gun的对象，查看结果

```c
#include <iostream>
#include <stdlib.h>
#include <string>
using namespace std;

/**
 * 定义射击类：CanShut
 * 定义纯虚函数：aim、reload
 */
class CanShut
{
public:
    virtual void aim() =0;
    virtual void reload() =0;
};

/**
 * 定义枪类：Gun
 * 公有继承射击类
 * 实现成员函数：aim、reload
 */
class Gun : public CanShut
{
public:
    virtual void aim()
    {
         cout << "Gun -- aim" << endl;
    }
    virtual void reload()
    {
         cout << "Gun -- reload" << endl;
    }
};

/**
 * 定义含参函数射击：hunting
 * 调用参数的aim与reload函数
 */
void hunting(CanShut *s)
{
    s->aim();
    s->reload();
}

int main(void)
{
    // 实例化枪对象
    CanShut *p = new Gun();
    // 调用含参函数hunting,将对象枪传入函数中
    hunting(p);//因为已经是个指针。所以直接传入指针本身。
    //如果是对象。那要加上&取地址符
    // 释放内存
    delete p;
    p = NULL;

    return 0;
}
```

输出：
>- aim 
>- Gun
