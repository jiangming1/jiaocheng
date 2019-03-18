##C++远征之模板篇(下)

###运算符重载
>给原有的运算符赋予新功能

`+` 数字相加。重载为字符串拼接

举个栗子：

```c
int main()
{
	string str1("james");
	string str2("yuan");
	string str3 = str1 + "" + str2;
	cout << str3 <<endl;
	return 0; 
}
```
上述代码中`=` , `+`, `<<`都做了重载

运算符重载的本质：函数重载

>关键字`operator`

####一元运算符重载

- 负号重载
- `++`符号重载

一元运算符：只与一个操作数运算。

`-`（负号）的重载：

- 友元函数重载（类中定义一个友元函数，全局函数）
- 成员函数重载

**成员函数重载**

```c
class Coordinate
{
public:
	Coordinate(int x,int y);
	Coordinate& operator-();//

private:
	int m_iX;
	int m_iY;
}

//实现
//
Coordinate& Coordinate::operator-()
//隐藏的参数
{
	m_iX = -m_iX;
	m_iY = -m_iY;
	return *this;
}
```
使用时

```c
int main()
{
	Coordinate coor1(3,5);

	-coor1; //coor1.operator-();

	return 0;
}
```

####友元函数重载

```c
class Coordinate
{
friend Coordinate& operator-(Coordinate &coor);
public:
	Coordinate(int x,int y);
	Coordinate& operator-();//

private:
	int m_iX;
	int m_iY;
}

//实现
//
Coordinate& operator-(Coordinate &coor)
{
	coor.m_iX = -coor.m_iX;
	coor.m_iY = -coor.m_iY;

	return *this;
}

//调用
//
int main()
{
	Coordinate coor1(3,5);

	-coor1; //operator-(cool);

	return 0;
}
```
成员函数重载与友元函数重载的区别。

>- `operator-(cool)`:友元函数
- `coor1.operator-();`：成员函数

++符号的重载：

- 前置`++`符号重载
- 后置`++`符号重载

**`++`运算符前置重载**

```c
class Coordinate
{
public:
	Coordinate(int x,int y);
	Coordinate& operator++();//前置++&成员函数

private:
	int m_iX;
	int m_iY;
}

//定义实现
//
Coordinate& Coordinate::operator++()
{
	m_iX++;
	m_iY++;

	return *this;
}

//使用

int main()
{
	Coordinate coor1(3,5);
	++coor1; //coor1.operator++()

	return 0;
}
```

**重载后置++**

```c
class Coordinate
{
public:
	Coordinate(int x,int y);
	Coordinate operator++(int);//后置++
	//1. 返回的对象为对象。
	//2. 传入参数int。int没有任何用。只是为了标识。

private:
	int m_iX;
	int m_iY;
}


Coordinate operator++(int)
{
	Coordinate old(*this);
	m_iX++;
	m_iY++;

	return old;
}

int main()
{
	Coordinate coor1(3,5);
	coor1++; //coor1.operator(0);

	return 0;
}
```
####一元运算符编码实现

![运算符重载要求](http://upload-images.jianshu.io/upload_images/1779926-e6a7a1c60481aaa4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

成员函数重载：

```c
#ifndef COORDINATE_H
#define COORDINATE_H
#include <iostream>
using namespace std;

class Coordinate
{
public:
	Coordinate(int x,int y);
	Coordinate & operator-();
	int getX();
	int getY();
private:
	int m_iX;
	int m_iY;

};
#endif


#include "Coordinate.h"

Coordinate::Coordinate(int x, int y)
{
	m_iX = x;
	m_iY = y;
}

int Coordinate::getX()
{
	return m_iX;
}

int Coordinate::getY()
{
	return m_iY;
}
Coordinate &Coordinate::operator-()
{
	m_iX = -m_iX;
	this->m_iY = -this->m_iY;

	return *this;
}


#include "Coordinate.h"
#include <iostream>
#include <stdlib.h>
using namespace std;

int main()
{
	Coordinate coor1(1, 3);
	cout << coor1.getX() << "," << coor1.getY() << endl;
	-coor1; //coor1.operator-()
	cout << coor1.getX() << "," << coor1.getY() << endl;
	system("pause");
	return 0;
}
```

运行结果：
1,3

-1,-3

友元函数运算符重载

Coordinate.h:

```c
class Coordinate
{
	friend Coordinate &operator-(Coordinate &c);

public:
	Coordinate(int x,int y);
	int getX();
	int getY();
private:
	int m_iX;
	int m_iY;

};
```

coordinate.cpp:

```c
#include "Coordinate.h"

Coordinate::Coordinate(int x, int y)
{
	m_iX = x;
	m_iY = y;
}

int Coordinate::getX()
{
	return m_iX;
}

int Coordinate::getY()
{
	return m_iY;
}
Coordinate &operator-(Coordinate &c)
{
	c.m_iX = -c.m_iX;
	c.m_iY = -c.m_iY;

	return c;
}
```

main.cpp:没有变化

运行结果也没有变化。

###编码实现二

```c
#ifndef COORDINATE_H
#define COORDINATE_H
#include <iostream>
using namespace std;

class Coordinate
{
	friend Coordinate &operator-(Coordinate &c);

public:
	Coordinate(int x,int y);
	Coordinate &operator++();//前置++
	Coordinate operator++(int);//后置++

	int getX();
	int getY();
private:
	int m_iX;
	int m_iY;

};
#endif


#include "Coordinate.h"

Coordinate::Coordinate(int x, int y)
{
	m_iX = x;
	m_iY = y;
}

int Coordinate::getX()
{
	return m_iX;
}

int Coordinate::getY()
{
	return m_iY;
}
Coordinate &operator-(Coordinate &c)
{
	c.m_iX = -c.m_iX;
	c.m_iY = -c.m_iY;

	return c;
}
Coordinate &Coordinate::operator++()
{
	m_iX++;
	m_iY++;
	return *this;
}
Coordinate Coordinate::operator++(int)
{
	Coordinate old(*this);
	this->m_iX++;
	this->m_iY++;
	return old;
}


#include "Coordinate.h"
#include <iostream>
#include <stdlib.h>
using namespace std;

int main()
{
	Coordinate coor1(1, 3);
	cout << coor1.getX() << "," << coor1.getY() << endl;
	++coor1;
	cout << coor1.getX() << "," << coor1.getY() << endl;
	-coor1; //coor1.operator-()
	cout << coor1.getX() << "," << coor1.getY() << endl;
	cout << (coor1++).getX() << ",";
	cout << (coor1++).getY() << endl;
	cout << coor1.getX() << "," << coor1.getY() << endl;
	//上面两个分号所以coor1++执行了两次。
	//到上一行打印的时候已经是x，y都加了2了。
	system("pause");
	return 0;
}
```

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-916dacf882aceba6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###二元运算符重载

- +号运算符： 友元函数重载  & 成员函数重载

####`+`成员函数重载

```c
class Coordinate
{
public:
	Coordinate(int x,int y);
	Coordinate operator+(const Coordinate &coor);

private:
	int m_iX;
	int m_iY;
}


Coordinate operator+(const Coordinate &coor)
{
	Coordinate temp;
	temp.m_iX = this ->m_iX + coor.m_iX;
	temp.m_iY = this ->m_iY + coor.m_iY;

	return temp;
}

int main()
{
	Coordinate coor1(3,5);
	Coordinate coor1(4,7);
	Coordinate coor1(0,0);

	coor3 = coor1 + coor2; 
	//operator+(coor1,coor2)
	//
	return 0; 
}

```

<<输出运算符

```c
class Coordinate
{
	friend ostream& operator <<(ostream &out,const Coordinate &coor);
public:
	Coordinate(int x,int y);
private:
	int m_iX;
	int m_iY;
}

//实现
//
ostream& operator<<(ostream &out,const Coordinate &coor)
{
	out << coor.m_iX <<","<<coor.m_iY;

	return out;
}

//使用
//
int main()
{
	Coordinate coor(3,5);
	cout <<coor;//operator<<(cout,coor);

	return 0;
}
```

理解到cout是一个ostream的对象。

输出运算符可以采用成员函数重载？

- 使用加号运算符重载。传入一个参数。第一个加数默认当前的对象。
- 第一个对象必须是ostream。不能是当前指针对象。

####`[]`索引运算符

```c
class Coordinate
{
public:
	Coordinate(int x,int y);
	int operator[](int index);
private:
	int m_iX;
	int m_iY;
}

int Coordinate::operator [](int index)
{
	if(0==index)
		{return m_iX;}
	if(1==index)
		{return m_iY;}
}

//使用
int main()
{
	Coordinate coor[3,5];
	cout << coor[0]; // coor.operator[](0);
	cout << coor[1]; // coor.operator[](1);

	return 0;
}
```

索引运算符可以采用友元函数重载？
>不能采用友元函数重载。友元函数第一个参数可以是this指针，也可以是其他东西。只有this指针才能指向对象。

####二元运算符代码示例
![要求](http://upload-images.jianshu.io/upload_images/1779926-000fd71612806090.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运算符的重载：

```c
#ifndef COORDINATE_H
#define COORDINATE_H
#include <iostream>//包含了ostream
using namespace std;

class Coordinate
{
	friend Coordinate &operator-(Coordinate &c);
	friend Coordinate operator+(Coordinate c1,Coordinate c2);
	friend ostream &operator<<(ostream &output, Coordinate &coor);
public:
	Coordinate(int x,int y);
	Coordinate &operator++();//前置++
	Coordinate operator++(int);//后置++
	//Coordinate operator+(Coordinate &c);
	//其实里面有两个参数。
	int operator [](int index);
	int getX();
	int getY();
private:
	int m_iX;
	int m_iY;

};
#endif

#include "Coordinate.h"

Coordinate::Coordinate(int x, int y)
{
	m_iX = x;
	m_iY = y;
}

int Coordinate::getX()
{
	return m_iX;
}

int Coordinate::getY()
{
	return m_iY;
}
Coordinate &operator-(Coordinate &c)
{
	c.m_iX = -c.m_iX;
	c.m_iY = -c.m_iY;

	return c;
}
Coordinate &Coordinate::operator++()
{
	m_iX++;
	m_iY++;
	return *this;
}
Coordinate Coordinate::operator++(int)
{
	Coordinate old(*this);
	this->m_iX++;
	this->m_iY++;
	return old;
}

//Coordinate Coordinate::operator+(Coordinate &c)
//{
//	Coordinate temp(0, 0);
//	temp.m_iX = this->m_iX +c.m_iX;
//	temp.m_iY = this->m_iY +c.m_iY;
//
//	return temp;
//}
Coordinate operator+(Coordinate c1, Coordinate c2)
{
	Coordinate temp(0, 0);
	temp.m_iX = c1.m_iX + c2.m_iX;
	temp.m_iY = c1.m_iY + c2.m_iY;

	return temp;
}

ostream &operator<<(ostream &output, Coordinate &coor)
{
	output << coor.m_iX << "," << coor.m_iY;
	return output;
}
int Coordinate::operator [](int index)
{
	if (0 == index)
	{
		return m_iX;
	}if (1 == index)
	{
		return m_iY;
	}
}

#include "Coordinate.h"
#include <iostream>
#include <stdlib.h>
using namespace std;

int main()
{
	Coordinate coor1(1, 3);
	Coordinate coor2(2, 4);
	Coordinate coor3(0, 0);

	coor3 = coor1 + coor2;

	//cout << coor3.getX() << "," << coor3.getY() << endl;

	cout << coor3[0] <<endl;
	cout << coor3[1] << endl;
	

	system("pause");
	return 0;
}
```

- 运算符重载可以使运算符具有新的功能。
- 运算符重载使用关键字operator
- 有些运算符必须使用成员函数重载，有些则必须使用友元函数重载。如索引运算符重载就不可以使用友元函数重载。（输出运算符<< 只能用友元函数重载（因为输出第一个参数为ostream）。因为索引运算符第一个参数必须传入this。）
- ++运算符重载需要区分前置++重载和后置++重载。

####单元巩固
>定义Coordinate类
数据成员：m_iX, m_iY
成员函数：构造函数
重载“--”运算符，重载“+”运算符

```c
#include <iostream>
using namespace std;

/**
 * 定义Coordinate类
 * 数据成员：m_iX，m_iY
 * 成员函数：构造函数
 * 重载--运算符，重载+运算符
 */
class Coordinate
{
public:
    Coordinate(int x, int y)
	{
		m_iX = x;
		m_iY = y;
	}
    // 前置--运算符重载
	Coordinate & operator--()
    {
        this ->m_iX--;
        this ->m_iY--;
        return *this;
    }
    
    
    // 后置--运算符重载
    Coordinate operator--(int)
    {
        Coordinate old = *this;
        this ->m_iX--;
        this ->m_iY--;
        return old;
    }
    
    
    
    // +号运算符重载
	Coordinate &operator+(Coordinate c)
    {
        Coordinate temp(0,0);
        temp.m_iX = this ->m_iX + c.m_iX;
        temp.m_iY = this ->m_iY + c.m_iY;
        return temp;
    }
    
    

public:
	int m_iX;
	int m_iY;
};

int main(void)
{
	Coordinate coor1(1, 3);
	Coordinate coor2(2, 4);
	Coordinate coor3(0, 0);

	coor1--;
	--coor2;
	coor3 = coor1 + coor2;

	cout << coor3.m_iX << endl;
	cout << coor3.m_iY << endl;

	return 0;
}

```

运行结果：1 5

###函数模板
为什么要使用模板？

```c
int max(int a,int b)
{
	return (a>b)?a:b;
}
float max(float a,float b)
{
	return (a>b)?a:b;
}
char max(char a,char b)
{
	return (a>b)?a:b;
}
```

三种不同类型，但是运算逻辑完全一致。

>解决方案：
类型作为参数传入。计算机来做出这三个函数。

关键字：`template` `typename` `class`

这里的class不是类。是来表面数据类型的。

```c
template <class T>
//声明一种参数类型
T max(T a,T b)     //函数模板
{
	return (a>b)?a:b;
}

int ival = max(100,99); //模板函数
//不写明，计算机会自动进行识别。
char cval = max<char>('A','B');
//指定之后，传入参数一定要是这种数据类型才可以
```

函数模板是模子，生产出来的是模板函数。
只有函数模板，计算机不会产生代码数据。只有使用时才会产生真正的代码

```c
template <typename T>
void swap(T& a,T& b)
{
	T tmp = a;
	a = b;
	b = tmp;
}

int x =20,y=30;
swap<int>(x,y);
```

**变量作为模板参数**

```c
template <int size>
void display()
{
	cout << size <<endl;
}

display<10>();
```

多参数函数模板

```c
template <typename T,typename C>
void display(T a,C b)
{
	cout << a <<" " <<b <<endl;
}

//使用
int a = 1024; string str = "helloworld";
display<int ,string>(a,str);
```

typename 和class可以混用

```c
template <typename T,class U>
T minus(T* a, U b)

template <typename T,int size>
void display(T a)
{
	for(int i=0;i < size;i++)
	cout << a << endl;
}

display<int,5>(15);
```

**函数模板与重载**

函数模板可以做出无数个模板函数来。

模板函数之间形成重载。

不同的函数模板做出来的模板函数也能形成重载

```c
template <typename T>
void display(T a);

template <typename T>
void display(T a,T b);//参数个数不同

template <typename T，int size>
void display(T a);

display<int>(10);
display<int>(10,20);
display<int,5>(30);//三个函数形成重载
```

- 函数模板本身不会在内存中产生代码, 因为没有模板参数就无从知道要合成怎样的函数

- 模板参数可以是类型, 变量(编译时实际上是常量), 或多个类型和变量的组合

- 同一个函数模板的不同的模板函数之间可以看作互为重载

- 函数名称相同但模板参数或函数参数不同的来自不同函数模板的模板函数之间也可以互为重载

####函数模板代码实践

![要求](http://upload-images.jianshu.io/upload_images/1779926-68b98b3e46175250.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
#include <iostream>
#include <stdlib.h>
using namespace std;

template <typename T>

void display(T a)
{
	cout << a << endl;
}

template <typename T, class S>
void display(T t,S s)
{
	cout << t << endl;
	cout << s << endl;
}

template <typename T,int Ksize>
//该变量实例化时变为常量

void display(T a)
{
	for (int i = 0; i < Ksize; i++)
	{
		cout << a << endl;
	}
}

int main()
{
	display<int>(10);
	display<double>(10.98);
	display<int,double>(5, 8.3);
	display<int,3>(10);
	system("pause");
	return 0;
}
```

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-632730fbeccc310b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 函数模板的参数可以是一个也可以是多个。
- 如果函数模板的参数个数为零个就没必要使用函数模板了。
- 使用函数模板时，需要指定模板参数，此时的函数称为模板函数。
- 当需要定义多个功能相同，数据类型不同的函数时，可以使用函数模板来定义。

####单元巩固
定义一个函数模板，功能是交换两个数的位置

```c
#include <iostream>
using namespace std;

/**
 * 定义模板函数swapNum
 * 实现功能：交换两个数的位置
 */
template <typename T>
void swapNum(T &a,T &b)
{
    T temp = a;
    a = b;
    b = temp;
}

int main(void)
{
    int x = 10;
    int y = 20;
    // 调用模板函数
    swapNum(x,y);
        cout << "x = " << x << endl;
        cout << "y = " << y << endl;
	return 0;
}

```

运行结果：
x = 20 y = 10

####类模板

```c
template <class T>
class MyArray
{
public:
	void display()
	{...}
private:
	T *m_pArr;
}
```

只有数据类型不同，其他基本都相同。

类外定义成员函数：

```c
template <class T>
void MyArray<T>::display()
{

}

//使用
int main()
{
	MyArray<int> arr;
	arr.display();

	return 0;
}
```

类模板



**模板类多个参数的使用情况**

```c
template <template T,int KSize>
class Container
{
public:
	void display();
private:
	T m_obj;
}

//
template<typename T,int KSize>
void Container<T,KSize>::display()
{
	for (int i = 0; i < KSize; ++i)
	{
		cout << m_obj << endl;
	}
}

int main()
{
	Container<int,10> ct1;
	ct1.display();

	return 0;
}
```

包含多个参数的类模板（类外定义），每一个成员函数前都要写上template代码。

- 模板代码不能分离编译（类的声明与定义都要写在.h文件中）。
- 类模板类外定义，每一个成员函数前都要写上`template<typename T, int KSize>`

####类模板编码实现
![要求](http://upload-images.jianshu.io/upload_images/1779926-e9eb9d0041a41cc1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

myarry.h:

```c
#ifndef MYARRAY_H
#define MYARRAY_H

#include <iostream>
using namespace std;

template <typename T,int Ksize,int KVal>//模板参数列表
class MyArray
{
public:
	MyArray();
	~MyArray()
	{
		delete[]m_pArr;
		m_pArr = NULL;
	}//写在类内部的函数不需要注意什么。
	void display();

private:
	T *m_pArr;
};
template <typename T, int KSize, int KVal>
MyArray<T, KSize, KVal>::MyArray()
{
	m_pArr = new T[KSize];
	for (int i = 0; i < KSize; i++)
	{
		m_pArr[i] = KVal;
	}
}

template <typename T,int KSize,int KVal>
void MyArray<T, KSize, KVal>::display()
{
	for (int i = 0; i < KSize; i++)
	{
		cout << m_pArr[i] << endl;
	}
}

#endif

```

myarry.cpp:

```c
//什么都不写
```

main.cpp:

```c
#include <stdlib.h>
#include <string>
#include "MyArray.h"
using namespace std;

int main()
{
	MyArray<int, 5, 6> arr;//已经形成模板类了
	arr.display();
	system("pause");
	return 0;
}
```

运行结果：

![5个6](http://upload-images.jianshu.io/upload_images/1779926-a93c0f8ba0353bc7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>- 定义一个类模板就相当于定义了一系列功能相同类型不同的类
- 定义类模板需要使用关键字template
- 模板参数既可以是类型，也可以是变量
- 定义类模板的参数可以使用typename和class，可以混用

####单元巩固
>定义一个矩形类模板
该模板中含有计算矩形面积和周长的成员函数
数据成员为矩形的长和宽。

```c
#include <iostream>
using namespace std;

/**
 * 定义一个矩形类模板Rect
 * 成员函数：calcArea()、calePerimeter()
 * 数据成员：m_length、m_height
 */
template <typename T>
class Rect
{
public:
    Rect(T length,T height);
    T calcArea();
    T calePerimeter();
public:
	T m_length;
	T m_height;
};

/**
 * 类属性赋值
 */
template <typename T>
Rect<T>::Rect(T length,T height)
{
	m_length = length;
	m_height = height;
}

/**
 * 面积方法实现
 */
template <typename T>
T Rect<T>::calcArea()
{
	return m_length * m_height;
}

/**
 * 周长方法实现
 */
template <typename T>
T Rect<T>::calePerimeter()
{
	return ( m_length + m_height) * 2;
}

int main(void)
{
	Rect<int> rect(3, 6);
	cout << rect.calcArea() << endl;
	cout << rect.calePerimeter() << endl;
	return 0;
}

```

注意事项：

```c
template <typename T>
Rect<T>::Rect(T length,T height)//《》加在类上而不是方法。
```

###标准模板库
STL: standard template Lib

>标准模板库就是系统已经预先写好了的一些模板的集合，你可以直接使用（将之实例化）

常用部分：

####向量
本质：数组的封装。根据存储的元素个数自动变长或缩短。
特点：随机读取能在常数时间内完成

![向量的初始化方法](http://upload-images.jianshu.io/upload_images/1779926-6bcc4f7752306c26.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

分别是：

- 初始化一个空向量
- 用一个向量初始化另一个向量
- v3中存储n个i。
- 初始化十个0.0为初始化元素。n为个数。

具体使用：

```c
vector<int> ivec1;
vector<int> ivec2(ivec1);

vector<string> svec1;
vector<string> svec2(svec1);

vector<int> ivec4(10,-1);//数组中包含10个-1
vector<string> ivec4(10,"hi");//数组中包含10个hi

```

向量初始化之后必须要有配套的函数才能让我们体会到方便

![配套的函数](http://upload-images.jianshu.io/upload_images/1779926-928faf4255d0e7c3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
int main()
{
	vector<int> vec;
	vec.push_back(10);//最尾部插入元素10
	vec.push_pop();//删除10
	cout << vec.size()<<endl;//此时仍然为0
	return 0;
}

for(int k=0;k < vec.size();k++)
{
	cout << vec[k] << endl;
}
```

####迭代器(iterator)遍历。

```c
int main()
{
	vector vec;
	vec.push_back("hello");
	vector<string>::iterator citer = vec.begin();
	//<>标明向量使用的数据类型。::标识出当前迭代器是属于向量的迭代器
	//迭代器的变量名citer 数据类型：`vector<string>::iterator citer`
	//通过该迭代器指向当前向量的第一个元素。
	for (;citer != vec.end();citer++)
	//中止条件：end指当前向量vec最后一个向量的下一个位置。
	//指向下一个元素。
	{
		cout << *citer << endl;
	}

}
```

####list：链表

![链表](http://upload-images.jianshu.io/upload_images/1779926-7943c49217be75fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 链表会有一个头结点。每一个链表都由若干结点组成。
- 一个结点都没有称之为空链表
- 存在多个结点。第一个结点称之为头结点。
- 对于每一个结点由两部分组成。一部分是数据部分（ABCDE数据域），还有一部分是指针部分。
- 指针部分用来将结点串联起来。

双链表：可以从头找到尾，也可以从尾找到头。

d 和 e中间插入数据，让d指向新数据，新数据指向e就可以了。

特点：数据插入速度快
使用方法上与数组基本相同。

####map：映射

![map映射](http://upload-images.jianshu.io/upload_images/1779926-84d551b658e93892.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

键值映射一一对应。通过键找值

```c
map<int,string> m;//通过映射定义一个映射对象
pair<int,string> p1(10,"shanghai");//向映射中放入key&value
pair<int,string> p2(20,"beijing");
m.insert(p1);//将pair放入m中
m.insert(p2);
cout << m[10] << endl;//通过key访问值
cout << m[20] << endl;


map<string,string> m;//通过映射定义一个映射对象
pair<string,string> p1("S","shanghai");//向映射中放入key&value
pair<string,string> p2("B","beijing");
m.insert(p1);//将pair放入m中
m.insert(p2);
cout << m["S"] << endl;//通过key访问值
cout << m["B"] << endl;
```

####标准模板库代码实现

![标准模板库](http://upload-images.jianshu.io/upload_images/1779926-3cb2c4a281b80a89.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
#include <iostream>
#include <stdlib.h>
#include <vector>
#include <list>
#include <map>
using namespace std;

int main()
{
	vector<int> vec;
	vec.push_back(3);
	vec.push_back(4);
	vec.push_back(6);//从尾部去插入
	/*vec.pop_back();

	cout << vec.size() << endl;*/

	/*for (int i=0;i<vec.size();i++)
	{
		cout << vec[i] << endl;
	}
*/
	//迭代器
	vector<int>::iterator itor = vec.begin();
	//cout << *itor << endl;
	for (;itor != vec.end();itor++)
	{
		cout << *itor << endl;
	}
	cout << vec.front() << endl;//第一个元素
	cout << vec.back() << endl;//最后一个元素

	system("pause");
	return 0;
}
```

list的遍历代码：

```c
#include <iostream>
#include <stdlib.h>
#include <vector>
#include <list>
#include <map>
using namespace std;

int main()
{
	list<int> list1;
	list1.push_back(4);
	list1.push_back(7);
	list1.push_back(10);

	//for (int i=0;i<list1.size();i++)
	//{
	//	cout << list1[i] << endl;//错误

	//}
	list<int>::iterator itor = list1.begin();
	for (;itor != list1.end();itor++)
	{
		cout << *itor << endl;
	}
	system("pause");
	return 0;
}
```
map遍历

```c
#include <iostream>
#include <stdlib.h>
#include <vector>
#include <list>
#include <map>
#include <string>
using namespace std;

int main()
{
	map<int,string> m;
	pair<int,string> p1(3,"hello");
	pair<int,string> p2(6,"world");
	//m.push_back(p1);//错误
	m.insert(p1);
	m.insert(p2);

	cout << m[3] << endl;
	cout << m[6] << endl;

	map<int,string>::iterator itor = m.begin();
	for (;itor != m.end();itor++)
	{
		//cout << *itor << endl;//错误，每个都包含keyvalue
		cout << itor->first << endl;
		cout << itor->second << endl;
	}

	system("pause");
	return 0;
}
```

string类型map

```c
#include <iostream>
#include <stdlib.h>
#include <vector>
#include <list>
#include <map>
#include <string>
using namespace std;

int main()
{
	map<string,string> m;
	pair<string,string> p1("H","hello");
	pair<string,string> p2("W","world");
	//m.push_back(p1);//错误
	m.insert(p1);
	m.insert(p2);

	cout << m["H"] << endl;
	cout << m["W"] << endl;

	map<string,string>::iterator itor = m.begin();
	for (;itor != m.end();itor++)
	{
		//cout << *itor << endl;//错误，每个都包含keyvalue
		cout << itor->first << endl;
		cout << itor->second << endl;
	}

	system("pause");
	return 0;
}
```

- vector是对数组的封装，所以一旦对象被实例化，其大小就可以改变.大小可以根据元素数量改变
- list的特点是数据插入速度快。
- map需要与pair一起使用，用来存储多个key-value对。
- 不同厂商的标准模板库的实现细节可以不同，基本用法及原理相同。

####单元巩固
>使用vector存储数字3，6,8,4，并遍历。
使用map存储S-Shang Hai   B-Bei Jing    G-Guang Zhou，并遍历

```c
#include <vector>
#include <map>
#include <string>
#include <iostream>
using namespace std;

int main(void)
{
    // 使用vector存储数字：3、4、8、4
    vector<int> vec;
    vec.push_back(3);
    vec.push_back(4);
    vec.push_back(8);
    vec.push_back(4);
    //循环打印数字
    
    vector<int>::iterator itor1 = vec.begin();
    for(;itor1 != vec.end();itor1++)
    {
        cout << *itor1 <<endl;
    }
    
    // 使用map来存储字符串键值对
    map<string, string> m;
    pair<string, string> p1("S","Shang Hai");
    pair<string, string> p2("B","Bei Jing");
    pair<string, string> p3("G","Guang Zhou");
    m.insert(p1);
    m.insert(p2);
    m.insert(p3);
    // 打印map中数据
    map<string, string>::iterator itor2 = m.begin();
    for(;itor2 != m.end();itor2++)
    {
        cout << itor2->first <<endl;
        cout << itor2->second <<endl;
    }
    return 0;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-1e0eb60f0f455285.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意事项：

```c
 cout << itor2->first <<endl;
 cout << itor2->second <<endl;

```

itor1和itor2不能重名
