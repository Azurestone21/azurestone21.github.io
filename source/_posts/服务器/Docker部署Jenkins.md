---
title: Docker部署Jenkins
date: 2026-03-12 21:24:42
category: [服务器]
tags: [Docker, Jenkins]
---

> 基于阿里云 Alibaba Cloud Linux 3 服务器，安装 Jenkins 2.504.3 LTS 版本。部署 Gitee 项目到服务器。

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

# 4. 删除指定版本（例如2.479.3-lts）镜像（如果有）
sudo docker rmi jenkins/jenkins:2.479.3-lts
```

### 创建 Jenkins 数据持久化目录

```shell
# 创建目录
sudo mkdir -p /data/jenkins_home
# 设置权限（Jenkins 容器内默认用户 UID 为 1000，需匹配权限）
sudo chown -R 1000:1000 /data/jenkins_home
sudo chmod -R 755 /data/jenkins_home
```

### 拉取新镜像

1. 拉取 Jenkins 官方镜像。最好拉取最新的 LTS 稳定版，避免安装插件时出现版本不兼容问题。

```shell
# 拉取最新 LTS 镜像
sudo docker pull jenkins/jenkins:lts

# 拉取指定版本（推荐）
sudo docker pull jenkins/jenkins:2.504.3-lts
# 2.504.3-lts: Pulling from jenkins/jenkins
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
# Status: Downloaded newer image for jenkins/jenkins:2.504.3-lts
# docker.io/jenkins/jenkins:2.504.3-lts
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
  --privileged \
  --restart=always \
  -p 8080:8080 \
  -p 50000:50000 \
  -v /data/jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /usr/bin/docker:/usr/bin/docker \
  -e TZ=Asia/Shanghai \
  jenkins/jenkins:2.504.3-lts
```

- `--restart=always`：容器异常退出自动重启
- `--privileged`：授予容器特权访问宿主机的能力，用于执行 Docker 命令
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
2. 配置入方向规则，添加端口 8080 和 50000（启动 Jenkins 容器时的映射端口），授权对象为 0.0.0.0/0（公网访问）。

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

初次安装，如果有梯子，可以选择 “安装推荐的插件”。如果没有就 “选择插件来安装”，取消所有插件，点击“安装”。因为国内安装会很慢，大概率失败，需要换源。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260313003531467.png)

取消所有插件。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260313003531468.png)

如果已经卡在安装步骤上，浏览器输入 http://公网IP:8080/restart，回车，点 Yes 重启 Jenkins。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260313003531470.png)

### 配置管理员

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260313003531471.png)

### 实例配置

直接保存

### 配置完成

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260313003531472.png)

### 换源

https://updates.jenkins.io/update-center.json

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260316005711621.png)

### 手动安装插件

插件库：https://updates.jenkins-ci.org/download/plugins/

把需要的插件文件下载到本地，然后在 Jenkins 插件管理中部署。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260316010148362.png)

安装 Jenkins 插件最好按「先装底层依赖插件，再装业务功能插件」—— 若跳过依赖直接装上层插件，会导致插件加载失败、功能异常。不过在 Jenkins 上安装会自动安装依赖的插件，一般不需要处理。

安装完插件需要重启 Jenkins 容器，使插件生效。

下面是部署前端项目需要用到的插件：

1. bouncycastle-api：加密算法底层依赖，credentials 插件的核心依赖，必须最先装。
2. Credentials：基础凭证管理，管理 Gitee 令牌 / SSH 密钥，依赖 bouncycastle-api。
3. SSH Credentials：SSH 类型凭证管理，依赖 Credentials Plugin
4. git-client：Git 客户端核心插件所有 Git 相关插件的底层依赖
5. Git：Git 仓库对接，依赖 git-client，需先装 git-client
6. Gitee：Gitee 仓库专属对接，强依赖 Git Plugin/git-client
7. NodeJS：Node.js 环境管理，无依赖，可与 Gitee Plugin 同阶段装
8. Pipeline：流水线核心插件，依赖 Git Plugin，需后装
9. Publish Over SSH：远程上传 / 执行命令，无强依赖，最后装即可

### 全局工具配置

#### 配置 NodeJS

Jenkins → 系统管理 → 全局工具配置 → NodeJS → 新增 NodeJS：

1. 别名：NodeJS 20（自定义）
2. 版本：选择 20.x（和本地的node版本一致）
3. Global npm packages to install：空
4. Global npm packages refresh hours：空 / 0
5. 保存。

### 新建任务

1. 输入一个任务名称
2. 选择“构建一个自由风格的软件项目”
3. 点击“确定”

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260316005332805.png)

### 配置任务

#### 获取gitee令牌

如果原来没有gitee令牌，需要先在gitee上创建一个。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260316003209696.png)

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260316003209697.png)

#### 配置Git和凭证

源码管理选择Git，输入仓库URL和选择凭证（没有凭证可以在右边按钮添加。也可以在 Jenkins 的**系统管理**中的**凭据管理**中添加）

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260316003209698.png)

#### shell脚本

```shell
#!/bin/bash
set -e

# ===================== 1. 进入项目目录 =====================
cd $WORKSPACE

# ===================== 2. 安装依赖（关键：去掉 --production，安装所有依赖） =====================
echo "开始安装依赖（包含开发依赖 vite）..."
npm install # 不要加 --production，vite 是 devDependencies，会被跳过

# ===================== 3. 打包 Vue 项目（用 npx 调用本地 vite，避免全局依赖问题） =====================
echo "开始打包项目..."
npx vite build # 核心修改：用 npx 执行本地 node_modules 中的 vite

# ===================== 4. 备份旧文件（可选） =====================
echo "备份旧项目文件..."
mkdir -p /data/project/admin-vue.bak
cp -r /data/project/admin-vue/* /data/project/admin-vue.bak/ || true

# ===================== 5. 清空旧文件 =====================
echo "清空旧文件..."
rm -rf /data/project/admin-vue/*

# ===================== 6. 复制新打包文件 =====================
echo "部署新文件到 /data/project/admin-vue..."
cp -r $WORKSPACE/dist/* /data/project/admin-vue/

# ===================== 7. 修复权限 =====================
echo "修复目录权限..."
sudo chown -R nobody:nobody /data/project/admin-vue
sudo chmod -R 755 /data/project/admin-vue

# ===================== 8. 重载 Nginx =====================
echo "重载 Nginx..."
/usr/local/nginx/sbin/nginx -s reload

echo "✅ 项目部署完成！"
```

## 挂载项目目录

我的项目放在服务器的 `/data/project` 目录。

```shell
# 1. 停止现有 Jenkins 容器（如有）
sudo docker stop jenkins && sudo docker rm jenkins

# 2. 重新启动 Jenkins，挂载 /data/project 目录（项目存放目录）
docker run -d \
  --name jenkins \
  --privileged \
  -p 8080:8080 \
  -p 50000:50000 \
  -v /data/jenkins_home:/var/jenkins_home \
  -v /data/project:/data/project \
  -v /data/jenkins-npm-cache:/root/.npm \
  --restart=always \
  jenkins/jenkins:2.504.3-lts
```

## 立即构建

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260316010723179.png)

构建后，服务器崩了，直接卡死。

服务器排查显示：

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260316004250083.png)

原因：我的服务器是2核2G内存，存储只有40G，Jenkins 在容器里打包非常吃 CPU 和内存，把服务器资源占满。

重启服务器后，为了避免再次发生崩溃，限制了 Jenkins 容器的资源使用，避免占满整个服务器。

```shell
sudo docker stop jenkins && sudo docker rm jenkins

docker run -d \
  --name jenkins \
  --privileged \
  --cpus 1 \
  --memory 1g \
  -p 8080:8080 \
  -p 50000:50000 \
  -v /data/jenkins_home:/var/jenkins_home \
  -v /data/project:/data/project \
  -v /data/jenkins-npm-cache:/root/.npm \
  -v /usr/local/nginx/sbin/nginx:/usr/local/nginx/sbin/nginx \
  --restart=always \
  jenkins/jenkins:2.504.3-lts
```

- --cpus 1：限制 CPU 使用，避免 CPU 占满导致系统卡死；
- --memory 1g：限制内存使用，避免触发 swap 交换加剧 IO 压力；

限制资源后，服务器没有崩，但是导致 Jenkins 打包项目超时失败。可能还是需要扩容服务器才能解决。

最后使用一个简单的静态项目进行测试，部署成功。
