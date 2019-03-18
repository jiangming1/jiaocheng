## 做机也要做一只全能的机哦

值此新年来临之即，面对两百多个G的c盘。忍痛割爱将电脑系统重装，版本为(win10:1079)之后的所有电脑环境更新，专业软件安装均会记录在此文。

- 程序员装机必备爆款软件推荐与配置。
- win10系统使用技巧
## 编程软件篇

### sublimetext

C:\software\Sublime Text 3

包管理地址: https://packagecontrol.io/installation

#### Chinese Localization  汉化

`ctrl + shift + p` 打开`package install` 安装插件` Chinese Localization`即可。

#### Theme - Afterglow 主题

主题设置：

`Sublime Text -> Preferences -> Settings - User:`

```
  {
     "color_scheme": "Packages/Theme - Afterglow/Afterglow-twilight.tmTheme",
    "ignored_packages":
    [
      "Vintage"
    ],
    "theme": "Afterglow-blue.sublime-theme",
      "tabs_small": true,
      "sidebar_size_12": true,
      "color_inactive_tabs": true,
      "tabs_padding_small": true
  }
```
#### Markdown 插件必备
- markdown preview
- markdown LivePrivew
- OmniMarkupPreviewer

#### markdown_snippets
https://praveenpuglia.com/github_markdown_snippets/

#### 自定义snippet设置(Tab补全)

-  加入快捷键`pyb`,快速创建代码块(Tab补全)+`pyb`

工具 -> 插件开发 ->新建代码片段

```
<snippet>
  <content>
  代码模板
</content>
  <!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
  <tabTrigger>pyb</tabTrigger>
  <!-- Optional: Set a scope to limit where the snippet will trigger -->
  <scope>text.html.markdown</scope>
</snippet>
```
- 加入新建hexo博文的快捷模板(tab补全)+`hexopy`

```
<snippet>
  <content>
---
title: ${1:}
date: ${2:}
tags:
- Python
- ${3:}
- ${4:} 
categories: ${5:python从入门到精通}
copyright: true 
---

{% cq %} ${6:} {% endcq %}

{% note  %} ${7:} {% endnote %}

\<!--more-->\
</content>
  <!-- Optional: Set a tabTrigger to define how to trigger the snippet -->
  <tabTrigger>hexopy</tabTrigger>
  <!-- Optional: Set a scope to limit where the snippet will trigger -->
  <scope>text.html.markdown</scope>
</snippet>

```
#### `ctrl + shift +,` sublime插入当前时间快捷键设置
参考： https://stackoverflow.com/questions/11879481/can-i-add-date-time-for-sublime-snippet

- 加入直接插入当前时间的快捷键：`ctrl + shift +,`

新建插件: datetimestamp.py

```python
import datetime, getpass
import sublime, sublime_plugin

class AddDateTimeStampCommand(sublime_plugin.TextCommand):
    def run(self, edit):
        self.view.run_command("insert_snippet", { "contents": "%s" %  datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S") } )

class AddDateStampCommand(sublime_plugin.TextCommand):
    def run(self, edit):
        self.view.run_command("insert_snippet", { "contents": "%s" %  datetime.datetime.now().strftime("%Y-%m-%d") } )

class AddTimeStampCommand(sublime_plugin.TextCommand):
    def run(self, edit):
        self.view.run_command("insert_snippet", { "contents": "%s" %  datetime.datetime.now().strftime("%H:%M:%S") } )
```

用户快捷键设置:

```
[
{"keys": ["shift+ctrl+,"], "command": "add_date_time_stamp" },
{"keys": ["shift+ctrl+."], "command": "add_date_stamp" },
{"keys": ["shift+ctrl+/"], "command": "add_time_stamp" }
]
```

#### sulimeTempel(新建文件模板)插件

未完待续

#### 设置python3环境

默认python2环境为安装Python自动设置的。而python3环境则需要自行配置。

```
{
"encoding": "utf-8",  
"working_dir": "$file_path",  
"shell_cmd": "D:\\softEnvDown\\Anaconda2\\envs\\py3\\python.exe -u \"$file\"",  
"file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",  
"selector": "source.python"
}
```


### cmder(替代windowscmd的神器)

C:\greenSoft\cmder_mini


#### cmder右键菜单添加，修改默认路径为桌面

添加环境变量

在管理员权限下打开cmd，输入以下命令增加右键菜单

Cmder.exe /REGISTER ALL

选择Startup-Task，修改{cmd::Cmder}项，改为:

`cmd /k "%ConEmuDir%\..\init.bat"  -new_console:d:C:\Users\mtian\Desktop`


#### 删除右键菜单(备用)

  ```

  @echo off 
  Reg delete "HKEY_CLASSES_ROOT\Directory\Background\shell\Cmder" /f 
  pause

  cmd /k "%ConEmuDir%\..\init.bat" -new_console:%__CD_

  ```

#### 设置右键打开当前目录cmder

  Startup:specified named task

  shell::cmd `cmd /k “%ConEmuDir%..\init.bat” -new_console:d:%CD%`


### editplus安装

C:\software\editplus

注册码：http://www.98key.com/editplus?sub=mtianyan

### office2016安装

### 7zip

C:\software\7-Zip

### VMware-workstation

https://download3.vmware.com/software/wkst/file/VMware-workstation-full-14.1.0-7370693.exe

### xshell 5

C:\software\Xshell

### 图床软件 mpic

C:\greenSoft\MPic 2.2.1.3

### java8u151

http://www.oracle.com/technetwork/java/javase/downloads/

C:\softEnvir\java

- C:\softEnvir\java\jdk1.8.0_152\bin
- C:\softEnvir\java\jre1.8.0_152\bin

### eclipse-oxygen安装

C:\software\eclipse\java-oxygen


### PyCharm 2017.3.2

C:\Windows\System32\drivers\etc

host文件

0.0.0.0 account.jetbrains.com

添加解释器：

setting - Project Interpreter：

[站外图片上传中...(image-b39a90-1515134228972)]

[站外图片上传中...(image-e2daec-1515134228972)]

一直定位到python.exe点击确认。


## 普通软件
### Tim

安装目录：C:\software\tim
文件接收： C:\Users\mtian\Desktop
聊天记录：C:\softData\QQuser

### chrome浏览器

C:\Program Files (x86)\Google\Chrome\Application

### Shadowsocks & kcptun

C:\Users\mtian\OneDrive\Shadowsocks

### c盘绿色软件记录

C:\greenSoft

- cmder

- pdf password

- 流程图

- 冰点文库


## win10系统使用技巧

### win10开机启动项修改

C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp

### 去除右键windows denfer

`win +r ` regedit
https://jingyan.baidu.com/article/4b52d702ae79affc5c774be3.html


## Python相关环境
anaconda下载：

https://repo.continuum.io/archive/

Anaconda2: 

D:\softEnvDown\Anaconda2\

Anaconda3:

D:\softEnvDown\Anaconda2\envs\py3

注意: py3不是自己创建的新文件夹，而是在路径上打字指定。





