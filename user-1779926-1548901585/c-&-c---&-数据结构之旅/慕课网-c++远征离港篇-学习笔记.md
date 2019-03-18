##c++远征离港篇
###离港总动员
- 引用VS指针、
- `#define VS const` 
- 函数默认值&函数重载
- 内存管理(头疼)
- 封装 继承 多态

###c++语言引用：
引用就是变量的别名

####基本数据类型的引用。

```c++
#include "stdafx.h"
#include <stdlib.h>
#include <iostream>
using namespace std;
int main(void)
{
	int a = 3;
	int &b = a;//引用必须初始化

	b = 10;
	cout << a << endl;

	system("pause");
	return 0;
}
```
为a起别名b：对别名做的操作就是对实际做了操作

####结构体类型的引用
使用别名对于结构体做操作

```c++
#include "stdafx.h"
#include <stdlib.h>
#include <iostream>
using namespace std;
typedef struct
{
	int x;
	int y;
}Coor;
int main(void)
{
	Coor c1;
	Coor &c = c1;
	c.x = 10;
	c.y = 20;
	cout << c1.x << endl << c1.y << endl;

	system("pause");
	return 0;
}
```
####指针类型的引用

类型 *&指针引用名 = 指针

```c++
#include "stdafx.h"
#include <iostream>
using namespace std;
int main(void)
{
	int a = 10;
	int *p = &a;
	int *&q = p;
	*q = 20;
	cout << a << endl;
	system("pause");
	return 0;
}
```
- int a = 10;   // 给a分配一个内存逻辑地址0x100001, 这个地址存放了值10;
- int *p = &a;  //创建指针变量p指向a, 给p分配地址0x100002, 这个地址存放的值是"0x100001"(a的逻辑地址值);
- int *&q = p;  //（给p起别名q）创建变量q, 给q分配地址也是0x100002, 因此这个地址存放的值还是a的逻辑地址值;
- *q = 20;  //（对q做操作）访问存放在q变量地址下的值, 获得了a的地址值, 再访问一下a的地址值, 修改上面的内容为20;

###引用作为函数参数

```c
void fun(int *a, int *b)
{
	int c =0;
	c =*a;
	*a =*b;
	*b =c;
}
int main(int argc, char const *argv[])
{
	int x =10;
	int y =20;
	fun(&x,&y);
	return 0;
}
```

c++中引用实现：

```c
void fun(int &a, int &b)
{
int c =0;
c =a;
a =b;
b =c;
}

int x=10,y=20;
fun(x,y)
```
a是x的别名。b是y的别名。

###代码示例：

```c
#include "stdafx.h"
#include <iostream>
#include <stdlib.h>
using namespace std;
int main(void)
{
	int a = 10;
	int &b = a;

	b = 20;
	cout << a << endl;

	a = 30;
	cout << b << endl;
	system("pause");
	return 0;
}
```
结构体引用示例

```c
#include "stdafx.h"
#include <stdlib.h>
#include <iostream>
using namespace std;
typedef struct
{
	int x;
	int y;
}Coord;
int main(void)
{
	Coord c;
	Coord &c1 = c;
	c1.x = 10;
	c1.y = 20;
	cout << c.x << endl << c.y << endl;

	system("pause");
	return 0;
}
```

指针引用示例：

```c
#include "stdafx.h"
#include <stdlib.h>
#include <iostream>
using namespace std;

int main(void)
{
	int a = 3;
	int *p = &a;
	int *&q = p;
	*q = 20;

	cout << a << endl;
	system("pause");
	return 0;
}
```

函数参数引用示例：

```c
#include "stdafx.h"
#include <stdlib.h>
#include <iostream>
using namespace std;
void fun(int &a, int &b);
int main(void)
{
	int x = 10;
	int y = 20;

	cout << x<< endl;
	cout << y << endl;
	fun(x, y);
	cout << x << endl;
	cout << y << endl;
	system("pause");
	return 0;
}

void fun(int & a, int & b)
{
	int c = 0;
	c = a;
	a = b;
	b = c;
}

```

###单元巩固
定义一个引用y，y是x的引用，然后打印x和y的值。将y的值更改之后再次打印，x和y的值。

```c
#include "stdafx.h"
#include <iostream>
using namespace std;
int main(void)
{
	int x = 3;
	//定义引用，y是x的引用
	int &y = x;
	//打印x和y的值
	printf("%d\n", x);
	printf("%d\n", y);
	//修改y的值
	y = 20;
	//再次打印x和y的值
	printf("%d,%d\n", x, y);
	system("pause");
	return 0;
}
```

###const 与基本数据类型
   int x =3;
   变量名x->存储地址&x->存储内容3

   const int x=3;  //此时的x为常量
   常量名x->存储地址&x->存储内容3（不可改变）

###const与指针类型
	
- `const int *p=NULL;`等价于`int const *p=NULL`     
  `int *const p=NULL`与前两种有区别。
- `const int * const p = NULL;` 完全等价于 `int const * const p = NULL;`

const 修饰*p;

```c
int x =3;
const int *p = &x;
// p = &y;正确
//*p = 4;错误
```

| 变量名     | 存储地址   |  存储内容  |
| --------   | -----:  | :----:  |
| x   | &x|   3     |
| p   | &p|   &x     |

const 修饰p;

```c
int x =3;
int *const p = &x;
// p = &y;错误
```


| 变量名     | 存储地址   |  存储内容  |
| --------   | -----:  | :----:  |
| x   | &x|   3     |
| p   | &p|   &x （不可改变）    |


const 修饰p;

```c
const int x =3;
const int *const p = &x;
// p = &y;错误
// *p =4; 错误
```


| 变量名     | 存储地址   |  存储内容  |
| --------   | -----:  | :----:  |
| x   | &x|   3  （不可改变）   |
| p   | &p|   &x （不可改变）    |

#const 与引用

```c
int x =3; 
const int &y =x;
// x=10;正确
// y=20，错误
```

| 变量名     | 存储地址   |  存储内容  |
| --------   | -----:  | :----:  |
| x   | &x|   3    |

###const 实例

```c
//错误
const int x =3;
x =5;

//错误
int x =3;
const int y =x ;
y = 5;

//错误
int x =3;
const int *y =&x;
*y = 5;

//错误
int x =3,z=4;
int * const y = &x;
y = &z;

//错误
const int x =3;
const int &y =x;
y =5;

//错误:指针会存在改变常量的风险。
const int x =3;
int *y = &x;

//正确。x拥有读写，y只可读。
int x =3;
const int *y =&x;


```

###const代码示例

```c
#include "stdafx.h"
#include <iostream>
using namespace std;
int main(void)
{
	const int x = 3;
	x = 5;

	system("pause");
	return 0;
}
```
>1> error C3892: “x”: 不能给常量赋值

- 在编译的时候要检查语法错误,const定义的有数据类型
- 而#define定义的没有数据类型，是宏定义在编译时不再检查语法错误
- 推荐用const来定义常量

```c
#include "stdafx.h"
#include <iostream>
using namespace std;
int main(void)
{
	int x = 3;
	int const *p = &x;// const int *p = &x等价

	//*p = 5;
	x = 5;
	p = &x;
	cout << *p << endl;
	system("pause");
	return 0;
}

```
此时对于*p的值不能进行修改。但是可以修改p指针指向的地址


```c
#include "stdafx.h"
#include <iostream>
using namespace std;
int main(void)
{
	int x = 3;
	int y = 5;
	int *const p = &x;
	//p = &y;
	*p = 10;
	cout << x << endl;
	system("pause");
	return 0;
}
```

此时对于p指向的地址不能修改。但是对于*p的值可以进行修改。


```c
#include "stdafx.h"
#include <iostream>
using namespace std;
int main(void)
{
	int x = 3;
	int y = 5;
	int const *p = &x;
	cout << *p << endl;
	p = &y;
	//*p = 10;
	cout << *p << endl;
	system("pause");
	return 0;
}
```

因为此时const修饰的*p，而p是可以移动到其他地址。

```c
#include "stdafx.h"
#include <iostream>
using namespace std;
int main(void)
{
	int x = 3;
	int y = 5;
	
	int const &z = x;
	//z = 10;
	x = 20;
	system("pause");
	return 0;
}
```

别名被限制上了不能修改，但是原变量是可以修改的。

###函数的const
因为可以保证传入函数内部的值不会因为误操作而修改原有值


```c
#include "stdafx.h"
#include <iostream>
using namespace std;

void fun( const int &a,  const int &b);

int main(void)
{
	int x = 3;
	int y = 5;
	fun(x, y);
	cout << x << "," << y << endl;
	system("pause");
	return 0;
}

void fun( const int & a, const int & b)
{
	//错误因为传入的值为const。不能进行修改。
	a = 10;
	b = 20;
}

```

指针指向const修饰的变量时，应该是const int const *p = &a;


使用const关键字定义整型变量count，并定义指针p引用变量count。利用for循环打印count次Hello imooc

```c
#include "stdafx.h"
#include <iostream>
using namespace std;


int main(void)
{
	//定义常量count
	const int count = 3;
	 int const *p = &count;
	//打印count次字符串Hello C++
	for (int i = 0; i < *p; i++)
	{
		cout << "Hello imooc" << endl;
	}
	system("pause");
	return 0;
}

```
因为只对*p进行了const。因此可以让p指向其他地址

```c
#include "stdafx.h"
#include <iostream>
using namespace std;


int main(void)
{
	//定义常量count
	const int count = 3;
	const int y = 5;
	int const *p = &count;
	p = &y;
	//打印count次字符串Hello C++
	for (int i = 0; i < *p; i++)
	{
		cout << "Hello imooc" << endl;
	}
	system("pause");
	return 0;
}


```

###c++函数参数默认值

```c
void fun(int i, int j=5, int k=10);
void fun(int i, int j=6, int k);
```

有默认值的参数必须在参数表的最右端

声明写默认值，定义不要写。

```c
void fun(int i, int j = 5, int k = 10);

void fun(int i, int j, int k)
{
	cout << i << j << k;
}

```

无实参则用默认值，否则实参覆盖默认值

###函数重载

前提：在相同作用域下

- 用同一个函数名定义的多个函数
- 参数个数和参数类型不同

demo代码：

```c
int getMax(int x, int y, int z)
{

}
double getMax(double x ,double y)
{

}
```

思考：编译器如何识别重载的函数

- `int getMax(int x, int y, int z)` ->`getMax_int_int_int`
- `double getMax(double x ,double y)` ->`getMax_double_double`

根据实参类型和个数自动识别

重载的好处：

- 比如有时候求三个数有时候求5个数。

###内联函数


![内联函数](http://upload-images.jianshu.io/upload_images/1779926-5cb6ebca6707f613.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 主调函数调用普通函数进行上图五个步骤。
- 省掉了2和4步骤

内联函数代码：
关键字inline


```c
inline int max(int a,int b,int c);

int main()
{
	int i =10,j=20,k=30,m;
	m = max(i,j,k);
	return 0;
}
```
使用时和普通函数一样使用。
相当于代码粘贴进来。

###为什么不所有地方都使用内联函数

- 内联编译时建议性的，由编译器决定
- 逻辑简单（最好不要包含for循环等），调用频繁的函数建议使用内联
- 递归函数无法使用内联方式。

###内容总结

1. 函数参数默认值 ：实参覆盖默认值
2. 函数重载： 名称相同参数可辩（个数类型）
3. 内联函数 ： inline 效率高 有条件（1.逻辑简单，2.不能是递归）


###代码示例

```c
#include "stdafx.h"
#include <iostream>
using namespace std;
void fun(int i=30, int j = 20, int k = 10);

int main(void)
{
	fun();
	fun(100);
	fun(100, 200);
	fun(100, 200, 300);
	system("pause");
	return 0;
}

void fun(int i, int j, int k)
{
	cout << i << "," << j << "," << k << endl;
}

```
实参覆盖默认值

####函数重载：在同一个命名空间
```c
#include "stdafx.h"
#include <iostream>
using namespace std;
inline void fun(int i=30, int j = 20, int k = 10);
inline void fun(double i, double j);
int main(void)
{
	fun(1,2);
	fun(1.1, 2.2);

	system("pause");
	return 0;
}

void fun(int i, int j, int k)
{
	cout << i << "," << j << "," << k << endl;
}

void fun(double i, double j)
{
	cout << i << "," << j  <<endl;
}

```
inline函数实现只需要加上inline关键字

C++的重载的两个函数参数数量可以相同也可以不同，当参数数量相同时，只需要对应参数类型不同即称为重载

###代码练习

```c
#include "stdafx.h"
#include <iostream>
using namespace std;
/**
*函数功能：返回a和b的最大值
*a和b是两个整数
*/
int getMax(int a, int b)
{
	return a > b ? a : b;
}

/**
* 函数功能：返回数组中的最大值
* arr：整型数组
* count：数组长度
* 该函数是对上面函数的重载
*/
int getMax(int arr[], int count)
{
	//定义一个变量并获取数组的第一个元素
	int a = arr[0];
	for (int i = 1; i < count; i++)
	{
		//比较变量与下一个元素的大小
		if (a <arr[i])
		{
			//如果数组中的元素比maxNum大，则获取数组中的值
			a = arr[i];
		}
	}
	return a;
}

int main(void)
{
	//定义int数组并初始化
	int numArr[3] = { 3, 8, 6 };

	//自动调用int getMax(int a, int b)
	cout << getMax(6, 4) << endl;

	//自动调用返回数组中最大值的函数返回数组中的最大值
	cout << getMax(numArr, 3) << endl;
	return 0;
}
```
###内存管理

内存的本质是资源，由操作系统掌控。
我们可以对内存进行申请和归还操作，称为内存管理。

####内存的申请与释放
运算符：new ，delete

- 内存的申请：`int *p=new int;`
- 释放：`delete p;`

####申请和释放块内存
`int *arr=new int[10];`
`delete []arr;`

内存操作注意事项

- c语言中：`void *malloc(size_t size);`
  `void free(void *menblock)`
- c++:  `new` ,`delete`

- 配套使用不要混搭

- 申请内存是否一定成功：不一定会有那么多内存.

```c
int *p=new int [1000];

if（NULL==p）
{
	//内存分配失败
}
```

释放内存注意：

- 在释放内存后，要将指针值赋为空

```c
int *p=new int [1000];

if（NULL==p）
{
	//内存分配失败
}

delete []p;
p = NULL;
int *p=new int;

if（NULL==p）
{
	//内存分配失败
}

delete p;
p = NULL;
```

###内容总结：

使用new申请内存，使用delete释放内存

申请内存需要判断是否失败。释放内存要记得指针置空。

new和delete配套使用

####内存管理代码演示

```c
#include "stdafx.h"
#include <iostream>
using namespace std;

int main(void)
{
	//int *p = new int(20);//申请同时初始化
	
	int *p = new int;
	if (NULL == p)
	{
		system("pause");
		return 0;
	}

	*p = 20;
	cout << *p << endl;

	delete p;
	p = NULL;

	system("pause");
	return 0;
}

```

申请块内存：

```c
#include "stdafx.h"
#include <iostream>
using namespace std;

int main(void)
{
	//int *p = new int(20);//申请同时初始化
	
	int *p = new int[1000];//
	if (NULL == p)
	{
		system("pause");
		return 0;
	}

	p[0] = 10;//
	p[1] = 20;//
	cout << p[0] <<","<< p[1]<<endl;//

	delete []p;//
	p = NULL;

	system("pause");
	return 0;
}
```

在堆中申请100个char类型的内存，拷贝Hello imooc字符串到分配的堆中的内存中,打印字符串,最后释放内存。

```c
#include <string.h>
#include <iostream>
using namespace std;
int main(void)
{
    //在堆中申请100个char类型的内存
    char *str = new char[100];
    if (NULL == p)
	{
		system("pause");
		return 0;
	}
    //拷贝Hello C++字符串到分配的堆中的内存中
	strcpy(str, "Hello imooc");
    //打印字符串
    cout << str <<endl;
    //释放内存
	delete []str;
    str = NULL;
	return 0;
}
```
