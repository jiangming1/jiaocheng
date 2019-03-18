优秀的程序员都是不用持续集成的
前几天我面试了一个码农，连续几个专业问题他都没答上来。
尴尬之余，我问它：「你没有什么理想吗？你现在最渴望的事情是什么？」
码农转悠着大眼睛，不假思索道：「做一个持续集成的自动部署！」
真没想到在面试中居然还有这种操作。
我问为什么这能成为现阶段最渴望的事情。
他反问「你就没有改动代码频繁，deploy无数次到崩溃的夜晚吗？寂寞长夜，总想好好专心写写博客，宽慰下自己啊。」
今天我就要做为一个不优秀的程序员使用持续集成。

跟着本教程同步配置的前置条件：

1. 已经配置好了hexo博客。拥有一个仓库如：`mtianyan.github.io`\
2. 已经将博客的源码也`push`到了一个仓库如：`hexoBlog-Github`
3. 已经配置了使用gulp进行博文压缩。


参考：推荐观看我的博文《Hexo+Next主题搭建博客安装美化及SEO优化指南》`中将博客源码备份到github或码云` 与 `使用gulp进行博文压缩`

完成上述前置条件，虽然不是必须的，但是可以与我保持一致。减少错误。


部分内容转载来源参考博客: https://www.karlzhou.com/2016/05/28/travis-ci-deploy-blog/

个人对上面博客做了新版本的修订。我最近每天改动博客的内容。一会目录有问题，一会改了站点参数，主题参数，出现Bug,
不停的提交快崩溃了：寂寞长夜，总想好好专心写写博客，宽慰下自己啊。

本文是一次在这个命题下的个人实践记录：大部分文字转载自参考博客

通常更新一篇Hexo博客文章，基本流程是：

1. 本地新建post页面

```
hexo n travis-ci-deploy-blog
INFO  Created: e:\WORK\GitHub\think-diff.me.source\source\_posts\travis-ci-deploy-blog.md
```
2. 在文本编辑器里用`markdown`语法编辑新建页面
3. 本地生成public文件：`hexo g && gulp`
4. 启动本地测试web server：`hexo s --debug`
5. 浏览器打开`http://localhost:4000/`, 浏览生成文章
6. 如果满意，即可部署到Github存放page仓库里：`hexo d`


本文主要介绍如何利用`TravisCI`自动完成第`3-6`步.

# What is Travis CI?

Travis CI

![mark](http://upload-images.jianshu.io/upload_images/1779926-0d87f625736d8f44.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

`CI(Continuous Integration)`翻译为`持续集成`。`Travis CI`是一个提供**持续集成功能**的平台，在`Github`上，可以添加`Travis CI`，当有`code push`时候，会推送通知到`Travis`，根据设置的脚本运行指定任务。

目前有两个站点:

1. `Travis.org` 对于所有`public`项目完全免费

2. `Travics.com` 只针对`private`项目，提供更多一些额外功能，如`cache`，并行`build`个数

两个站点只能看到各自的项目，不能通用。

# Why we need Travis CI?

有人可能会有疑问: 在本地写完博客，直接一个命令`hexo d`，不就搞定了么， 为啥要费力搞`CI`？

的确, 想用`TravisCI`来自动部署Hexo博客程序，需要不少设置（瞎折腾），为了给大伙信心，列举一些优点：

**优点1：直接在线编辑文件，立即生效**


假设你已经发表了一篇文章，过了几天你在朋友机器上浏览发现有几个明显的错别字，对于有强迫症的，这是不能容忍的。 但你手头又没有完整的hexo+nodejs+git的开发环境，重新下载git，node，hexo配置会花费不少时间，特别不划算。

如果按照这篇完整折腾完，你可以直接用浏览器访问github个人项目仓库，直接编辑那篇post的原md文件，前后2分钟改完。 稍等片刻，你的博客就自动更新了。


**优点2：自动部署，同时部署到多个地方**


在`gitcafe`是被收购之前，很多同学（包括我）都是托管在上面的，国内访问速度比`Github`快很多。
配合`DNS`根据`IP`位置可以自动选择导到`gitcafe`, 还是`github`，甚至你还可以部署到七牛云的静态网站。
利用`Travis CI`可同时更新多个仓库。

比如我的博客现在有两个站：一个部署在码云，一个部署在github。都需要我自己手动部署。

注：最后发现码云并不支持。emmmmm

**优点3：部署快捷方便**

手动`deploy`需要推送`public`整个folder到github上，当后期网站文章、图片较多时候，对于天朝的网络，有时候连接github 就是不顺畅，经常要傻等不少上传时间。
有了CI，你可以只提交`post`文件里单独的`md`文件即可，很快很爽，谁用谁知道。

**优点4：bigger than bigger**
build icon


你的项目`Readme`里面可以显示`CI build`图标，很酷有没有？
另外通过设置，可以在当`build失败`时**自动发邮件提醒你。**
上面的图标，如果登陆后你在Github项目里，直接点击图标，会跳转到你当前项目build的log界面，很方便。

当然有了`CI`，你可以做很多事情，如`自动运行单元测试`，`成功后再deploy`等等。很多项目里的持续集成基本也是这个道理。

# How to use Travis CI to deploy hexo blog?

原博主是使用private项目演示的，虽然我也就用学校邮箱加入了`Github`的`Education plan` 但是为了通用性，我本次做了`public`的实践。

## 步骤：

### 准备Travis CI账号, 传送门：[public项目](https://travis-ci.org/), [private项目](https://travis-ci.com/), 在登陆成功后.

![mark](http://upload-images.jianshu.io/upload_images/1779926-fd95a8187bf41308.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果发现没有`Repositories
`，点击上图加号会看到自己的目录然后把想要持续集成的仓库开关打开，会自动hook到Github。

![mark](http://upload-images.jianshu.io/upload_images/1779926-ce0c34ee445c07f6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

注意: 这里需要打开的是自己的

### 准备Github Personal Access Token。

在Github的setting页面，左侧面板选择`Developer settings`然后`Personal access tokens`, 右上角点击`Generate new token`。生成`token`时候需要确定`访问scope`，这里我们选择第一个`repo`即可。**重要：生成的`token`只有第一次可见，一定要保存下来备用。**

![mark](http://upload-images.jianshu.io/upload_images/1779926-6319f25fe00d9ad2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### (可选操作：除非你对于Travis CI保存你的密钥不信任，才需要做，否则可以直接跳过省事)

推荐直接跳过：有点奇怪原博主加了密又自行设置了环境变量保存token。加密在最后都没有用到。。。

- 准备[Travis命令行工具](https://github.com/travis-ci/travis.rb)，需要依赖ruby环境。对于Windows环境，可以使用这里的[安装包](https://rubyinstaller.org/downloads/)，安装完成后可用`ruby -v`检查。

请安装`2.4.x`版本。

![mark](http://upload-images.jianshu.io/upload_images/1779926-19ae7484357063c5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**注意**不要安装`2.5`版本。暂时不支持。会报错:

```
ERROR: Error installing travis: The last version of ffi (>= 1.3.0) to support your Ruby & RubyGems was 1.9.18. Try installing it with `gem install ffi -v 1.9.18` and then running the current command again ffi requires Ruby version < 2.5, >= 2.0. The current ruby version is 2.5.0.
```

- 安装命令行工具，参考这里[官方文档](https://github.com/travis-ci/travis.rb#installation)：

```
gem install travis
```
验证travis安装成功

![mark](http://upload-images.jianshu.io/upload_images/1779926-6d0417b5a71ed942.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

- 用Github的用户名/密码登录这个命令行工具。

```
travis login
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-f5e30e1955395eb1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

登陆成功后，开始加密，参考下面代码：

```
travis encrypt -r <github name>/<github repo> GH_Token=XXX

#sample：
travis encrypt -r mtianyan/hexoBlog-Github GH_TOKEN=33ba3948bxxx
```
![mark](http://upload-images.jianshu.io/upload_images/1779926-799f1046357b124f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

把输出的`secure:"xxxx"`保存。可以在文件的

```
env:
 global:
   - secure: `你加密后的`
```

`travis-ci`会自动对于你的密钥进行解密。当做加密前的环境变量:`GH_TOKEN`


### 配置`.travis.yml`（如果没有，新建)

添加环境变量来保护自己的github密钥。

![mark](http://upload-images.jianshu.io/upload_images/1779926-519b4c9517bf871d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

点击`more options` 选择 `setting`.

![mark](http://upload-images.jianshu.io/upload_images/1779926-4819bb95a8fa7658.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

找到环境变量值。将自己在github得到的`个人token值` 作为`value`，`name`可以自行定义
`Travis_Token`

![mark](http://upload-images.jianshu.io/upload_images/1779926-156b93c814973838.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下面`gulp`的压缩安装，参考我Hexo中教程的方法。
如果不需要压缩，可自行替换为`hexo g`


我个人的`.travis.yml` 可供[参考](https://github.com/mtianyan/hexoBlog-Github/blob/master/.travis.yml)

```
language: node_js  #设置语言

node_js: stable  #设置相应的版本

cache:
    apt: true
    directories:
        - node_modules # 缓存不经常更改的内容

before_install:
    - export TZ='Asia/Shanghai' # 更改时区

install:
  - npm install  #安装hexo及插件

script:
  - hexo clean  #清除
  - hexo g && gulp  #生成

after_script:
  - git clone https://${GH_REF} .deploy_git  # GH_REF是最下面配置的仓库地址
  - cd .deploy_git
  - git checkout master
  - cd ../
  - mv .deploy_git/.git/ ./public/   # 这一步之前的操作是为了保留master分支的提交记录，不然每次git init的话只有1条commit
  - cd ./public
  - git config user.name "mtianyan"  #修改name
  - git config user.email "1147727180@qq.com"  #修改email
  - git add .
  - git commit -m "Travis CI Auto Builder at `date +"%Y-%m-%d %H:%M"`"  # 提交记录包含时间 跟上面更改时区配合
  - git push --force --quiet "https://${Travis_Token}@${GH_REF}" master:master  #Travis_Token是在Travis中配置环境变量的名称

branches:
  only:
    - master  #只监测master分支，master是我的分支的名称，可根据自己情况设置
env:
 global:
   - GH_REF: github.com/mtianyan/mtianyan.github.io.git  #设置GH_REF，注意更改yourname

# configure notifications (email, IRC, campfire etc)
# please update this section to your needs!
# https://docs.travis-ci.com/user/notifications/
notifications:
  email:
    - 1147727180@qq.com
    - mtianyan@outlook.com
  on_success: change
  on_failure: always
```

这里需要注意的是：`GH_REF`，这个地址其实就是你github上存放`静态博客最终文件的仓库地址`，末尾加上`.git`。

### 集成`build icon`，在`Travis CI`控制台里，点击那个`icon`，选择`markdown`的样式，然后放到项目`Readme`里即可。

![mark](http://upload-images.jianshu.io/upload_images/1779926-510b5fa9d3c47c40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 大功告成后的使用。

你可以在本地hexo源码目录内，使用命令:

```
git add .
git commit -am "实验"
git push -u origin master
```

来将自己的博客源码提交至`github中的源码仓库` CI检测到你的源码更新改动会自动帮你编译`git push 到你的静态页面存放仓库`

![mark](http://upload-images.jianshu.io/upload_images/1779926-f5d1dfc3302e637e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在`commit`记录中会显示`bulid`的状态。

注意：有点慢。毕竟他要在Ci的服务器上根据你的配置文件安装下载，然后使用它的虚拟机进行部署。
