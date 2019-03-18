好久没碰过php了，那本《PHP从入门到精通》都被放在地上压箱子用了。今天被老师拖着要安装lnmp和h5ai做一个文件服务器。
##1. 安装lnmp
[lnmp官网安装教程](https://lnmp.org/install.html)
安装完成之后。
**注意记录mysql密码**
我就悲催的忘记了。如果忘记lnmp的mysql密码：
>[lnmp 的mysql密码重置](https://lnmp.org/tag/mysql%E5%AF%86%E7%A0%81%E9%87%8D%E7%BD%AE)

##2. 添加虚拟主机
[LNMP添加、删除虚拟主机及伪静态使用教程](https://lnmp.org/faq/lnmp-vhost-add-howto.html)

##3. 安装ftp服务器
[LNMP下FTP服务器的安装和使用(Pureftpd和Proftp)](https://lnmp.org/faq/ftpserver.html)

##4. 上传h5ai到当前域名目录
www.mtianyan.cn
  ├─ _h5ai
> 访问http://域名/_h5ai/public/index.php

查看是否能访问成功。

##5. 修改nginx配置文件。

**!!!重点：修改当前域名下的，否则会报403 Forbidden错误找不到**

**!!!重点：修改当前域名下的，否则会报403 Forbidden错误找不到**

**!!!重点：修改当前域名下的，否则会报403 Forbidden错误找不到**

重要的话说三遍。
添加的虚拟主机配置文件：`/usr/local/nginx/conf/vhost/域名.conf`
此时访问域名可以查看到下图所示。h5ai只有标题栏没有目录内容

![下方没有文件](http://upload-images.jianshu.io/upload_images/1779926-a7fcbe86b3860ba2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##6. 解决h5ai不显示目录内容问题

修改`PHP配置文件：/usr/local/php/etc/php.ini`

将scandir从disabled中删除掉。

##7. 最终成果（h5ai与ftp中文件目录）
![ftp中文件目录](http://upload-images.jianshu.io/upload_images/1779926-b2eed96af12b7d59.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![网页端显示](http://upload-images.jianshu.io/upload_images/1779926-dde3c2ef38efdcc9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##附录，可能要用到的命令：

###vim下命令：

- `i `插入
- `esc` 退出模式
- `：wq! `保存
-  `/scandir` 搜索该字符

###linux下命令：

-  增加用户并为其创建目录
`sudo useradd -g root 用户名  -m -s /bin/bash `


-  为该用户增加root权限：

root@ubuntu:~# sudo vim /etc/sudoers
修改文件如下：
```
#User privilege specification
root ALL=(ALL) ALL
mtianyan ALL=(ALL) ALL
```
保存退出，mtianyan用户就拥有了root权限。
