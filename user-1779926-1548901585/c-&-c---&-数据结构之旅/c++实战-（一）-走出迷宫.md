##走出迷宫

走出规则：

- 左手规则 & 右手规则
- 原则：保证手始终触墙
- 结果：走出迷宫

情况1：有入有出。（特殊情况，出入口是一个）

![出入为一个](http://upload-images.jianshu.io/upload_images/1779926-ec9b50716ec1182f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

架构描述：

- 迷宫类（MazeMap）

二维数组：

	- 1代表墙，0代表路

![1墙0路](http://upload-images.jianshu.io/upload_images/1779926-19df5fd7acfd835b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

数据成员：

	- 墙壁字符
	- 通路字符
	- 迷宫数组

成员函数：

	- 构造函数
	- 数据封装函数
	- 迷宫回执函数
	- 迷宫边界检查函数

- 人类(MazePerson)

数据成员：

 	- 人的字符
 	- 人的朝向
 	- 人当前位置
 	- 人前一个位置（人走动，前位置抹掉。后一个位置重绘）
 	- 人的速度

 成员函数：

 	- 构造函数
 	- 数据封装函数
 	- 向不同方向前进的函数
 	- 转弯函数
 	- 开始函数

 控制台动画控制：

 ```c
/*
 * 函数名称：gotoxy
 * 函数功能：确定控制台中字符的输出位置
 * 函数列表：
 *		x：横坐标
 *		y：纵坐标
 */
void MazePerson::gotoxy(int x, int y)   
{   
	COORD cd;    
	cd.X   =   x; 
	cd.Y   =   y;
	HANDLE handle = GetStdHandle(STD_OUTPUT_HANDLE);  
	SetConsoleCursorPosition(handle,cd);     
};
 ```

 - 枚举类型：方向
 - 常量定义：宏定义 & const

成就感源于困难

源代码下载地址：http://img.mukewang.com/down/55b042fa000161b600000000.rar

未完待续
