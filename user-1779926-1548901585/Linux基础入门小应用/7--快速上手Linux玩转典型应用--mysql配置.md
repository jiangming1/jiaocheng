8 - mysql配置

| 解释 | 命令 |
| ------------- | ------------- |
| 安装服务端 | yum install mysql-community-server |
| 启动 | service mysqld start/restart |
| 停止 | service mysqld stop |

Centos7默认安装mariadb数据库

被oracle收购了之后。将mysql开源实现

```
yum remove mariadb-libs.x86_64
```

```
yum search mariadb
yum remove mariadb-libs.x86_64
```

- 添加mysql的源

https://dev.mysql.com/downloads/repo/yum/

```
wget https://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
```

- 安装源

```
yum localinstall mysql57-community-release-el7-11.noarch.rpm
```

- 安装mysql的服务

```
yum install mysql-community-server
```


- 查看服务有没有启动起来

```
ps -ef | grep mysql

sudo chown -R mysql:mysql /var/lib/mysql/

systemctl enable mysqld

systemctl start mysqld

```
启动报错 Failed to start mysql.service: Unit not found.

>`sudo chown -R mysql:mysql /var/lib/mysql/`

- 默认密码

```
cat /var/log/mysqld.log | grep password
```

可以看到mysql为我们默认产生的密码：

```
A temporary password is generated for root@localhost: XjgCcwsUl9=L
mysql -uroot -pXjgCcwsUl9=L
```
```
set password = 'ty123456';

更改密码策略为LOW
set global validate_password_policy=0;
更改密码长度
set global validate_password_length=0;
```

因为一般黑客会通过`history` 命令来查看你使用过的命令。

```
mysql -uroot -p
mysql -h127.0.0.1 -uroot -p 
# 通过h指定主机连接
```

- 运程连接

```
show databases;
use mysql;
show tables;
select * from user \G
select Host,User from user \G
```
![mark](http://myphoto.mtianyan.cn/blog/180129/JKId6f1jFh.png?imageslim)

`\G`表示格式化输出。

```
update user set host = '%' where Host = "localhost" and User = 'root';
```

root 账号 允许任何主机进行连接

```
flush privileges;
```

%代表品所有主机。用户为root。除过刷新权限，我们还可以直接将我们的mysql服务直接进行重启。

```
ps -ef | grep firewalld
sudo service firewalld stop
```

将防火墙关闭。

>1. 连接到数据库里面，然后showdatabases。
2. usemysql 修改mysql 
3. update user 
4. flush privileges;
5. firewalld

- 开启Genelog

会记录我们所有操作sql语句的记录。

Mysql>

```
set global general_log_file="/tmp/general.log";
```

设置目录之后开启。

```
set global general_log=on;
```

重新打开一个其他的终端：

```
tail -f /tmp/general.log
```

`-f` 代表循环读取

权限禁止:

```
sudo su -
```

![mark](http://myphoto.mtianyan.cn/blog/180129/8F69jD9EEG.png?imageslim)

>根据我的观察：执行成功的语句会被记录下来.

>两步走； 设置位置 & 开启

- 新建用户和权限操作

mysql>

```
create user 'mtianyan'@'%' identified by 'ty123456'
```

![mark](http://myphoto.mtianyan.cn/blog/180129/CgjKD0B6fG.png?imageslim)

可以看到这时候我们的新用户并没有所有表的权限。

```
grant all privileges on *.* to 'mtianyan'@'%' identified by 'ty123456' with grant option;

flush privileges;
```

`*.*`代表任意库的任意表，将权限赋予'mtianyan'用户使用任何主机都可以访问。

如果只想赋予一部分权限:

```
grant select,insert on *.* to 'mtianyan'@'%' identified by 'ty123456' with grant option;
```

收回权限

```
revoke all privileges on *.* from mtianyan;
flush privileges;
```

- 忘记root密码怎么办？

```
sudo vim /etc/my.cnf

最后添加一行 skip-grant-tables

sudo service mysqld restart
```

输入空密码回车即可进入。

```
use mysql;
show tables;

set password = "ty158917"

set password = password("ty158917")

当我们第一次安装数据库时我们可以通过上面的set命令来更新我们的密码。

当我们已经用过数据库。得使用其他方式进行更新操作。
```

```
update user set authentication_string = password("ty158917") where user = 'root';
flush privileges;
```

将我们刚才设置的跳过表验证删除。






