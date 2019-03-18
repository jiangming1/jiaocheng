## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

演示地址： http://mxonline.mtianyan.cn

>教程仓库地址1: https://github.com/mtianyan/DjangoGetStarted
教程仓库地址2: https://github.com/mtianyan/Mxonline2
教程仓库地址3: https://github.com/mtianyan/Mxonline3

# 把项目最终部署上线所需命令

nginx + uwsgi(Python) Tomcat(java)

端口转发。负载均衡。
静态文件交给nginx转发。静态文件代理

```
sudo apt-get install nginx
ps aux | grep nginx
ifconfig
```

```
sudo apt-get install mysql-server
提示:输入用户名密码
ps aux | grep mysql
```

```
mysql -u root -p
showdatabases
```

修改`bind address`为`0.0.0.0` 是为了让win进行连接。真正部署尽量127.0.0.1

```
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf

sudo service mysql restart

ifconfig
```
 
然后Navicat中连接会报错

你不用localhost(127)。使用本机ip都是不行的。

`*.*`里面是可以指定某一张表。root用户名 IP地址
通过该IP地址过来的root用户，通过密码才可以访问所有表。
'%'表示所有ip可以访问。

mysql下运行

```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'tp131861' WITH GRANT OPTION;

flush privileges;
```
1. 安装pip

```
wget https://bootstrap.pypa.io/get-pip.py  --no-check-certificate
sudo python get-pip.py
```

```
pip install virtualenv
pip install virtualenvwrapper

workon

vim ~/.bashrc

export WORKON_HOME=$HOME/.virtualenvs
source /usr/local/bin/virtualenvwrapper.sh

source ~/.bashrc

workon mxonline2

pip freeze > requirements.txt

pip install -r requirements.txt

sudo pip install virtualenvwrapper

sudo apt-get install libmysqlclient-dev

pip install -i https://pypi.douban.com/simple pillow==3.4.1

pip install uwsgi

uwsgi --http :8000 --module MxOnline.wsgi(暂时不管报错)

python manage.py runserver

数据库设置

数据传输

```
sudo ln -s /mnt/Mxonline2/uc_nginx.conf /etc/nginx/conf.d/

```

将setting中 static路径指明。然后将staticDIRS注释掉

项目根目录创建conf文件夹。然后创建uwsgi.ini.

`uwsgi -i /mnt/Mxonline2/conf/uwsgi.ini &`
