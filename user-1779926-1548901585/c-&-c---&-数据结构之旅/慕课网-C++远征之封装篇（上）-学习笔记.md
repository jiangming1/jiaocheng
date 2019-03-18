##C++远征之封装篇（上）
###简介
- 类（抽象概念），对象（真实具体）
- 附件：数据成员和成员函数（构成了精彩而完整的类）
- 构造函数 & 析构函数（描述了对象的生生死死）
- 对象复制和对象赋值（使类的定义充满艺术）
- 对象数组和对象指针（将应用型态发挥到淋漓尽致）
- this指针（影子）

面向过程到面向对象。

###类和对象
- 类（概念，从对象中抽象出的）和对象（具体的事物）

![c++类定义](http://upload-images.jianshu.io/upload_images/1779926-5bac13238396c872.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>数据成员(属性)、成员函数

结论：目的不同抽象出的信息不同,我们只抽象出我们自己需要的信息。

封装: 选择性暴露。

- 访问限定符:public公共的，protected受保护的，private私有的

###内容总结：

-  什么是类，什么是对象
- 类的定义： 数据成员，成员函数
- 类的访问限定符

###对象的实例化

从类中将对象实例化出来

- 从栈中实例化
- 从堆中实例化

从栈中实例化

```c
#include <iostream>
#include <stdlib.h>
using namespace std;

class TV
{
   public:
	  char name[20];
	  int type;

	  void changeVol();
	  void power();
}

int main(void)
{
	TV tv;//定义一个对象
	TV tv[20]; 
	return 0;
}//从栈中实例化对象  自动回收
```

从堆中实例化对象.

- 从堆中实例化的对象。需要我们手动管理delete
- 从栈中申请实例化的不需要手动维护。系统会自动回收。

```c
int main(void)
{
	TV *p = new TV();
	TV *q = new TV[20];

	delete p;
	delete []q;

	return 0;

}//从堆中实例化对象
```

###对象成员的访问
栈中使用`.`

```c

int main(void)
{
	TV tv;//定义一个对象
	tv.type = 0;
	tv.changeVol();
	return 0;
}//从栈中实例化对象  自动回收
```
堆中使用`->`

```c
int main(void)
{
	TV *p = new TV();
	p -> type = 0;
	p -> changeVol();

	delete p;
	p = NULL;

	return 0;

}//从堆中实例化对象
```

当堆中使用数组时

```c
int main(void)
{
	
	TV *q = new TV[5];
	for (int i = 0; i < 5; ++i)
	{
		p[i] ->type =0;
		p[i] ->changeVol();
	}

	delete []q;
	p = NULL;
	return 0;

}//从堆中实例化对象
```

###代码演示：

定义一个坐标类：包含x，y两个数据成员。以及打印x和打印y成员函数。

```c
#include "stdafx.h"
#include <iostream>
using namespace std;

class Coordinate {
public:
	int x;
	int y;
	void printx(){
		cout << x << endl;
	}
	void printy() {
		cout << y << endl;
	}
};
int main(void)
{
	Coordinate coor;
	coor.x = 10;
	coor.y = 20;
	coor.printx();
	coor.printy();

	Coordinate *p = new Coordinate();
	if (NULL == p) {
		//failed
		return 0;
	}
	p->x = 100;
	p->y = 200;
	p->printx();
	p->printy();

	delete p;
	p = NULL;

	system("pause");
	return 0;
}
```

###c++初始化String
使用频繁，操作繁琐的数据：

- char数组：`（strlen | strcat | strcpy | strcmp | strncpy |strncmp | strstr）`
- 字符串类型string类型 

###string类型

```c
#include "stdafx.h"
#include <iostream>
#include <string>
using namespace std;

int main(void)
{
	string name = "mtianyan";
	string hobby("football");
	cout << name << hobby << endl;
	system("pause");
	return 0;
}

```
初始化string对象的多个方式

```c
string s1; //s1为空串
string s2("ABC"); //用字符串字面值初始化s2；
string s3(s2); //将s3初始化为s2的一个副本。
string s4(n,'c') // 将s4初始化为字符‘c’的n个副本。3个ccc
```
string的常用操作

```c
s.empty() //若s为空串，返回true，否则返回false；
s.size() //返回s中字符的个数
s[n]     //返回s中位置为n的字符，位置从0开始
s1 + s2  //将两个字符串连接成新串，返回新生成的串
s1 = s2  //把s1的内容替换为s2的副本；
v1 == v2 //判定相等，相等返回true，否则返回false
v1 != v2  //判定不等，不等返回true，否则返回false

```
![非法字符串link](http://upload-images.jianshu.io/upload_images/1779926-dbd59fb5b9980998.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

纯字符串连接为非法操作。

###代码演示

![题目](http://upload-images.jianshu.io/upload_images/1779926-d3d8b45330469b53.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

因为要判定输入是不是为空。所以不能简单的使用cin而应该使用getline

```c
#include "stdafx.h"
#include <iostream>
#include <string>
using namespace std;


int main(void)
{

	string name;
	cout << "please input your name:";
	getline(cin, name);
	if (name.empty()){
		cout << "input is null" << endl;
		system("pause");
		return 0;
	}
	if (name == "mtianyan")
	{
		cout << "you are a admin" << endl;

	}
	cout << "hello ," + name << endl;
	cout << "your name length is :" << name.size() << endl;
	cout << "your name frist letter is :" << name[0] << endl;

	system("pause");
	return 0;
}
```

###单元巩固
定义一个Student类，包含名字和年龄两个数据成员，实例化一个Student对象，并打印出其成两个数据成员

```c
#include <iostream>
#include <string>
using namespace std;

/**
  * 定义类：Student
  * 数据成员：名字、年龄
  */
class Student
{
public:
    // 定义数据成员名字 m_strName 和年龄 m_iAge
    string m_strName;
    int m_iAge;
};

int main()
{
    // 实例化一个Student对象stu
    Student stu;
    // 设置对象的数据成员
    stu.m_strName = "慕课网";
    stu.m_iAge = 2;
    
    // 通过cout打印stu对象的数据成员
    cout << stu.m_strName << " " << stu.m_iAge << endl;
    return 0;
}
```

###数据的封装

面向对象的基本思想:

- 将所有问题转化为谁做什么，即调用成员函数解决问题。  通过函数封装数据成员
- set & get 方法。
- 封装的好处。在set中对于参数条件进行限制。数据只读不写：只写get方法。不写set方法。
- 只读属性

####代码展示
要求：

- 定义一个student类，含有如下信息
- 姓名：name
- 性别 ：gender
- 学分（只读） ：score
- 学习：study方法


```c
#include "stdafx.h"
#include <iostream>
#include <string>
#include "imooc_c++.h"
using namespace std;
class Student {
public:

	string getName() const 
	{
		return m_strName; 
	}
	void setName(string name) 
	{ 
		m_strName = name; 
	}
	std::string getGender() const { return m_strGender; }
	void setGender(std::string val) { m_strGender = val; }
	int getScore() {
		return m_iScore;
	}
	void study(int _score) {
		m_iScore += _score; 
	}
	void initScore() {
		m_iScore = 0;
	}
private:
	string m_strName;
	string m_strGender;
	int m_iScore;

};

int main(void)
{
	Student stu;
	stu.initScore();
	stu.setName("张三");
	stu.setGender("女");
	stu.study(5);
	stu.study(3);

	cout << stu.getName() << " " << stu.getGender() << " " << stu.getScore() << endl;
	
	system("pause");
	return 0;
}
```

注意，赋初值。！！！构造函数专用来初始化。

###单元巩固

定义一个Student类，包含名字一个数据成员，使用get和set函数封装名字这个数据成员。在main函数中通过new实例化对象，并打印其相关函数。

```c
#include <iostream>
#include <string>
using namespace std;

/**
  * 定义类：Student
  * 数据成员：m_strName
  * 数据成员的封装函数：setName()、getName()
  */
class Student
{
public:
    // 定义数据成员封装函数setName()
    void setName(string name) 
	{ 
		m_strName = name; 
	}
    
    
    // 定义数据成员封装函数getName()
    string getName() const 
	{
		return m_strName; 
	}
    
//定义Student类私有数据成员m_strName
string m_strName;

};

int main()
{
    // 使用new关键字，实例化对象
	Student *str = new Student();
    // 设置对象的数据成员
	str->setName("慕课网");
    // 使用cout打印对象str的数据成员
    cout << str ->getName();
    // 将对象str的内存释放，并将其置空
	delete str;
	str = NULL;
	return 0;
}
```

###类内定义与内联函数

内联函数：inline

- 普通函数在调用时先找到函数体，执行完函数内容后再返回调用函数的语句；
- 内联函数inline在调用时直接用函数内容替换调用语句，执行速度更快，但只局限于简单的函数。

类内定义与类外定义

- 类内定义是指在类里面定义成员函数的函数体（优先编译为inline）
- 类外定义是指在类的外面先定义好这个函数如何运作，然后再通过类里面声名这个函数。

类外定义：

- 同文件类外定义
- 份文件类外定义

Car.cpp

![同文件类外定义](http://upload-images.jianshu.io/upload_images/1779926-6a89b9f194e5b3ed.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为了标识这是属于汽车car的函数：`car::`

分文件类外定义

![分文件类外定义](http://upload-images.jianshu.io/upload_images/1779926-be3fff38aa91ca4d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###代码演示

要求：

![题目要求](http://upload-images.jianshu.io/upload_images/1779926-cd17ab8da69d750a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
#include "stdafx.h"
#include <iostream>
#include <string>
#include "imooc_c++.h"
using namespace std;
class Teacher {
public:
	void setName(string name);
	string getName();
	void setGender(std::string val);
	string getGender();
	void setAge(int _age);
	int getAge();
	void teach();
private:
	string m_strName;
	string m_strGender;
	int m_iAge;


};

string Teacher::getName()
{
	return m_strName;
}
void Teacher::setName(string name)
{
	m_strName = name;
}
std::string Teacher::getGender() { return m_strGender; }
void Teacher::setGender(std::string val) { m_strGender = val; }
int Teacher::getAge() {
	return m_iAge;
}
void Teacher::setAge(int _age) {
	m_iAge = _age;
}
void Teacher::teach() {
	cout << "现在上课" << endl;
};
int main(void)
{
	Teacher teacher;
	teacher.setAge(30);
	teacher.setName("孔子");
	teacher.setGender("男");
	cout << teacher.getName() << " " << teacher.getGender() << " " << teacher.getAge() << endl;
	teacher.teach();
	system("pause");
	return 0;
}


```

####分文件类外定义实现。

在解决方案，头文件处右键添加新建项：头文件。Teacher.h
在源文件右键添加新建项：cpp。Teacher.cpp

Teacher.cpp:只存放类外方法的定义

```c
#include "stdafx.h"
#include "Teacher.h"
#include <iostream>
#include <string>
using namespace std;

string Teacher::getName()
{
	return m_strName;
}
void Teacher::setName(string name)
{
	m_strName = name;
}
std::string Teacher::getGender() { return m_strGender; }
void Teacher::setGender(std::string val) { m_strGender = val; }
int Teacher::getAge() {
	return m_iAge;
}
void Teacher::setAge(int _age) {
	m_iAge = _age;
}
void Teacher::teach() {
	cout << "现在上课" << endl;
};
```

teacher.h只存放类的声明

```c
#include <string>
using namespace std;

class Teacher {
public:
	void setName(string name);
	string getName();
	void setGender(std::string val);
	string getGender();
	void setAge(int _age);
	int getAge();
	void teach();
private:
	string m_strName;
	string m_strGender;
	int m_iAge;


};
```

imooc_c++.cpp存放类的实例化等
以及程序主入口

```c
#include "stdafx.h"
#include <iostream>
#include <string>
#include "imooc_c++.h"
#include "Teacher.h"
using namespace std;



int main(void)
{
	Teacher teacher;
	teacher.setAge(30);
	teacher.setName("孔子");
	teacher.setGender("男");
	cout << teacher.getName() << " " << teacher.getGender() << " " << teacher.getAge() << endl;
	teacher.teach();
	system("pause");
	return 0;
}


```

main.cpp的头文件为：

- `#include <iostream>`
- `#include <stdlib.h>`
- `#include <string>`
- `#include "teacher.cpp"`

teacher.h的头为

- `#include <string>`
- `using namespace std;`

tercher.cpp的头为：

- `#include "teacher.h"`

注意：不要把<string>写成<string.h>

###c++构造函数


- 实例化的对象是如何在内存中存储的？
- 类中的代码又是如何存储的？
- 数据与代码的关系

####对象的结构
内存分区

- 栈区  `int x=0;` `int*p=NULL;` 内存由系统管理。
- 堆区： `int *p = new int[20];` new & delete
- 全局区：存贮全局变量及静态变量
- 常量区 `string str = "hello";`
- 代码区：存贮编译之后的二进制代码

定义一个汽车类，在类被实例化之前，是不会占用栈或者堆内存的。
但是当它实例化出car1 ，car2 ,car3 。每个对象在栈上开辟一段内存。
代码区只有一份代码。 


![类实例化内存](http://upload-images.jianshu.io/upload_images/1779926-fc4c07842edd3369.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###对象初始化
- 有且仅有一次的初始化
- 根据条件初始化

>对于有且仅有一次：

构造函数的规则和特点：

- 构造函数在对象实例化时被自动调用；
- 构造函数与类同名；
- 构造函数没有返回值；
- 构造函数可以被重载；
- 实例化对象时仅用一个构造函数；
- 用户没有定义时，程序会自动生成一个构造函数。

####无参构造函数

```c
class Student
{
public:
	Student(){
		m_strName = "jim";
	}
private:
	string m_strName;
}
```

####有参构造函数

```c
class Student
{
public:
	Student(string name){
		m_strName = name;
	}
private:
	string m_strName;
}
```

####重载构造函数

```c
class Student
{
public:
	Student(){
		m_strName = "jim";
	}
	Student(string name){
		m_strName = name;
	}
private:
	string m_strName;
}
```

####代码示例

![要求](http://upload-images.jianshu.io/upload_images/1779926-3ee334e092969aa1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
	Teacher();
	Teacher(string name, int age=20);
```

这样的计算机是可以分辨的，但是如果给name也给默认值。那么将无法通过编译
两个不能共存，但是可以单独存在

main.cpp

```c
#include "stdafx.h"
#include <iostream>
#include <string>
#include "Teacher.h"
using namespace std;

int main(void)
{
	//Teacher teacher;//无参数实例化
	Teacher t2("merry",15);//有参数实例化
	Teacher t3("james");//,构造函数有默认值20
	//cout << teacher.getName() << " " << teacher.getAge() << endl;
	cout << t2.getName() << " " << t2.getAge() << endl;
	cout << t3.getName() << " " << t3.getAge() << endl;
	//teacher.teach();
	system("pause");
	return 0;
}


```
teacher.h

```c
#include <string>
using namespace std;

class Teacher {
public:
	Teacher();
	Teacher(string name ="hi", int age=20);
	void setName(string name);
	string getName();
	void setAge(int _age);
	int getAge();
	void teach();
private:
	string m_strName;
	int m_iAge;


};
```

teacher.cpp

```c
#include "stdafx.h"
#include "Teacher.h"
#include <iostream>
#include <string>
using namespace std;

Teacher::Teacher() {
	m_strName = "jim";
	m_iAge = 5;
	cout << "Teacher()" << endl;
}

Teacher::Teacher(string name, int age) {
	m_strName = name;
	m_iAge = age;
	cout << "Teacher(string name, int age)" << endl;
}

string Teacher::getName()
{
	return m_strName;
}
void Teacher::setName(string name)
{
	m_strName = name;
}
int Teacher::getAge() {
	return m_iAge;
}
void Teacher::setAge(int _age) {
	m_iAge = _age;
}
void Teacher::teach() {
	cout << "现在上课" << endl;
};
```



####默认构造函数
- 把实例化时不需要传递参数的构造函数称为默认构造函数

```c
Student(){}
Student(string name = "jim");
```

###构造函数初始化列表

```c
class Student
{
public:
	Student():m_strName("jim"),m_iAge(10){}//构造函数初始化列表
private:
	string m_strName;
	int m_iAge;

}
```
####初始化列表的特性

- 初始化列表先于构造函数执行
- 初始化列表只能用于构造函数
- 初始化列表可以同时初始化多个数据成员

####初始化列表的必要性：

```c
class Circle
{
public:
	Circle(){m_dPi=3.14} //错误，给常量赋值
private:
	const double m_dPi;
}
```

###初始化列表代码展示
![要求](http://upload-images.jianshu.io/upload_images/1779926-47df57f0eac5b312.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

main.cpp

```c
#include "stdafx.h"
#include <iostream>
#include <string>
#include "Teacher.h"
using namespace std;

int main(void)
{
	Teacher teacher("merry",12,150);//无参数实例化
	cout << teacher.getName() << " " << teacher.getAge() <<" "<<teacher.getMax()<< endl;
	system("pause");
	return 0;
}
```
teacher.h

```c
#include <string>
using namespace std;

class Teacher {
public:
	Teacher(string name ="hi", int age=1,int m =100);
	void setName(string name);
	string getName();
	void setAge(int _age);
	int getAge();
	int getMax();
	void setMax(int m_iMax);
private:
	string m_strName;
	int m_iAge;
	const int m_iMax;

};
```

teacher.cpp

```c
#include "stdafx.h"
#include "Teacher.h"
#include <iostream>
#include <string>
using namespace std;

Teacher::Teacher(string name, int age ,int m):m_strName(name),m_iAge(age),m_iMax(m)
{
	cout << "Teacher(string name, int age)" << endl;
}

string Teacher::getName()
{
	return m_strName;
}
void Teacher::setName(string name)
{
	m_strName = name;
}
int Teacher::getAge() {
	return m_iAge;
}
void Teacher::setAge(int _age) {
	m_iAge = _age;
}
int Teacher::getMax() {
	return m_iMax;
}
void Teacher::setMax(int m_iMax) {
	m_iMax = m_iMax;
}
```

一个类可以没有默认构造函数，有别的构造函数也可以实例化对象

###c++拷贝构造函数

```c
int main()
{
	Student stu1;
	Student stu2 =stu1;
	Student stu3(stu1);
	return 0;
}
```
上述代码只会执行一次构造函数内的代码。
三次实例化调用了构造函数。但不是我们自己定义的，而是拷贝构造函数

拷贝构造函数：
- 定义格式：类名（const 类名&变量名）

```c
Student(){
	m_strName = "jim";
}
Student(const Student& stu){

}
```

- 如果没有自定义拷贝构造函数则系统自动生成一个默认的拷贝构造函数
- 当采用直接初始化或者复制初始化实例化对象时系统自动调用拷贝构造函数

构造函数总结：

- 无参构造函数：一定为默认构造函数
- 有参构造函数：
		
		- 参数带默认值。如果参数都带默认值，那么也将是默认构造函数
		- 参数无默认值

系统自动生成的函数：

- 普通构造函数
- 拷贝构造函数

一旦我们自行定义，系统就不会再生成了

初始化列表：

- 普通构造函数
- 拷贝构造函数

####拷贝函数代码展示

![要求](http://upload-images.jianshu.io/upload_images/1779926-ede989bee2605202.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

main.cpp

```c
#include "stdafx.h"
#include <iostream>
#include <string>
#include "Teacher.h"
using namespace std;

void test(Teacher t) {

}

int main(void)
{
	Teacher teacher;
	Teacher t2 = teacher;
	Teacher t3(teacher);
	test(teacher); //函数使用已实例化的对象时调用。
	cout << teacher.getName() << " " << teacher.getAge() << endl;
	system("pause");
	return 0;
}


```

teacher.h

```c
#include <string>
using namespace std;

class Teacher {
public:
	Teacher(string name ="hi", int age=1,int m =100);
	Teacher(const Teacher &tea);
	void setName(string name);
	string getName();
	void setAge(int _age);
	int getAge();
private:
	string m_strName;
	int m_iAge;

};
```
teacher.cpp

```c
#include "stdafx.h"
#include "Teacher.h"
#include <iostream>
#include <string>
using namespace std;

Teacher::Teacher(string name, int age ,int m):m_strName(name),m_iAge(age)
{
	cout << "Teacher(string name, int age)" << endl;
}
Teacher::Teacher(const Teacher &tea) {
	cout << "Teacher(const Teacher &tea)" << endl;
}
string Teacher::getName()
{
	return m_strName;
}
void Teacher::setName(string name)
{
	m_strName = name;
}
int Teacher::getAge() {
	return m_iAge;
}
void Teacher::setAge(int _age) {
	m_iAge = _age;
}
```

拷贝构造函数的参数是确定的，不能重载

###析构函数
构造函数是对象来到世界的第一声哭泣
析构函数是对象离开世界的最后一声叹息。

析构函数：
- 定义格式： ~类名() //不加任何的参数

```c
class Student
{
public:
	Student(){cout << "Student" << endl;}
	~Student(){cout << "~Student" << endl;}
}
```

析构函数的价值：
如果我们在定义数据时使用了指针,使用指针指向了堆中分配的内存（new）。在对象销毁时我们要释放这些内存，最好时机，对象销毁临终。

```c
class Student
{
public:
	Student(){m_pName = new char[20];}
	~Student(){delete []m_pName;}
private:
	char *m_pName;
}
```

- 析构函数没有返回值，没有参数也不能重载
- 如果没有自定义的析构函数则系统自动生成
- 析构函数在对象销毁时自动调用

对象的生命历程：

1. 申请内存 
2. 初始化列表 
3. 构造函数 
4. 参与运算 
5. 析构函数 
6. 释放内存

###代码演示说明

![要求](http://upload-images.jianshu.io/upload_images/1779926-2bce5aa076cc5705.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

按回车后可以看到析构函数被调用

main.cpp

```c
#include "stdafx.h"
#include <iostream>
#include <string>
#include "Teacher.h"
using namespace std;

void test(Teacher t) {

}

int main(void)
{
	Teacher t1;
	Teacher t2(t1);
	Teacher *p = new Teacher();
	delete p;
	p = NULL;
	system("pause");
	return 0;
}
```
teacher.h

```c
#include <string>
using namespace std;

class Teacher {
public:
	Teacher(string name ="hi", int age=1,int m =100);
	Teacher(const Teacher &tea);
	~Teacher();
	void setName(string name);
	string getName();
	void setAge(int _age);
	int getAge();
private:
	string m_strName;
	int m_iAge;

};
```
teacher.cpp

```c
#include "stdafx.h"
#include "Teacher.h"
#include <iostream>
#include <string>
using namespace std;

Teacher::Teacher(string name, int age ,int m):m_strName(name),m_iAge(age)
{
	cout << "Teacher(string name, int age)" << endl;
}
Teacher::Teacher(const Teacher &tea) {
	cout << "Teacher(const Teacher &tea)" << endl;
}
Teacher::~Teacher() {
	cout << "~Teacher()" << endl;
}
string Teacher::getName()
{
	return m_strName;
}
void Teacher::setName(string name)
{
	m_strName = name;
}
int Teacher::getAge() {
	return m_iAge;
}
void Teacher::setAge(int _age) {
	m_iAge = _age;
}
```

![要求](http://upload-images.jianshu.io/upload_images/1779926-fec75ede9738b5f2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###总结

- 类：命名空间，成员函数，数据成员
- 数据成员（普通数据成员int, char, char[]）->string；const成员；静态数据成员；对象成员.
- 成员函数(属性封装函数[get,set]);一般功能函数;{特殊函数{构造函数:拷贝构造函数-默认构造函数}-析构函数)
- 成员函数{参数默认值；函数重载；引用；const；}
- 对象实例化{堆中实例化；栈中实例化；}

>- 对象可以是个引用？对象可以用const修饰么？
- 成员函数中如何辨识数据成员。
- 多个对象如何共享数据
- 对象成员初始化

###综合练习：
定义一个Student类，包含名字一个数据成员，定义无参构造函数、有参构造函数、拷贝构造函数、析构函数及对于名字的封装函数，在main函数中实例化Student对象，并访问相关函数，观察运行结果。

```c
#include <iostream>
#include <string>
using namespace std;
/**
 * 定义类：Student
 * 数据成员：m_strName
 * 无参构造函数：Student()
 * 有参构造函数：Student(string _name)
 * 拷贝构造函数：Student(const Student& stu)
 * 析构函数：~Student()
 * 数据成员函数：setName(string _name)、getName()
 */
class Student{
    public:
        Student(){
            m_strName = "";
        };
        Student(string _name){
            m_strName = _name;
        };
        Student(const Student& stu){
            
        };
        ~Student(){

        };
        void setName(string _name){
            m_strName = _name;
        };
        string getName(){
            return m_strName;
        };
    private:
        string m_strName;
};

int main(void)
{
    // 通过new方式实例化对象*stu
    Student *stu = new Student();
    // 更改对象的数据成员为“慕课网”
	stu->setName("慕课网");
    // 打印对象的数据成员
	cout << stu->getName() << endl;
	return 0;
}
```
