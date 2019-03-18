## 【博主推荐】免费学习《Flask零基础到项目实战》视频教程：http://dwz.cn/llgX3LQt

- Centos7(博主使用的是ubuntu)
- 数据库: mysql
- 编程语言: python3.6
- 队列缓存: redis
- web反向代理: nginx
- 依赖环境: flask pymysqlclient wtform sqlachemy redis

![mark](http://myphoto.mtianyan.cn/blog/180301/bH8DkAHj3E.png?imageslim)


```
cd /usr/bin
```

![mark](http://myphoto.mtianyan.cn/blog/180301/GI1fAaC5E1.png?imageslim)

可以看到腾讯云已经默认安装有python3.5的环境

使用Nginx作为Python Web的反向代理实战

>https://www.imooc.com/article/19538

动手实战实现Redis数据库主从同步

>https://www.imooc.com/article/19536

nginx结合jwplayer实现视频流媒体点播

>https://www.imooc.com/article/19452


## centos安装lnmp环境

![mark](http://myphoto.mtianyan.cn/blog/180302/B5eLf2743l.png?imageslim)

```
ssh root@ip

# 查看服务器配置(centos)
cat /etc/redhat-release

# 查看内存剩余
free -m

# 查看硬盘剩余
df -h
```

安装python3.6的依赖包

``` 
yum -y install openssl-devel bzip2-devel expat-devel gdbm-devel readline-devel
sqllite-devel
```
下载python3.6.1的包

然后解包

```
tar -zxf python-3.6.1.tgz
cd python-3.6.1
./configure -- prefix=/usr/local

yum -y install gcc gcc-c++

make altinstall
```

更改python链接

```
cd /usr/bin

mv python python.backup

# 创建软连接
ln -s /usr/local/bin/python3.6 /usr/bin/python
ln -s /usr/local/bin/python3.6 /usr/bin/python3

cd /usr/bin/
ls yum*
vim yum
```

修改第一行尾python2

```
vim yum-config-manager
```

修改第一行尾python2

```
vim yum-debug-restore
vim yum-group-manager
vim yum-build-dep
vim yum-debug-dump
vim yumdownloader
vim /usr/libexec/urlgrabber-ext-down
```

修改第一行尾python2

此时输入python就是3.6的python了。

博主的腾讯云此时输入python3.5是python环境

### 安装mysql

centos

```
yum -y install mariadb-server

# 启动
systemctl start mariadb.service

# 设置开机自启
enable mariadb.service
```

```
mysqladmin -uroot password "root"
mysql -uroot -proot
```

修改mysql的字符集

```
vim /etc/my.cnf
```

mysqld节点添加

```
character--set-server=utf8
```

然后restart

```
systemctl restart mariadb.service
```

### 安装nginx

centos下:

拷贝我们的nginx安装包到服务器

```
scp nginx.tar.gz root@192.0.x.x:/root/
```

安装依赖包

``` 
yum -y install gcc gcc-c++ openssl-devel pcre-devel httpd-tools
```

```
tar -zxf nginx.tar.gz
cd nginx/

useradd nginx
./configure -- prefix=/usr/local/nginx --user=nginx --group=nginx --with-http_ssl_module --with-http_mp4_module --with-http_flv_moudle

make && make install

ln -s /usr/local/nginx/sbin/nginx /usr/sbin/
```

启动nginx

```
nginx
netstat -anptu | grep nginx
```

## 部署微电影网站

1. 安装依赖包
2. 关闭调试模式 app.debug = False
3. 修改mysql数据库连接，导入数据
4. 开启多个端口后台运行
5. 配置nginx 反向代理

首先manage.py中从flask_script 中

```python
pip install flask-script
from flask_script import Manager

manage = Manager(app)

if __name__ == "__main__":
    manage.run()
```

命令行工具。

修改服务器数据库连接。

使用xshell的xz命令上传req.txt

```
mkvirtualenv --python=python3 movie
pip install -r req.txt
```

把项目zip打包上传到服务器

```
unzip movie_project
```

导入数据

```
create database movie;
use movie;
source /root/movie.sql;
```

上传nginx的配置文件

```
worker_processes 4;
events {
    worker_connections 262140;
}
http {
    include mime.types;
    default_type application/octet-stream;
    sendfile on;
    keepalive_timeout 65;
    limit_conn_zone $binary_remote_addr zone=addr:5m;
    upstream movie {
        server 127.0.0.1:5001;
        server 127.0.0.1:5002;
        server 127.0.0.1:5003;
        server 127.0.0.1:5004;
    }
    server {
        listen 80;
        server_name movie.mtianyan.cn;
        location / {
            root html;
            index index.html index.htm;
            proxy_pass http://movie;
        }
        location ~ \.flv$ {
            flv;
            limit_conn addr 4;
            limit_rate 1024k;
            rewrite ^/static/uploads/(.+?).flv$ /movie_project/app/static/uploads/$1.flv permanent;
        }
        location ~ \.mp4$ {
            mp4;
            limit_conn addr 4;
            limit_rate 1024k;
            rewrite ^/static/uploads/(.+?).mp4$ /movie_project/app/static/uploads/$1.mp4 permanent;
        }
        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
            root html;
        }
    }
}
```

将项目拷贝到nginx的html目录

```
cp -r movie_project /usr/local/nginx/html
或
cp -r movie_project /usr/share/nginx/html

```

找到nginx的目录

```
cp nginx.conf /usr/local/nginx/conf/

ubuntu: cp nginx.conf /etc/nginx/conf.d
```

nginx -s stop

```
cd /usr/local/nginx/html/movie_project

python manage.py runserver
```

报错

```
nohup: ignoring input and appending output to '/home/ubuntu/nohup.out'
```

代码有错误，路径没加全。

然后开上四个进程。换端口就行

```
nohup python manage.py runserver -h 139.199.189.211 -p 5001
```

## 流媒体访问限制

1. 限制单个ip能发起的连接: limit_conn addr 1;
2. 限制视频速率: limit_rate 1024k;
3. 刷新nginx nginx -s reload
