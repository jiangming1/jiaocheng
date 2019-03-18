##Linux C语言结构体

- 编译指令：预处理，宏定义，
- 建立自己的数据类型：结构体，联合体，动态数据结构
- 逻辑运算符：&  |  ^  ~  <<  >>
- 递归函数的调用

###什么是预处理

>.c文件->.i文件->.s文件->.o文件->可执行文件

- 预处理
- 编译     
- 汇编      
- 链接

`gcc -o helloworld.i helloworld.c -E`
-E表示只让gcc执行预处理。

vim跳到整个文档底部，命令：`:$`

- 展开头文件。
`#include <stdio.h>`展开

###宏是什么
c语言常量分为直接常量和符号常量:
`#define 标识符 常量值（没有分号）`

hello.c源代码：
```c
#include <stdio.h>
#define R 10

int main()
{
    int a =R;
    printf("a=%d\n");
    printf("hello,world!\n");
    return 0;
}

```
预处理过之后的代码
```c
# 4 "helloworld.c"
int main()
{
    int a =10;
    printf("a=%d\n");
    printf("hello,world!\n");
    return 0;
}
```
10是直接当做一个字符串来替换。
宏的本质是在预处理阶段发生的单纯的字符串替换（宏替换）；
在预处理阶段，宏不考虑语法；

```c
#include <stdio.h>
#define R 10
#define M int main(

M){
    printf("hello,world!\n");
    return 0;
}
```
预处理是没有问题的。可以编译执行。

- 宏用于大量使用的常量、数组buffer中，便于修改。

###宏函数
源代码：
```c
#include <stdio.h>
#define R 10
#define M int main(
#define N(n) n*10



M){
    int a = R;
    int b =N(a);
    printf("b = %d\n",b);
    printf("a =%d\n",a);
    printf("hello,world!\n");
    return 0;
}

```

预处理之后：

```c
# 8 "hello.c"
int main(){
    int a = 10;
    int b =a*10;
    printf("b = %d\n",b);
    printf("a =%d\n",a);
    printf("hello,world!\n");
    return 0;
}

```
宏替换中直接替换的优先级问题。

```c
#include <stdio.h>
#define R 10
#define M int main(
#define N(n) n*10
#define ADD(a,b) a+b
int add(int a,int b){
    return a+b;
}


M){
    int a = R;
    int b =N(a);
    int c =add(a,b);
    int d =ADD(a,b);
    int e = ADD(a,b) * ADD(a,b);

    printf("e = %d\n",e);
    printf("b = %d\n",b);
    printf("a =%d\n",a);
    printf("c =%d\n",c);
    printf("d =%d\n",d);
    printf("hello,world!\n");
    return 0;
}
```

- 宏定义时由于是看作字符串的替换，因此在设计函数时利用宏可以不用考虑输入值的类型，这与普通的函数定义不同。
- `#define ADD(a,b) (a+b)`
- 这里后面的括号是为了防止调用多次时优先级出错：
- main 函数种。`ADD(2,3)*ADD(2,3)`如果定义时没有括号则是`2+3*2+3`其与`（2+3）*（2+3）`不同。因为预处理阶段没有函数运算，定义的东西只会被当作字符串。但在调用后可以实现其功能。
- 而普通函数例如int add(int a,int b)除了在开头要声明值的类型，还要设置返回值，因此在定义过程与调用过程相对复杂。若能用宏定义实现的情况应优先考虑宏定义.

```c
float d = ADD(10.5,20.0)
```
宏是不考虑数据类型等的。

条件编译。

###typedef是怎么回事
- typedef 简记：取别名；但是属于C语法，结束要加分号，与#define 不同。
- typedef int tni；即将int 用tni代替，在之后的int定义可直接写为：tni i；
- `typedef int *p`；给`int *`起了别名：p，
- 在指针定义中`int * q=null`即可等价为`pq=null`

- 宏是会在预处理阶段被替换的，而tni不会
-  `typedef struct stu{ }stu_t;`      给结构体起别名
- `typedef unsigned long size_t`

注意typedef如果写在方法体内则只可作用于该作用域内{}，#define一直全局。

###结构体的声明与定义

```c
#include <stdio.h>
struct weapon{
    char name[20];
    int atk;
    int price;
};

int main()
{
   int a =0;
   float b =0.0;

   struct weapon weapon_1;
   return 0;
}
```
>struct 结构体类型名{}

在函数中调用时 struct 结构体类型名 变量名（前两部分效果类似定义 int 变量 中的int）


###结构体的初始化与引用。

```c
struct weapon weapon_1 = {"weapon_name",100,200};
   printf("%s\n,%d\n",weapon_1.name,++weapon_1.price);

```

结构体元素可以和普通变量一样进行操作。

###结构体数组
- 运算符是一个成员运算符，在所有运算符中运算级最高，可用.运算符访问结构体内部成员
- //数组里包含两个结构体元素，每个元素有结构体里的三个成员

```c
struct weapon weapon_2[2]={{"weapon_name1",50,100},{"weapon_name2",100,200}};
printf("%s\n,%d\n",weapon_2[0].name,weapon_2[1].atk);
```

###结构体指针

```c
struct weapon *w ;  //定义一个指向weapon的w结构体指针
w=&weapon_1;       //具体指向weapon_1的内存地址
(*w).name
w->name
weapon_1.name           //这3种访问类型都是一样效果
   struct weapon *w;
   w = &weapon_1;
   printf("---------------------------------\n");
   printf("name=%s\n,name=%s\n,name=%s\n",(*w).name,w->name,weapon_1.name);
   return 0;

```
结构体数组指针。不用取地址符&：数组的名字代表了这个数组的内存首地址，数组括号内的长度代表了数组的单元数，数据类型是int的话就按照int类型（32位系统上是4个字节）乘以单元数的长度，如果数据类型是结构体的话就按照结构体的长度乘以单元的长度。
p++，不是内存位置右移了一个字节，而是右移了一个单元长度的结构体weapon的内存长度。所以就不难理解为什么右移到了第二个结构体实例的首地址上了

```c
   struct weapon *p;
   p = weapon_2; //p->name weapon_2[0].name
   printf("%s\n",p->name);
   printf("---------------------------------\n");
   p++;// weapon_2 +1 weapon_2[1]
   printf("%s\n",p->name);
   return 0;
```

###共用体类型是怎么定义的以及怎么使用的

- union共用体是多种不同数据类型的合集，所占字节按照共用体里面类型所占最长字节决定，
- 例如有char（1字节）型和int（4字节）型，按照int型来计，整个共用体所占四个字节；此外，共同体里面所有数据的地址是相同的，这个决定了它在有多种数据类型的时候，有且只能存放这些数据类型里面的一种数据。以最后一次赋值为准。
- struct结构体也是不同数据类型的合集，所占字节按照里面所有数据类型的长度来决定，
- 例如：char（1字节）和int（4字节），struct所占空间是8个字节，不是5个字节，原因是涉及到字节对齐，字节对其是方便计算机快速的读取数据。
- 大小 = 最后一个成员的偏移量+最后一个成员的大小+末尾填充字节数。
- 偏移量，实际地址和结构体首地址之间的距离。

```c
#include <stdio.h>
struct data{
    int a;
    char b;
    int c;
};

int main()
{
   // union data data_1;
   //data_1.b ='C';
   // data_1.a =10;
    printf("%lu",sizeof(struct data));
    return 0;

}

```
a的偏移量0;b的偏移量4，自身大小为1。偏移量是大小的整数倍，不会填充空间；c的偏移量为5，c自身大小为4.要补齐为8.然后加上c自身的4。
所以整个占用12.然后再判断这个大小12是不是最宽成员的整数倍。

- %p表示输出这个指针
- %d表示后面的输出类型为有符号的10进制整形，
- %u表示无符号10进制整型，
- %lu表示输出无符号长整型整数(long unsigned)

```c
#include <stdio.h>
union data{
    int a;
    char b;
    int c;
};

int main()
{
    union data data_1;
    data_1.b ='C';
    data_1.a =10;
    printf("%p\n,%p\n,%p\n",&data_1.a,&data_1.b,&data_1.c);
    return 0;

}
```

共用体地址全部相同。

###动态数据结构-静态链表

静态数据结构：

- 如：整型、浮点型、数组。
- 系统分配固定大小的存储空间

链表：

- 有头指针，存放地址，地址指向第一个元素。没有头指针链表无法访问
- 链表中的每一个元素都是一个节点。
- 节点里包括用户需要的数据和下一个节点的地址，各个元素的地址不一定是连续的

静态链表;（所有节点都是在程序中定义的，而不是临时开辟的）
【由三个武器信息的节点组成，所以用结构体类型作为节点元素】

```c
#include <stdio.h>

struct weapon{
    int price;
    int atk;
    struct weapon * next;
};

int main()
{
   struct weapon a,b,c,*head;
   a.price =100;
   a.atk = 100;
   b.price =200;
   b.atk =200;
   c.price =300;
   c.atk =300;
   head = &a;
   a.next =&b;
   b.next =&c;
   c.next = NULL;

   struct weapon *p;
   p =head;
   while(p!=NULL){
	printf("%d,%d\n",p->atk,p->price);
	p=p->next;

   }
}

```
- 链表：可以用malloc来动态分配所需的内存，并且需要用free手动释放在堆里面申请的内存。链表有一个头指针和尾指针，每个指针指向的是链表下一个数据的地址。在结构体里面加入指针就构成链表，此时指针结构体包括两个部分，一个是信息域，另一个是指针域。

###动态链表

程序执行过程中从无到有的建立起一个链表，也就是说需要一个一个的开辟新节点，输入新节点的数据，然后建立起前后相连的关系。
建立武器信息的单向动态链表：

```c
#include <stdio.h>
#include <malloc.h>
struct weapon{
  int price;
  int atk;
  struct weapon * next;
};
//【需要一个创建链表的函数，返回值是链表的头指针】
struct weapon * create(){
   struct weapon *head;
   struct weapon *p1,*p2;//3个指针都用来指向struct weapon类型数据,p1,p2分别指向当前新创建和上一个。
   int n=0;//记录当前节点个数
   //malloc分配内存块的函数，sizeof判断数据类型长度符
   p1=p2=(struct weapon*)malloc(sizeof(struct weapon));
   scanf("%d,%d",&p1->price,&p1->atk);
   head = NULL;//一开始链表不存在，置空
   while(p1->price!=0){//约定price为0时停止输入
     n++;
     if(n==1) head=p1;
     else p2->next=p1;

     p2=p1;//保留p1当前所指向的的地址

     //需要开辟一个新的动态存储区，把这个的地址载给p1
     p1=(struct weapon*)malloc(sizeof(struct weapon));
     scanf("%d,%d",&p1->price,&p1->atk);//开辟后输入数据
}
p2->next=NULL;
return (head);
}//p1,p2一个用来指向链表新创立的节点，一个用来指向下一个节点
int main()
{
   struct weapon *p;
   p=create();//p成为链表的头指针
   printf("%d,%d",p->price,p->atk);//打印第一个节点的信息
   return 0;
}

```
###C语言中的位运算符:按位与、按位或、按位异或、左移和右移

####按位与

- 位：指二进制数中的一位 0false 1true
- 古老的微处理器速度：比加减运算快一些，比乘除法快很多
现在的框架中：通常与加减运算快相同，比乘除法快很多

六种位运算符

- & 按位与
- | 按位或
- ^按位异或
- ~按位取反
- <<左移
- >>右移

```c
#include <stdio.h>

int main()
{
   // & | ^ ~ << >>

   int a =4;//0100
   int b =7;//0111

  int c =a&b;//0100

   printf("%d",c);
   return 0;
}
```
- 位与运算就是将参与运算的两个数据按照对应的二进制数逐位进行逻辑与运算。
- 参与运算的两个数必须是整型或者是字符型。
- 参与运算的数必须要以补码的方式出现。

按位与的应用

- 迅速清零(对于一个数中为1的位,让另一个数的相应位为0);
- 保留指定位置(对另一个数的相应位置1);
- 奇偶判断（和1做与运算）[a&1//得到结果1位奇数，得到0为偶数]


```c
#include <stdio.h>

int main()
{
   // & | ^ ~ << >>

   int a =4;//0100
   int b =7;//0111

   int c =a&1;//0100

   int d =b&1;//0100
   printf("%d\n",c);
   printf("%d\n",d);
   return 0;
}

```

输出为0,1.

###按位或运算。

```c
#include <stdio.h>

int main()
{
   // & | ^ ~ << >>

   int a =9;//1001
   int b =5;//0101

   int c =a|b;//1101

   printf("%d\n",c);
   return 0;
}
```
输出为1101:13

按位或的作用：
设定数据的指定位,与255(0xFF)做或运算；
>b = b| 0xFF,能设定数据b的指定二进制数后8位置为1
即b的十进制等于255

###按位异或
将参与运算的两个数据按对应的二进制数逐位进行逻辑异或运算
- 定位反转 a^0xff;
- 数值交换  a = a^b;b= b^a; a=a^b;
###按位取反
~1000 = 0111

###左移：高速乘以2；右移：高速除以2
左移：将数据对应的二进制值逐位左移若干位；高位丢弃，低位补零；乘以2的n次方
右移：将数据对应的二进制值逐位右移若干位：低位丢弃，高位补0或1（根据符号位判断，就是正数数补0，负数补1）；除以2的n次方
高位丢弃，低位补零

###递归调用

递归调用有时候会牺牲效率

```c
#include <stdio.h>
int func(int a)
{
    int r =0;
    if(a<0){
	printf("error");
}else if(a==0 || a==1){
    return 1;
}else{
    r =a *func(a-1);
    return r;
}

}

int main()
{
   int a =0;
   printf("please input the num:");
   scanf("%d",&a);
   int r = func(a);
   printf("the result is %d\n",r);
   return 0;
}
```

###递归的工作原理。

- 函数调用需要做的第一件事是为被调用的函数的形参分配一个临时的内存单元然后才能把实参的值传递过来。
- 同时还需要传递的是主调函数的返回地址。（保护现场）
- 被调函数执行完了还需要继续执行主调函数后面的代码。
- 数据保存到栈里面。

>- 递归：大规模——化简——>小规模，直到问题可求。
- 递归函数同时必须有 递归条件和递归表达式，否则会进入死循环。
-  递推(for)：则是由小问题的解逐步代入大问题并求出解。

###总结
- 编译预处理：头文件、宏替换

- 自定义数据类型：结构体、联合体、链表声明定义等，结构体涉及字节对齐，共同体所有成员公用结构体内存地址

- 逻辑运算符位运算

- 递归和递推
