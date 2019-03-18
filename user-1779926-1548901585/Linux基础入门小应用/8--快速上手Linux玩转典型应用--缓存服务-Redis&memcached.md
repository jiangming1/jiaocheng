9- 缓存服务

数据库服务承载服务量有限。

在程序和数据库之间增加缓存服务层。

Redis & memcached

## memcached 基本操作

| 解释 | 命令 |
| ------------- | ------------- |
| 安装 |  yum install memcached |
| 启动 | memcached -d -l -m -p |
| 停止 | kill pid |


`-d` 以后台守护进程进行运行。 `-m`是分配多大的内存。
`-l` 监听哪些服务器地址。 `-p` 端口号

11211 服务端口

## Redis 基本操作

| 解释 | 命令 |
| ------------- | ------------- |
| 安装 | 源码编译安装 |
| 启动 | redis-server start/restart |
| 停止 | redis-server  stop |
| 客户端 | redis-client |

线上环境推荐使用yum install


Redis 不仅仅支持简单的k/value类型的数据,同时还提供list set hash 等数据结构的存储

Redis支持数据的备份，即master-slave 模式的数据备份

Redis数支持数据的持久化, 可以将内存中的数据保存在磁盘中。重启的时候可以再次加载进行使用。

```
sudo yum install memcached
ps -ef|grep memcache
man memcached
memcached
ps -ef|grep memcache
sudo netstat -anpl | grep memcache
```

`d`结尾一般代指这是个服务。Nginx Apache是为了提供网页服务解析。转发给我们的python java

![mark](http://myphoto.mtianyan.cn/blog/180129/4LIKDi4kAD.png?imageslim)

可以看到监听的端口是11211

命令行方式与mem 进行交互

```
telnet
yum install telnet.*
```

![mark](http://myphoto.mtianyan.cn/blog/180129/0fFLejiHj6.png?imageslim)

>Telnet可以判断我们的ip是否畅通

```
telnet 127.0.0.1 80
telnet 127.0.0.1 3306
telnet 127.0.0.1 11211
```

可以发送简单的报文数据到我们连接的。

![mark](http://myphoto.mtianyan.cn/blog/180129/amkIBBHG38.png?imageslim)

存储数据到mem & 获取数据 & 删除

![mark](http://myphoto.mtianyan.cn/blog/180129/Ff0LlcBBe4.png?imageslim)

60秒自动过期。

服务启动后控制台被占用。无法进行下面的输入。所以启动时使用`-d`参数使其成为守护进程。

```
set mtianyan 0 60 10
helloworld
```

key :mtianyan vlaue:helloworld

quit 退出mem终端

windows 和 xshell的本地shell都有。可以用来检验服务端口是否畅通.

![mark](http://myphoto.mtianyan.cn/blog/180129/1Ag9B0HlGJ.png?imageslim)

## Redis基本操作

源码编译。

![mark](http://myphoto.mtianyan.cn/blog/180129/eBdfj34GAC.png?imageslim)

点击右键复制链接

http://download.redis.io/releases/redis-4.0.7.tar.gz

进入虚拟机

```
wget http://download.redis.io/releases/redis-4.0.7.tar.gz
```

下载源码包。

```
tar xzvf redis-4.0.7.tar.gz
cd redis-4.0.7

```

一般源码编译的目录中都有一个install文件。打开readme进行阅读。

- 第一步make进行编译

```
make[3]: gcc: Command not found
make[3]: *** [net.o] Error 127
make[3]: Leaving directory `/home/mtianyan/redis-4.0.7/deps/hiredis'
make[2]: *** [hiredis] Error 2
make[2]: Leaving directory `/home/mtianyan/redis-4.0.7/deps'
make[1]: [persist-settings] Error 2 (ignored)
    CC adlist.o
/bin/sh: cc: command not found
make[1]: *** [adlist.o] Error 127
make[1]: Leaving directory `/home/mtianyan/redis-4.0.7/src'
make: *** [all] Error 2
```

提示哪个不存在我们就装哪个喽。

``` 
sudo yum install gcc
```

将gcc安装完成之后我们再次执行make命令

```
make
```

继续报错:

```
make[1]: Entering directory `/home/mtianyan/redis-4.0.7/src'
    CC adlist.o
In file included from adlist.c:34:0:
zmalloc.h:50:31: fatal error: jemalloc/jemalloc.h: No such file or directory
 #include <jemalloc/jemalloc.h>
                               ^
compilation terminated.
make[1]: *** [adlist.o] Error 1
make[1]: Leaving directory `/home/mtianyan/redis-4.0.7/src'
make: *** [all] Error 2
```

提示我们不存在。

```
make MALLOC=libc
```

我们附加指定参数。

make编译完成之后 我们就可以 `sudo make insatll`了 此时我们就安装完成了。

| 命令 | 简介 |
| ------------- | ------------- |
| redis server | redis服务器端启动程序 |
| redis-cli | redis客户端操作工具 也可以使用telnet根据其纯文本协议来操作|
| redis-benchmark | redis性能测试工具 |
| redis-check-aof | 数据修复工具 |
| redis-check-dump | 检查导出工具 |

启动 `./redis-server`: 别忘了先进入src目录。

![mark](http://myphoto.mtianyan.cn/blog/180129/fGfml471Bg.png?imageslim)

端口6379 PID 2221

```
ps -ef|grep redis
netstat -anpl |grep redis
```

cd src : 运行redis-cli

![mark](http://myphoto.mtianyan.cn/blog/180129/89B0B4ekcF.png?imageslim)

![mark](http://myphoto.mtianyan.cn/blog/180129/fG7GjFh075.png?imageslim)

set delete get 

使用telnet进行连接操作。

```
telnet 192.168.0.7 6379
```
![mark](http://myphoto.mtianyan.cn/blog/180129/4jDd3jf3cC.png?imageslim)

报错无法连接，将报错信息中的命令在cli的控制台进行运行。

![mark](http://myphoto.mtianyan.cn/blog/180129/hEmAFBKee8.png?imageslim)

set get delete 操作

**向redis中存入list**

```
LPUSH mtianyan redis
LPUSH mtianyan mysql
LPUSH mtianyan emmmm
```

```
LRANGE mtianyan 0 10
```
![mark](http://myphoto.mtianyan.cn/blog/180129/6aIh0gLm9d.png?imageslim)

源码编译两步走。
