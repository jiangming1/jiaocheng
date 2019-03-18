##Linux C语言指针与内存
###工具与原理
- 指针
- 数组
- 字符串
- 堆内存与栈内存
- gdb内存调试工具。

###C语言中指针的基本用法

```c
#include <stdio.h>
void change(int a, int b)
{
    int tmp =a;
    a=b;
    b=tmp;
}

int main()
{
    int a=5;
    int b=3;
    change(a,b);
    printf("num a =%d\nnum b =%d\n",a,b);
    return 0;
}

```

上述代码无法实现a,b数值的交换。

改为指针实现代码如下：

```c
#include <stdio.h>
void change(int *a, int *b)
{
    int tmp =*a;
    *a=*b;
    *b=tmp;
}

int main()
{
    int a=5;
    int b=3;
    change(&a,&b);
    printf("num a =%d\nnum b =%d\n",a,b);
    return 0;
}

```
3和5可以成功的交换。

需要将实参的地址传到子函数才能改变实参！（&a，&b）

C语言 int未初始化时，初值为随机
int变量未初始化的默认初值，和变量的类型有关。
- 局部变量，在未初始化情况下，初值为随机值。C规范对该初值并没有做规定，具体实现由编译器决定。如VC/VS等编译器，会将初始值值为0xCCCCCCCC， 而GCC等编译器则是不可预知的随机值。
- 静态局部变量，即带static修饰的局部变量。
全局变量和静态全局变量，即定义在函数外，不属于任何一个函数的变量。
这几种默认初值为0.

###gdb工具的使用
安装gdb工具：`apt-get install gdb`

`gcc -g main.c -o main.out`生成可调试版本。
`gdb ./main.out`

>- l：查看源代码
- 回车：继续执行上条指令
- break 行数：设置断点
- start ：单步调试
- n：执行到下一条语句
- s：进入函数内部
- p a：查看a在内存中的情况
- bt：查看函数堆栈
- f 1：切换到1号函数
- q：退出调试
- p *a(int *a 时 p a 打印出的是a的内存地址，p *a打印的是这个地址里对应的值.P &a 显示a的内存地址空间
- P &functionname p + &函数名，显示函数程序在代码段的内存地址)

*a     取a这个地址的内容
&a    去a这个变量的地址

因为不知道一个指针指向的数据有多大， 所以需要在声明一个指针变量的时候需要明确的类型。

解析：只是传值，只是change的局部变量，是实参的备份。

解决：加个指针，取地址符，实现交换功能。

###计算机中数据表示方法：

0x表示十六进制
1个16进制的数字，就可以表示4位二进制数字

- 计算用二进制
- 显示用十进制
- 编程用16进制

###内存管理
32根地址总线就有2的32次方个状态
内存分配
1byte = 8bit
1字节 = 8进制位

用户程序的内存空间从高到低又划分为：
- 系统内核
- 栈（暂时存储首先执行的程序状态）
- 自由可分配内存（可动态分配内存）
- 堆
- 数据段（声明一些全局变量或者声明一些常量）
- 代码段（程序源代码编译后存放在此）

高位内存空间分配给操作系统内核使用，低位内存空间分配给用户程序使用。
我们编写的函数在编译后存到磁盘，运行程序时，就把源代码编译后的二进制数据加载到内存空间中的代码段中。声明的全局变量或常量放置在数据段。每次调用新的函数，就将新的函数压入栈区。
64位系统中 只有前48位是给程序员使用的。 0x7fffffffffffffff ~ 0x0

###变量与指针的本质
```c
#include <stdio.h>

int global = 0;

int rect (int a,int b)
{
    static int count=0;
    count++;
    global++;
    int s=a*b;
    return s;
}

int quadrate(int a)
{
    static int count=0;
    count++;
    global++;
    int s = rect(a,a);
    return s;
}

int main()
{
    int a=3;
    int b=4;
    int *pa =&a;
    int *pb =&b;
    int *pglobal =&global;
    int (*pquadrate)(int a)= &quadrate;
    int s = quadrate(a);
    printf("%d\n",s);  
}

```
- gcc -g main.c -o main.out  //加-g生成的main.out才可以用gdb进行调试
- gdb ./main.out   //调试

gdb调试命令:

- l（list）     列出代码
- start         开始调试
- n             单步走
- s             进入函数
- p 变量名      输出变量的值
- bt            查看栈标号
- f 栈标号      切换栈
- q             退出gdb
- 回车          重复执行上一次的命令

变量的本质是什么？
  - 变量名只是一个代号。
  - 变量的本质就是内存。

指针的本质？
	指针保存内存的地址。
	a = 第五个柜子第二个抽屉。

###操作系统对于内存的管理

栈先声明的地址大，后声明的地址小，与代码段数据段相反。
数据段（data segment）通常是指用来存放程序中已初始化的全局变量的一块内存区域。数据段属于静态内存分配。 
32位系统指针占用4个字节， 也就是32个bit，64位系统占用64个bit，也就是8字节。

64位系统下，指针占8个字节，32位 4个字节。

编译器优化代码,把声明时不在一起的同一类型变量,放到一起(某种程度上修改了源码)

如 声明 int a ; float b ; int c; 编译后变量a的地址和c的地址是连在一起的.
 
CPU在编译的时候对栈内变量的存储地址进行优化，他会将类型相同的变量在连续地址中储存。

地址分配:代码,数据段是从下往上分配(先低地址,后高地址),栈是从上往下分配(先高地址,后低地址)

函数中静态变量,局部变量区别:
局部变量在栈(相对数据段而言的高地址)中,而静态变量在数据段(低地址)中.
所以在多次调用函数时,静态变量不会被重新,初始化. 或者这么说,静态变量的生存周期和数据段相同,局部变量生存时间受调用函数时,所属函数进栈出栈的影响而会重新初始化.

全局变量和静态变量都在数据段中,但静态变量是某个函数特有的.

###函数指针与指针指向的数据访问

```c
#include <stdio.h>

int global = 0;

int rect (int a,int b)
{
    static int count=0;
    count++;
    global++;
    int s=a*b;
    return s;
}

int quadrate(int a)
{
    static int count=0;
    count++;
    global++;
    int s = rect(a,a);
    return s;
}

int main()
{
    int a=3;
    int b=4;
    int *pa =&a;
    int *pb =&b;
    int *pglobal =&global;
    int (*pquadrate)(int a)= &quadrate;
    int s = quadrate(a);
    printf("%d\n",s);  
}

```

- `&*p`：取变量a的地址（先进行`*`运算，`*p`相当于变量a，再进行`&`运算，`&*p`就相当于取变量a的地址）
- `*&p`：取变量a所在地址的值（先进行`&`运算，`&a`相当于取变量a的地址，在执行`*`运算，`*&p`相当于取变量a所在地址的值）
- pa ====》0x7fffffffddfc 内存地址
- *pa  代表取出0x7fffffffddfc 这个地址代表的值
- p *pa指找到pa中的数据；
- p &pa指找到pa本身的地址。

###数组申明的内存排列

- 0x7fffffffcc78：3
- 0x7fffffffcc7C：2
- 0x7fffffffcc78:	3	2	
- 0x7fffffffcc84：i

>(gdb)p *0x7fffffffcc6c
$15 = 1

>(gdb) p *0x7fffffffcc70
$17 = 10

>(gdb) p *0x7fffffffcc74
$18 =100

可以看出数组是按顺序放置元素的。

###指针运算。

指针偏移运算。
p +=3;
把指针往下移三格-整型移动12个字节
*p =101;
将p指针所指向的值修改为101
p =&a;
让p再次指向a的地址
p[4] = 101
把指针往下移三格-整型移动12个字节
P[4]不是p往下面移动了4个位置，而是从p开始的地址往后移动4个位置取值，p指向的地址还是不变的

数组其实就是个指针常量，指针是指针变量，常量就是不可更改的

```c
int array[2];
int *pa =array;
pa[0]=1;
pa[1]=10;
pa[2]=100;

```

###字符数组和指针字符串

```c
int a;
scanf("%d",&a);

int main()
{
    char str[]="hello";
    char *str2="world";
    char str3[10];
    printf("input the value\n");
    scanf("%s",str3);
    printf("str is %s\n",str); 
    printf("str2 is %s\n",str2);
    printf("str3 is %s\n",str3);
}
```
因为str3是一个字符数组，

gdb的x命令，可以打印地址中的值
- x/个数  地址
- x/6cb 地址： 打印该地址后的6个字符，c：字符形式打印，b：按字节显示

scanf可以将输入存入str或str3，但是不能存入str2
堆和栈内存里才可以写入（预留空间才可写入）
而str2是一个代码段变量。不允许写入。


###字符串数组的深入理解

```c
#include <stdio.h>

int main()
{
    char str[]="hello";
    char *str2="world";
    char str3[10];
    printf("input the value\n");
    str[3]='\0';
    scanf("%s",str3);
    printf("str is %s\n",str); 
    printf("str2 is %s\n",str2);
    printf("str3 is %s\n",str3);
}

```

只会打印出hel，因为prinf打印以/0为结束。

```c
char str1[] = "Hello";
char str3[5];
str1 = "HelloWorld";
```
很有意思 ：

- 当 str1 声明时，长度为 5 ，str3 也是数组类型，与str1都在同一个内存空间中，并且在 str1 之后声明 ， 他们的地址应该是连续的；
- 当改变 str1 的值为 “HelloWorld”时，str1之前的大小装不下这么多字符，就会使用后面的内存地址，str3 本是空的并没有赋值，但是由于 str1的内存溢出，装到了str3中，所以str3也是有值的；
- 总之，数组内存溢出是件危险的事；

String类型输出遇到/0 结束
char类型输出遇到/0 继续输出.

>指针变量char *str2 = "hello"，用scanf 向str2中输入字符串出错，其实也可以这么理解，指针str2只是指向一个地址，从这个地址开始写入"hello",没有指定内存长度，没有空间去容纳字符串。内存溢出！这个与char str[] = "hello"不同，str已经有了6个字节的内存空间，

