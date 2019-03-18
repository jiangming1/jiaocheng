##C++远征之多态篇
**面向对象三大特征：封装，继承，多态**
>多态：发出一条命令时，不同的对象接收到同样的命令做出的动作不同

目录：

1. 普通虚函数 & 虚析构函数
2. 纯虚函数：抽象类 & 接口类
3. RTTI（运行时类型识别）
4. 异常处理
5. 概念：隐藏 & 覆盖 | 早绑定与晚绑定
6. 虚函数表

多态的内容很多，概念也听起来有点变态

![多态变态](http://upload-images.jianshu.io/upload_images/1779926-fc54c620ab20498f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

不过这也是最精彩的部分了。

####c++ 虚函数

什么是多态

>多态是指相同对象收到不同消息或不同对象收到相同消息时产生不同的动作

**静态多态 & 动态多态**

- 静态多态（早绑定）
- 动态多态（晚绑定）

####静态多态：相同对象收到不同消息

例子：

```c
class Rect
{
public:
	int calcArea(int width);
	int calcArea(int width,int height);//互为重载
}

int main(void)
{
	Rect rect;
	rect.calcArea(10);
	rect.calcArea(10,20);

	return 0;
}
```

>当我们传入一个参数，两个参数会调用两个不同的同名函数。
计算机在编译阶段就会自动根据参数使用不同的确定使用哪个函数。

####动态多态（晚绑定）：不同对象收到相同消息

![动态多态-一消息：多对象。](http://upload-images.jianshu.io/upload_images/1779926-3db50819fd00d29a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

动态多态：必须以封装与继承为基础。

代码例子：

```c
class Shape
{
public:
	double calcArea()
	{
		cout << "calcArea" <<endl;
		return 0;
	}
}

class Circle:public Shape
{
public:
	Circle(double r);
	double calcArea();
private:
	double m_dR;
}
//实现
double Circle::calcArea()
{
	return 3.14 * m_dR * m_dR;
}
//矩形
class Rect::public Shape
{
public:
	Rect(double width,double height);
	double calcArea();
private:
	double m_dWidth;
	double m_dHeight;
}

//实现
double Rect::calcArea()
{
	return m_dWidth * m_dHeight;
}
```

main函数中的使用：

```c
int main()
{
	Shape *shape1 = new Circle(4.0);
	Shape *shape2 = new Rect(3.0,5.0);
	shape1 -> calcArea();
	shape2 -> calcArea();
	//...
	return 0;
}
```
>上述代码会调用两次父类的clacArea();

- 使用父类指针指向子类对象，子类与父类有同名函数，加virtual成为虚函数，则调用相同的函数名的时候调用的是子类的函数.不添加的时候，使用父类指针指向的是父类自身的calc。

####virtual 虚函数

```c
class Shape
{
public:
	virtual double calcArea() //虚函数
	{
		cout << "calcArea" <<endl;
		return 0;
	}
}
```

- 子类中不加也可以，不过如果考虑子类也有可能成为父类情况下也加上。

>加上virtual后，父类指针指向子类对象。子类与父类有同名函数。父类指针可以使用子类方法。

- 如果是用父类指针，不加virtual关键字的话就会调用父类，如果是用子类指针，则调用子类，因为造成了隐藏

####虚函数代码示例

![虚函数：要求](http://upload-images.jianshu.io/upload_images/1779926-43739aeb8530928a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![未使用virtual时,调用两遍父类的](http://upload-images.jianshu.io/upload_images/1779926-f33e547f0d7bad5e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图为shape的析构函数添加了virtual。因此销毁父类指针，会将子类的也一并销毁。

附录代码：

```c
#ifndef SHAPE_H
#define SHAPE_H

#include <iostream>
using namespace std;

class Shape
{
public:
	Shape();
	virtual ~Shape();
	virtual double calcArea();
};


#endif


//
//
#include "Shape.h"


Shape::Shape()
{
	cout << "Shape()" << endl;
}

Shape::~Shape()
{
	cout << "~Shape()" << endl;
}

double Shape::calcArea()
{
	cout << "Shape - > calcArea()" << endl;
	return 0;
}

//
//
#ifndef RECT_H
#define RECT_H

#include "Shape.h"
class Rect : public Shape
{
public:
	Rect(double width,double height);
	~Rect();
	double calcArea();

protected:
	double m_dwidth;
	double m_dHeight;
};
#endif // RECT_H


//
//
#include "Rect.h"

Rect::Rect(double m_dwidth, double m_dHeight)
{
	cout << "Rect()" << endl;
	this->m_dHeight = m_dHeight;
	this->m_dwidth = m_dwidth;
}

Rect::~Rect()
{
	cout << "~Rect()" << endl;
}

double Rect::calcArea()
{
	cout << "Rect::calcArea()"<< endl;
	return m_dwidth * m_dHeight;
}

//
//
#ifndef CIRCLE_H
#define CIRCLE_H

#include "Shape.h"
class Circle:public Shape
{
public:
	Circle(double r);
	~Circle();
	double calcArea();
protected:
	double m_dR;
};
#endif

//
//
#include "Circle.h"


Circle::Circle(double r)
{
	cout << "Circle()" << endl;
	m_dR = r;
 }

Circle::~Circle()
{
	cout << "~Circle()" << endl;
}
double Circle::calcArea()
{
	cout << "Circle-->calcArea()" << endl;
	return 3.14 * m_dR * m_dR;
}

//
//
#include <iostream>
#include "Circle.h"
#include "Rect.h"
#include <stdlib.h>
using namespace std;

int main()
{
	Shape *shape1 = new Circle(3.0);
	Shape *shape2 = new Rect(3.0, 4.0);
	shape1->calcArea();
	shape2->calcArea();
	//当基类不添加virtual时。打印两遍基类的。
	delete shape1;
	shape1 = NULL;
	delete shape2;
	shape2 = NULL;
	system("pause");
	return 0;
}
```

>在给shape的clacArea函数加上virtual后。可调用子类的成员函数。

![shape的calcArea函数加上virtual后](http://upload-images.jianshu.io/upload_images/1779926-757bdf7a126529da.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

推荐为子类也加上virtual。

###虚析构函数
动态多态的内存泄漏

```c
class Shape
{
public:
	Shape();
	virtual double calcArea();

} 


class Circle: public Shape
{
public:
	Circle(int x,int y,double r);
	~Circle();
	virtual double calcArea();

private:
	double m_dR;
	Coordinate *m_pCenter;//圆心坐标
}


//构造函数圆心坐标：
//
Circle::Circle(int x,int y,double r)
{
	m_pCenter = new Coordinate(x,y);
	m_dR = r;
}
Circle::~Circle()
{
	delete m_pCenter;
	m_pCenter = NULL;
}
```

上述代码我们可以实现堆中内存的释放。

>但是在多态中：

```c
int main(void)
{
	Shape *shape1 = new Circle(3,5,4.0)
	shape1 -> calcArea();
	delete shape1;
	shape1 = NULL;
	return 0;
}
```

上述代码在shape的析构函数未添加virtual时将只会释放Shape对象。而实例化出的Circle对象不会被销毁。

>- delete后面跟着父类的指针。只会执行父类的析构函数。
- 跟着子类，会先执行子类，然后执行父类。

####virtual -> 析构函数

只需要在基类的析构函数添加virtual。父类指针可以一起销毁掉子类的对象。

###virtual在函数中的使用限制

- 普通函数不能是虚函数。（必须是类的成员函数，不能是全局函数）
- 静态成员函数不能是虚函数

```c
class Animal
{
public:
	virtual static int getCount()//因为被修饰过的静态函数是类的，不属于任何一个对象。
}
```

- 内联函数不能是虚函数

>会忽略inline。使他变成一个纯虚函数。

```c
class Animal
{
public:
	inline virtual int eat()
	{

	}
}
```

- 构造函数不能是虚函数

####虚析构函数使用方法代码示例

![虚析构函数要求](http://upload-images.jianshu.io/upload_images/1779926-3ca84f59264ee94e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

shape.h:

```c
#ifndef SHAPE_H
#define SHAPE_H

#include <iostream>
using namespace std;

class Shape
{
public:
	Shape();
	virtual ~Shape();
	virtual double calcArea();
};


#endif
```
Shape.cpp 

```c
#include "Shape.h"


Shape::Shape()
{
	cout << "Shape()" << endl;
}

Shape::~Shape()
{
	cout << "~Shape()" << endl;
}

double Shape::calcArea()
{
	cout << "Shape - > calcArea()" << endl;
	return 0;
}
```
rect.h:

```c
#ifndef RECT_H
#define RECT_H

#include "Shape.h"
class Rect : public Shape
{
public:
	Rect(double width,double height);
	~Rect();
	double calcArea();

protected:
	double m_dwidth;
	double m_dHeight;
};
#endif // RECT_H

```

rect.cpp：

```c
#include "Rect.h"

Rect::Rect(double m_dwidth, double m_dHeight)
{
	cout << "Rect()" << endl;
	this->m_dHeight = m_dHeight;
	this->m_dwidth = m_dwidth;
}

Rect::~Rect()
{
	cout << "~Rect()" << endl;
}

double Rect::calcArea()
{
	cout << "Rect::calcArea()"<< endl;
	return m_dwidth * m_dHeight;
}
```
circle.cpp:

```c
#ifndef CIRCLE_H
#define CIRCLE_H

#include "Shape.h"
#include "Coordinate.h"
class Circle:public Shape
{
public:
	Circle(double r);
	~Circle();
	double calcArea();
protected:
	double m_dR;
	Coordinate *m_pCenter;
};
#endif

```

circle.cpp:

```c
#include "Circle.h"


Circle::Circle(double r)
{
	cout << "Circle()" << endl;
	m_dR = r;
	m_pCenter = new Coordinate(3, 5);
 }

Circle::~Circle()
{

	cout << "~Circle()" << endl;
	delete m_pCenter;
	m_pCenter = NULL;
}
double Circle::calcArea()
{
	cout << "Circle-->calcArea()" << endl;
	return 3.14 * m_dR * m_dR;
}
```
Coordinate.h :

```c
#ifndef COORDINATE_H
#define COORDINATE_H
#include <iostream>

using namespace std;

class Coordinate
{
public:
	Coordinate(int x, int y);
	~Coordinate();
private:
	int m_iX;
	int m_iY;
};

#endif
```
Coordinate.cpp:

```c
#include "Coordinate.h"
#include <iostream>
using namespace std;

Coordinate::Coordinate(int x, int y)
{
	cout << "Coordinate()" << endl;
	m_iX = x;
	m_iY = y;
}
Coordinate::~Coordinate()
{
	cout << "~Coordinate()" << endl;

}
```
main.cpp:

```c
#include <iostream>
#include "Circle.h"
#include "Rect.h"
#include <stdlib.h>
using namespace std;

int main()
{
	Shape *shape2 = new Rect(3.0, 4.0);
	Shape *shape1 = new Circle(3.0);
	shape1->calcArea();
	shape2->calcArea();
	//当基类不添加virtual时。打印两遍基类的。
	delete shape1;
	shape1 = NULL;
	delete shape2;
	shape2 = NULL;
	system("pause");
	return 0;
}
```

给shape的析构函数变成一个虚析构函数。推荐大家把子类的虚析构函数。因为子类也有可能成为其他类的父类。

![shape的虚析构函数帮助继承于他的对象一起销毁](http://upload-images.jianshu.io/upload_images/1779926-68e76fe218737a73.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####virtual限制情况

```c
virtual Shape();
//“inline”是构造函数的唯一合法存储类
```

```c
virtual void test() {
//error C2575: “test”: 只有成员函数和基可以是虚拟的
}
```

```c
class Shape
{
public:
	Shape();
	//error C2216: “virtual”不能和“static”一起使用
	virtual static void test();
	virtual ~Shape();
	virtual double calcArea();
};
```

inline会失效：

```c
virtual inline void test() {

	}
```

####练习题

- 虚函数特性可以被继承，当子类中定义的函数与父类中虚函数的声明相同时，该函数也是虚函数。
- 虚函数使用virtual关键字定义，但使用virtual关键字时，并非全部是虚函数
- 虚析构函数是为了避免使用父类指针释放子类对象时造成内存泄露。

###虚函数与虚析构函数原理

>如何实现虚函数和虚析构函数：虚函数的实现原理

**函数指针**

指针指向对象 - - - 对象指针
指针指向函数 - - - 函数指针

指针指向代码的开头，然后一行一行执行到函数结尾

![函数指针](http://upload-images.jianshu.io/upload_images/1779926-81a6953ee001cc1d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

指针指向代码内存的首地址。

```c
class Shape
{
public:
	virtual double calcArea() //虚函数
	{
		return 0;
	}
protected:
	int m_iEdge;
}

//子类

class Circle: public Shape
{
public:
	Circle(double r);
	//circe使用的也是Shape的虚函数
private:
	double m_dR;
}
```

![虚函数表指针](http://upload-images.jianshu.io/upload_images/1779926-0196a1eec39ee2d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>当我们实例化一个shape对象时。shape中除了数据成员m_iEdge还有另外一个成员（虚函数表指针：也是指针占有四个内存单元）。指向一个虚函数表。该表会与shape类的定义同时出现。在计算机中虚函数表占有一定空间。假设虚函数表的起始位置：0xCCFF.那么虚函数表指针的值就是0xCCFF。父类的虚函数表只有一个。通过父类实例化出的多个对象。虚函数表指针都只有一个。0xCCFF.父类的虚函数表中定义了一个函数指针->指向calcArea()的入口地址。

![Circle实例化时虚函数表指针](http://upload-images.jianshu.io/upload_images/1779926-e6b837f543c66e24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>Circle中并没有定义虚函数，但是他却从父类中继承了虚函数。所以我们在实例化Circle也会产生一个虚函数表指针。他是Circle自己的。在Circle中计算面积的方法首地址与父类的一致。这使得在Circle中访问父类的计算面积函数也能通过虚函数表指针找到自己的虚函数表。在自己的虚函数表中找到的也是指向父类的计算面积的。

如果我们在circle中定义了计算面积的函数：

```c
class Circle: public Shape
{
public:
	Circle(double r);
	virtual double calcArea();
private:
	double m_dR;
}
```

shape没有发生变化。

![虚函数表指针](http://upload-images.jianshu.io/upload_images/1779926-0196a1eec39ee2d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

对于Circle来说则有变化：

![Circle自有父类虚函数](http://upload-images.jianshu.io/upload_images/1779926-a3dda9255e6bdb27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时Circle中关于计算面积的指针指向自己的方法首地址。
0X6688中的calcArea_ptr已经指向了自己的。

###函数的覆盖与隐藏

父类和子类出现同名函数。称之为隐藏。

- 如果没有在子类中定义同名虚函数。那么子类的虚函数表指针会写上父类函数的地址。
- 如果在子类中也定义了同名的虚函数。那么将覆盖并指向子类。

上面这种情况称之为函数的覆盖。

####虚析构函数原理。

特点：在父类中通过virtual修饰析构函数。通过父类指针指向子类对象。那么释放父类指针，可以同时释放子类对象。

理论前提：
>执行完子类的析构函数就会执行父类的析构函数。
只要我们可以实现执行子类的析构函数。

```c
class Shape
{
public:
	virtual double calcArea() //虚函数
	{
		return 0;
	}
	virtual ~Shape(){} //虚析构函数
protected:
	int m_iEdge;
}

class Circle: public Shape
{
public:
	Circle(double r);
	virtual double calcArea();
	virtual ~Circle();//不写计算机也会自行定义

private:
	double m_dR;
}

//main.cpp:
//

int main()
{
	Shape *shape = new Circle(10.0);
	delete shape;
	shape = NULL;

	return 0;
}
```

![虚析构函数如何运作](http://upload-images.jianshu.io/upload_images/1779926-887ecf6a3ebb6a8b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>如果我们在父类中定义了虚析构函数。那么我们在父类当中的虚函数表中就会有一个父类的析构函数的函数指针。


![子类的虚析构函数表](http://upload-images.jianshu.io/upload_images/1779926-20eab24b427ee1cb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>那么子类的虚函数表中也会有一个函数指针，指向子类的析构函数。这个时候使用父类对象指向子类对象。就会销毁子类与父类的对象。

虚析构函数与上面虚函数是同理可得的，就是子类中有同名对象，那么这个虚函数表中指针将指向子类的函数。而因为子类析构函数执行会触发父类执行。所以实现了销毁父类指针，释放子类和父类的对象。

- 比如此时我们删去子类的析构函数。那么将只执行父类的析构函数。

###证明虚函数表指针的存在（代码示例）
![虚函数表指针要求](http://upload-images.jianshu.io/upload_images/1779926-d329ada716a04326.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 对象的大小：在类实例化的对象当中数据成员所占据的大小。不包括成员函数
- shape没有数据成员（理论上不占），Circle有数据成员int型。应该占四个。
- 对象的地址：通过类实例化的对象在内存单元中占据的首地址
- 对象成员的地址：当用一个类实例化一个对象之后，这个对象中可能与一个或多个数据成员，每一个数据成员所占据的地址就是这个对象的成员地址，对象的数据成员由于数据类型不同那么占据的内存大小也不同，地址也是不同的。
- 虚函数表指针：在具有虚函数的情况下实例化对象时，这个对象的第一个内存存储的是一个指针，即虚函数表的指针，占四个内存单元，因此我们可以通过计算对象的大小来证明指针的存在。

```c
#ifndef SHAPE_H
#define SHAPE_H

#include <iostream>
using namespace std;

class Shape
{
public:
	Shape();
	~Shape();
	double calcArea();
	//virtual ~Shape();
	//virtual double calcArea();
};


#endif

//cpp
#include "Shape.h"


Shape::Shape()
{
	//cout << "Shape()" << endl;
}

Shape::~Shape()
{
	//cout << "~Shape()" << endl;
}

double Shape::calcArea()
{
	cout << "Shape - > calcArea()" << endl;
	return 0;
}
```

```c
#ifndef CIRCLE_H
#define CIRCLE_H

#include "Shape.h"
#include "Coordinate.h"
class Circle:public Shape
{
public:
	Circle(int r);
	~Circle();
protected:
	int  m_iR;
};
#endif


//cpp:
//
#include "Circle.h"


Circle::Circle(int r)
{
	m_iR = r;
 }

Circle::~Circle()
{

}
```
main.cpp:

```c
#include <iostream>
#include "Circle.h"
#include "Rect.h"
#include <stdlib.h>
using namespace std;

int main()
{
	Shape shape;
	cout << sizeof(shape) << endl;
	//shape对象没有任何的数据成员。理论应该为0.
	Circle circle(100);
	cout << sizeof(circle) << endl;
	//int 理论为4.

	system("pause");
	return 0;
}
```

运行结果：

![证明的运行结果](http://upload-images.jianshu.io/upload_images/1779926-99e148fe50b10b67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>4是因为int占四个字节。如何解释1？
- 在没有一个数据成员的情况下，c++会给该对象1个内存单元来标记此对象的地址，
- 而对于含有内存单元的对象来说，他的大小则是该数据成员的大小

main.cpp:

```c
#include <iostream>
#include "Circle.h"
#include "Rect.h"
#include <stdlib.h>
using namespace std;

int main()
{
	Shape shape;
	int *p = (int *)&shape;
	//强制类型转换
	cout << p << endl;
	Circle circle(100);

	int *q = (int *)&circle;
	cout << q << endl;
	//shape和Circle对象在内存中地址不同。
	cout << (unsigned int)(*q) << endl;
	//打印出里面存着的值
	system("pause");
	return 0;
}
```

运行结果：

![两对象指向不同地址，指针直接访问内部值](http://upload-images.jianshu.io/upload_images/1779926-faad1016997c026f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####虚函数示例2

```c
class Shape
{
public:
	Shape();
	~Shape();

	//virtual ~Shape();
	virtual double calcArea();
};

```

```c
int main()
{
	Shape shape;
	cout << sizeof(shape) << endl;
	int *p = (int *)&shape;//强制类型转换
	cout << p << endl;
	
	Circle circle(100);
	int *q = (int *)&circle;
	cout << q << endl;
	//shape和Circle对象在内存中地址不同。
	cout << (unsigned int)(*q) << endl;
	//打印出里面存着的值
	system("pause");
	return 0;
}
```

运行结果：

![指针占四个](http://upload-images.jianshu.io/upload_images/1779926-8d1e40ac862067b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当我们使shape拥有一个虚函数时。

- 指向Shape的指针应该指向一个虚拟函数表。
- 因为是个指针，所以占四个内存空间。

shape.h:

```c
class Shape
{
public:
	Shape();
	double calcArea();
	virtual ~Shape();虚析构函数也有虚函数表。
};
```

运行结果：

![虚析构-虚表-四个](http://upload-images.jianshu.io/upload_images/1779926-8696336027962f20.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
int main()
{
	Shape shape;
	cout << sizeof(shape) << endl;
	int *p = (int *)&shape;//强制类型转换
	//cout << p << endl;
	
	Circle circle(100);
	cout << sizeof(circle) << endl; 
	//虚函数指针父类有了子类也会有。所以应该是8
	int *q = (int *)&circle;
	cout << q << endl;
	//shape和Circle对象在内存中地址不同。
	cout << (unsigned int)(*q) << endl;
	//打印出里面存着的值
	system("pause");
	return 0;
}
```

运行结果：

![父类指针4个，子类：4+4](http://upload-images.jianshu.io/upload_images/1779926-556a4e096730426a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

8 为int 4 & 虚函数表指针4

```c

int main()
{
		Shape shape;
		int *p = (int *)&shape;
		cout << (unsigned int)(*p) << endl;
		//虚函数表地址
		//
		Circle circle(100);
		int *q = (int *)&circle;
		cout << (unsigned int)(*q) << endl;
		//打印出的还是虚函数表地址


}
```
运行结果：

![父类&子类虚函数表地址](http://upload-images.jianshu.io/upload_images/1779926-e1f3281b97f97bfd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
int *q = (int *)&circle;
q++;
cout << (unsigned int)(*q) << endl;
```
输出结果为100.说明：
- circle指针8位。前四位是指针指向虚地址表首地址。后四位是里面存着的内容。
- 父类和子类拥有不同的属于自己的虚函数表。

练习；

- 在C++中多态的实现是通过虚函数表实现的
- 每个类只有一份虚函数表，所有该类的对象共用同一张虚函数表
- 两张虚函数表中的函数指针可能指向同一个函数。(当子类没有父类的同名函数)

###巩固练习：

>定义一个动物（animal）类，要求含有虚函数eat和move，并定义构造函数和虚析构函数
定义一个狗（Dog）类，要求共有继承动物类，定义构造函数和虚析构函数，并实现自己的eat和move函数
使用父类对象实例化子类，调用子类成员函数

```c
#include <iostream>
#include <stdlib.h>
#include <string>
using namespace std;

/**
 *  定义动物类：Animal
 *  成员函数：eat()、move()
 */
class Animal
{
public:
    // 构造函数
    Animal(){cout << "Animal" << endl;}
    // 析构函数
	virtual  ~Animal(){cout << "~Animal" << endl;}
    // 成员函数eat()
	virtual void eat(){cout << "Animal -- eat" << endl;}
    // 成员函数move()
	virtual void move(){cout << "Animal -- move" << endl;}
};

/**
 * 定义狗类：Dog
 * 此类公有继承动物类
 * 成员函数：父类中的成员函数
 */
class Dog : public Animal
{
public:
    // 构造函数
	Dog(){cout << "Dog" << endl;}
    // 析构函数
	virtual ~Dog(){cout << "~Dog" << endl;}
    // 成员函数eat()
	virtual void eat(){cout << "Dog -- eat" << endl;}
    // 成员函数move()
    virtual void move(){cout << "Dog -- move" << endl;}
};

int main(void)
{
    // 通过父类对象实例化狗类
    Animal *a = new Dog();
    // 调用成员函数
	a ->eat();
    a ->move();
    // 释放内存
    delete a;
    a = NULL;
    
	return 0;
}
```

运行结果：

```
Animal
 Dog 
 Dog -- eat
 Dog -- move
 ~Dog
 ~Animal
```

- 因为父类使用虚析构函数。所以释放内存。
- 因为子类种有父类的同名函数，所以在子类的虚函数表中指向方法的指针被覆盖。
- 实现了父类对象调用子类方法。












