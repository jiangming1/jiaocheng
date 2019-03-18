容器技术。docker。

### 到底什么是docker？

![mark](http://myphoto.mtianyan.cn/blog/180514/2IFD9Lm0B0.png?imageslim)

虚拟机和容器的区别。创建虚拟机，不同应用在不同虚拟机内进行隔离。容器技术进行隔离。
不需要虚拟化这一层虚拟的操作系统，只需要在本地操作系统之上。

### Docker能干什么？

![mark](http://myphoto.mtianyan.cn/blog/180514/cbHg7jLgFb.png?imageslim)

简化配置: 源代码和运行环境及配置打包到一个容器里面。

实现本地环境，开发环境，线上环境统一。 减少开发测试部署中的错误。

### 容器时代的“双城记”

![mark](http://myphoto.mtianyan.cn/blog/180514/1lf3lAGf7l.png?imageslim)

简称为K8S 因为K和s之间有8个字母。

![mark](http://myphoto.mtianyan.cn/blog/180514/dbmk45FEjj.png?imageslim)

大海航行靠舵手-Kubernetes

![mark](http://myphoto.mtianyan.cn/blog/180514/efEHfe4CG8.png?imageslim)、

进行容器编排的工具，编排就是对于容器的创建，管理，运维等。实际的生产环境一般有成千上万的容器去创建，维护。就需要容器编排工具给我们做这事。

Docker- 和 k8s的区别

### Devops = 文化 + 过程 + 工具

![mark](http://myphoto.mtianyan.cn/blog/180514/4e5BlCEdKg.png?imageslim)

工具，管理，持续集成，监控。

### 课程章节 和内容介绍

- 第1章 容器技术和Docker简介

- 第2章 Docker环境的各种搭建方法

>本章详细讲解了Docker实验环境的各种搭建方法，在不同的操作系统，如windows，Mac，Linux上搭建，在不同的平台，如主机和云平台上搭建，用不同的工具，如shell，docker-machine，vagrant去搭建。

- 第3章 Docker的镜像和容器

> 本章主要讲解Docker技术里最为基础的两大概念：镜像和容器。镜像的 获取方式：从registry拉取，从Dockerfile构建；容器的基本操作：创建，启动，停止删除。

- 第4章 Docker的网络

>这一章会从Linux的network namespace开始，通过详细讲解Docker的内置网络host和bridge是怎么使用的，来给大家展示不同容器之间网络的隔离和互访是如何实现的。当然好包括一个高级话题：Docker的多机通信和Overlay网络介绍

- 第5章 Docker的持久化存储和数据共享

>数据库容器的数据如何才能不会丢失？本章会给大家讲解Docker的持久化存储技术。Docker的数据共享技术能极大提高开发人员的开发效率，边写代码，边看运行结果。

- 第6章 Docker Compose多容器部署

>如何快速部署和管理一个具有多容器的应用？本章会介绍docker Compose这个强大的工具，包括其安装和基本使用，以及快速实现应用的水平扩展和负载均衡。

- 第7章 容器编排Docker Swarm

>想在生产环境中使用Docker？那你肯定离不开容器编排技术，本章会介绍Docker内置的容器编排工具Docker Swarm，包括Swarm的基本架构介绍，集群搭建，服务的创建，维护和水平扩展，服务的内部和外部访问，Secret的管理和使用。

- 第8章 DevOps初体验——Docker Cloud和Docker企业版

>本章介绍Docker的两个收费产品，一个是Docker Cloud，一个是Docker企业版。包括Docker Cloud的使用，CI/CD DevOps体验，Docker企业版的本地安装和阿里元安装，Docker企业版UCP和DTR的基本使用。

- 第9章 容器编排Kubernetes

>本章介绍容器编排领域的领头羊Kubernetes。包括Kubernetes架构介绍，集群的本地搭建和Tectonic在亚马逊AWS上的搭建方式，Kubernetes的重要概念：Replication Controller， Deployment，Service介绍和操作演示，横向扩展，负载均衡等。

- 第10章 容器的的运维和监控

>本章主要面向运维人员，一起看一下对于部署的应用，如何通过Docker自带的命令和第三方的工具监控应用是否正常运行，比如Docker stat，Prometheus，Cadvisor， ELK等。

- 第11章 Docker+DevOps实战——过程和工具(完全开源免费的工具)

>本章秉承DevOps的思想，给大家完整的演示DevOps的一种实现方案，其中涉及到GitLab版本管理，Docker-Compose本地测试，GitLab CI持续集成，持续部署，AWS，Kubernetes生产环境等工具和组件。通过一个实际示例项目和大家一起看一下，从需求提出，到生产环境更新的完整DevOps过程。

- 第12章 课程总结

>对前面章节进行简单的总结

课程的授课特点

>细致的理论讲解 + 丰富的实际操作

![mark](http://myphoto.mtianyan.cn/blog/180517/bHb63f6Dcm.png?imageslim)

>完整演示devops的完整过程。

开发人员，测试人员，运维人员。

熟悉linux基本操作。


## 1.2 容器技术概述

先聊聊历史

很久很久以前，安装app需要

![mark](http://myphoto.mtianyan.cn/blog/180517/bkgHLaBCbj.png?imageslim)

>物理机 + 操作系统 + app

部署非常慢，安装东西太多。 成本非常高。资源浪费。难于迁移和扩展。可能会被限定硬件厂商

### 虚拟化技术出现以后

![mark](http://myphoto.mtianyan.cn/blog/180517/2c4cg2lK71.png?imageslim)

在物理服务器之上多了一层，hypervisor层。 虚拟化层之上安装虚拟机。

- 一个物理机可以部署多个app
- 每个app独立运行在一个VM里

虚拟机的优点

- 资源池: 一个物理机的资源分配到了不同的虚拟机里
- 很容易扩展: 加物理机器 加虚拟机

虚拟化层进行了隔离。

- 很容易云化 - 亚马逊AWS 阿里云

虚拟化技术的局限性

- 每一个虚拟机都是一个完整的操作系统，要给其分配资源，当虚拟机数量增多时，操作系统本身消耗的资源势必增多。

### 容器为什么会出现。

![mark](http://myphoto.mtianyan.cn/blog/180517/mhckeiKGmk.png?imageslim)

上面是面向开发人员的。 前后台，存储，缓存，队列技术。

运维面临挑战，app的部署，环境准备，依赖很多。app运行之后的监控运维也面临很多挑战。

开发软件的测试环境，运维环境不一样。

容器解决了什么问题？

![mark](http://myphoto.mtianyan.cn/blog/180517/b27lAF0GKc.png?imageslim)

容器就是集装箱打包的意思。按照集装箱标准打包。

打包好的容器可以运行在其他环境。

>解决了开发和运维之间的矛盾。

在开发和运维之间搭建了一个桥梁，是实现devops的最佳解决方案。

### 什么是容器？

对软件和其依赖的标准化打包。应用之间相互隔离。共享同一个OS kernel，可以运行在很多主流操作系统上。

![mark](http://myphoto.mtianyan.cn/blog/180517/k2BJhdG0g4.png?imageslim)

容器和虚拟机的区别

![mark](http://myphoto.mtianyan.cn/blog/180517/gc7aFIGiEE.png?imageslim)

一个是app层面的隔离，一个是物理资源层面的隔离。

底层是物理资源，每个vm里面装os

hostos 之上安装docker(容器化技术的一种实现) docker创建container

容器是用来运行app的。

虚拟化+ 容器是可以结合使用。

![mark](http://myphoto.mtianyan.cn/blog/180517/I2DE18ej0E.png?imageslim)

### docker是容器技术的一种实现

![mark](http://myphoto.mtianyan.cn/blog/180517/B9l5L3EBef.png?imageslim)

还有其他的实现。利用了LXC技术。2016年企业版社区版。

## docker魅力初体验。

一个demo，容器技术简单的开发和部署。部署一个WordPress

前端，数据库，操作系统。

我们的目录01-chapter中有一个docker-compose.yml文件

```
docker-compose build
docker-compose up
```

![mark](http://myphoto.mtianyan.cn/blog/180517/jfjImG1iaD.png?imageslim)

up时会自动安装mysql和WordPress的image，并帮我们启动起来。

![mark](http://myphoto.mtianyan.cn/blog/180520/e2I1feDkC7.png?imageslim)

![image.png](https://upload-images.jianshu.io/upload_images/1779926-214659e38f03a2b2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

运行出来的效果，如上图所示。

![mark](http://myphoto.mtianyan.cn/blog/180517/jfjImG1iaD.png?imageslim)










