---
layout: post
title: "ververica-platform社区版搭建"
subtitle: "ververica-platform community version installation note"
author: "Marshall"
header-img: "img/squirrel-mushroom.jpeg"
header-mask: 0.3
tags:
  - 实时计算平台
  - 开源
  - ververica-platform
  - 笔记
---

### ververica-platform社区版搭建流程
**ververica-platform flink母公司开发的sql计算平台（又名VVP），本文针对社区版本**

> [官方文档](https://docs.ververica.com/getting_started/installation.html) *官方文档比较详细，但是实践起来发现与最新版本的操作流程并不是完全一致。并且期间遇到不少问题，特此记录下。*

#### 所需组件

> git
>
> docker
>
> minikube
>
> kubectl
>
> helm

#### 详细步骤

*我的机器自带 git，docker安装可参照* [官方教程](https://docs.docker.com/install/linux/docker-ce/centos/#install-using-the-repository)

##### 安装minikube

参照：[minikube start](https://minikube.sigs.k8s.io/docs/start/)

```

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
# start 
minikube start
# 这里可以安装对应版本kubectl。。。 或者通过 安装kubectl教程安装，下方
minikube kubectl -- get po -A
```

##### 安装kubectl（可选）

> 如果通过minikube kubectl -- get po -A   安装了kubectl就不用了

参照：[Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-linux)

```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
# 测试
kubectl version --client
```

##### 安装helm

参照 [安装helm](https://helm.sh/docs/intro/install/)

官方有很多方式安装，我采用的是安装包方式

> From the Binary Releases
>
> Every release of Helm provides binary releases for a variety of OSes. These binary versions can be manually downloaded and installed.


> Download your [desired version](https://github.com/helm/helm/releases)


> Unpack it (tar -zxvf helm-v3.0.0-linux-amd64.tar.gz)
>
>
> Find the helm binary in the unpacked directory, and move it to its desired destination (mv linux-amd64/helm /usr/local/bin/helm)
>
> From there, you should be able to run the client and add the stable repo: helm help.

> Note: Helm automated tests are performed for Linux AMD64 only during CircleCi builds and releases. Testing of other OSes are the responsibility of the community requesting Helm for the OS in question.

#### 安装vvp

*重点来了哈* 参照：[官方文档](https://docs.ververica.com/getting_started/installation.html)

**这里先确保minikube已启动，kubectl docker命令就绪**

```
# 执行kubectl get pods -n kube-system
kubectl get pods -n kube-system
```

**观察结果，如果差不多就说明环境可以了**

```
NAME                               READY   STATUS    RESTARTS  AGE
coredns-5644d7b6d9-56zhg           1/1     Running   1         2m
coredns-5644d7b6d9-fdnts           1/1     Running   1         2m
etcd-minikube                      1/1     Running   1         2m
kube-addon-manager-minikube        1/1     Running   1         2m
kube-apiserver-minikube            1/1     Running   1         2m
kube-controller-manager-minikube   1/1     Running   1         2m
kube-proxy-9w92r                   1/1     Running   1         2m
kube-scheduler-minikube            1/1     Running   1         2m
storage-provisioner                1/1     Running   2         2m
```

**通过git clone下来社区版vvp，执行：**

```
git clone --branch release-2.3 https://github.com/ververica/ververica-platform-playground.git
cd ververica-platform-playground

# 备注，如果这里执行完  vvp  vvp-jobs namespace就已经安装成功了
./setup.sh --edition community
# 备注。建议按照ververica document操作，因为在git项目目录下可以自由的更改配置
```

> 建议按照ververica document [官方文档](https://docs.ververica.com/getting_started/installation.html) 操作，因为在git项目目录下可以自由的更改配置

**安装minio**

```
helm repo add stable https://kubernetes-charts.storage.googleapis.com

helm --namespace vvp \
    install minio stable/minio \
    --values values-minio.yaml
```

**安装vvp**

```
helm --namespace vvp \
    install vvp ververica/ververica-platform \
    --values values-vvp.yaml \
    --set acceptCommunityEditionLicense=true

kubectl --namespace vvp port-forward services/vvp-ververica-platform 8080:80
# !!!如果希望局域网内其他机器可访问
kubectl --namespace vvp port-forward services/vvp-ververica-platform --address [your host ip] 8080:80
```

#### 结束语

> 安装社区版vvp我觉得还是有一定难度的，因为依赖的组件过多，好在文档都比较全面，祝有需要的读者成功！