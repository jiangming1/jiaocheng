##c++教程起航篇

>- C语言是C++的子集。
- c语言是面向过程的语言，而C++是面向过程的语言又是面向对象的语言。
- C语言的运行效率比c++更好。

### IDE环境搭建

vs2015

- 新建项目
- 新建文件
- 如何设置字体字号（工具选项）

```c++
#include<iostream>
using namespace std;    //关于这里，且听下回分解
int main()
{
   cout<<" Hello imooc     ";  //在此填写我们的开篇Hello imooc
   return 0;
}
```
###c与c++ 区别
1. c++新增bool数据类型，数值为true与false   
优：快捷方便，还有视觉上清晰易懂
2. c++除了拥有语言自带的初始化方式外，新增了一种定义方式（直接初始化）   
优点：很快捷方便
3. c++可随用随定义，而c的对变量的定义必须放置在函数体最前面

###C++输入与输出。

>cin输入方式，ＸＹ为变量，可以不管变量的类型，是不同于Ｃ语言的地方。

```c++
cout << x << endl;
cin >> x >> y;
```
- 不用关注占位符。
- 不用关注数据类型。

###小demo
- 八进制：oct；
- 十进制：dec；
- 十六进制：hex
- bool类型输出：boolalpha

c++中默认的i/o类型为十进制；如果不加说明，是无法辨认0，0x开头的八进制和十六进制的。在cin或cout中指明进制类型后，该进制将一直有效，直到再次改变它的进制。

```c++
#include "stdafx.h"
#include<iostream>
#include <stdlib.h>
using namespace std;

int main()
{
	cout << "请输入一个整数：" << endl;
	int x = 0;
	cin >> x;
	cout << oct << x << endl;
	cout << dec << x << endl;
	cout << hex << x << endl;

	cout << "请输入一个布尔值（0、1）:"<< endl;
	bool y = false;
	cin >> y;
	cout << boolalpha << y << endl;

	system("pause");
    return 0;
}
```

**f5 开始调试**

###c++命名空间

namespace 区别来自不同的对象的相同名字的函数，为程序划片取名字。

命名空间关键字：
`namespace A`,`namespace B`

```c++
namespace A{
   int x;
   void f1 ();
}
namespace B{
   int x;
   void f1 ();
}
```
要调用命名空间内变量或函数写法：

```c++
//输出A中的变量x
cout << A::x << endl;
//使用B中的f1
B::f1();
```

###namespace演示
- 正常的system("pause")包含于stdlib.h

```c++
#include "stdafx.h"
#include <stdlib.h>

int main()
{
	system("pause");
	return 0;
}
```
- 要引用相应的头文件，还要引用对应的命名空间才可以正常运行。

1. `#include<iostream>`
2. `using namespace std;`

```c++
#include "stdafx.h"
#include <stdlib.h>
#include<iostream>

using namespace std;
int main()
{
	cout << "hello" << endl;
	system("pause");
	return 0;
}
```

###模拟A，B公司写代码

主函数为main函数模拟c公司。

```c++
#include "stdafx.h"
#include <stdlib.h>
#include<iostream>

namespace A 
{
	int x = 1;
	void fun()
	{
		std::cout << "A" << std::endl;
	}
}

namespace B
{
	int x = 2;
	void fun()
	{
		std::cout << "B" << std::endl;
	}
	void fun2()
	{
		std::cout << "2B" << std::endl;
	}
}

using namespace B;
int main()
{
	std::cout << A::x << std::endl;
	B::fun();
	fun2();
	system("pause");
	return 0;
}
```

###章节练习

>定义一个命名空间为myNum,在该命名空间中定义一个整型变量x,并给该变量赋值为105；使用C++新特性判断myNum命名空间下的变量是奇数还是偶数。

```c++
#include "stdafx.h"
#include <iostream>
#include <stdlib.h>
using namespace std;
namespace    myNum           //填写命名空间的关键字
{
	int x = 106;
}
int main()
{
	// 使用bool类型定义isOdd，作为状态位
	bool   isFlag = false;

	if (myNum::x % 2 == 0)
	{
		//改变状态位的值，使其为false
		isFlag = false;
	}
	else
	{
		//改变状态位的值，使其为true
		isFlag = true;
	}
	// 判断状态位的值
	if (isFlag)
	{
		// 如果状态位的值为true，则打印变量x是奇数
		cout << myNum::x << "是奇数"<<endl;
	}
	else
	{
		// 如果状态位的值为false，则打印变量x是偶数
		cout << myNum::x << "是偶数"<<endl;
	}
	system("pause");
	return 0;
}
```

###综合练习
- 知识点：bool类型 命名空间 输入输出。
>使用一个函数找出一个整型数组中的最大值最小值。

```c++

/********************************************************/
/*知识点：bool类型 命名空间 输入输出                    */
/*题目要求:                                             */
/*        使用一个函数找出一个整型数组中的最大值最小值。*/
/********************************************************/
#include "stdafx.h"
#include <stdlib.h>
#include <iostream>

namespace CompA {
	int getMaxOrMin(int *arr, int count, bool isMax)
	{
		int temp = arr[0];
		for (int i = 1; i < count; i++)
		{
			if (isMax)
			{
				if (temp < arr[i]) {
					temp = arr[i];
				}
			}
			else
			{
				if (temp > arr[i]) {
					temp = arr[i];
				}
			}

		}

		return temp;
	}
}


int main(void)
{
	int arr1[4] = { 3,5,1,7 };
	bool isMax = false;
	std::cin >> isMax;
	std::cout << CompA::getMaxOrMin(arr1, 4, isMax) << std::endl;
	system("pause");
	return 0;
}
```
