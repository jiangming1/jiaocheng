### 课程安排

idea 是 java开发者使用最广泛的IDE之一， 开发者都是工匠，磨刀不误砍柴工。

批量操作，比如查找方法在哪个类，使用IDE可以大大节省时间。

### IDEA下载

idea下载安装与窗口介绍

>官网下载，download,一直点下一步下一步，根据电脑选32位还是64位; 电脑上如果没有java，就勾上下载java，如果有就不勾;没有sdk的选项，去new一个。找到jdk文件夹选择即可。推荐下载idaeVim。

创建项目时，路径和项目名都要进行填写。

- File:文件进行操作
- Edit:对文本进行操作
- View:当前idea有哪些视图
- Navigate:在项目工程中跳转
- Code:源码文件
- Analyze:对源码进行分析,对项目的依赖进行分析
- Refactor: 代码重构时的快捷操作，抽取函数方法，静态类。
- Tools: 工具

导航栏，运行等菜单栏,运行过程中出现信息。

代码中的Todo信息。 可以通过`Alt + 数字`来展开窗口(如2 favorite)等,`esc`返回原来窗口

Favorite，查看代码时添加的标签，断点，收藏。

![](http://myphoto.mtianyan.cn/20180808222019_Z1reGk_Screenshot.jpeg)

database，实时展示数据库。maven构建展示信息。

#### 无处不在的跳转

我的mackeymap设置为Macos10.5+，但不管什么系统，什么keymap设置，右上角快捷键查询，find action(ctrl + shift +a)是万能查询。

![](http://myphoto.mtianyan.cn/20180808230205_hF8zaV_Screenshot.jpeg)

1. 项目之间的跳转。

![](http://myphoto.mtianyan.cn/20180808230049_JW9ljN_Screenshot.jpeg)

当有多个项目时，通过点击next project window。

2. 文件之间的跳转

find action 中查找Recent files;一般为`ctrl + E`; 

![](http://myphoto.mtianyan.cn/20180808230323_7hydZ5_Screenshot.jpeg)

3. 按类名打开文件 

find action中查找: go to by name Action;Windows一般为`ctrl + N` 

![](http://myphoto.mtianyan.cn/20180808230730_YEBOPT_Screenshot.jpeg)

4. 上次编辑过的文件位置

![](http://myphoto.mtianyan.cn/20180808230907_nYezEd_Screenshot.jpeg)

上次编辑的文件跳转,浏览时的跳转是 ALT + 左箭头/右箭头

5. 利用书签进行跳转: 浏览别人代码做一些标记。

![mark](http://myphoto.mtianyan.cn/blog/180708/23l1GD6Fbb.png?imageslim)

find action中查找: Bookmarks

![](http://myphoto.mtianyan.cn/20180808231249_Db0KAb_Screenshot.jpeg)

标记上bookmarks之后可以通过快捷键按标签序号进行跳转

6. 收藏位置和文件

find action中查找: add to favorites 收藏类，收藏函数。

7. 字符跳转插件 emacsIdea跳转字符

find action中查找: plugins,搜索: emacsIdeas 进行安装。restart idea后。插件的作用: 实现快速的字符跳转

keymap中jumpword 为其添加快捷键`shift + j`

![](http://myphoto.mtianyan.cn/20180808231628_vYtvmM_Screenshot.jpeg)

![](http://myphoto.mtianyan.cn/20180808231720_54lDl3_Screenshot.jpeg)

按下shift+j之后，再输入字母，会找到以该字母开头的，输入对应的蓝色字母，光标调整。

![](http://myphoto.mtianyan.cn/20180808232127_7ytOhA_Screenshot.jpeg)

安装ideaVim插件可以进行多窗口编辑

### 精准搜索

1. 搜索类,文件搜索,符号: 函数属性等

![](http://myphoto.mtianyan.cn/20180808232305_nlA1pA_Screenshot.jpeg)

如果要搜索jar包里的类，将include勾选即可

2. 字符串搜索

edit分类下find in path

![](http://myphoto.mtianyan.cn/20180808232658_aFqYDi_Screenshot.jpeg)

match Case 是否匹配大小写，搜索的字符串是否是个单词 regex通过正则表达式来搜索 files mask 只在某一个格式的文件中去搜索

### 代码小助手们学习

批量编辑, 模板生成; 

单词变为全部大写 `ctrl + shift + U` 

![](http://myphoto.mtianyan.cn/20180808233658_c9IsYJ_Screenshot.jpeg)

选中所有相同的

![](http://myphoto.mtianyan.cn/20180808233724_dk8YyH_Screenshot.jpeg)

### live template

位置：菜单->File->Setting->Editor->Live Templates
功能：定义模板，使用特定“字符”快速敲击出某段代码。
举例：psvm 敲出 public static void main(String[] args)
使用：

1. 点击右侧+号。选中Template Group（模板组），自己定一个组。

```
$var1$ $var2$
$END$
```
### postfix

```java
100.fori
```

一个点之后会给你很多提示。

1. foo.fori for(int i = 0; i < foo; i++){}
2. foo.return return foo;
3. foo.sout System.out.println(foo);
4. foo.field private Foo foo; this.foo = foo;
5. foo.nn

### alter + enter

![mark](http://myphoto.mtianyan.cn/blog/180708/EJ3D6gF73c.png?imageslim)

自动完成代码，创建函数等。list replace 是把 for i的初级实现变成foreach的实现。

字符串format 或 build

```
("name: " + name + ",age: " + age)
replace string format
```

实现接口:implement interface;单词拼写建议;导包

### 重构

编写高质量代码

1. 重构变量

abc bcd 改成有意义的名字

![mark](http://myphoto.mtianyan.cn/blog/180708/e4J2h75EEK.png?imageslim)

2. 重构方法

直接添加参数，然后`alt + enter`

### 抽取

3. 抽取变量

代码中重复的出现字符串，最好把这个字符串抽取成一个变量。

![mark](http://myphoto.mtianyan.cn/blog/180709/92bkf0kDmE.png?imageslim)

抽取静态常量，抽取成员变量，抽取方法参数，抽取函数。

### 寻找修改轨迹

git的集成

看别人代码，找到这行代码的作者,Annotate 

查看每一个修改过的地方

>Previous change

![mark](http://myphoto.mtianyan.cn/blog/180709/j8bjH23meD.png?imageslim)

- 撤销，包括单个文件和项目

revert `ctrl + alt + z`

空白处按住`ctrl + alt + z`，撤销整个文件。

local history

### 关联一切

代码编写过程中和常用框架建立起关联

- 与spring进行关联

controller依赖service service依赖DAO UserEntity对应数据库

project structure -> facets ->选中后点击“＋”

- 数据库的关联

会自动关联数据库，然后进行字段提示。 可以直接进行数据库重构。

exclude 进行排除

### 开始调试你的程序吧

- 断点调试

添加断点。

- 单步运行

查看断点

禁止所有断点: mute breakpointer

- 条件断点

打断点时必须满足一定条件才会生效

- 表达式求值

![mark](http://myphoto.mtianyan.cn/blog/180709/LIa3jL0E8g.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180709/bABm64mm0j.png?imageslim)

>直接运行到光标所在行

- setValue

测试等于NUll，在debug的值窗口改值即可

#### run anywhere if you can

debug contest

Ctril + Shift + F9 debug configuration
Alt   + Shift + F9 debug

### 其它操作

文件操作

- 在当前文件同一级目录下面新建文件
- 复制当前文件
- 移动当前文件

#### 文本操作

复制文件名

复制多个文件名

>批量复制文件名可以按住Ctrl键多选文件，然后Ctrl+C就可以了

#### 结构图

- 查看field method 大纲

![mark](http://myphoto.mtianyan.cn/blog/180709/Ihbl04eHci.png?imageslim)

m methos f field

- 查看maven 类图

![mark](http://myphoto.mtianyan.cn/blog/180709/2Id7ffdGKg.png?imageslim)

类关系

![mark](http://myphoto.mtianyan.cn/blog/180709/EfmCCeh69C.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180709/kjIJ171GJ4.png?imageslim)

- 查看类继承结构，方法调用层次

## 课程总结

多练习。
