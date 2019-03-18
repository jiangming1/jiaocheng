##C++远征之模板篇
将会学到的内容：

- 模板函数 & 模板类 -> 标准模板类
- 友元函数 & 友元类
- 静态数据成员 & 静态成员函数
- 运算符重载。

###友元函数

函数定义：
	1. 全局函数
	2. 成员函数

友元全局函数 & 

- 友元成员函数定义在类中，并把该函数声明为另外一个类的友元函数。

例子：

```c
class Coordinate
{
	friend void printXY(Coordinate &c);
public:
	Coordinate(int x,int y);
private:
	int m_iX;
	int m_iY;
}
```

关键字friend + 声明友元函数（对象的引用或指针）

- 传入引用或指针访问速度更快。 

![友元函数](http://upload-images.jianshu.io/upload_images/1779926-f2081a945673d998.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因为我们在main函数中想使用printxy来调用坐标类的私有数据成员。所以我们需要在被调用的坐标类中声明该函数为友元函数。

####友元成员函数

```c
class Coordinate
{
	friend void Circle::printXY(Coordinate &c);
public:
	Coordinate(int x,int y);
private:
	int m_iX;
	int m_iY;
}
```
此时的xy并不是一个全局函数。而是一个Circle类中的成员函数。
也就是现在Circle的成员函数想使用坐标类的私有数据，那么我们就要去coorinate中去声明。

```c
class Circle
{
public:
	void printXY(Coordinate &c)
	{
		cout << c.m_iX << c.m_iY;
	}
}

int main()
{
	Coordinate coor(3,5);
	Circle circle;
	circle.printXY(coor);
	return 0;
}
```

友元函数。破坏了coordinate的封装性。

####友元函数编码实现

![要求](http://upload-images.jianshu.io/upload_images/1779926-67a4594d95d3f185.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####友元全局函数

```c
#ifndef TIME_H
#define TIME_H

#include <iostream>
using namespace std;

class Time
{
	friend void printTime(Time &t);//重点
public:
	Time(int hour,int min,int sec);

private:
	int m_iHour;
	int m_iMinute;
	int m_iSecond;

};
#endif

#include "Time.h"

Time::Time(int hour, int min, int sec)
{
	m_iHour = hour;
	m_iMinute = min;
	m_iSecond = sec;
}

//main.cpp:

#include <iostream>
#include <stdlib.h>
#include "Time.h"
using namespace std;

void printTime(Time &t);
int main()
{
	Time t(6, 34, 35);
	printTime(t);
	system("pause");
	return 0;
}

void printTime(Time & t)
{
	cout << t.m_iHour << endl;
	//Time::m_iHour”: 无法访问 private 成员(在“Time”类中声明)
	cout << t.m_iMinute << endl;
	cout << t.m_iSecond << endl;
}
```

可以访问到t内部的私有数据成员。

####友元成员函数

```c
#ifndef TIME_H
#define TIME_H
#include "Match.h"
#include <iostream>
using namespace std;

class Time
{
	friend void Match::printTime(Time &t);
	//重点,建议写在最外面。但是放在public，private都不影响。
public:
	Time(int hour,int min,int sec);

private:
	int m_iHour;
	int m_iMinute;
	int m_iSecond;

};
#endif
```

```c
#include "Time.h"

Time::Time(int hour, int min, int sec)
{
	m_iHour = hour;
	m_iMinute = min;
	m_iSecond = sec;
}

#ifndef STUDENT_H
#define STUDENT_H

class Time;//声明有这样一个类
class Match
{
public:
	void printTime(Time &t);//
};
#endif

#include "Match.h"
#include "Time.h"
#include <iostream>

using namespace std;

void Match::printTime(Time &t)
{
	cout << t.m_iHour << ":" << t.m_iMinute << ":" << t.m_iSecond << endl;

}


#include <iostream>
#include <stdlib.h>
#include "Time.h"
#include "Match.h"
using namespace std;

int main()
{
	Time t(6, 34, 35);
	Match m;
	m.printTime(t);
	system("pause");
	return 0;
}
```

match的printTime函数想要用t里面的私有数据。所以必须去向time申请。也就是time类声明`friend void Match::printTime(Time &t);`match的这个方法可以访问它。
而match因为要访问到time内部的数据。所以match要声明time类。`class Time;`

####单元巩固
>定义Coordinate类，并将全局display函数声明为Coordinate类的友元函数
Coordinate类数据成员`m_iX`和`m_iY`

display函数用于显示`m_iX`和`m_iY`

```c
#include <iostream>
using namespace std;

/**
 * 定义Coordinate类
 * 友元函数：display
 * 数据成员：m_iX、m_iY
 */
class Coordinate
{
    // 友元函数
    friend void display(Coordinate &coor);
public:
	Coordinate(int x, int y)
	{
		m_iX = x;
		m_iY = y;
	}
public:
	int m_iX;
	int m_iY;
};

/**
 * display函数用于显示m_iX、m_iY的值
 */
void display(Coordinate &coor)
{
	cout << "m_iX：" << coor.m_iX << endl;
	cout << "m_iY：" << coor.m_iY << endl;
}

int main(void)
{
    // 实例化Coordinate对象
	Coordinate c(0,0);
    // 调用display函数
    display(c);
	return 0;
}
```

###友元类

```c
class Circle;//声明类的存在
class Coordinate
{
	friend Circle;//声明友元。
public:
	Coordinate(int x,int y)
private:
	int m_iX;
	int m_iY;
}

class Circle
{
public:
	void printXY()
	{
		cout << m_coor.m_iX << m_coor.m_iY;
	}
private:
	Coordinate m_coor;//声明他要用到的对象
}
```

任何Circle的 成员函数都可以使用这个对象。

**对于友元的注意事项**

>- 友元关系不可传递（b是a的朋友，c是b的朋友，c不一定是a的朋友）
- 友元关系的单向性。（单向好友关系）
- 友元声明的形式与数量不受限制。（好友人数不设上限，好友可以是类，函数：混搭）

友元只是封装的补充：定向的暴露。

####友元类编码实现

![要求](http://upload-images.jianshu.io/upload_images/1779926-4a30aa1c2bf7f8b9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
#ifndef TIME_H
#define TIME_H

class Match;//
class Time
{
	friend Match;//
public:
	Time(int hour,int min,int sec);

private:
	void printTime();
	int m_iHour;
	int m_iMinute;
	int m_iSecond;

};
#endif


//

#include "Time.h"
#include <iostream>
using namespace std;

Time::Time(int hour, int min, int sec)
{
	m_iHour = hour;
	m_iMinute = min;
	m_iSecond = sec;
}
void Time::printTime()
{
	cout << m_iHour << "时" << m_iMinute << "分" << "秒" << endl;
}

//
//
#ifndef MATCH_H
#define MATCH_H

#include "Time.h"

class Match
{
public:
	Match(int hour,int min,int sec);
	void testTime();
private:
	Time m_tTimer;
};
#endif

//
#include "Match.h"
#include <iostream>

using namespace std;

Match::Match(int hour, int min, int sec):m_tTimer(hour, min, sec)
{

}
void Match::testTime()
{
	m_tTimer.printTime();
	cout << m_tTimer.m_iHour << ":" << m_tTimer.m_iMinute << ":" << m_tTimer.m_iSecond << endl;

}

//

#include <iostream>
#include <stdlib.h>
#include "Time.h"
#include "Match.h"
using namespace std;

int main()
{
	Match m(6, 30, 50);
	m.testTime();
	//无法访问 private 成员
	system("pause");
	return 0;
}
```

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-360afa081d6dbcde.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`class match;	friend Match;//`

####单元巩固
>定义Time类，数据成员：m_iHour, m_iMinute，m_iSecond 成员函数：构造函数
定义Watch类，数据成员：m_tTime, 成员函数：构造函数，display用于显示时间
Time类是Watch类的友元(Watch是Time友元类)

注：由于编译器不同，友元类有两种写法。1、friend class 类名; 2、friend 类名;

- 如果对象A中有对象成员B，对象B没有默认构造函数（也就是有参数传递），那么对象A必须在初始化列表中初始化对象B。
```c
#include <iostream>
using namespace std;
class Watch;

/**
 * 定义Time类
 * 数据成员：m_iHour， m_iMinute，m_iSecond 
 * 成员函数：构造函数
 * 友元类：Watch
 */
class Time
{
    // 友元类
    friend class Watch;
public:
	Time(int hour, int min, int sec)
	{
		m_iHour = hour;
		m_iMinute = min;
		m_iSecond = sec;
	}
public:
	int m_iHour;
	int m_iMinute;
	int m_iSecond;
};

/**
 * 定义Watch类
 * 数据成员：m_tTime
 * 成员函数：构造函数
 * display用于显示时间
 */
class Watch
{
public:
	Watch(Time& t):m_tTime(t){
	    
	}
	void display()
	{
		cout << m_tTime.m_iHour << endl;
		cout << m_tTime.m_iMinute << endl;
		cout << m_tTime.m_iSecond << endl;
	}
public:
	Time m_tTime;
};

int main()
{
	Time t(6, 30, 20);
	Watch w(t);
	w.display();

	return 0;
}

```

- 友元的声明不受访问限定符影响，可以声明在类中的任何位置。
- 友元具有单向性，A是B的友元，B不一定是A的友元。
- 友元函数和友元类必须使用关键字friend定义。
- 友元不具有传递性

####c++静态
前面介绍过了：

- 普通的数据成员
- 普通的成员函数
- const关键字
- 常数据成员 & 常成员函数

关键字：static ：静态数据成员 & 静态的成员函数

举个例子：

```c
class Tank
{
public:
	Tank(){s_iCount++;}
	~Tank(){s_iCount--;}

	static int getCount(){ return s_iCount;}
	static int s_iCount;

private:
	string m_strCode;
int Tank::s_iCount = 0;
//静态数据成员的单独初始化
}
```
- 静态数据成员不依赖于对象而存在，仅此一份。
- 静态成员，不必实例化就是存在的.
- 不能在构造函数中实例化
-静态数据成员必须单独初始化

两种访问方法：

```c
int main()
{
	cout << Tank::getCount() <<endl;
	cout << Tank::s_iCount <<endl;
	Tank tank;
	cout << tank.getCount() <<endl;
	cout << tank.s_iCount << endl;

	return 0;
}
```
内存中静态数据成员和普通数据成员的区别。

![普通成员函数 & 静态](http://upload-images.jianshu.io/upload_images/1779926-242a7103dfbcfbf2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Tank实例化出多个对象，那么普通数据成员code就会诞生。
在这四个对象诞生之前，s_iCount就已经诞生了。就这一个。

![静态成员函数不可以调用非静态](http://upload-images.jianshu.io/upload_images/1779926-1c117636901f1537.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 静态成员函数不能调用非静态成员函数和非静态数据成员
- 非静态成员函数可以调用静态成员函数和静态数据成员

对象（孙子）都没有出生。你还是爷爷辈的人。就不能用孙子的钱。

####从this指针谈静态成员函数。

```c
class Tank
{
public:
	void fire();
	static int getCount();
private:
	string m_strCode;
	static int s_iCount;
}
```
fire去调用普通和静态成员。

```c
void fire(Tank *this)
{
	this -> m_strCode = "01";
	s_iCount = 0;
}

static int getCount()
{
	m_strCode = "01"; 
	//并不会传入this指针。
	//并不能确定是哪个对象的成员了，
	return s_iCount;
}
```

**注意事项**

- 静态成员必须单独初始化。（与类一起，不与对象一起）
- 静态成员函数不能调用非静态成员函数和非静态数据成员
- 非静态成员函数可以调用静态成员函数和静态数据成员
- 静态数据成员只有一份，且不依赖对象而存在

sizeof求对象的大小。不会包含静态数据成员大小。

####静态成员函数编码

![要求](http://upload-images.jianshu.io/upload_images/1779926-7ed51f2d062e8fca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
#ifndef TANK_H
#define TANK_H

class Tank
{
public:
	Tank(char code);
	~Tank();
	void fire();
	static int getCount();
private:
	static int s_iCount;
	char m_cCode;

};

#endif


#include <iostream>
#include "Tank.h"
using namespace std;

int Tank::s_iCount = 10;//单独初始化

Tank::Tank(char code)
{
	m_cCode = code;
	s_iCount++;
	cout << "tank" << endl;

}
Tank::~Tank()
{
	s_iCount--;
	cout << "~Tank()" << endl;
}

void Tank::fire()
{
	cout << "Tank--fire" << endl;
}
int Tank::getCount()
//声明时添加static。定义时普通
{
	return s_iCount;
}


#include "Tank.h"
#include <stdlib.h>
#include <iostream>
using namespace std;

int main()
{
	cout << Tank::getCount() << endl;
	//在类实例化之前就能使用
	Tank t1('A');
	cout << Tank::getCount() << endl;
	//变成11
	cout << t1.getCount() << endl;
	Tank *p = new Tank('B');
	cout << Tank::getCount() << endl;
	Tank *q = new Tank('C');
	cout << q->getCount() << endl;

	delete p;
	delete q;

	cout << Tank::getCount() << endl;
	system("pause");
	return 0;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-f7edb7c9cf8c8546.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
static int getCount() const;

//错误。本来是给this指针加const。现在没有指针了
//静态成员函数上不允许修饰符
```

在普通成员函数中调用静态成员函数

```c
void Tank::fire()
{
	getCount();
	cout << "Tank--fire" << endl;
}
```
>可以正常调用。

在静态成员函数中调用普通成员函数。

```c
int Tank::getCount()
//声明时添加static。定义时普通
{
	fire(); //错误
	m_cCode = 'C';
	// 对非静态成员“Tank::m_cCode”的非法引用
	return s_iCount;
}
```

>非静态成员函数的非法调用

- 定义静态成员函数和静态数据成员都需要static关键字。
- 公有静态成员函数可以被类直接调用。
- 静态成员函数只能访问静态数据成员和调用静态成员函数。
- 静态数据成员不能在构造函数初始化，必须单独初始化。
