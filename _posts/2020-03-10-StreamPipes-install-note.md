---
layout: post
title: "Stream Pipes搭建"
subtitle: "Stream pipes install note"
author: "Marshall"
header-img: "img/streampipes-bg.jpg"
header-mask: 0.3
tags:
  - 实时计算平台
  - 开源
  - 笔记
---

### Stream Pipes搭建


#### 简介
**streampipes是flink foward Europe2019一家名为FZI的公司贡献出来的，非常雷锋，页面也很酷炫。本文主要讲解下如何安装streampipes，以及安装过程可能遇到的坑。官网连接：[点击这里](https://www.streampipes.org/en/)**

---

#### 安装步骤
- 安装docker
- 安装docker-compose
- 配置环境
- 安装streampipes

---

#### 详细步骤
##### 安装docker

安装docker 保证hello world成功 网址: [官方教程](https://docs.docker.com/install/linux/docker-ce/centos/#install-using-the-repository)

> *说明：机器是centos7，我是安装的最新版，官网介绍有多种方式安装，我选择的是set up Docker's repostories，官网介绍详细，可以step by step，最后保证hello world正常运行即可。*

##### 安装docker-compose
安装docker-compose 并且保证可运行 

```
docker-compose --version

sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

> 如果不好下载，可以本机下载传到远程路径下，之后sudo chmod +x /usr/local/bin/docker-compose，保证可执行

##### 配置环境
docker需要使用socket端口，socket只有root和docker用户组下的可以使用

需要依次执行：
```
sudo groupadd docker #添加docker用户组

sudo gpasswd -a $USER docker #将登陆用户加入到docker用户组中

newgrp docker #更新用户组

docker ps #测试docker命令是否可以使用sudo正常使用
```

##### 安装streampipes
下载streampipes ，启动安装streampipes ，期间很多下载的东西，具体组件可见下图
![](https://img-blog.csdnimg.cn/20191118094344959.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIxMjE1ODc=,size_16,color_FFFFFF,t_70)

下载完成all done之后启动成功视图
![](https://img-blog.csdnimg.cn/20191118094417475.png)

登陆注册以及试用
![](https://img-blog.csdnimg.cn/20191118094652956.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIxMjE1ODc=,size_16,color_FFFFFF,t_70)
![](https://img-blog.csdnimg.cn/20191118094619702.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIxMjE1ODc=,size_16,color_FFFFFF,t_70)






