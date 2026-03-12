---
title: Docker部署Jenkins
date: 2026-03-12 21:24:42
category: [服务器]
tags: [Docker, Jenkins]
---



## Docker安装Jenkins

1. 拉取 Jenkins 官方镜像（推荐 LTS 稳定版）

```shell
sudo docker pull jenkins/jenkins:lts

# 指定版本（推荐）
sudo docker pull jenkins/jenkins:2.479.3-lts
```

> 安装报错：Error response from daemon: Get "https://registry-1.docker.io/v2/": net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers)
>
> 原因：docker无法访问dockerhub镜像仓库，需要配置Docker加速器。

2. 验证镜像

```shell
sudo docker images | grep jenkins

# jenkins/jenkins   lts       2a4bbe50c40b   4 years ago   441MB
```

3. 创建并启动 Jenkins 容器

```shell
sudo docker run -d \
  --name jenkins \
  --restart=always \
  -p 8080:8080 \
  -p 50000:50000 \
  -v /data/jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /usr/bin/docker:/usr/bin/docker \
  -e TZ=Asia/Shanghai \
  jenkins/jenkins:2.479.3-lts
```

- `--restart=always`：容器异常退出自动重启
- `-p 8080:8080`：映射 Web 端口
- `-p 50000:50000`：映射 agent 通信端口
- `-v /data/jenkins_home:/var/jenkins_home`：数据持久化
- `-v /var/run/docker.sock:/var/run/docker.sock`：让 Jenkins 操作宿主机 Docker
- `-v /usr/bin/docker:/usr/bin/docker`：挂载 Docker 命令
- `-e TZ=Asia/Shanghai`：设置时区为上海，解决 Jenkins 日志和任务时间与本地不一致的问题
- `jenkins/jenkins:` 后面跟 安装的版本

## 阿里云配置开放端口

1. 登录阿里云控制台 → 云服务器 ECS → 实例 → 网络与安全组
2. 配置入方向规则，添加端口 8080 和 50000，授权对象为 0.0.0.0/0（公网访问）。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260313003531473.png)

## Jenkins配置

### 访问

获取初始管理员密码

```shell
# 方式 1：从容器日志获取
sudo docker logs jenkins | grep "Initial password"
# 方式 2：从持久化目录获取
sudo cat /data/jenkins_home/secrets/initialAdminPassword
```

访问地址：http://阿里云服务器公网IP:8080

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260313003531466.png)

###  安装插件

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260313003531467.png)

3. 选第二个，取消所有插件，点击“安装”（因为国内安装会很慢，大概率失败，需要换源）

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260313003531468.png)

4. 如果已经卡在安装步骤上，浏览器输入 http://公网IP:8080/restart，回车，点 Yes 重启 Jenkins。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260313003531470.png)

### 配置管理员

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260313003531471.png)

### 实例配置

直接保存

### 配置完成

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260313003531472.png)