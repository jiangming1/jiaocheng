##安卓基础知识
###1. 目录结构：
 - src存放java源代码。
 - gen存放系统自动生成的配置文件      
 - res存放应用用到的所有资源文件，如图片，布局等等
 - drawable存放不同分辨率的图片
 - layout存放布局文件
 - values存放字符串strings.xml、主题，颜色、样式styles.xml等资源文件
 - andriodmanifest.xml清单文件.配置一些与应用有关的重要信息，包括包名，权限，程序组件等等
 - libs放置应用的第三方的jar包
 - res存放应用到的所有资源
 - bin存放apk文件，以及应用到被打包到apk中的资源文件
 - asssets存放资源文件，不会自动生成id且不会自动占用空间
 - menu是存放与菜单有关的布局文件

###2. 控件：
>TextView 和 EditText 的应用和区别：
  TextView：显示文本框（显示静态文本）
  EditText：输入文本框（输入文本）

####控件的属性
 - android:id -- 控件的 ID
 - android:layout_width -- 控件的宽度
 - android:layout_height -- 控件的高度
 - android:text -- 文本内容
 - android:textSize -- 文本大小
 - android:textColor -- 文本颜色
 - android:background -- 控件背景
 - android:hint -- 输入显示文本
 - android:inputType -- 输入文本类型

####在res/layout目录中设置布局文件
 - hint属性:提示输入信息
 - text属性：与hint的区别---hint仅仅是提示；
 - text是实际的内容
 - 控件宽度属性layout_width的三个属性：
 - wrap_content:包裹实际文本内容
 - match_parent:当前控件铺满父类容器：2.3API之后添加的一个属性
 - fill_parent:当前控件铺满父类容器：2.3API之前添加的一个属性

####过关评测：
 >在Activity中需要获取一个id是bookName的TextView控件对象,
使用findViewById方法获取一个View对象，参数为控件的id，同时还需要把View对象强制转换成相应的类型

>一般情况下，布局属性与对象的setXXX方法一一对应，
比如设置字体颜色，在xml中属性是android:textColor，
而在Activity中则是setTextColor方法

>设置EditText的android:inputType属性可以限制文本输入类型比如
```
android:inputType="textPassword"为设置输入格式为密码格
android:inputType="phone"为设置输入格式为拨号键盘
```
###3. ImageView：
是显示图片的一个控件
ImageView控件：
```
A:android:src="@drawable/ic_launcher"--->ImageView的内容图像
B:android:background="@drawable/ic_launcher" --->ImageView背景图片
C:ardroid:background="#00ff00"  --->ImageView的RGB颜色 
android:orientation="vertical" -->布局内垂直摆放
```

####Button和ImageButton特征：
>1. 共有的特征
  都可以作为一个按钮产生点击事件
2. 不同点：
  - Button有text的属性，ImageButton没有
  - ImageButton有src属性，Button没有
3. 产生明显的点击效果

####使用中文字符声明
>一般不在activity_main.xml中直接使用中文，习惯在strings.xml文件中先声明然后在activity_main.xml中通过android:text="@string/buttonname"来使用

###4. 监听事件：
####onClick事件：
1. Button和ImageButton都拥有一个onclick,
通过自身的.setOnClickListener(OnClickListener)方法添加点击事件
2. 所有的控件都有一个onclick的事件，不仅仅Button和ImageButton拥有
3. 通过点击事件的监听可以实现点击按钮之后要发生什么动作

监听事件实现的几种写法：
>1. 匿名内部类的实现
2. 独立类的实现：
所有按钮都要实现的事件。
3. 实现接口的方式来实现

**onc关键字调用自己独立类实现的onclick方法。（android studio）**
```
Toast.makeText(MainActivity.this,"bt1要执行的逻辑", 1).show();
Log.i("tag","父类的onclick事件");
设置透明度:view.setAlpha(0.5f);
```

1. 通过匿名内部类new OnClickListener,常用
2. 自己写外部类接入OnCLickListener,绑定监听事件时new自己书写的外部类，这样相当于有一个独立监听事件的和一个共有的父类监听两个执行动作
3. 通过接口直接在class中接入Onclicklistener,然后在绑定监听后用this 指向类中接入的onclick方法
```
public class MainActivity extends AppCompatActivity implements View.OnClickListener
```
###5. textview
 1. 定义textView标签的4个属性：
```
 android:singleLine="true"//使其只能单行
 android:ellipsize="marquee"//去掉省略号
 android:focusable = "true"//使其循环
 android : focusableInTouchMode = "true"
```
 2. 设置跑马灯效果
```
        android:singleLine="true"
        android:ellipsize="marquee"
        android:focusable="true"
        android:focusableInTouchMode="true"
```
 3. 自定义类继承TextView：
   >实现三个构造函数；
   复写isfocued方法，返回true（默认都有有焦点，平常只有一个有焦点在           第一行上）

**使用自定义的类，方法是用包名和自定义类名代替TextView(src下的包名+类名）**

单位：
> px / dp /dip 
 推荐使用dp
 文字推荐使用sp

####6. AutoCompleteTextView控件& MultiAutoCompleteTextView：
1.功能：
  动态匹配输入的内容，如百度搜索引擎输入文本时可以根据内容显示匹配的热门信息
2.独特属性：
```
 android：completionThreshold="2" --->设置输入多少字符时自动匹配
```
 AutoCompleteTextView是像百度搜索框那样的，
 MultiAutoCompleteTextView像qq邮箱收件人，可以多次输入和检索那样的

 MultiAutoCompleteTextView控件：
 独特属性：
```
  android:completionThreshold="2" -->设置输入多少字符时自动匹配
```
设置分隔符
```
  mtxt.setTokenizer(new MultiAutoCompleteTextView.CommaTokenizer());
  mltv.setTokenizer(new MultiAutoCompleteTextView.CommaTokenizer());

```
###ToggleButton控件：

1. 什么是ToggleButton：
  ToggleButton有两种状态：选中和未选中状态并且需要为不同的状态设置不同的显示文本
2. ToggleButton属性：
```
  android:checked="true"
  android:textOff="关"
  andriod:textOn="开"
```
###CheckBox 复选框
 1. 两种状态:选中状态(true)
           未选中状态（false）
 2. 属性
```
      android:id="@+id/checkbox"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:chrck="false"
      android:text="男"
```
使用onCheckedChange(...)方法监听CheckBox的状态改变事件
如果想自定义CheckBox的样式应该设置style属性
如果想CheckBox默认被选中，则需要设置android:checked="true"

####RadioGroup和RadioButton
RadioButton按下去就起不来,不适合单独使用
RadioGroup和RadioButton控件：
1. RadioGroup:
   RadioButton的一个集合，提供多选一机制
2. 属性：
```
   android:orientation={
   "vertical-->垂直排布"
   "horizontal-->水平排布"
} 
```
决定当前RadioGroup中RadioButton以什么形式排列

vertical是垂直排列，horizontal是水平排列

###布局概述
1. 布局决定页面里内容的排布方式
2. 布局文件位于layout文件夹下
3. 线性布局（LinearLayout）：可添加子类控件

一、TextView 显示文本框 
二、AutoCompleteTextView
自动匹配输入内容-：completionThreshold=“1”设置输入的几字节自动匹配
三、MultiAutoCompleteTextView
自动匹配输入内容，可支持选择多个内容，可多选

layout_gravity调整该布局相对父布局的位置
gravity是调整该布局中子布局的位置

**LinearLayout常用的两个属性：**
使控件水平或垂直排布
1. android:orientation="vertical"
  --->该属性决定它子类控件的排布方式（vertical：垂直；horizontal：水平）
gravity是父容器LinearLayout指定包含控件位置所用的属性
layout_gravity是控件自己设置在当前LinearLayout中的位置
2. android：gravity="center"androdi:gravity的属性可以设置多个，用"|"分隔
  --->该属性决定他子类的xy的位置（常用到的几个属性值如下：）
  （1）、center_vertical:垂直（Y轴）居中
  （2）、center_horizontal:水平（X轴）居中
  （3）、center:水平垂直都居中
  （4）、right：子类控件位于当前布局的右边
  （5）、left：子类控件位于当前布局的左边
  （6）、bottom：子类控件位于当前的下面
**子类控件:**
android:layout_weight="1"本身控件占当前父容器的一个比例
android：layour_gravity="bottom"本身控件占当前父容器的xy的一个位置
如果两个控件都是铺满设置。那么权重比例将与大小相反

当作为父layout的LinearLayout的属性为androidrientation="vertical" 的时候，android:layout_gravity="？"这里设为横向的时候才能生效。比如：left，right，center_horizontal等

当作为父layout的LinearLayout的属性为androidrientation="horizental" 的时候，android:layout_gravity="？"这里设为纵向的时候才能生效。比如：top，bottom，center_vertical等；

有一个比较特殊的是center，不管是横向还是纵向的时候，它总有一个方向起作用

####布局可以嵌套布局

水平的LinearLayout要从左边开始依次放置布局，所以设置TextView的layout_gravity属性为right不起作用

相对布局的属性（子类控件在RelativeLayout中常用到得属性（相对父容器的一个位置））：
```
1. android:layout_alignParentLeft="true" 子类控件相对当前父类容器靠左边
2. android:layout_alignParentTop="true" 子类控件相对父类容器靠上边
3. android:layout_marginLeft="41dp" 子类控件距离父类容器左边的距离
4. android:layout_marginTop="33dp" 子类控件距离父类容器上边的距离
5. android:layout_centerInParent="true" 子类控件相对父类容器即水平居中又垂直居中
6.android:layout_centerHorizontal="true" 子类控件相对父类容器水平居中
7.android:layout_centerVertical="true" 子类控件相对父类容器垂直居中
```
第一类:属性值为true或false
```
    Android:layout_centerHrizontal                                           
    水平居中
    android:layout_centerVertical                                            
    垂直居中
    android:layout_centerInparent                                          
    相对于父元素完全居中
    android:layout_alignParentBottom                                     
    贴紧父元素的下边缘
    android:layout_alignParentLeft                                          
    贴紧父元素的左边缘
    android:layout_alignParentRight                                        
    贴紧父元素的右边缘
    android:layout_alignParentTop                                          
    贴紧父元素的上边缘
    android:layout_alignWithParentIfMissing                           
    如果对应的兄弟元素找不到的话就以父元素做参照物
```
第二类控件相对于控件的：属性值必须为id的引用名“@id/id-name”
```
    android:layout_below                          在某元素的下方
    android:layout_above                          在某元素的的上方
    android:layout_toLeftOf                       在某元素的左边
    android:layout_toRightOf                     在某元素的右边
    android:layout_alignTop                      本元素的上边缘和某元素的的上边缘对齐
    android:layout_alignLeft                      本元素的左边缘和某元素的的左边缘对齐
    android:layout_alignBottom                 本元素的下边缘和某元素的的下边缘对齐
    android:layout_alignRight                    本元素的右边缘和某元素的的右边缘对齐
```
    第三类：属性值为具体的像素值，如30dip，40px
```
    android:layout_marginBottom              离某元素底边缘的距离
    android:layout_marginLeft                   离某元素左边缘的距离
    android:layout_marginRight                 离某元素右边缘的距离
    android:layout_marginTop                   离某元素上边缘的距离

    android:layout_margin="40dp"
    android:layout_marginLeft="40dp"
    android:layout_marginTop="40dp"
    android:layout_marginRight="40dp"
    android:layout_marginBottom="40dp"
```

####相对布局（子类控件相对子类控件的一个位置）：

1. android:layout_below="@+id/button1" 该控件位于给定id控件（button1）的底部

2. android:layout_torightOf="@+id/button1" 该控件位于给定id控件的右边

3. android:layout_above="@+id/button1" 该控件位于给定id控件的上面

4. android:layout_toLeftOf="@+id/button1" 该控件位于给定控件的左边

5. android:layout_alignBaseline="@+id/button1" 该控件的内容与给定id控件的内容在一条线上
6. android:layout_alignBottom: 该控件的底部边缘与给定id控件的底部边缘对齐

7. android:layout_alignLeft: 该控件的左边缘与给定id控件的左边缘对齐

8. android:layout_alignRight: 该控件的右边缘与给定id控件的右边缘对齐

9. android:layout_alignTop: 该控件的顶部边缘与给定id控件的顶部对齐

####RelativeLayout布局中的子布局有gravity属性

1. android:gravity：是针对控件里的元素来说的，用来控制元素在该控件里的显示位置。例如，在一个Button按钮控件中设置如下两个属性，android:gravity="left"和android:text="提交"，这时Button上的文字“提交”将会位于Button的左部。

2. android:layout_gravity：是针对控件本身而言，用来控制该控件在包含该控件的父控件中的位置。同样，当我们在Button按钮控件中设置android:layout_gravity="left"属性时，表示该Button按钮将位于界面的左部。
**@+id是在R.java中添加id，@id表示引用现有id**
toLeftOf是在左边，toRightOf是在右边

```
android:gravity="center_horizontal"
A是设置子布局中内容居中
android:gravity="center_vertical|left "
B是子布局中内容左侧居中
android:layout_gravity="center_vertical|left"
C是子布局相对父布局左侧居中
layout_centerInParent是设置子布局相对父布局居中
应该是与android:layout_gravity="center_vertical|center_horizontal"类似
```


###FrameLayout（帧布局）特性：
>1、所有放在布局里的控件，都按照层次堆叠在屏幕的左上角；
2、后加进来的控件覆盖前面的控件；
3、由1和2确定：帧布局没有gravity，以及layout_gravity属性；
4、可以在控件中使用layout_gravity定义控件的放置方式

B帧布局中先添加的布局在下面

android:foreground(设置前景图，在所有子视图的前面)
android:background(设置背景)
android:keepScreenOn(保持屏幕唤醒)
android:foregroundGravity(设置前景图的位置)

###五布局之绝对布局（AbsoluteLayout）:

1. AbsoluteLayout(绝对布局)又可以叫做坐标布局，可以直接指定子元素的绝对位置（xy）

2. 由于手机屏幕尺寸差别比较大使用绝对定位的适应性会比较差，在屏幕的适配器上有缺陷
```
android：layout_x="**dp" 控制当前子类控件的x位置
android:layout_y="**dp" 控制当前子类控件的y位置
```
屏幕的（0，0）点在屏幕左上角位置
绝对布局中 X，Y值越大位置会靠右下

###五布局之表格布局（TableLayout）:
TableLayout表格布局模型以行列的形式管理子控件，每一行为一个TableRow的对象，当然也可以是一个View的对象。
TableLayout的属性（全局属性）：
1. android:collapseColumns="1,2" 
  -->隐藏从0开始的索引列。列直接必须用逗号隔开：1，2,5
2. android:shrinkColumns="1,2"
  -->收缩从0开始的索引列。当可收缩的列太宽（内容过多）不会被挤出屏幕，列直接必须用逗号隔开：1,2,5你可以通过 "*" 代替收缩所有列。注意一列能同时表示收缩和拉伸。
3. android:stretchColumns="1,2"
  -->拉伸从0开始的索引列。以填满剩下的多余空白空间，列直接必须用逗号隔开：1,2,5，你可以通过 "*" 代替收缩所有列。注意一列能同时表示收缩和拉伸。
TableLayout的局部属性（内部控件所用属性）

1.android:layout_column="1" -->该控件显示在第2列
2.android:layout_span="2" -->该控件占据2列宽

**布局：线性布局，相对布局，帧布局，绝对布局，还有表格布局**
开发中主要用线性布局和相对布局。
表格布局用于计算机布局等！
帧布局主要用于页面加载，霓虹灯等
绝对布局不怎么用
