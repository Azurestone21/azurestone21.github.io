---
title: Linux安装Docker
date: 2026-03-12 21:02:14
category: [服务器]
tags: [Linux, Docker]
---

## 测试环境

服务器：阿里云 Alibaba Cloud Linux 3

## 安装 Docker

如果已安装Docker，需要先卸载旧版Docker。

教程：https://help.aliyun.com/zh/ecs/user-guide/install-and-use-docker#940c78642dmq9

1. 安装Docker社区版本

```shell
#添加Docker软件包源
sudo wget -O /etc/yum.repos.d/docker-ce.repo http://mirrors.cloud.aliyuncs.com/docker-ce/linux/centos/docker-ce.repo
sudo sed -i 's|https://mirrors.aliyun.com|http://mirrors.cloud.aliyuncs.com|g' /etc/yum.repos.d/docker-ce.repo

#Alibaba Cloud Linux3专用的dnf源兼容插件
sudo dnf -y install dnf-plugin-releasever-adapter --repo alinux3-plus

#安装Docker社区版本，容器运行时containerd.io，以及Docker构建和Compose插件
sudo dnf -y install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

2. 启动Docker并设置开机自启

```shell
#启动Docker
sudo systemctl start docker

#设置Docker守护进程在系统启动时自动启动
sudo systemctl enable docker
```

3. 查看Docker状态

```shell
sudo systemctl status docker

# active (running)
```

> 退出查看状态：Ctrl + c

4. 验证 Docker 是否安装成功

```shell
docker --version

# 会出现版本号
# Docker version 26.1.3, build b72abbb
```

## 卸载 Docker

1. 卸载旧版Docker、相关软件包和软件源。

```shell
#删除Docker相关源
sudo rm -f /etc/yum.repos.d/docker*.repo

#卸载Docker和相关的软件包
sudo dnf -y remove \
docker \
moby \
docker-ce \
containerd.io \
docker-ce-rootless-extras \
docker-buildx-plugin \
docker-ce-cli \
docker-compose-plugin
```

2. 卸载Docker不会自动移除镜像、容器、存储卷和网络。这些数据默认存储在/var/lib/docker/目录。需手动删除此目录。

## 配置镜像源

1. 登录[容器镜像服务控制台](https://cr.console.aliyun.com/?spm=a2c4g.11186623.0.0.6bd96740lfIoyK)，在左侧导航栏选择**镜像工具** > **镜像加速器**，在**镜像加速器**页面获取**加速器地址**。
2. 编辑Docker配置文件`/etc/docker/daemon.json`。若文件不存在，先创建。

```json
// {
// 	 "registry-mirrors": ["<加速器地址>"]
// }

{
  "registry-mirrors": [
    "https://docker.registry.cyou",
    "https://docker-cf.registry.cyou",
    "https://dockercf.jsdelivr.fyi",
    "https://docker.jsdelivr.fyi",
    "https://dockertest.jsdelivr.fyi",
    "https://mirror.aliyuncs.com",
    "https://dockerproxy.com",
    "https://mirror.baidubce.com",
    "https://docker.m.daocloud.io",
    "https://docker.nju.edu.cn",
    "https://docker.mirrors.sjtug.sjtu.edu.cn",
    "https://docker.mirrors.ustc.edu.cn",
    "https://mirror.iscas.ac.cn",
    "https://docker.rainbond.cc"
  ]
}
```

3. 重启

```shell
sudo systemctl daemon-reload
sudo systemctl restart docker
```

## 安装 Docker Compose

教程：https://help.aliyun.com/zh/ecs/user-guide/install-and-use-docker#a7a051253bxz5

```shell
# 安装 Docker Compose
sudo yum -y install docker-compose-plugin
```