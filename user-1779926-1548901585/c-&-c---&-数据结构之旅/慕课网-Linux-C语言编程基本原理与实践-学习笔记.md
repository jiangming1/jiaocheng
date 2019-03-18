个人整理，学习自用。课程内容by慕课网。
##Linux C语言编程基本原理与实践

###高效的学习带着目的性: 是什么 -> 干什么 -> 怎么用

###重识C语言

1. C语言是一种通用的, 面向过程的编程语言, 在系统与应用软件的开发应用较广
  2. 是人类和计算机交流的一种方式
  3. ANSI C: 是C语言的标准, 为了避免各开发商用的C语言语法的差异
  4. C语言的特点: 简单, 快速, 高性能, 兼容性好, 功能强大, 易于学习

###C语言适合做什么
  1. Linux嵌入式, 小工具(命令行下的cd, ls之类的命令)
  2. 与硬件打交道的程序: 操作系统, ARM嵌入式, 单片机编程以及Arduino编程等等
  3. 对性能要求较高的应用程序: NGINX(C)的并发量 = Apache(C++) * 10

###C适合领域
1. 小工具（语法简单）
2. 和硬件打交道的程序（有指针，可操作内存）
3. 有高性能要求的程序
nginx c apache c++
linux嵌入式

###linux下c环境：
 - C语言是随着UNIX诞生而产生的一门编程语言
 - Mac电脑是Unix内核; Windows下可以安装Linux虚拟机

Ubuntu：

  1. Ubuntu和CentOS是较为常用的Linux发行版本, 个人电脑用Ubuntu更好
  2. Ubuntu的kylin版对中文支持很好
  3. amd64版: AMD当初率先推出64位CPU, 所以Ubuntu把64位CPU型号定义为amd64(Intel照用), 一直沿用到现今; 32位用x86
  4. LTS版: 长时间的技术支持版本
  5. 装Ubuntu系统可以选择双系统, 也可在原来的Windows电脑上装虚拟机

PS: 尽量在Linux环境下开发C语言程序

- 终端编辑器：emacs vim
- 安装软件：sudo apt-get install 【软件名】
- 更新软件：sudo apt-get update

- Ctrl+Alt+T：打开终端
- cd ～     ：进入当前用户的根目录
- pwd       ：查看当前所在路径
- ls        ：当前章目录包含哪些文件
- ls -l     :显示当前文件的类型，权限，创建时间，名字（如果前面是“d”就是文件                         	     夹，“-”就是普通类型的文件）
- touch **  ：创建字符型文件
- rm **     ：删除
- mkdir **  :创建目录(文件夹)
- vi **     ：打开(进入)文件
(vi 一个不存在的文件，进入后无法输入内容，因当前在命令模式下；按字符i，可进入INSERT插入模式，就可输入内容，按Esc返回命令模式；)
- 在命令模式下：
- ：w       ：保存该文件
- ：q       ：退出
- i         ：当前光标前面插入字符
- Shift+i   ：跳到本行行首插入字符
- a         ：当前光标后面插入字符
- Shift+a   ：跳到本行末尾插入字符
- o         ：在当前下一行插入字符
- Shift+o   ：在当前上一行插入字符
- x         ：删除当前光标所处字符
- d+d       ：删除整行

Linux下最好用的文本编辑器: emacs, vim

- cc -v(gcc -v)查看编译器版本

- apt-get是一条linux命令，适用于deb包管理式的操作系统，主要用于自动从互联网的软件仓库中搜索、安装、升级、卸载软件或操作系统。

- clear：清洁屏幕

linux下一般不用void main
- .(./)表示当前路径下，
- ./a.out 执行当前路径下的a.out文件
- r表示可读  w表示可写  x表示可执行
三组重复的顺序为创建者 用户组 任意其他用户

vim分屏显示

- :sp 文件名 //创建（打开）新文件
- 上屏 ctrl+w+上箭头
- 下屏 ctrl+w+下箭头

- 复制：(行数)+dd
- 粘贴：p
//这两个不用点冒号

- 打开行号：set nu
- 关闭行号：set nonu

终端下：

- gcc 文件名.c -o 命名.c
- 生成.out并命名
- `#include <>`表示在预装的库里查找，""表示在当前文件内查找

###头文件与函数定义分离

代码没有main函数不能执行，main是入口。

- .h 头文件
- .o 编译之后的;D
- .c 源代码

`gcc -c max.c -o max.o`


gcc的编译流程分为4步：

预处理(Pre-Processing) －> 编译(Compling) -> 汇编(Assembling) -> 连接(Linking) 

预处理：处理#include、#define、#ifdef 等宏命令

编译：把预处理完的文件编译为汇编程序.s

汇编：把汇编程序.s编译为.o二进制文件

- gcc -c min.c -o min.o  //把文件min.c预编译成文件min.o
- cp 文件名a  文件b    //把文件a拷贝成新的文件b
- 使用yy复制一行   使用 行数n+yy 复制n行    
- 使用p对复制的行进行粘贴

- `gcc max.o min.o hello.c`

- 源代码文件用cat命令可以查看

hello.c代码：

```c
#include <stdio.h>
#include "max.h"
#include "min.h"

int main()
{
    int a1 = 33;
    int a2 = 21;
    int maxNum = max(a1,a2); 
    int minNum = min(a1,a2);
    printf("the max value is %d\n",maxNum);
    printf("the min value is %d\n",minNum);
}

```

max.h 代码：

```c
int maxNum(int a, int b);
```

min.h 代码：

```c
int minNum(int a ,int b);
```

min.c代码:

```c

int min (int a, int b)
{
    if(a<b){
	    return a;
    }else{
	    return b;
    }
}

```

###makFile的编写
- make工具可以将大型的开发项目分成若干个模块
- make -v
- sudo apt-get install make

编写一个Makefile可以同时编译多个文件。

-  vi Makefile
-  写入：
```
hello.out:max.o min.o hello.c
	gcc max.o min.o hello.c -o hello.out
max.o:max.c
	gcc -c max.c
min.o:min.c
	gcc -c min.c
# 添加注释
```
-  执行命令make
编写 Makefile 缩进使用 tab 键(六个空格，否则出错)
- 重命名 mv MakeFile makefile 

###详细讲解main函数中的返回值的作用以及main函数中的参数意义

`gcc main.c -o main.out && ./main.out`

- gcc main.c -o main.out && ./ main.out 可以依次执行两条命令。
- return  0 用来验证程序运行是否成功。
- 命令echo $?用来查看返回值

`./main.out && ls`打印出helloworld的同时，列出当前目录。因为main.out的return值为0，判定为成功执行。

`./main2.out && ls`打印出helloworld，未列出当前目录。因为main.out的return值为101（非0），判定为不成功执行。

###main函数中的参数

```c
int main(int argc, char* argv[])//main函数完整形式
```
argc获取输入参数的个数。只输入文件名为1.

argv[]存放每个参数的内容
如：

- 输入  ./main.out -l -a
- argv = 3
- argc[0] = ./main.out
- argc[1] = -l

```c
#include <stdio.h>

int main(int argc,char* argv[])
{
    printf("argc is %d\n",argc);
    //int i;
    for(int i =0;i<argc;i++){
	printf("argv[%d]is %s\n",i,argv[i]);
    }

    return 101;
}
```

###Linux下的标准输入流输出流与错误流

linux把所有东西当作文件处理

- 标准输入流文件：stdin 键盘
- 标准输出流文件：stdout 显示器
- 标准错误流文件：stderr
- 这是系统默认创建
- fprintf fscanf：将输入输出放入...
- printf("") 是对fprintf(stdout,"")函数的封装.
- scanf("") 是对fscanf(stdin,"")函数的封装

```c
#include <stdio.h>

int main()
{
    // printf("hello world!\n");
    fprintf(stdout,"hello world \n");
    int a;
    //scanf("%d",&a);
    fscanf(stdin,"%d",&a);
    if(a<0){
	 fprintf(stderr,"the value must >0\n");
	 return 1;
    }

    //printf("input value is: %d\n",a);
    return 0;

}

```

###输出输出流与错误流的重定向
Linux几乎可以用于任何领域，这里我们不得不提出linux的通道，管道起到了很重要的作用，不同应用程序之间要配合使用，就需要用到管道。

demo:main.c
>先理解输入流，输出流和错误流的重定向机制，对于管道的理解会比较容易些。

```c
#include <stdio.h>

int main()
{
int i,j;
printf("input the int value i:\n"); \\printf其实对fprintf的封装，是从标准输出流（即stdout）来输出这个过程
scanf("%d", &i); //默认输入流是键盘
printf("input the int value j:\n");
scanf("%d", &j);
printf("i+j=%d\n", i+j);
}
```
- 执行命令编译cc main.c，得到a.out，运行a.out，我们分别输入3和5输入到终端。我们标准输出流是1，输出入是0。我们可以使用命令./a.out 1>> a.txt，其中>>符号（不写参数就是输出流），之前默认输出流是终端，现在我们则改为输出到a.txt中，我们执行命令后，分别输入3回车后再输入5。再使用命令cat a.txt，我们可以看到我们已经输出到文件里的内容。
- 我们再次执行./a.out >> a.txt，我们再次输入参数，完成后我们再次使用cat来查看a.txt文件里的内容，发现之前的内容还在，新的输出内容追加到了后面。
- 再举一个重定向的例子，我们使用命令ls /etc >> etc.txt，我们将ls目录下的内容输入到了etc.txt文件中；但我们如若改重定向符号想覆盖掉之前的内容，可以把双箭头>>改为单箭头>，则文件中先前的内容就会被覆盖掉。
###输入流重定向
- 我们可以创建一个文件vi input.txt，内容如下：
6
8

- 我们再次执行./a.out < input.txt，不存在追加模式，所以我们用单箭头<，我们可以将要输入的内容全部在input.txt中准备好，命令执行后，我们便在终端上可以看到结果。

```c
#include <stdio.h>

int main()
{
	int i,j;
	printf("input the int value i:\n"); 
	scanf("%d", &i); //默认输入流是键盘
	printf("input the int value j:\n");
	scanf("%d", &j);
	if(0!=j){	
	    printf("%d/%d=%d\n",i,j,i/j);
	}else{
	    fprintf(stderr,"j != 0\n");
	    return 1;
	
	}


	return 0;
}

```

`./a.out 1>t.txt 2>f.txt <input.txt`

- 将输出保存到t.txt.
- 错误保存到f.txt.
- 从input.txt读入数据


###管道原理及应用。

管道：
把前面的输出流作为后面工具的输入流，用一个“|”表示.

- ps -e:查看系统运行的进程

- grep:查看指定文本，搜出包含字符的文本

- 如输入： ls /etc/ | grep ab

>- ls /etc/ 的输出作为 grep 的输入，
- ls /etc/ | grep ab 在/etc/文件下查找含有ab字符的文件名

ps -e | grep ssh
>查看包含ssh的进程

###实践编程，用简单的C语言代码，编写一个实用的C语言小程序

avg.c :

```c
#include <stdio.h>

int main()
{
    int s,n;
    scanf("%d,%d",&s,&n);
    float v = s/n;
    printf("v =%f\n",v);
    return 0;
}

```

input.c:

```c
#include <stdio.h>

int main()
{
    int flag =1;
    int i,s;
    int count=0;
    while(flag){
	scanf("%d",&i);
	if(0==i) break;
	count++;
	s+=i;
    }
    printf("%d,%d\n",s,count);
    return 0;

}

```

使用时：
`./input.out | ./avg.out`
实现将input的数据之间求平均值。
