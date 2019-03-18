前置环境：
>* win10

###1. 安装DockerToolbox
[国内地址：下载最新的DockerToolbox进行安装](https://get.daocloud.io/toolbox/)

![我下载的是17.06.0a-ce.exe](http://upload-images.jianshu.io/upload_images/1779926-94f3a1ee9b126c55.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下载完成之后，下一步下一步安装。如果virtual box等已经装过的可以不勾选。

安装成功桌面会多三个图标，如下图：


![桌面多的三个图标](http://upload-images.jianshu.io/upload_images/1779926-0677d1a85fdbd207.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###2. 点击quickstart图标，然后会报错
Default Boot2Docker ISO is out-of-date 

解决方案：本地安装。

[下载Boot2Docker](https://github.com/boot2docker/boot2docker/releases)
下载与dockertoolbox版本一致的[**boot2docker.iso**](https://github.com/boot2docker/boot2docker/releases/download/v17.06.0-ce/boot2docker.iso)


![iso](http://upload-images.jianshu.io/upload_images/1779926-db6d7d9fc0599729.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

下载完成之后，放入本地C:\Users\当前你自己的用户\.docker\machine\cache
目录下，将原来的内容删除掉。

然后重新点击 点击quickstart图标。

![成功.png](http://upload-images.jianshu.io/upload_images/1779926-dd8b6a4d56b08a14.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###3. 自行申请加速地址：
[申请地址](https://www.daocloud.io/mirror#accelerator-doc)
C:\Users\mtianyan\.docker\machine\machines\default\config.json
![加速地址.png](http://upload-images.jianshu.io/upload_images/1779926-f87052790fd217c2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



