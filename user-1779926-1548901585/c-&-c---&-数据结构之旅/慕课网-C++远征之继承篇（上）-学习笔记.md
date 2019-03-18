##C++远征之继承篇

###开篇介绍

起航->离港->封装->继承

- 为什么要用继承？
- 为什么要有继承？
- 基类 <----> 派生类
- 公有继承，保护继承，私有继承
- IS-a & Has a
- 多重继承 & 多继承 & 虚继承

###为什么继承？
儿子继承父亲财产

- 生活中继承 不等于 c++中的继承

为什么要有继承？

例子：

人类：

```c
class Person
{
public:
	void eat();
	string m_strName;
	int m_iAge;
}
```

工人(人类的一种)：

```c
class Worker
{
public:
	void eat();//
	void work();
	string m_strName;//
	int m_iAge;//
	int m_iSalary;

}
```

代码重用。超集子集

```c
class Worker: public Person
{
public:
	void work();
	int m_iSalary;

}
```

- 此时工人类是人类的派生类。人类是工人类的基类。
- 工人类（子类） & 人类（父类）


![基类-派生 & 父类-子类](http://upload-images.jianshu.io/upload_images/1779926-8007bbc7c694b397.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

内存中的对象：

![内存中的父类子类](http://upload-images.jianshu.io/upload_images/1779926-bf879716a2ebab71.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####代码演示
要求：

- 实例化时先实例化父类，再实例化子类。而析构函数正好相反。
- 子类已经同时具有父类的数据成员和成员函数

![要求](http://upload-images.jianshu.io/upload_images/1779926-c6cb2ed611720a6d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Person.h:

```c
#include <string>
using namespace std;

class  Person
{
public:
	 Person();
	~ Person();
	void eat();
	string m_strName;
		int m_iAge;
private:

};
```
Person.cpp:

```c
#include "Person.h"
#include <iostream>

using namespace std;

Person::Person()
{
	cout << "Person()" << endl;

}

Person::~Person()
{
	cout << "~Person()" << endl;

}

void Person::eat() {
	cout << "eat()" << endl;
}

```

worker.h:

```c
#include "Person.h"

class Worker:public Person
{
public:
	Worker();
	~Worker();
	void work();
	int m_iSalary;
};
```

worker.cpp:

```c
#include "Worker.h"
#include <iostream>

using namespace std;

Worker::Worker()
{
	cout << "Worker()" << endl;

}
Worker::~Worker()
{
	cout << "~Worker()" << endl;

}

void Worker::work()
{
	cout << "Work" << endl;
}
```

main.cpp:

```c
#include "Worker.h"
#include <stdlib.h>
#include <iostream>

using namespace std;

int main(void)
{
	Worker *p = new Worker();
	p->m_strName = "Jim";
	p->m_iAge = 10;
	p->eat();
	p->m_iSalary = 1000;
	p->work();
	delete p;
	p = NULL;
	system("pause");
	return 0;
}
```


![运行结果](http://upload-images.jianshu.io/upload_images/1779926-3e7ebc5537275c99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###继承方式

- 公有继承: `class A:public B`
- 保护继承: `class A:protected B`
- 私有继承: `class A:private B`

####公有继承

```c
class  Person
{
public:
	void eat();
	string m_strName;
		int m_iAge;
};
```

```c
class Worker:public Person
{
public:
	void eat();//
	void work();
	int m_iAge;//
	string m_strName;//
	int m_iSalary;
};
```

private和protected在继承时的区别：

- 子类通过protect继承了父类的时候，子类可以通过自己来访问父类的数据成员。
- 子类通过private继承了父类的时候，子类不能通过自己来访问父类的数据成员，父类的数据成员只有通过自己才能访问。

```c
class  Person
{
public:
	void eat();
protected:
	int m_iAge;
private:
	string m_strName;
	
};
```

```c
int main()
{
	Person person;
	person.eat(); //正确
	person.m_iAge = 20; //禁止访问
	person.m_strName = "jim"; //禁止访问
	return 0;
}
```

```c
void Person::eat()
{
	m_strName = "jim";
	m_iAge = 10;
}
```
实现成员函数时。我们的私有保护数据成员也是可以正常访问的。
但当protected 遇到 继承

```c
class  Person
{
public:
	void eat();
protected:
	int m_iAge;
	string m_strName;
	
};

class Worker:public Person
{
public:
	void eat();//
	void work(){m_iAge = 20;};//继承下来之后可以通过work访问到age
protected:
	int m_iAge;//
	string m_strName;//
	int m_iSalary;
};
```

public 的继承到public。 protected的继承到protected

```c
class  Person
{
public:
	void eat();
private:
	int m_iAge;
	string m_strName;
	
};

class Worker:public Person
{
public:
	void eat();//
	void work(){m_iAge = 20;};//继承下来之后可以通过work访问到age
private:
	int m_iSalary;
};
```

public的继承到public。父类中的private被继承到了不可见位置。而不是private。
所以，此时通过work操作m_iage会出错。

![公有继承](http://upload-images.jianshu.io/upload_images/1779926-8700ba4330d2aef6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####公有继承代码示例

要求：

![要求](http://upload-images.jianshu.io/upload_images/1779926-3ec84146770eb41b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

person.h:

```c
#include <string>
using namespace std;

class  Person
{
public:
	 Person();
	~ Person();
	void eat();
protected:
	string m_strName;
private:
	int m_iAge;


};
```

```c
#include "Person.h"
#include <iostream>

using namespace std;

Person::Person()
{
	cout << "Person()" << endl;

}

Person::~Person()
{
	cout << "~Person()" << endl;

}

void Person::eat() {
	m_strName = "Jim";
		m_iAge = 20;
	cout << "eat()" << endl;
}

```
main.cpp
```c
//#include "Worker.h"
#include <stdlib.h>
#include <iostream>
#include "Person.h"

using namespace std;

int main(void)
{/*
	Worker worker;
	worker.m_strName = "Jim";
	worker.m_iAge = 10;
	worker.eat();
	worker.m_iSalary = 1000;
	worker.work();*/

	Person person;
	//person.eat();
	//person.m_strName = "merry"; //无法访问 protected 成员
	//person.m_iAge = 20; //无法访问 private 成员
	system("pause");
	return 0;
}
```

>- 类本身的public成员函数，函数内部可以访问到类自身的保护以及私有成员。
- 而类内受保护的私有成员，保护成员。无法在类外直接被访问。

公有继承之后父类的私有数据成员被子类继承却无法正常使用。

子类中的protected成员父类也可以正常访问。被放在了父类protected。

```c
class  Person
{
public:
	 Person();
	~ Person();
	void eat();
protected:
	string m_strName;
	int m_iAge;
};
```

```c
void Worker::work()
{
	m_iAge = 10;
	m_strName = "Jim";
	cout << "Work" << endl;
}
```

```c
int main(void)
{
	Worker worker;
	worker.work();

	system("pause");
	return 0;
}
```

结果：正常运行。

- 父类的private数据成员父类是否可以正常访问：不可以

将上面代码中person下的protected改为private

则会报错：

```
Person::m_iAge”: 无法访问 private 成员(在“Person”类中声明)
```

####c++保护继承，私有继承

- 公有继承: `class A:public B`
- 保护继承: `class A:protected B`
- 私有继承: `class A:private B`

![公有继承](http://upload-images.jianshu.io/upload_images/1779926-8700ba4330d2aef6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![保护继承](http://upload-images.jianshu.io/upload_images/1779926-0142a008960271e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![私有继承](http://upload-images.jianshu.io/upload_images/1779926-5efbe54a59063d3f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

例子：

```c
class Line{
public:
    Line(int x1,int y1, int x2,int y2);
private:
    Coordinate m_coorA;//只能访问到公有数据成员和公有成员函数
    Coordinate m_coorB;
}
```
- 线段和坐标的关系：has -a（包含）
- 私有继承（包含） 因为子类对象包含父类：只能访问父类当中公有数据成员和成员函数。

####保护与私有继承代码演示
要求：

![要求](http://upload-images.jianshu.io/upload_images/1779926-0cb44f761f8bcd14.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

protected继承：
- public成员可以一直protected继承下去，被继承后属于protected，不可以被对象直接访问；
- protected成员可以一直protected继承下去，被继承后属于protected，不可以被对象直接访问；
- private成员不能被protected继承。


公有继承代码：

person:

```c
#include <string>
using namespace std;

class  Person
{
public:
	 Person();
	 void play();

protected:
	string m_strName;
};

#include <iostream>
#include "Person.h"
using namespace std;

Person::Person()
{
	m_strName = "merry";
}

void Person::play() {
	cout << "person - play" << endl;
	cout << m_strName << endl;
}
```

Soldier:

```c
#include "Person.h"

class  Soldier:public Person
{
public:
	 Soldier();
	 void work();

private:
	int m_iAge;
};

#include "Soldier.h"
#include <iostream>
using namespace std;

Soldier::Soldier()
{
}

void Soldier::work()
{
	m_strName = "JIm";
	m_iAge = 20;
	cout << m_strName << endl;
	cout << m_iAge << endl;
	cout << "soldier - work" << endl;
}
```

Infantry:

```c
#include "Soldier.h"

class Infantry:public Soldier {
public:
	void attack();
};

#include <iostream>
#include "Infantry.h"

using namespace std;

void Infantry::attack() {
	cout << "Infantry --attack" << endl;

}
```
main.cpp:

```c
#include "Soldier.h"
#include <stdlib.h>
#include <iostream>

int main()
{
	Soldier soldier;
	soldier.work();//work会间接的访问到基类person中的m_strname
	//进行了公有继承，父类的protected数据成员会被继承到子类的protected下面
	//父类的public也会继承到子类的public下面
	soldier.play();//调用父类的成员函数
	system("pause");
	return 0;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-1802dc3de700099b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

改用protected：

- 如果slodier保护继承了person，那么person下的public和protected数据成员成员函数都会被继承到solider的protect中
- 此时通过一个类（Infantry）来公共继承soldier则可以在它自身的成员函数attack中访问到person的数据成员以及成员函数。


完整代码：

```c
#include <string>
using namespace std;

class  Person
{
public:
	 Person();
	 void play();

protected:
	string m_strName;
};

#include <iostream>
#include "Person.h"
using namespace std;

Person::Person()
{
	m_strName = "merry";
}

void Person::play() {
	cout << "person - play" << endl;
	cout << m_strName << endl;
}


#include "Person.h"

class  Soldier:protected Person
{
public:
	 Soldier();
	 void work();
//通过子类的对象只能访问子类自己的public下面的成员。
//不能访问到基类的public成员了，因为基类的被放到protected下了

protected:
	int m_iAge;
};


#include "Soldier.h"
#include <iostream>
using namespace std;

Soldier::Soldier()
{
}

void Soldier::work()
{
	m_strName = "JIm";
	m_iAge = 20;
	cout << m_strName << endl;
	cout << m_iAge << endl;
	cout << "soldier - work" << endl;
}


#include "Soldier.h"

class Infantry:public Soldier {
public:
	void attack();
};


#include <iostream>
#include "Infantry.h"

using namespace std;

void Infantry::attack() {
	m_strName = "Jim";
	cout << m_strName<< endl;
	cout << "Infantry --attack" << endl;

}


//#include "Soldier.h"
#include <stdlib.h>
#include <iostream>
#include "Infantry.h"
int main()
{
	//Soldier soldier;
	//soldier.work();//work会间接的访问到基类person中的m_strname
	//进行了公有继承，父类的protected数据成员会被继承到子类的protected下面
	//父类的public也会继承到子类的public下面
	//soldier.play();//调用父类的成员函数
	//“Person::play”不可访问，因为“Soldier”使用“protected”从“Person”继承
	
	Infantry soldier;
	soldier.attack();

	system("pause");
	return 0;

}
```

private继承方式：

```c
#include <string>
using namespace std;

class  Person
{
public:
	 Person();
	 void play();

protected:
	string m_strName;
};


#include <iostream>
#include "Person.h"
using namespace std;

Person::Person()
{
	m_strName = "merry";
}

void Person::play() {
	cout << "person - play" << endl;
	cout << m_strName << endl;
}


#include "Person.h"

class  Soldier:private Person
{
public:
	 Soldier();
	 void work();
//通过子类的对象只能访问子类自己的public下面的成员。
//不能访问到基类的public成员了，因为基类的被放到protected下了

protected:
	int m_iAge;
private:
	//private继承，此时person的protect，public被继承到了子类的private
	//此时soldier可以访问到。但是继承自它的infantry就访问不到了
};


#include "Soldier.h"
#include <iostream>
using namespace std;

Soldier::Soldier()
{
}

void Soldier::work()
{
	m_strName = "JIm";
	m_iAge = 20;
	cout << m_strName << endl;
	cout << m_iAge << endl;
	cout << "soldier - work" << endl;
}


#include "Soldier.h"

class Infantry:public Soldier {
public:
	void attack();
};


#include <iostream>
#include "Infantry.h"

using namespace std;

void Infantry::attack() {
	//“Person::m_strName”: 无法访问 无法访问的 成员(在“Person”类中声明)
	//m_strName = "Jim";
	//cout << m_strName<< endl;
	cout << "Infantry --attack" << endl;

}


//#include "Soldier.h"
#include <stdlib.h>
#include <iostream>
#include "Infantry.h"
int main()
{
	//Soldier soldier;
	//soldier.work();//work会间接的访问到基类person中的m_strname
	//进行了公有继承，父类的protected数据成员会被继承到子类的protected下面
	//父类的public也会继承到子类的public下面
	//soldier.play();//调用父类的成员函数
	//“Person::play”不可访问，因为“Soldier”使用“protected”从“Person”继承
	
	Infantry soldier;
	soldier.attack();

	system("pause");
	return 0;

}
```

- B类从A类派生，那么B类中含有A类的所有数据成员（私有的也被继承过来了，只不过不能用） 
- B类从A类公共派生，那么可以在B类中直接使用A的公共及保护限定符的数据成员。不能使用私有成员
- B类从A类公共派生，那么A类的私有成员函数不能被B类继承并使用。
- B类从A类私有派生，那么A类的公共成员函数成为B类的私有成员函数。

####巩固练习

>定义两个类，人类中含有数据成员姓名（m_strName）及成员函数eat()
士兵类从人类派生，含有数据成员编号（m_strCode)及成员函数attack()
在main函数通过对数据的访问，体会公有继承的语法特点。

```c
#include <iostream>
#include <stdlib.h>
#include <string>
using namespace std;

/**
 * 定义人的类: Person
 * 数据成员姓名: m_strName
 * 成员函数: eat()
 */
class Person
{
public:
    string m_strName;
	void eat()
	{
		cout << "eat" << endl;
	}
};

/**
 * 定义士兵类: Soldier
 * 士兵类公有继承人类: public
 * 数据成员编号: m_strCode
 * 成员函数: attack()
 */
class Soldier:public Person
{
public:
	string m_strCode;
	void attack()
	{
		cout << "fire!!!" << endl;
	}
};

int main(void)
{
    // 创建Soldier对象
	Soldier soldier;
    // 给对象属性赋值
    soldier.m_strName = "Jim";
	soldier.m_strCode = "592";
    // 打印对象属性值
	cout << soldier.m_strName << endl;
	cout << soldier.m_strCode << endl;
    // 调用对象方法
	soldier.attack();
	soldier.eat();

	return 0;
}
```

###c++隐藏

覆盖 <-> 隐藏 很容易混淆

####什么是隐藏？

![隐藏](http://upload-images.jianshu.io/upload_images/1779926-296ab910c1d094ca.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 父子两代，都有同名函数。子类会将父类的函数隐藏。
- 表现在。实例化b对象时，只能直接访问到b中的abc方法。
- 但是实际上父类的abc方法只是隐藏了起来，通过特殊手段还可以访问到。
- 同名数据成员和成员函数都具有隐藏性质

**父子关系 & 成员同名 & 隐藏**

代码：

```c
class Person
{
public:
	void play();
protected:
	string m_strName;
};

class Soldier:public Person
{
public:
	void play();
	void work();
protected:
	int m_iCode;
}
```
调用示例代码：

```c
int main()
{
	Soldier soldier;
	soldier.play();//soldier自己的play
	soldier.Person::play();//可以调用到父类人的play

	return 0;

}
```

数据成员同名：

![数据成员同名](http://upload-images.jianshu.io/upload_images/1779926-a697367d6c91f7f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
void Soldier::work()
{
	code = 1234;
	Person::code = "5678";//访问到的是父类的数据成员
}
```

可以通过命名方法避免重名

####隐藏编码实例

![要求](http://upload-images.jianshu.io/upload_images/1779926-8c57fcd3ad9cb386.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- `""`在程序目录下找
- `<>`在程序默认库查找- 右键打开文档可以打开
- `#include<string>`时，需要配套使用`using namespace std;`才可正常使用string,否则会出错,也就是string类型应该是在std命名空间。
- 继承只有一个冒号。而类的方法。两个。
- 使用string类型必须包含<string>
- 声明类的最后要加分号.

程序代码：

```c
#include <string>
using namespace std;

class Person
{
public:
	Person();
	void play();
protected:
	string m_strName;
};

//
#include "Person.h"
#include <iostream>
using namespace std;
#include "stdafx.h"
Person::Person()
{
	m_strName = "merry";

}

void Person::play()
{
	cout << "person - play()" << endl;
	cout << m_strName << endl;
}

//
#include "Person.h"

class  Soldier:public Person
{
public:
	 Soldier();
	 void play();
	 void work();

protected:


};


//
#include "Soldier.h"
#include <iostream>
using namespace std;
#include "stdafx.h"

Soldier::Soldier()
{

}
void Soldier::play() {
	cout << "soldier - play()" << endl;

}

void Soldier::work() {
	cout << "soldier - work()" << endl;
}

//main.cpp:
#include <iostream>
#include <stdlib.h>
#include "Soldier.h"
#include "stdafx.h"
int main()
{
	Soldier soldier;
	soldier.play();
	soldier.work();
	system("pause");
    return 0;
}
```

运行结果：

>play和work都调用的是soldier的。

####隐藏编码二

如果想要打印Person的。则需要`soldier.Person::play();`

>可以调用到person下的play。

当前不仅同名而且参数相同。如果参数不同，还会隐藏？

>是的即使参数不同，也不会形成重载，只是隐藏。

```c
int main()
{
	Soldier soldier;
	soldier.play(7);
	soldier.work();
	soldier.play();//虽然与父类的参数要求一致。但当前父类方法被隐藏无法调用到
	//soldier.Person::play();
	system("pause");
    return 0;
}

void Person::play()
{
	cout << "person - play()" << endl;
	cout << m_strName << endl;
}

void Soldier::play(int x) {
	cout << "soldier - play()" << endl;

}
```

报错：

```
error C2660: “Soldier::play”: 函数不接受 0 个参数

```

- 说明即使参数不同。父类和子类的同名函数也不形成重载。而是隐藏。

####数据成员重名

```c
#include <string>
using namespace std;

class Person
{
public:
	Person();
	void play();
protected:
	string m_strName;
};

//

#include "Person.h"
#include <iostream>
using namespace std;
#include "stdafx.h"
Person::Person()
{
	m_strName = "merry";

}

void Person::play()
{
	cout << "person - play()" << endl;
	cout << m_strName << endl;
}

//
//
#include "Person.h"

class  Soldier:public Person
{
public:
	 Soldier();
	 void play(int x);
	 void work();

protected:
	string m_strName;

};

//
//
#include "Soldier.h"
#include <iostream>
using namespace std;
#include "stdafx.h"

Soldier::Soldier()
{

}
void Soldier::play(int x) {
	cout << m_strName << endl;//打印出的是soldier下的
	cout << Person::m_strName << endl;//打印父类的
	cout << "soldier - play()" << endl;

}

void Soldier::work() {
	m_strName = "solider"; //只能赋值给soldier下的m_strname
	Person::m_strName = "Person";
	cout << "soldier - work()" << endl;
}

//
//
#include <iostream>
#include <stdlib.h>
#include "Soldier.h"
#include "stdafx.h"
int main()
{
	Soldier soldier;
	soldier.work();
	soldier.play(7);
	soldier.Person::play();
	system("pause");
    return 0;
}
```

###isA

隐形眼镜也是眼镜 - `Is-a`

- 眼镜： 基类
- 隐形眼镜： 派生类

![工人士兵都是人的对象](http://upload-images.jianshu.io/upload_images/1779926-cdf8f0a6c71ff2b3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
int main()
{
	Soldier s1;
	Person p1 = s1;//用s1区实例化p1.这样做在语法上是正确的。士兵也是人。

	Person *p2 = &s1; //正确

	s1 = p1;//人是士兵，错误。
	Soldier *s2 = &p1;//错误
	return 0;
}
```

将基类的指针或者引用。作为函数参数来使他可以接受子类的对象也可以传入基类的对象。

![基类作为参数](http://upload-images.jianshu.io/upload_images/1779926-9fcef333330a5dbc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>- fun1和fun2都可以传入Person 或 Soldier的对象。
- fun1要求传入的参数是指针，所以传入 `&p1`对象p1的地址。
- 而fun2要求传入的是对象p。所以可以直接传入

- 基类指针指向派生类对象：   Person　*p = &soldier; 
- 派生类对象初始化基类对象： Person p1 = soldier;

存储结构：

![子类父类内存](http://upload-images.jianshu.io/upload_images/1779926-6016a90d7ed19384.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>- 子类中有父类继承下来的数据成员.
- 当通过子类初始化父类时。会将继承下来的数据成员复制。其他子类自有的截断。
- 父类指针指向子类对象。父类也只能访问到自己遗传下去的。无法访问到子类自有的。

#### Is-a 编码

![要求](http://upload-images.jianshu.io/upload_images/1779926-0311fb860913f3d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

main.cpp:

```c
#include <iostream>
#include <stdlib.h>
#include "Soldier.h"
#include "stdafx.h"
int main()
{
	//Soldier soldier;
	//Person p = soldier;//使p中那么改变

	//Person p;
	//p = soldier;

	//无论是soldier初始化p。还是直接赋值。都使得p的name值为子类的name。
	//Person *p = &soldier;
	//p->play();
	//无论是对象赋值，还是指针指向。都使得p的name值为子类的name。
	//p->work();// error C2039: “work”: 不是“Person”的成员
	
	//如果通过父类的指针指向子类的对象。那么销毁时，执行子类还是父类的析构函数
	Person *p = new Soldier;

	p->play();

	delete p;
	p = NULL;
	//只执行了父类的析构函数。子类的没有执行。要用到虚的析构函数
	//当存在继承关系。父类的指针指向堆中的子类的对象。并且还想使用父类的指针把子类的也释放掉
	
	system("pause");
    return 0;
}

//实例化步骤：1. person() 2. soldier 3. person-play 4. james

```
person.h:

```c
#include <string>
using namespace std;

class Person
{
public:
	Person(string name = "jim");
	virtual ~Person();// 虚析构函数，可继承。soldier内的也会是虚的。
	void play();
protected:
	string m_strName;
};
```

Person.cpp:

```c
#include "Person.h"
#include <iostream>
#include <string>
using namespace std;
#include "stdafx.h"
Person::Person(string name)
{
	m_strName = name;
	cout << "person()" << endl;

}
Person::~Person()
{
	cout << "~person()" << endl;
}

void Person::play()
{
	cout << "person - play()" << endl;
	cout << m_strName << endl;
}
```

soldier.h:

```c
#include "Person.h"
#include <string>
using namespace std;

class  Soldier:public Person
{
public:
	 Soldier(string name = "james",int age =20);
	 virtual ~Soldier();
	 void work();

protected:
	string m_iAge;

};
```

slodier.cpp:

```c
#include "Soldier.h"
#include <iostream>
using namespace std;
#include "stdafx.h"

Soldier::Soldier(string name,int age)
{
	m_strName = name;
	m_iAge  = age;
	cout << "Soldier()" << endl;
 }

Soldier::~Soldier() {
	cout << "~Soldier()" << endl;
}
void Soldier::work() {
	cout << m_strName << endl;
	cout << m_iAge << endl;
	cout << "Soldier -- work" << endl;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-d9139d654ab0b326.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 虚析构函数是为了解决基类的指针指向堆中的派生类对象，并用基类的指针删除派生类对象。
- 如果析构函数不是虚函数，则编译器实行静态绑定，在删除基类指针时，只调用基类的析构函数而不调用派生类的析构函数。

###is-A 编码二

![要求](http://upload-images.jianshu.io/upload_images/1779926-0311fb860913f3d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

情况1：

```c
#include <iostream>
#include <stdlib.h>
#include "Soldier.h"
#include "stdafx.h"

void test1(Person p)
{
	p.play();

}
void test2(Person &p)
{
	p.play();

}
void test3(Person *p)
{
	p->play();

}

int main()
{
	Person p;
	Soldier s;
	test1(p);
	test1(s);

	system("pause");
    return 0;
}
```

运行结果：

![两次析构函数](http://upload-images.jianshu.io/upload_images/1779926-b3ecc2960888a243.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>两次析构函数是在两个test1执行完之后自动执行的。因为此时传入一个临时变量p。用完就要销毁掉。

```c
	test2(p);
	test2(s);
```

运行结果：

![test2:引用中间没有中间变量的销毁](http://upload-images.jianshu.io/upload_images/1779926-dca040b1d8a0fd8d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>因为传入的是引用。所以里面调用的仍是传入的对象本身。没有中间变量。

```c
	test3(&p);
	test3(&s);
```

>与test2实验结果完全一致。p分别调用基类和派生类的play

结论：test2 & test3 不会产生临时变量，效率更高。

####巩固练习

>定义两个类，基类是人类，定义数据成员姓名（name），及成员函数void attack()。
士兵类从人类派生，定义与人类同名的数据成员姓名（name）和成员函数void attack()。
通过对同名数据成员及成员函数的访问理解成员隐藏的概念及访问数据的方法。

```c
#include <iostream>
#include <stdlib.h>
#include <string>
using namespace std;

/**
 * 定义人类: Person
 * 数据成员: m_strName
 * 成员函数: attack()
 */
class Person
{
public:
    string m_strName;
	void attack()
	{
		cout << "attack" << endl;
	}
};

/**
 * 定义士兵类: Soldier
 * 士兵类公有继承人类
 * 数据成员: m_strName
 * 成员函数: attack()
 */
class Soldier:public Person
{
public:
	string m_strName;
	void attack()
	{
		cout << "fire!!!" << endl;
	}
};

int main(void)
{
    // 实例士兵对象
	Soldier soldier;
    // 向士兵属性赋值"tomato"
    soldier.m_strName = "tomato";
    // 通过士兵对象向人类属性赋值"Jim"
	soldier.Person::m_strName = "Jim";
    // 打印士兵对象的属性值
	cout << soldier.m_strName << endl;
    // 通过士兵对象打印人类属性值
	cout << soldier.Person::m_strName << endl;
    // 调用士兵对象方法
	soldier.attack();
    // 通过士兵对象调用人类方法
    soldier.Person::attack();

	return 0;
}
```
