##C++远征之封装篇（下）
###c++封装概述

- 类 & 对象
- 对象+数据成员 = 对象成员
- 对象 + 数组 = 对象数组
- 深拷贝 & 浅拷贝
- 对象指针 & 对象指针成员
- this指针
- const + 对象 -> 常对象
- const + 函数 -> 常成员函数
- const + 对象成员 -> 常对象成员

###c++ 对象数组
如何实例化一个对象？需要实例化一组对象时。

>对象数组

```c
class Coordinate
{
public:
	int m_iX;
	int m_iY;
}

int main()
{
	Coordinate coord[3];//栈
	coord[1].m_iX = 10;
	Coordinate *p = new Coordinate[3];//堆
	p[0].m_iY =20;p -> m_iY =20;
	delete []p;
	p =NULL;

	return 0;
}
```

![堆栈](http://upload-images.jianshu.io/upload_images/1779926-2fcf3c03840ebcb4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###对象数组实践代码

![要求](http://upload-images.jianshu.io/upload_images/1779926-45eab56a0210d803.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

main.cpp

```c
#include "stdafx.h"
#include <iostream>
#include <string>
#include "Coordinate.h"
using namespace std;



int main(void)
{
	Coordinate coor[3];
	coor[0].m_iX = 3;
	coor[0].m_iY = 5;

	Coordinate *p = new Coordinate[3];
	p->m_iX = 7;
	p[0].m_iY = 9;

	p++;
	p->m_iX = 11;
	p[0].m_iY = 13;//此时因为上面p++。p已经指向第二个地址了

	p[1].m_iX = 15;//此时p指向第三个元素

	p++;
	p->m_iX = 17;//此时p指向第三个元素

	for (int i = 0;i<3;i++)
	{
		cout << coor[i].m_iX <<" coor x&y "<< coor[i].m_iY << endl;
		
	}
	for (int j =0;j<3;j++)
	{
		//cout << "p_x" << p[j].m_iX << endl;
		//cout << "p_y" << p[j].m_iY << endl;
		cout << "p_x" << p->m_iX << endl;
		cout << "p_y" << p->m_iY << endl;
		p--;
	}

	p++;//因为上面p=-1时才退出了循环。因此释放时已经不是原来那段内存了。

	delete []p;
	p = NULL;
	system("pause");
	return 0;
}


```

coordinate.h

```c
class Coordinate
{
public:
	Coordinate();
	~Coordinate();
public:
	int m_iX;
	int m_iY;
};
```

coordinate.cpp

```c
#include "stdafx.h"
#include <iostream>
#include "Coordinate.h"
using namespace std;

Coordinate::Coordinate()
{
	cout << "Coordinate" << endl;

}
Coordinate::~Coordinate()
{
	cout << "~Coordinate" << endl;
}
```

###c++数组对象实践
如果new的是一个数组，那么delete时要加中括号？

- 当实例化一个数组时其实数组中的每一个对象都执行了它的构造函数。
- 在销毁的时候，我们也希望他们都执行自己的析构函数
- 如果不加中括号，则只销毁第一个元素。

```c
delete p;//将中括号去掉之后，则只销毁当前指针指向的内存，执行一次析构函数
```

###对象成员

- 对象中包含其他对象

- 笛卡尔坐标系

点坐标的定义

```c
class Coordinate
{
public:
	Coordinate();
private:
	int m_iX;
	int m_iY;
}
```

线段的定义

```c
class Line{
public:
	Line();
private:
	Coordinate m_coorA;
	Coordinate m_coorB;
}
```
实例化描述线段

```c
int main(int argc, char const *argv[])
{
	Line *p = new Line();

	delete p;
	p = NULL;
	return 0;
}
```

结论：

- 当我们实例化一个line对象时，会先实例化a坐标点，再实例化b坐标点，当这两个对象实例化完成之后再实例化line对象
- 当销毁时正好与创建相反，先销毁line再销毁b，最后销毁a

比如造汽车- 零件-图纸
拆汽车-图纸 -零件

以上情况构造函数都没有参数-坐标类的构造函数需要有参数

```c
class Coordinate
{
public:
	Coordinate(int x, int y);
private:
	int m_iX;
	int m_iY;
}

class Line{
public:
	Line(int x1,int y1, int x2,int y2);
private:
	Coordinate m_coorA;
	Coordinate m_coorB;
}
```

如果直接写成如下会出问题

```c
int main(int argc, char const *argv[])
{
	Line *p = new Line(2,1,6,4);
	return 0;
}
```
要对代码进一步改造：为line的构造函数配备初始化列表

```c
Line(int x1,int y1, int x2,int y2):m_coorA(x1,y1),m_coorB(x2,y2)
{
};
```

###编码示例代码(c++对象成员实践一)

![要求](http://upload-images.jianshu.io/upload_images/1779926-dd04fc75eb61435c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- Coordinate.h ：

```c
class Coordinate
{
public:
	Coordinate();
	~Coordinate();
public:
	int getX();
	void setX(int x);
	int getY();
	void setY(int y);
private:
	int m_iX;
	int m_iY;
};
```

- Coordinate.cpp ：

```c
#include "stdafx.h"
#include <iostream>
#include "Coordinate.h"
using namespace std;

Coordinate::Coordinate()
{
	cout << "Coordinate" << endl;

}
Coordinate::~Coordinate()
{
	cout << "~Coordinate" << endl;
}

int Coordinate::getX() {
	return m_iX;
}
void Coordinate::setX(int x) {
	m_iX = x;
}
int Coordinate::getY(){
	return m_iY;
}
void Coordinate::setY(int y) {
	m_iY = y;
}

```
- Line.h :

```c
#include "Coordinate.h"

class Line {
public:
	Line();
	~Line();
	void setCoorA(int x, int y);
	void setCoorB(int x, int y);
	void printInfo();
private:
	Coordinate m_coorA;
	Coordinate m_coorB;
};

```

- Line.cpp :

```c
#include "stdafx.h"
#include "Line.h"
#include <iostream>
using namespace std;

Line::Line() {
	cout << "Line()" << endl;
}
Line::~Line() {
	cout << "~Line()" << endl;
}
void Line::setCoorA(int x, int y) {
	m_coorA.setX(x);
	m_coorA.setX(y);
}
void Line::setCoorB(int x, int y) {
	m_coorB.setX(x);
	m_coorB.setY(y);
}
void Line::printInfo() {
	cout << "("<<m_coorA.getX()<<","<<m_coorA.getY()<< ")" << endl;
	cout << "(" << m_coorB.getX() << "," << m_coorB.getY() << ")" << endl;

}
```

- main.cpp

```python
#include "stdafx.h"
#include <iostream>
#include <string>
#include "Line.h"
using namespace std;

int main(void)
{
	Line *p = new Line();

	delete p;
	p = NULL;
	system("pause");
	return 0;
}
```
作为线段这个类，我们希望在他创建的时候将里面的两个点确定下来。

线段构造函数带参数，并且传给点。改造程序。

```c
	Coordinate(int x, int y);
Line::Line(int x1,int y1,int x2,int y2):m_coorA(x1,y1),m_coorB(x2,y2) {
	cout << "Line()" << endl;
}
```

重点在于：

- coordinate有两个参数
- line使用初始化列表

####完整代码：

Line.h：

```c
#include "Coordinate.h"

class Line {
public:
	Line(int x1,int y1,int x2,int y2);
	~Line();
	void setCoorA(int x, int y);
	void setCoorB(int x, int y);
	void printInfo();
private:
	Coordinate m_coorA;
	Coordinate m_coorB;
};

```

line.cpp:

```c
#include "stdafx.h"
#include "Line.h"
#include <iostream>
using namespace std;

Line::Line(int x1,int y1,int x2,int y2):m_coorA(x1,y1),m_coorB(x2,y2) {
	cout << "Line()" << endl;
}
Line::~Line() {
	cout << "~Line()" << endl;
}
void Line::setCoorA(int x, int y) {
	m_coorA.setX(x);
	m_coorA.setX(y);
}
void Line::setCoorB(int x, int y) {
	m_coorB.setX(x);
	m_coorB.setY(y);
}
void Line::printInfo() {
	cout << "("<<m_coorA.getX()<<","<<m_coorA.getY()<< ")" << endl;
	cout << "(" << m_coorB.getX() << "," << m_coorB.getY() << ")" << endl;

}
```

Coordinate.h:

```c
class Coordinate
{
public:
	Coordinate(int x, int y);
	~Coordinate();
public:
	int getX();
	void setX(int x);
	int getY();
	void setY(int y);
private:
	int m_iX;
	int m_iY;
};
```

Coordinate.cpp :

```c
#include "stdafx.h"
#include <iostream>
#include "Coordinate.h"
using namespace std;

Coordinate::Coordinate(int x,int y)
{
	m_iX = x;
	m_iY = y;
	cout << "Coordinate()"<<m_iX<<","<<m_iY << endl;

}
Coordinate::~Coordinate()
{
	cout << "~Coordinate()" << m_iX << "," << m_iY << endl;
}

int Coordinate::getX() {
	return m_iX;
}
void Coordinate::setX(int x) {
	m_iX = x;
}
int Coordinate::getY(){
	return m_iY;
}
void Coordinate::setY(int y) {
	m_iY = y;
}

```

main.cpp:

```c
#include "stdafx.h"
#include <iostream>
#include <string>
#include "Line.h"
using namespace std;

int main(void)
{
	Line *p = new Line(1,2,3,4);
	p->printInfo();
	delete p;
	p = NULL;
	system("pause");
	return 0;
}
```

**注意：**如果对象A中有对象成员B，对象B没有默认构造函数，那么对象A必须在初始化列表中初始化对象B。

####单元巩固
定义具有2个对象的Coordinate数组，遍历对象数组，打印对象信息

```c
#include <iostream>
using namespace std;
class Coordinate
{
    
public:
	Coordinate()
	{
	}
	// 打印坐标的函数
	void printInfo()  
	{
	    cout << "("<<m_iX<<","<<m_iY<<")"<<endl;
	}
public:
	int m_iX;
	int m_iY;
};
int main(void)
{
	//定义对象数组
    Coordinate coorArr[2];
    coorArr[0].m_iX = 1;
    coorArr[0].m_iY = 2;
    coorArr[1].m_iX = 3; 
    coorArr[1].m_iY = 4;

	//遍历数组，打印对象信息
	for(int i = 0; i < 2; i++)
	{
		coorArr[i].printInfo();
	}	
	return 0;
}
```

###深拷贝和浅拷贝

```c
class Array
{
public:
	Array(){m_iCount = 5;}
	Array(const Array& arr){
		m_iCount = arr.m_iCount;
	}
private:
	int m_iCount;

}

int main(void)
{
	Array arr1;
	Array arr2 = arr1;
	return 0;
}
```

增强版

```c
class Array
{
public:
	Array(){
		m_iCount = 5;
		m_pArr = new int[m_iCount];
	}
	Array(const Array& arr){
		m_iCount = arr.m_iCount;
		m_pArr = arr.m_pArr;
	}
private:
	int m_iCount;
	int *m_pArr;
}

int main(void)
{
	Array arr1;
	Array arr2 = arr1;
	return 0;
}
```

- 普通版和增强版均为。浅拷贝的话只是简单地把值拷贝过去。
- 但是增强版会出现问题就是只简单地把指针拷贝过去的话（对象arr1和arr2的指针会指向同一个地址），一旦修改拷贝对象的数据，被拷贝对象里面的数据也会随之发生改变。（销毁了arr1，指针不存在了，销毁arr2时会出现空指针回收）

我们想要的是指向两块不同的内存，然后只对应相等

```c
class Array
{
public:
	Array(){
		m_iCount = 5;
		m_pArr = new int[m_iCount];
	}
	Array(const Array& arr){
		m_iCount = arr.m_iCount;
		m_pArr = new int[m_iCount];
		for(int i=0;i<m_iCount;i++){
			m_pArr[i] = arr.m_pArr[i];
		}
	}
private:
	int m_iCount;
	int *m_pArr;
}

int main(void)
{
	Array arr1;
	Array arr2 = arr1;
	return 0;
}
```

- 深拷贝：新建一个堆，然后就通过循环的方法把堆中的一个一个的数据拷过去。这样就可以避免在修改拷贝对象的数据时，同时改变了被拷贝对象的数据

####浅拷贝代码实践

要求：

![要求](http://upload-images.jianshu.io/upload_images/1779926-c8f45ea62713b564.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

浅拷贝代码：

Array.h

```c
class  Array
{
public:
	 Array();
	 Array(const Array& arr);
	~ Array();

	int getCount();
		void setCount(int val);
private:
	int m_iCount;
};
```
Array.cpp:

```c
#include <iostream>
#include "Array.h"
using namespace std;

Array::Array()
{
	cout << "Array()" << endl;
}

Array::Array(const Array& arr) {
	m_iCount = arr.m_iCount;
	cout << "Array(&)" << endl;
}

Array::~Array() {
	cout << "~Array()" << endl;
}

void Array::setCount(int c) {
	m_iCount = c;
}
int Array::getCount() {
	return m_iCount;
}
```

main.cpp:

```c
#include <iostream>
#include <stdlib.h>
#include "Array.h"
using namespace std;

int main(void)
{
	Array arr1;
	arr1.setCount(5);

	Array arr2 = arr1;

	cout << "arr2,count:" <<arr2.getCount() << endl;
	system("pause");
	return 0;
}
```

####深拷贝代码实践

浅拷贝代码:

```c
Array::Array(const Array& arr) {
	m_pArr = arr.m_pArr;//使用浅拷贝
	m_iCount = arr.m_iCount;
	cout << "Array(&)" << endl;
}
```

**注意**因为这样使得两个arr指向同一块内存。在销毁时会产生错误中止

完整代码：

Array.h:

```c
class  Array
{
public:
	 Array(int count);
	 Array(const Array& arr);
	~ Array();

	int getCount();
	void setCount(int val);
	void printAddr();
private:
	int m_iCount;
	int *m_pArr;
};
```
Array.cpp:

```c
#include <iostream>
#include "Array.h"
using namespace std;

Array::Array(int count )
{
	m_iCount = count;
	m_pArr = new int[m_iCount];
	cout << "Array()" << endl;
}

Array::Array(const Array& arr) {
	m_pArr = arr.m_pArr;//使用浅拷贝
	m_iCount = arr.m_iCount;
	cout << "Array(&)" << endl;
}

Array::~Array() {
	delete[]m_pArr;
	m_pArr = NULL;
	cout << "~Array()" << endl;
}

void Array::setCount(int c) {
	m_iCount = c;
}
int Array::getCount() {
	return m_iCount;
}
void Array::printAddr() {
	cout << "m_pArr:" << m_pArr << endl;
};
```
main.cpp

```c
#include <iostream>
#include <stdlib.h>
#include "Array.h"
using namespace std;

int main(void)
{
	Array arr1(5);

	Array arr2(arr1);

	arr1.printAddr();
	arr2.printAddr();
	system("pause");
	return 0;
}
```

- 深拷贝

修改代码如下

修改Array.cpp的拷贝函数

```c
Array::Array(const Array& arr) {
	m_iCount = arr.m_iCount;
	m_pArr = new int[m_iCount];//使用深拷贝
	for (int i =0;i<m_iCount;i++)
	{
		m_pArr[i] = arr.m_pArr[i];
	}

	cout << "Array(&)" << endl;
}
```
- 深拷贝：1.申请一段内存。再将源对象的内存中数值拷贝一份。

为了看起来更加清晰，在构造函数为array赋值

```c
Array::Array(int count )
{
	m_iCount = count;
	m_pArr = new int[m_iCount];
	for (int i=0;i<m_iCount;i++)
	{
		m_pArr[i] = i;
	}
	cout << "Array()" << endl;
}

```

此时再次运行。不会报错而且两个已经指向不同内存地址。

在Array.cpp新增函数：

```c
void Array::printArr() {
	for (int i=0;i<m_iCount;i++)
	{
		cout << m_pArr[i] << endl;
	}
}
```

此时main.cpp添加:

```c
arr1.printArr();
arr2.printArr();
```

####完整深拷贝代码：

array.h:

```c
class  Array
{
public:
	 Array(int count);
	 Array(const Array& arr);
	~ Array();

	int getCount();
	void setCount(int val);
	void printAddr();
	void printArr();
private:
	int m_iCount;
	int *m_pArr;
};
```
array.cpp:

```c
#include <iostream>
#include "Array.h"
using namespace std;

Array::Array(int count )
{
	m_iCount = count;
	m_pArr = new int[m_iCount];
	for (int i=0;i<m_iCount;i++)
	{
		m_pArr[i] = i;
	}
	cout << "Array()" << endl;
}

Array::Array(const Array& arr) {
	m_iCount = arr.m_iCount;
	m_pArr = new int[m_iCount];//使用深拷贝
	for (int i =0;i<m_iCount;i++)
	{
		m_pArr[i] = arr.m_pArr[i];
	}

	cout << "Array(&)" << endl;
}

Array::~Array() {
	delete[]m_pArr;
	m_pArr = NULL;
	cout << "~Array()" << endl;
}

void Array::setCount(int c) {
	m_iCount = c;
}
int Array::getCount() {
	return m_iCount;
}
void Array::printAddr() {
	cout << "m_pArr:" << m_pArr << endl;
};

void Array::printArr() {
	for (int i=0;i<m_iCount;i++)
	{
		cout << m_pArr[i] << endl;
	}
}
```

main.cpp：

```c
#include <iostream>
#include <stdlib.h>
#include "Array.h"
using namespace std;

int main(void)
{
	Array arr1(5);

	Array arr2(arr1);

	arr1.printAddr();
	arr2.printAddr();
	arr1.printArr();
	arr2.printArr();
	system("pause");
	return 0;
}
```

###对象指针
有一个指针用来指向一个对象。

demo：

```c
class Coordinate{
public:
	int m_iX;
	int m_iY;
}
```

堆中实例化：

```c
Coordinate *p = new Coordinate; //执行构造函数

```

![堆中](http://upload-images.jianshu.io/upload_images/1779926-2835a417e8d13cfc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

p指向m_iX ：访问方式：`p->m_iX`
*p变成一个对象。

具体示例代码：

```c
int main(void)
{
	Coordinate *p = new Coordinate;
	p -> m_iX = 10;  //(*p).m_iX =10;
	p -> m_iY = 20;  //(*p).m_iY =20;
	delete p;
	p = NULL;
	return 0;
}
```

- new 会自动调用对象的构造函数；
- malloc 则不会调用相关对象的构造函数，只是分配内存。

####编码示范：

![要求](http://upload-images.jianshu.io/upload_images/1779926-27b8e8cebb5227d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Coordinate.h

```c
class Coordinate
{
public:
	Coordinate();
	~Coordinate();
	int m_iX;
	int m_iY;

};
```

Coordinate.cpp

```c
#include <iostream>
#include "Coordinate.h"

using namespace std;
Coordinate::Coordinate()
{
	cout << "Coordinate()" << endl;
}

Coordinate::~Coordinate()
{
	cout << "~Coordinate()" << endl;
}
```

main.cpp:

```c
#include <iostream>
#include "Coordinate.h"
#include <stdlib.h>

using namespace std;

int main()
{
	//堆实现
	
	//Coordinate *p1 = NULL;
	//p1 = new Coordinate;//因为有默认构造函数，括号可写可不写
	//Coordinate *p2 = new Coordinate();
	//p1->m_iX = 10;
	//p1->m_iY = 20;
	//(*p2).m_iX = 30;//*p2使p2变成了一个对象
	//(*p2).m_iY = 40;
	//cout << (*p1).m_iX + (*p2).m_iX << endl;
	//cout << p1->m_iY + p2-> m_iY << endl;

	//delete p1;
	//p1 = NULL;
	//delete p2;
	//p2 = NULL;

	//栈中实例化
	Coordinate p1;

	Coordinate *p2 = &p1;//让p2指向p1的地址，p2可操作p1

	p2->m_iX = 10;
	p2->m_iY = 20;

	cout << p1.m_iX << "," << (*p2).m_iY << endl;
	system("pause");
	return 0;
}
```

####编码练习

>定义一个坐标类，在堆上实例化坐标对象，并给出坐标（3，5），然后打印坐标信息，销毁坐标对象。

```c
#include <iostream>
using namespace std;
class Coordinate
{
    
public:
	Coordinate(int x, int y)
	{
		// 设置X,Y的坐标
		m_iX = x;
        m_iY = y;
	}
public:
	int m_iX;
	int m_iY;
};

int main(void)
{
    // 在堆上创建对象指针
	Coordinate *p = new Coordinate(3,5);
    // 打印坐标
	cout <<"("<<(*p).m_iX<<","<<(*p).m_iY<<")"<< endl;
    // 销毁对象指针
	delete p;
    p = NULL;
	return 0;
}
```

###对象成员指针
>- 对象成员： 一个对象成为另外一个类的数据成员
- 对象成员指针： 对象的指针成为另外一个类的数据成员

坐标点类：

```c
class Coordinate
{
    
public:
	Coordinate(int x, int y);
public:
	int m_iX;
	int m_iY;
};
```

线段类：

```c
class Line {
public:
	Line();
	~Line();
private:
	Coordinate m_coorA;
	Coordinate m_coorB;
};

```

将线段类代码变成：

```c
class Line {
public:
	Line();
	~Line();
private:
	Coordinate *m_pCoorA;
	Coordinate *m_pCoorB;
};
```

初始化时仍使用初始化列表进行初始化：

```c
Line::line():m_pCoorA(NULL),m_pCoorB(NULL){

}
```

也可以使用普通初始化：

```c
Line::line()
{
	m_pCoorB = NULL;
	m_pCoorA = NULL;
}
```

普遍情况：

```c
Line::line()
{
	m_pCoorB = new Coordinate(1,3);
	m_pCoorA = new Coordinate(5,6);
}
Line::~line()
{
	delete m_pCoorA;
	delete m_pCoorB;
}
```

对象成员与对象成员指针的不同：

```c
int main(void)
{
	Line line();
	cout << sizeof(line) <<endl; //8
	return 0;
}
```
//里面所有对象的总和。
- 对象成员指针的定义： 类名 * 指针名
- 指针在32位编译器下占4个基本内存单元
- 若存在对象成员指针1，2……。sizeof（指针1，指针2……）只计算各指针所占内存的总和- 不计算对象成员所占内存

![对象成员指针sizeof](http://upload-images.jianshu.io/upload_images/1779926-5676bd541d3286e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>创建时line对象中只有两个占4字节的指针。而实例化出的两个对象在堆中。
销毁时，先销毁堆中的，再释放line对象。

####编码示例

![要求](http://upload-images.jianshu.io/upload_images/1779926-bd5f3d087fc95188.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Coordinate.h

```c
class Coordinate
{
public:
	Coordinate(int x, int y);
	~Coordinate();
public:
	int getX();
	int getY();
private:
	int m_iX;
	int m_iY;
};

```
Coordinate.cpp

```c
#include "stdafx.h"
#include <iostream>
#include "Coordinate.h"
using namespace std;

Coordinate::Coordinate(int x,int y)
{
	m_iX = x;
	m_iY = y;
	cout << "Coordinate()"<<m_iX<<","<<m_iY << endl;

}
Coordinate::~Coordinate()
{
	cout << "~Coordinate()" << m_iX << "," << m_iY << endl;
}

int Coordinate::getX() {
	return m_iX;
}
int Coordinate::getY(){
	return m_iY;
}
```

Line.h:

```c
#include "Coordinate.h"

class Line {
public:
	Line(int x1,int y1,int x2,int y2);
	~Line();
	void setCoorA(int x, int y);
	void setCoorB(int x, int y);
	void printInfo();
private:
	Coordinate *m_pCoorA;
	Coordinate *m_pCoorB; //这里是一个指针而不是一个对象
};

```

Line.cpp:

```c
#include "stdafx.h"
#include "Line.h"
#include <iostream>
using namespace std;

Line::Line(int x1,int y1,int x2,int y2){
	m_pCoorA = new Coordinate(x1, y1);
	m_pCoorB = new Coordinate(x2, y2);
	cout << "Line()" << endl;
}
Line::~Line() {
	delete m_pCoorA;
	m_pCoorA = NULL;
	delete m_pCoorB;
	m_pCoorB = NULL;
	cout << "~Line()" << endl;
}
void Line::printInfo() {
	cout << "("<<(*m_pCoorA).getX()<<","<< (*m_pCoorA).getY()<< ")" << endl;
	cout << "(" << m_pCoorB->getX() << "," << m_pCoorB->getY() << ")" << endl;

}
```

main.cpp:

```c
#include "stdafx.h"
#include <iostream>
#include <string>
#include "Line.h"
using namespace std;

int main(void)
{
	Line *p = new Line(1,2,3,4);
	p->printInfo();


	delete p;
	p = NULL;
	cout << sizeof(p) << endl;
	cout << sizeof(Line) << endl;
	system("pause");
	return 0;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-4125f945e1af6194.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 可以看出在类包含指针对象时，先实例化指针对象a,b。再去调用父对象的构造函数。
- 在销毁时也是先按照顺序销毁指针对象a,b.最后销毁父对象。
- 这种情况下对象的销毁与创建顺序一致。

**这里我们需要注意对象成员与对象成员指针在创建与销毁时的不同**

###this指针

- 对象指针
- 对象成员指针
- this指针

例子：

```c
class Array
{
	public:
		Array(int _len){len = _len;}
		int getLen(){return len;}
		void setLen(int _len){len = _len;}
	private:
		int len;
}
```

数据成员与参数在表达同一个意思的时候取相似的名字。
但当两者完全重名。

```c
class Array
{
	public:
		Array(int len){len = len;}//错
		int getLen(){return len;}
		void setLen(int len){len = len;}//错
	private:
		int len;
}
```

>计算机和人类都无法判断是把参数赋值给成员了，还是成员赋值给参数。

this指针：指向对象自身数据的指针

Array arr1 this <-> &arr1
Array arr2 this <-> &arr2

![this](http://upload-images.jianshu.io/upload_images/1779926-f6e0eae0b96f6dfd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

this表达什么地址，取决于当前所在作用域

```c
class Array
{
	public:
		Array(int len){this->len = len;}//对
		int getLen(){return len;}
		void setLen(int len){this->len = len;}//对
	private:
		int len;
}
```

对象结构：

![对象结构](http://upload-images.jianshu.io/upload_images/1779926-e7540720fe4ef63c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 存在多个对象，成员函数只有代码区内的一份。又没有传递参数。成员函数如何确定该调用哪个对象的数据成员？

- 成员函数如何访问到对应对象的数据成员？

```c
class Array
{
	public:
		Array(T *this,int len){this->len = len;}//对
		int getLen(T *this){return len;}
		void setLen(T *this,int len){this->len = len;}//对
	private:
		int len;
}
```

![this指针](http://upload-images.jianshu.io/upload_images/1779926-910478622d6f6b02.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 通过this来实现分辨不同的arr。
- 编译器自动的为每一个成员函数的参数列表都自动加上了this。

####this指针在参数列表中的位置（代码实践）
要求：

![this代码实践](http://upload-images.jianshu.io/upload_images/1779926-9be48fbddcd62b10.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


正常版本1：
Array.h :

```c
class  Array
{
public:
	 Array(int len);
	~ Array();


	void printAddr();
	void printArr();
	int getLen();
	void setLen(int val);
	void printInfo();
private:
	int m_iLen;
};
```

Array.cpp:

```c
#include <iostream>
#include "Array.h"
using namespace std;

Array::Array(int len )
{
	m_iLen = len;
	cout << "Array()" << endl;
}
Array::~Array() {
	cout << "~Array()" << endl;
}

void Array::setLen(int len) {
	m_iLen = len;
}
int Array::getLen() {
	return m_iLen;
}

void Array::printInfo() {

}
```

main.cpp:

```c
#include <iostream>
#include <stdlib.h>
#include "Array.h"
using namespace std;

int main(void)
{
	Array arr1(10);


	system("pause");
	return 0;
}
```

改动：将所有m_iLen都改为与参数名相同的len
此时已经人眼分辨不出哪个是数据成员，哪个是参数

为了程序更好看。我们引入this。

改动过的Array.cpp:

```c
#include <iostream>
#include "Array.h"
using namespace std;

Array::Array(int len )
{
	this->len = len;
	cout << "Array()" << endl;
}
Array::~Array() {
	cout << "~Array()" << endl;
}

void Array::setLen(int len) {
	this->len = len;
}
int Array::getLen() {
	return len;
}

void Array::printInfo() {

}
```

####this指针用法2

return this.实现链式调用

Array.h

```c
class  Array
{
public:
	 Array(int len);
	~ Array();


	void printAddr();
	void printArr();
	int getLen();
	Array& setLen(int val);
	Array& printInfo();
private:
	int len;
};
```

Array.cpp:

```c
#include <iostream>
#include "Array.h"
using namespace std;

Array::Array(int len )
{
	this->len = len;
	cout << "Array()" << endl;
}
Array::~Array() {
	cout << "~Array()" << endl;
}

Array& Array::setLen(int len) {
	this->len = len;
	return *this;
}
int Array::getLen() {
	return len;
}

//添加引用之后才是arr1
Array& Array::printInfo() {
	cout << "len:" << len << endl;
	return *this;//this本身是一个指针，而加上*以后变成一个对象。

}
```

main.cpp:

```c
#include <iostream>
#include <stdlib.h>
#include "Array.h"
using namespace std;

int main(void)
{
	Array arr1(10);
	//因为此时this返回了当前对象。所以可以使用"."
	arr1.printInfo().setLen(5).printInfo();
	//这时输出的还是10.因为返回的*this只是一个临时对象。
	//cout << "len = " << arr1.getLen() << endl;
	system("pause");
	return 0;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-0b09bcfc77a5acf0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

指针实现版本：

>- 将`return *this`改为`return this`
- 将返回类型`Array &`改为 `Array *`
- 将对象的使用符`.`改为指针操作符`->`

完整代码如下：

array.h:

```c
class  Array
{
public:
	 Array(int len);
	~ Array();
	void printAddr();
	void printArr();
	int getLen();
	Array* setLen(int val);
	Array* printInfo();
private:
	int len;
};
```
Array.cpp:

```c
#include <iostream>
#include "Array.h"
using namespace std;

Array::Array(int len )
{
	this->len = len;
	cout << "Array()" << endl;
}
Array::~Array() {
	cout << "~Array()" << endl;
}

Array* Array::setLen(int len) {
	this->len = len;
	return this;
}
int Array::getLen() {
	return len;
}

//添加引用之后才是arr1
Array* Array::printInfo() {
	cout << "len:" << len << endl;
	return this;//this本身是一个指针，而加上*以后变成一个对象。

}
```

main.cpp:

```c
#include <iostream>
#include <stdlib.h>
#include "Array.h"
using namespace std;

int main(void)
{
	Array arr1(10);
	//因为此时this返回了当前对象。所以可以使用"."
	arr1.printInfo()->setLen(5)->printInfo();
	//这时输出的还是10.因为返回的*this只是一个临时对象。
	//cout << "len = " << arr1.getLen() << endl;
	system("pause");
	return 0;
}
```

- 最终结果与刚才一致。说明不管是引用还是指针都可以实现改变实际的值。

this指针的本质：
>相当于所在对象的地址

在printInfo中打印出this指针的值

Array.cpp:

```c
Array* Array::printInfo() {
	cout << this << endl;
	return this;//this本身是一个指针，而加上*以后变成一个对象。

}
```

main.cpp:

```c
int main(void)
{
	Array arr1(10);
	//因为此时this返回了当前对象。所以可以使用"."
	arr1.printInfo();
	cout << &arr1 << endl;
	system("pause");
	return 0;
}
```

运行结果：

![this指针指向所在对象本身地址](http://upload-images.jianshu.io/upload_images/1779926-8c34ce5e8425aee2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- this指针无需用户定义，是编译器自动产生的。

###常对象成员和常成员函数
####const重出江湖
例子：

```c
class Coordinate
{
public:
	Coordinate(int x,int y);
private:
	const int m_iX;
	const int m_iY;
}

//错误做法：

Coordinate::Coordinate(int x,int y)
{
	m_iY =y;
	m_iX =x;
}

//正确做法：使用初始化列表

Coordinate::Coordinate(int x,int y):m_iX(x),m_iY(y)
{

}

```

常对象成员：对象成员通过const修饰

线段：一旦起点和终点被确定就不允许修改了。

```c
class Line
{
public:
	Line(int x1,int y1,int x2 ,int y2)
private:
	const Coordinate m_coorA;
	const Coordinate m_coorB;
}

//初始化两个对象使用初始化列表
//
Line::Line(int x1,int y1,int x2 ,int y2):m_coorA(x1,y1),m_coorB(x2,y2)
{
	cout<< "Line" << endl;
}

//调用：
//
int main(void)
{
	Line *p = new Line(2,1,6,4);

	delete p;
	p = NULL;
	return 0;
}
```

####const修饰成员函数（常成员函数）
例子：

```c
class Coordinate
{
public:
	Coordinate(int x,int y);
	void changeX() const;
	void changeX();
private:
	int m_iX;
	int m_iY;
}

//定义函数
//
void Coordinate::changeX() const
{
	m_iX = 10;//错误
};

void Coordinate::changeX() 
{
	m_iX = 10;
};
```

为什么：常成员函数中不能改变数据成员的值？

![隐藏的参数this指针](http://upload-images.jianshu.io/upload_images/1779926-7684ca2720ef0116.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![const修饰的指针变成常指针](http://upload-images.jianshu.io/upload_images/1779926-f5dbc8b8ec896d87.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

互为重载：

```c
	void changeX() const;
	void changeX();//互为重载
```

Q：当直接调用：`coor.changeX()`到底调用的是哪一个呢？

>A：调用的是普通的不带const的。

Q:那么想调用那个带const的如何写？

>A:代码如下

```c
int main(void)
{
	const Coordinate coordinate(3,5);//常对象
	coordinate.changeX();
	return 0;
}
```

常对象调用的是常成员函数。

####常对象成员与常成员函数代码实践

![要求](http://upload-images.jianshu.io/upload_images/1779926-a32480c32e94235c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Coordinate.h :

```c
class Coordinate
{
public:
	Coordinate(int x, int y);
	~Coordinate();
public:
	int getX() const;//此处声明该成员函数为常成员函数
	void setX(int x);
	int getY() const;//同上
	void setY(int y);
private:
	int m_iX;
	int m_iY;
};
```
Coordinate.cpp

```c
#include "stdafx.h"
#include <iostream>
#include "Coordinate.h"
using namespace std;

Coordinate::Coordinate(int x, int y)
{
	m_iX = x;
	m_iY = y;
	cout << "Coordinate()" << m_iX << "," << m_iY << endl;

}
Coordinate::~Coordinate()
{
	cout << "~Coordinate()" << m_iX << "," << m_iY << endl;
}

int Coordinate::getX() const{
	return m_iX;
}
void Coordinate::setX(int x) {
	m_iX = x;
}
int Coordinate::getY() const{
	return m_iY;
}
void Coordinate::setY(int y) {
	m_iY = y;
}
```

Line.h:

```c
#include "Coordinate.h"

class Line {
public:
	Line(int x1, int y1, int x2, int y2);
	~Line();
	void setCoorA(int x, int y);
	void setCoorB(int x, int y);
	void printInfo();
	void printInfo() const;//互为重载

private:
	const Coordinate m_coorA;
	Coordinate m_coorB;
};
```

Line.cpp:

```c
#include "stdafx.h"
#include "Line.h"
#include <iostream>
using namespace std;

Line::Line(int x1, int y1, int x2, int y2) :m_coorA(x1, y1), m_coorB(x2, y2) {
	cout << "Line()" << endl;
}
Line::~Line() {
	cout << "~Line()" << endl;
}
void Line::setCoorA(int x, int y) {
	//m_coorA.setX(x);
	//m_coorA.setX(y);
}
void Line::setCoorB(int x, int y) {
	m_coorB.setX(x);
	m_coorB.setY(y);
}
void Line::printInfo() {
	cout << "printInfo()" << endl;
	cout << "(" << m_coorA.getX() << "," << m_coorA.getY() << ")" << endl;
	cout << "(" << m_coorB.getX() << "," << m_coorB.getY() << ")" << endl;

}
void Line::printInfo() const{
	cout << "printInfo() const" << endl;
	cout << "(" << m_coorA.getX() << "," << m_coorA.getY() << ")" << endl;
	cout << "(" << m_coorB.getX() << "," << m_coorB.getY() << ")" << endl;

}
```

main.cpp

```c
#include "stdafx.h"
#include <iostream>
#include <string>
#include "Line.h"
using namespace std;

int main(void)
{
	Line line(1, 2, 3, 4);
	line.printInfo();//调用的是普通的

	const Line line2(1, 2, 3, 4);
	line2.printInfo();//调用的是常成员函数
	
	system("pause");
	return 0;
}
```
- 常对象只能调用常成员函数。
- 普通对象可以调用全部成员函数(非常成员)。
- 当对一个对象调用成员函数时，编译程序先将对象的地址赋给this指针，然后调用成员函数，每次成员函数存取数据成员时，由隐含使用this指针。
- 当一个成员函数被调用时，自动向它传递一个隐含的参数，该参数是一个指向这个成员函数所在的对象的指针。 
- 在X类的const成员函数中，this指针的类型为：const X* const, 这说明this指针所指向的这种对象是不可修改的（即不能对这种对象的数据成员进行赋值操作）;
- 常成员函数中可以使用普通的数据成员，但是不能改变对象成员的值
- 常成员函数内不能调用普通的常用函数

###常指针与常引用

####对象指针与对象引用

```c
class Coordinate
{
public:
	Coordinate(int x, int y);
public:
	int getX();
	int getY();
	void printInfo() const;
private:
	int m_iX;
	int m_iY;
};
```
定义：

```c
int Coordinate::getX(){
	return m_iX;
}

int Coordinate::getY() const{
	return m_iY;
}

void Coordinate::printInfo() const
{
	cout << "(" <<m_iX <<","<<m_iY <<")"<<endl;
}
```

对象的引用 & 对象的指针

```c
int main(void)
{
	Coordinate coor1(3,5);
	Coordinate &coor2 = coor1;//对象的引用
	Coordinate *pCoor = &coor1;//对象的指针，注意取地址符。
	coor1.printInfo();
	coor2.printInfo();
	pCoor -> printInfo();
	return 0;
}
```
对象的常指针与对象的常引用

```c
int main(void)
{
	Coordinate coor1(3,5);
	const Coordinate &coor2 = coor1;//常：对象的引用
	const Coordinate *pCoor = &coor1;//常：对象的指针，注意取地址符。
	coor1.printInfo(); //普通对象正常使用
	coor2.getX(); //错误，常引用（只具有读权限）
	//内部隐藏传入的this是要求一个读写权限都有的
	//只能调用常成员函数
	coor2.printInfo();
	//getX并没有const
		int Coordinate::getX(){

		}
	pCoor -> getY();//错误。常指针（只有只读权限）。
	//getY要求传入的是读写权限的this
	pCoor -> printInfo();
	return 0;
}
```

更复杂的例子：

```c
int main()
{
	Coordinate coor1(3,5);
	Coordinate coor2(7,9);

	Coordinate *const pCoor = &coor1;//对象指针，const在*的后面。
	//一旦指向一个对象就不能指向其他的对象

	//指针不可以指向其他对象，但是指针本身指向的对象内容可变。
	//这是一个具有读写权限的指针。只限于当前指向的对象。
	pCoor ->getY(); //正确

	//pcoor已经被const修饰了，不允许修改
	pCoor = &coor2;

	//print常成员函数（要求读权限的指针），而pcoor具有读写权限
	pCoor -> printInfo();

	return 0;
}
```

常指针容易混淆的：

- `const *p`           -》 `*p`不可以再赋值
- `*const p`            -》  `p`不可以再赋值
- `const * const p`     -》 `*p`和`p`都不可以再赋值
- 不能把小权限的指向大权限的，但是可以把大权限的指向小权限的。

####练习题
>定义一个坐标类，在栈上实例化坐标类常对象，并给出坐标（3，5），然后定义常引用、常指针，最后使用对象、引用、指针分别通过调用信息打印函数打印坐标信息。

```c
#include <iostream>
using namespace std;
class Coordinate
{
    
public:
	Coordinate(int x, int y)
	{
		// 设置X,Y的坐标
	    m_iX = x;
        m_iY = y;
	}
    // 实现常成员函数
	void printInfo() const
	{
	    cout << "("<<m_iX<<","<<m_iY<<")"<<endl;
	}
public:
	int m_iX;
	int m_iY;
};


int main(void)
{
	const Coordinate coor(3, 5);

	// 创建常指针p
    const Coordinate *p = &coor;
    // 创建常引用c
    const Coordinate &c = coor;
	
	coor.printInfo();
	p->printInfo();
	c.printInfo();  
	
	return 0;
}
```
###迷宫程序
未完待续。
