##C++远征之继承篇（下）

###多继承与多重继承

多重继承：

![多重继承](http://upload-images.jianshu.io/upload_images/1779926-12ba113de26cb9ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![多重继承-Is-a关系](http://upload-images.jianshu.io/upload_images/1779926-1dc137a718778025.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
class Person
{

};
class Soldier: public Person
{

};
class Infantryman: public Soldier
{

}; 
```

多继承：

![多继承-一个儿子有两个爸爸](http://upload-images.jianshu.io/upload_images/1779926-345b51d74c778d3f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![多继承-isa-但是农民和工人没什么关系](http://upload-images.jianshu.io/upload_images/1779926-46bce9973d30cca0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
class Worker
{

};
class Framer
{

};
class MigrantWorker: public Worker,public Framer
{

};
```

**如果不写，那么系统默认为private继承**

####多重继承代码演示

![多重继承要求](http://upload-images.jianshu.io/upload_images/1779926-64d8b51de8378bfc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

```c
Person -> Soldier -> Infantryman
```

- 孙子实例化时先实例化爷爷，然后实例化爸爸。最后才能实例化孙子。

![孙子-爸爸-爷爷的生死](http://upload-images.jianshu.io/upload_images/1779926-2d0c5217d55399ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 孙子先死。然后爸爸死。最后爷爷死。

附录代码：

```c
#include <iostream>
#include <stdlib.h>
#include "Infantry.h"

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
	Infantry infantry;


	system("pause");
	return 0;
}


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

#include "Person.h"
#include <iostream>
#include <string>
using namespace std;

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


#include "Person.h"
#include <string>
using namespace std;

class  Soldier :public Person
{
public:
	Soldier(string name = "james", int age = 20);
	virtual ~Soldier();
	void work();

protected:
	string m_iAge;

};

#include "Soldier.h"
#include <iostream>
using namespace std;

Soldier::Soldier(string name, int age)
{
	m_strName = name;
	m_iAge = age;
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


#include "Soldier.h"

class Infantry:public Soldier {
public:
	Infantry(string name = "jack", int age = 30);
	~Infantry();
	void attack();
};


#include <iostream>
#include "Infantry.h"

using namespace std;
Infantry::Infantry(string name /* = "jack" */, int age /* = 30 */)
{
	m_strName = name;
	m_iAge = age;
	cout << "Infantry()" << endl;

}
Infantry::~Infantry()
{
	cout << "~Infantry()" << endl;
}
void Infantry::attack() {
	cout << m_iAge << endl;
	cout << m_strName<< endl;
	cout << "Infantry --attack" << endl;

}
```

**步兵 -isa 军人 - isa 人类 **

传递关系。

```c
int main()
{
	Infantry infantry;
	test1(infantry);
	test2(infantry);
	test3(&infantry);
	system("pause");
	return 0;
}
```

![test1，2,  3 传递的is-a](http://upload-images.jianshu.io/upload_images/1779926-5cc668738b2646e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>test1传入的是对象。所以会有临时生成的对象。然后销毁。

####c++多继承

![多继承的要求](http://upload-images.jianshu.io/upload_images/1779926-a46a626894671f46.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

实例化两个父类的顺序与冒号后顺序一致与初始化列表顺序无关。

```c
class  MigrantWorker:public Worker, public Framer
```

- 是按继承的声明顺序来构造超类的 不是按初始化列表的顺序 
- 函数参数默认值最好在声明时设置而不是在定义时。是因为定义出现在调用后 导致编译其无法识别 然后报错

![多继承-实例化与销毁](http://upload-images.jianshu.io/upload_images/1779926-7a5e6f395e20f4c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

完整代码：

```c
#include <string>
using namespace std;

class Worker
{
public:
	Worker(string code ="001");
	virtual ~Worker();
	void carry();
protected:
	string m_strCode;

};


//
//
#include "Worker.h"
#include <iostream>
using namespace std;
Worker::Worker(string code)
{
	m_strCode = code;
	cout << "worker()" << endl;

}

Worker::~Worker()
{
	cout << "~worker" << endl;

}
void Worker::carry()
{
	cout << m_strCode << endl;
	cout << "worker -- carry()" << endl;
}

//
//
#include <string>
using namespace std;

class Framer
{
public:
	Framer(string name = "jack");
	virtual ~Framer();
	void sow();
protected:
	string m_strName;
};


//
//
#include "Farmer.h"
#include <iostream>
using namespace std;

Framer::Framer(string name)

{
	m_strName = name;
	cout << "Framer()" << endl;
}

Framer::~Framer()
{
	cout << "~Framer()" << endl;
}

void Framer::sow()
{
	cout << m_strName << endl;
	cout << "sow()" << endl;
}


//
//
#include <string>
#include "Farmer.h"
#include "Worker.h"
using namespace std;

class  MigrantWorker:public Worker, public Framer//此处顺序决定实例化顺序。
{
public:
	 MigrantWorker(string name,string code);
	~ MigrantWorker();

private:

};


//
//
#include "MigrantWorker.h"
#include <iostream>
using namespace std;

MigrantWorker::MigrantWorker(string name,string code):  Framer(name), Worker(code)
{
	cout <<":MigrantWorker()" << endl;
}

MigrantWorker::~MigrantWorker()
{
	cout << "~MigrantWorker()" << endl;

}

//
//

#include <iostream>
#include "MigrantWorker.h"
#include <stdlib.h>

int main()
{
	MigrantWorker *p = new MigrantWorker("jim","100");
	p->carry();
	p->sow();
	delete p;
	p = NULL;
	system("pause");
	return 0;
}


```

![多继承-实例化与销毁](http://upload-images.jianshu.io/upload_images/1779926-7a5e6f395e20f4c4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 可以用指向子类的指针，调用他两个爸爸的方法。
- 实例化顺序：声明顺序。
- 销毁顺序：正好相反

####巩固练习

>- 定义worker工人类及children儿童类
- worker类中定义数据成员m_strName姓名
- children类中定义成员m_iAge年龄
- 定义ChildLabourer童工类,公有继承工人类和儿童类
- 在main函数中通过new实例化ChildLabourer类的对象，并通过该对象调用worker及children类中的成员函数，最后销毁该对象，体会多继承的继承特性及构造函数及析构函数的执行顺序。

```c
#include <iostream>
#include <stdlib.h>
#include <string>
using namespace std;

/**
 * 定义工人类: Worker
 * 数据成员: m_strName
 * 成员函数: work()
 */
class Worker
{
public:
    Worker(string name)
	{
		m_strName = name;
		cout << "Worker" << endl;
	}
	~Worker()
	{
		cout << "~Worker" << endl;
	}
	void work()
	{
		cout << m_strName << endl;
		cout << "work" << endl;
	}
protected:
	string m_strName;
};

/**
 * 定义儿童类: Children
 * 数据成员: m_iAge
 * 成员函数: play()
 */
class Children
{
public:
	Children(int age)
	{
		m_iAge = age;
		cout << "Children" << endl;
	}	
	~Children()
	{
		cout << "~Children" << endl;
	}	
	void play()
	{
		cout << m_iAge << endl;
		cout << "play" << endl;
	}
protected:
	int m_iAge;
};

/**
 * 定义童工类: ChildLabourer
 * 公有继承工人类和儿童类
 */
class ChildLabourer : public Children,public Worker
{
public:
	ChildLabourer(string name, int age):Children(age),Worker(name)
	{
		cout << "ChildLabourer" << endl;
	}

	~ChildLabourer()
	{
		cout << "~ChildLabourer" << endl;
	}	
};

int main(void)
{
    // 使用new关键字创建童工类对象
    ChildLabourer *c = new ChildLabourer("jim",10);
    // 通过童工对象调用父类的work()和play()方法
    c -> work();
    c -> play();
    // 释放
    delete c;
    c = NULL;

	return 0;
}
```

![不使用虚函数也可以](http://upload-images.jianshu.io/upload_images/1779926-1bf4efa10a5b3646.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>基类不需要定义虚析构函数啊 虚析构函数是在父类指针指向子类对象的时候使用的 这里只是简单的实例化子类对象而已 销毁的时候会执行父类和子类的析构函数的

- 虚析构函数是为了解决基类的指针指向堆中的派生类对象，并用基类的指针删除派生类对象。
- 如果析构函数不是虚函数，则编译器实行静态绑定，在删除基类指针时，只调用基类的析构函数而不调用派生类的析构函数

###c++虚继承（理论）

多继承 & 多重继承的烦恼

- 多继承和多重继承会出现问题呢？

![多继承与多重继续的钻石问题(菱形继承)](http://upload-images.jianshu.io/upload_images/1779926-15d29ed44bcf9230.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![钻石](http://upload-images.jianshu.io/upload_images/1779926-a93f85a90dfd8cae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>如图，假设类a是父类，b类和c类都继承了a类，而d类又继承了b和c，那么由于d类进行了两次多重继承a类，就会出现两份相同的a的数据成员或成员函数，就会出现代码冗余。

人 `->` 农民 | 工人` ->`  农民工

- 如何避免该情况的发生，就可以使用虚继承

```c
class Worker: virtual public Person // 等价于 public virtual Person
{

};
class Framer: virtual public Person // 等价于 public virtual Person
{

};

class MigrantWorker: public Worker,public Framer
{

};
```
使用虚继承。那么农民工类将只有一份继承到的Person成员。

####虚继承编码：
![虚继承要求](http://upload-images.jianshu.io/upload_images/1779926-0082f64158db93cf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们在framer中和worker中都引入了Person.h
当MigrantWorker继承自他们两。会引入两个Person

- 通过宏定义解决重定义：

我们在被公共继承的类中应该这样写：

```c
#ifndef PERSON_H//假如没有定义
#define PERSON_H//定义
//代码
//
#endif //结束符
```

附录完整代码：

```c
#ifndef PERSON_H//假如没有定义
#define PERSON_H//定义

#include <string>
using namespace std;

class Person
{
public:
	Person(string color = "blue");
	virtual ~Person();
	void printColor();
protected:
	string m_strColor;
};

#endif //结束符

//
//
#include <iostream>
#include "Person.h"
using namespace std;


Person::Person(string color)
{
	m_strColor = color;
	cout << "person()" << endl;

}

Person::~Person()
{
	cout << "~Person()" << endl;
}

void Person::printColor()
{
	cout << m_strColor << endl;
	cout << "Person -- printColor" << endl;

}

//
//
#include <string>
using namespace std;
#include "Person.h"
class Worker:public Person
{
public:
	Worker(string code ="001",string color ="red");//希望worker可以传入肤色给person
	virtual ~Worker();
	void carry();
protected:
	string m_strCode;

};

//
//
#include "Worker.h"
#include <iostream>
using namespace std;
Worker::Worker(string code,string color):Person(color)
{
	m_strCode = code;
	cout << "worker()" << endl;

}

Worker::~Worker()
{
	cout << "~worker" << endl;

}
void Worker::carry()
{
	cout << m_strCode << endl;
	cout << "worker -- carry()" << endl;
}

//
//
#include <string>
using namespace std;
#include "Person.h"

class Framer:public Person
{
public:
	Framer(string name = "jack",string color = "blue");

	virtual ~Framer();
	void sow();
protected:
	string m_strName;
};



//
//
#include "Farmer.h"
#include <iostream>
using namespace std;

Framer::Framer(string name,string color):Person(color)

{
	m_strName = name;
	cout << "Framer()" << endl;
}

Framer::~Framer()
{
	cout << "~Framer()" << endl;
}

void Framer::sow()
{
	cout << m_strName << endl;
	cout << "sow()" << endl;
}
//
//
#include <string>
#include "Farmer.h"
#include "Worker.h"
using namespace std;

class  MigrantWorker:public Worker, public Framer//此处顺序决定实例化顺序。
{
public:
	 MigrantWorker(string name,string code,string color);
	~ MigrantWorker();

private:

};

//
//
#include "MigrantWorker.h"
#include <iostream>
using namespace std;

MigrantWorker::MigrantWorker(string name,string code,string color):  Framer(name,color), Worker(code,color)
{
	cout <<":MigrantWorker()" << endl;
}

MigrantWorker::~MigrantWorker()
{
	cout << "~MigrantWorker()" << endl;

}

//
//
#include <iostream>
#include "MigrantWorker.h"
#include <stdlib.h>

int main()
{
	
	system("pause");
	return 0;
}
```

可以正常用过编译说明我们用宏定义成功的解决了菱形问题的重定义报错。

####c++虚继承（编码二）

与上面代码相同。

```c
int main()
{
	MigrantWorker *p = new MigrantWorker("merry", "200", "yellow");
	delete p;
	p = NULL;
	system("pause");
	return 0;
}
```

![菱形对象中生与死](http://upload-images.jianshu.io/upload_images/1779926-50c12ff582b0a926.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

现在MigrantWorker存在两份Person的成员。下面我们来证明。

1. 修改work.cpp和farmer:

```c
Worker::Worker(string code,string color):Person("Worker"+color)
Framer::Framer(string name,string color):Person("framer"+color)
```

2. 通过农民工的指针打印这两份值。
main.cpp

```c
int main()
{
	MigrantWorker *p = new MigrantWorker("merry", "200", "yellow");
	p->Framer::printColor();
	p->Worker::printColor();
	p = NULL;
	system("pause");
	return 0;
}
```

![运行结果：农民工中有两份](http://upload-images.jianshu.io/upload_images/1779926-83e76d01466373ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

使用虚继承，让农民工只有一份。

```c
class Worker: virtual public Person//work是虚基类。
{
};
class Framer:virtual public Person
```

>而C++中 注意 virtual 写在了共有继承前面，意思就是在继承时是真实存在但是不能被访问

![虚继承后孙子类调用父类，都只能拿到爷爷类的值](http://upload-images.jianshu.io/upload_images/1779926-f3cffb728da50107.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####巩固练习

>- 定义Person人类，worker工人类及children儿童类，
- worker类中定义数据成员m_strName姓名，
- children类中定义成员m_iAge年龄，
- worker类及children类均虚公有继承Person类，
- 定义ChildLabourer童工类，公有继承工人类和儿童类，从而形成菱形继承关系
- 在main函数中通过new实例化ChildLabourer类的对象，并通过该对象调用Person，Worker及Children类中的成员函数，最后销毁该对象，掌握多重继承，多继承，虚继承的定义方法。

```c
#include <iostream>
#include <stdlib.h>
#include <string>
using namespace std;

/**
 * 定义人类: Person
 */
class Person
{
public:
    Person()
	{
		cout << "Person" << endl;
	}
	~Person()
	{
		cout << "~Person" << endl;
	}
	void eat()
	{
		cout << "eat" << endl;
	}

};

/**
 * 定义工人类: Worker
 * 虚继承人类
 */
class Worker : virtual public Person
{
public:
	Worker(string name)
	{
		m_strName = name;
		cout << "Worker" << endl;
	}
	~Worker()
	{
		cout << "~Worker" << endl;
	}
	void work()
	{
		cout << m_strName << endl;
		cout << "work" << endl;
	}
protected:
	string m_strName;
};

/**
 * 定义儿童类:Children
 * 虚继承人类
 */
class Children : virtual public Person
{
public:
	Children(int age)
	{
		m_iAge = age;
		cout << "Children" << endl;
	}	
	~Children()
	{
		cout << "~Children" << endl;
	}	
	void play()
	{
		cout << m_iAge << endl;
		cout << "play" << endl;
	}
protected:
	int m_iAge;
};

/**
 * 定义童工类：ChildLabourer
 * 公有继承工人类和儿童类
 */
class ChildLabourer:public Children,public Worker
{
public:
	ChildLabourer(string name, int age):Worker(name),Children(age)
	{
		cout << "ChildLabourer" << endl;
	}

	~ChildLabourer()
	{
		cout << "~ChildLabourer" << endl;
	}	
};

int main(void)
{
    // 用new关键字实例化童工类对象
	ChildLabourer *p = new ChildLabourer("jim",15);
    // 调用童工类对象各方法。
	p->eat();
	p->work();
	p->play();
	delete p;
	p = NULL;

	return 0;
}
```

运行结果：

![运行结果](http://upload-images.jianshu.io/upload_images/1779926-c01d752f43deb415.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)









