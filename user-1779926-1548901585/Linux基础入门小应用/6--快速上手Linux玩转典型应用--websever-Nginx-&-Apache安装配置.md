7- websever安装配置

Nginx & Apache

并发量。

## Apache基本操作

| 解释 | 命令 |
| ------------- | ------------- |
| 安装 | yum install httpd |
| 启动 | service httpd start |
| 停止 | service httpd stop |

centos中它叫httpd。ubuntu中就叫Apache

虚拟主机 伪静态

```
sudo install httpd
sudo service httpd start
ps -ef|grep httpd
sudo service httpd restart
```

此时进入浏览器访问无法访问到我们的Apache。

```
sudo netstat -anpl | grep http
```

可以看到端口运行也是正常的。

防火墙机制。关闭防火墙

```
sudo service firewalld stop
```

此时通过`http://192.168.0.7/`可以访问到我们的Apache页面

## 虚拟主机

我们一个网站服务器希望有很多不同的域名

```
cd /etc/httpd/
ls
cd conf
vim httpd.conf
```

config.d 是配置文件 。config.modules.d是配置模块的文件。

![mark](http://myphoto.mtianyan.cn/blog/180128/CddB22djH3.png?imageslim)

监听80端口，模块下的任意conf文件

![mark](http://myphoto.mtianyan.cn/blog/180128/5k27i5bc6H.png?imageslim)

根目录是放在var/www/html 

搜索virtual 。esc之后输入"/virtual"回车

```
<VirtualHost *:80>
        ServerName www.mtianyan.test
        DocumentRoot /data/www
        <Directory "/var/www/html">
                Options Indexes FollowSymLinks
        	AllowOverride None
        </Directory>
</VirtualHost>

```

配置我们的域名和根目录

```
 sudo service httpd restart
```

重启httpd服务。

```
mkdir -p data/www
```

添加-p参数新建级联目录。

data目录所属用户是在root账号。如果对于目录的操作权限不够可以使用

```
sudo chown -R mtianyan:mtianyan /data
```
chown 改变文件或目录所有者。`-R`是递归的方式。

此时无法访问。我们的域名是虚构的，所以需要使用host

```
sudo vim /etc/hosts
```

C:\Windows\System32\drivers\etc

此时访问仍然是Apache的页面。

```
cd /etc/httpd/
cd logs/
```

权限禁止。

```
sudo su -
```

- 是一个环境变量切换到root用户

报错:

```
[Sat Jan 27 15:41:15.482043 2018] [authz_core:error] [pid 4847] [client 192.168.0.12:11981] AH01630: client denied by server configuration: /data, referer: http://www.mtianyan.test/
```

```
sudo setenforce 0
```

系统关于安全的一个设置。0宽松模式

```
sudo vim /etc/selinux/config
```

修改为disabled宽松模式。

伪静态: .html看起来像是静态的。

/loadMoudle

mod_rewrite

![mark](http://myphoto.mtianyan.cn/blog/180128/j9jk0D3iGc.png?imageslim)

## Nginx基本操作

| 解释 | 命令 |
| ------------- | ------------- |
| 安装 | yum install nginx |
| 启动 | service nginx start |
| 停止 | service nginx stop |
| 重载 | service nginx reload |

### 虚拟主机

```
yum install nginx
yum search nginx
```

```
sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
```

```
sudo yum install -y nginx
```

reload是一种无缝的重载。

```
 cd /etc/nginx
 sudo vim nginx.conf
 cd conf.d
 sudo vim default.conf
 sudo cp default.conf mtianyan.conf
```

新建一个我们自己的配置文件.mtianyan.conf

内容如下；

```
server {
    listen       80;
    server_name  www.mtianyan.test;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /data/www;
        index  index.html index.htm;
    }
}
```

不能同时启动nginx和Apache。可以看到

```
Redirecting to /bin/systemctl start nginx.service
Job for nginx.service failed because the control process exited with error code. See "systemctl status nginx.service" and "journalctl -xe" for details.

```

```
sudo systemctl status nginx.service
```
执行提示我们执行的命令之后我们可以看到提示80端口已经被占用。

一个端口只能被一个服务器所使用。


### 多域名多端口

多端口

```
    listen       80;
    listen       9999;
```

多域名

```
server_name  www.mtianyan.test www.mtianyan2.test;
```

### 伪静态

```
location/ { 
	rewrite ^(.*)\.htmp$ /index.html;
}
```

任意的以.htmp结尾的都去index.html

\代表转义。

### 日志格式化

nginx.conf文件进行日志格式化

```
log_format
```

![mark](http://myphoto.mtianyan.cn/blog/180128/fiDIiJABDe.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180128/2J1b046Jb0.png?imageslim)

自己新建的日志格式要保存一致。

希望自己的虚拟主机的日志存放在自己自定义位置mtianyan.conf中 server下

```
access_log /var/log/nginx/
```

### 反向代理和负载均衡

请求网站是代理返回过来的。我们请求nginx, nginx向另一台服务器请求资料。

负载均衡。网站请求量比较大，两台机器一起服务。

演示: 

1. 将我们的rewrite注释掉

```
upstream mtianyan_hosts{
	server 192.168.0.7:80 weight=5; 
	server 192.168.0.8:80 weight=1;
}

server{

	location /{
		proxy_set_header Host blog.mtianyan.cn
		proxy_pass http://mtianyan_hosts;
	}
}
```

轮流进行发送请求。以5比1的比例。负载均衡解决网站压力分流。


### 调试技巧

看配置是否出错。

```
add_header Content-Type "text/plain;charset=utf-8";
return 200 "$http_host";
```

## 回顾

httpd 

service 启动 关闭 停止

虚拟主机 伪静态 rewrite 

virtual 

nginx reload

多域名多端口，伪静态。诶之格式化nginx.conf log_format

反向代理 & 负载均衡。

