---
title: Docker部署Jenkins
date: 2026-03-12 21:24:42
category: [服务器]
tags: [Docker, Jenkins]
---

## Docker安装Jenkins

### 删除旧容器和镜像（可选）

如果之前已经安装过Jenkins，需要先删除之前的容器和镜像。

1. 停止并备份当前 Jenkins

```shell
# 1. 停止 Jenkins 容器
sudo docker stop jenkins

# 2. 备份旧数据（后面加上时间戳，防止覆盖）
sudo cp -r /data/jenkins_home /data/jenkins_home_backup_20260313

# 3. 验证备份是否成功（看文件大小是否一致）
du -sh /data/jenkins_home
du -sh /data/jenkins_home_backup_20260313
```

2. 清理旧镜像缓存（避免拉取旧版本）

```shell
# 1. 删除旧的 Jenkins 容器（仅删除容器，数据目录已备份）
sudo docker rm jenkins

# 2. 查看本地 Jenkins 镜像
sudo docker images | grep jenkins

# 3. 删除旧 LTS 镜像（如果有）
sudo docker rmi jenkins/jenkins:lts

# 4. 可选：删除指定版本（例如2.479.3-lts）镜像
sudo docker rmi jenkins/jenkins:2.479.3-lts
```

### 拉取新镜像

1. 拉取 Jenkins 官方镜像（推荐 LTS 稳定版）

```shell
# 拉取最新 LTS 镜像
sudo docker pull jenkins/jenkins:lts

# 拉取指定版本（推荐）
sudo docker pull jenkins/jenkins:2.492.3-lts
# 2.492.3-lts: Pulling from jenkins/jenkins
# 0a96bdb82805: Pull complete
# b5e2db483aae: Pull complete
# 72b137db2cdf: Pull complete
# d0aac1e9e20c: Pull complete
# c48e3974586c: Pull complete
# d281eadf0ae3: Pull complete
# be47ab5dc59d: Pull complete
# c09c222384de: Pull complete
# a91f2a3bb141: Pull complete
# 1a17773e6697: Pull complete
# b535e1c5efef: Pull complete
# 3cfa79b75794: Pull complete
# Digest: sha256:dc56634cc8fa476f36eba16d7db6c6bc1f5e3c7062432256738a008a339ee95c
# Status: Downloaded newer image for jenkins/jenkins:2.492.3-lts
# docker.io/jenkins/jenkins:2.492.3-lts
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
  jenkins/jenkins:2.492.3-lts
```

- `--restart=always`：容器异常退出自动重启
- `-p 8080:8080`：映射 Web 端口
- `-p 50000:50000`：映射 agent 通信端口
- `-v /data/jenkins_home:/var/jenkins_home`：数据持久化
- `-v /var/run/docker.sock:/var/run/docker.sock`：让 Jenkins 操作宿主机 Docker
- `-v /usr/bin/docker:/usr/bin/docker`：挂载 Docker 命令
- `-e TZ=Asia/Shanghai`：设置时区为上海，解决 Jenkins 日志和任务时间与本地不一致的问题
- `jenkins/jenkins:` 后面跟 安装的版本

4. 验证升级是否成功

```shell
# 1. 检查容器是否正常启动
sudo docker ps | grep jenkins
# 输出状态为 "Up" 且镜像为 2.492.3-lts 即正常
```

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

### 安装插件

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

### 手动安装插件

安装 Jenkins 插件有明确的顺序要求，核心原则是「先装底层依赖插件，再装业务功能插件」—— 若跳过依赖直接装上层插件，会导致插件加载失败、功能异常。

每安装一个插件，都需要重启 Jenkins 容器，才能使插件生效。

1. bouncycastle-api：加密算法底层依赖，credentials 插件的核心依赖，必须最先装。
2. Credentials：基础凭证管理，管理 Gitee 令牌 / SSH 密钥，依赖 bouncycastle-api。
3. SSH Credentials：SSH 类型凭证管理，依赖 Credentials Plugin，需紧随其后装
4. git-client：Git 客户端核心插件	所有 Git 相关插件的底层依赖
5. Git：Git 仓库对接	依赖 git-client，需先装 git-client
6. Gitee：Gitee 仓库专属对接	强依赖 Git Plugin/git-client
7. NodeJS：Node.js 环境管理	无依赖，可与 Gitee Plugin 同阶段装
8. Pipeline：流水线核心插件	依赖 Git Plugin，需后装
9. Publish Over SSH：远程上传 / 执行命令	无强依赖，最后装即可