## 【博主推荐】1元学习《Django零基础到项目实战》视频教程：http://dwz.cn/VHRVxWfC

Django2.0.2(Django-rest-framework)以及前端vue开发的前后端分离的商城网站

线上演示地址: http://vueshop.mtianyan.cn/
github源代码地址: https://github.com/mtianyan/VueDjangoFrameWorkShop

本小节: 通过docker实现sentry搭建错误日志监控系统


## sentry介绍

sentry也是通过drf开发的一款错误日志监控系统。

它的服务器是用drf做的，但是目前的最主流语言都是可以集成进来的。

官方文档中也宣传过这个。

crash收集。它是一个服务，它是一个用django写的网站

错误日志，上线之后是很重要的。

logging日志，可以记录错误栈。

- 必须登录到服务器查看文件
- 我们需要主动去查询，web系统
- 收邮件。一个bug被很多用户遇到，邮件很多。
- 集中管理bug jira(由测试来提bug)页面上能看到的bug

分配bug > bobby

项目管理。5个项目。分项目bug管理。

sentry是一个server，我们要像sentry发送错误消息就需要sdk

进入sentry的官网，注册一个账号进来。

sentry是分为收费版和免费版的一般是我们自己去搭建我们的sentry服务器

小项目可以直接使用sentry的服务，注册账号就行了。

### 安装sentry

通过python & 通过 docker

依赖服务特别多

17年3月之后，企业社区版本。

- 运行命令进行卸载

```
sudo yum remove docker \
docker-common \
docker-selinux \
docker-engine
```
- 安装依赖包

```
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

- 添加docker的稳定源

```
sudo yum-config-manager \
	--add-repo \
	https://download.docker.com/linux/centos/docker-ce.repo
```

- 安装docker ce

1. 更新yum包

```
sudo yum makecache fast
```

2. 安装docker ce

```
sudo yum install docker-ce
```

3. 启动docker

```
sudo systemctl start docker
```

4. 测试docker

```
sudo docker run hello-world
docker --version
```

安装方法2:

下载安装包https://download.docker.com/linux/centos/7/x86_64/stable/Packages/

下载rpm包

```
sudo yum install docker.rpm
```

安装docker-compose

```
sudo yum install epel-release
sudo yum install -y python-pip
sudo pip install docker-compose
```

### sentry

sentry依赖组件比较多，包括redis postgresql outboundemail

安装sentry前确保docker版本大于1.10

1. 安装git

```
sudo yum install git
```

2. 下载docker镜像

https://github.com/getsentry/onpremise

运行readme中的命令如下

```
~: mkdir -p data/{sentry,postgres}
```

克隆项目

```
~:git clone https://github.com/getsentry/onpremise.git
cd onpremise/
```
下面这个命令是要使用我们克隆下来的项目中的docker-compose.yml的。

```
docker-compose run --rm web config generate-secret-key

#生成secret-key
```
下载了很多的镜像，redis memcache postgresql smtp等

最后执行完成之后会给我们生成一个secret-key

需要将这个key填入到docker-compose.yml文件中

里面有一个变量叫做sentry_secret_key

```
SENTRY_SECRET_KEY: 'key'
```

填进来之后我们就可以执行下面的命令

```
docker-compose run --rm web upgrade
```

它会将我们的数据表建好。让我们新建一个用户，其实就是django的create superuser

询问我们是否创建一个user account 输入y

按住ctrl+回退才能执行回退。

输入邮箱 用户名 密码 是否超级管理 y

```
docker-compose up -d
```

运行起我们所有的服务。

```
docker ps
```

就可以看到运行的服务。

全部启动之后就可以访问。9000端口不在允许范围内。阿里云修改安全策略。

加载比较慢。ctrl + f5强制刷新。

是否允许注册。admin email是可以切换的。root url设置为当前地址

```
http://remote_ip:9000/
```

直接进入管理台页面。

## sentry的功能

如何使用sentry来完成错误日志的监控。

dashboard主页面

projects team 概念

虽然是错误日志的收集，也是按team 来组织的。

新注册到sentry的用户叫做member，可以注册也可以邀请用户进入。
用户可以加入相应的team team主要是属于projects的。

stats 关于bug的统计。

分配。出现bug可以将该bug分配给系统中的某一个用户。

### 新建项目

new project。mtianyan生鲜超市

>可以通过项目将组织中的事件细分至具体应用。项目创建好了。

获取项目的DSN。写python代码连接sentry最重要的url。
流行的框架的支持。

新建一个目录test包。目录下新建一个sentry的脚本

```python
pip install raven --upgrade
```

```
# encoding: utf-8
__author__ = 'mtianyan'
__date__ = '2018/3/15 0015 22:15'

from raven import Client

client = Client('dsn')

try:
    1 / 0
except ZeroDivisionError:
    client.captureException()
```

同样的错误只会在后面添加events的数量，不会新建一条。

bug的管理员可以把这个错误分配给某个人。

解决了之后，点击resolve，标记为错误已解决。

也可以重新标记为未解决。

忽略ignore。已知错误。标记设置删除。筛选，打tag。

错误栈。排除bug。

project_settings对于项目进行设置，项目名称，项目的team。
权限管理。group的功能。

项目是组管理的。sentry是默认的组，我们也可以新建组(team)。

发送邮件。当某一个错误首次发送给我们发邮件。

设置邮件的prefix前缀。事件的设定。允许的域名。

报警: roles配置邮件发送的规则。

发送邮件的规则: 防止过度。满足条件才发送。默认配置为第一次发生发邮件。

客户端密钥DSN。现有系统的集成。JIRA进行集成。github gitlab的集成。

api 的接口。接口文档。

## 如何将sentry集成到django的restframework中

- 将错误栈显示出来

- 发送邮件的功能

左下角账号: 外观里面默认是utc的时间。搜索上海。

通知是设置接收哪些消息。

测试邮件发送功能: 工作流接收消息。

多个邮箱都收到邮件。账户里修改邮箱。

>管理，才能看到管理。

阿里云发送哟偶见问题: 

docker-compose.yml中设置host等。

```
#  SENTRY_EMAIL_HOST
#  SENTRY_EMAIL_PORT
#  SENTRY_EMAIL_USER
#  SENTRY_EMAIL_PASSWORD
#  SENTRY_EMAIL_USE_TLS
```

运行之前的命令

```
docker-compose up -d
```

刷新页面。

```
SENTRY_SERVER_EMAIL
```

这个值和我们的user保持一致。重新启动。

```
docker-compose up -d
```

阿里云对于25端口进行了限制。 25端口禁用，465 ssl

阿里云强制ssl 当时sentry不支持ssl。只支持TLS

>阿里云服务器，sentry 邮件端口设置为587，TLS 设置为true，邮箱功能可以正常发送

一个很大的坑，关闭重启修改过好几次。

启动顺序的不一致:

>找到重要的三个进程 worker web cron

docker stop 这三个的id 

一个一个启动按顺序

```
docker  start worker
docker start cron
docker start web
```
项目点击项目设置: 设置中有一个警报。

点击规则，当第一次发生这个错误。

### 配置到django当中。

https://docs.sentry.io/clients/python/integrations/django/

install_app中添加

```
'raven.contrib.django.raven_compat',
```

settings文件

```python
import os
import raven

RAVEN_CONFIG = {
    'dsn': 'https://<key>:<secret>@sentry.io/<project>',
    # If you are using git, you can also automatically configure the
    # release based on the git info.
    'release': raven.fetch_git_sha(os.path.abspath(os.pardir)),
}
```

不用git集成就可以把release删除掉。

运行消息测试

```
python manage.py raven test
```

![mark](http://myphoto.mtianyan.cn/blog/180315/k01Ficm6jA.png?imageslim)

可以看到我们成功的接收到了邮件。

会将get的请求，以及是哪个用户出了错。哪个ip过来的等。

前后端分离的技术可以跨语言跨平台。
