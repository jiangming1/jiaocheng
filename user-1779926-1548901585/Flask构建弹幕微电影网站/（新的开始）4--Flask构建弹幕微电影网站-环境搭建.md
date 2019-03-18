## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

已上线演示地址: http://movie.mtianyan.cn
项目源码地址：https://github.com/mtianyan/movie_project

# 准备开发环境

## windows环境搭建：

- windows10 (or win7)
- python3.6
- mysql5.7
- 虚拟化环境：virtualenv：`pip install virtualenv`
- 代码编辑器：pycharm（社区版就足够，推荐下载专业版开发django也能用）

![mark](http://upload-images.jianshu.io/upload_images/1779926-df7b8159cad53595.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

mysql安装完成进入之后可以验证字符集。可以看到默认安装的client 字符集
是gbk。后面在数据库连接建表会出问题。到时候提供解决方案。

## macos 环境搭建

1. 操作系统 macos
2. 编程语言 python3.6
3. 数据库：mysql5.6(集成工具 MAMP)
4. 虚拟机环境virtualenv ：`pip install virtualenv`
5. 代码编辑器: pycharm

>博主是穷逼，没有苹果电脑。此处略。自行摸索。

## pycharm编辑器使用，介绍pip下载工具使用

pycharm：新建文件->点击run运行

## virtualenv的使用及flask安装

virtualenv的使用(`pip install virtualenv`)：

- 创建虚拟环境：`virtualenv venv`
- 激活虚拟环境： `source venv/bin/activate`
- 退出虚拟环境 ：`deactivate`

2. flask的安装：

- 安装前检测：`pip freeze`
- 安装flask： `pip install flask`
- 安装后检测：`pip freeze`

error: 如果安装flask报错：

```
UnicodeDecodeError: 'utf-8' codec can't decode byte 0xb6 in position 33: invalid
 start byte
```

>当前虚拟环境\lib\site-packages\pip\compat\__init__.py约75行 
return s.decode('utf_8') 改为return s.decode('cp936')

![mark](http://upload-images.jianshu.io/upload_images/1779926-d39b153e02d9ecc7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 第一个flask程序

点击新建project->movie_project->选择解释环境为刚才新建的`movieproject36`
 
```python
# _*_ coding: utf-8 _*_
__author__ = 'mtianyan'
__date__ = '2017/8/26 16:56'

from flask import Flask

# 创建一个app对象
app = Flask(__name__)

# 定义一个路由
@app.route("/")
def index():
    return "<h1 style='color:red'>Hello World!</h1>"

if __name__ == "__main__":
    app.run()
```

右键点击run之后：控制台画面如下：

![控制台信息](http://upload-images.jianshu.io/upload_images/1779926-15677b0b04c0af49.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击链接可看到红色的helloworld，此时flask安装及运行成功。
