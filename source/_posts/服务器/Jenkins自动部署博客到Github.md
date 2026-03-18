---
title: Jenkins自动部署博客到Github Pages
date: 2026-03-18 21:49:53
category: 服务器
tags: [Jenkins]
---

## 配置 SSH 免密登录 GitHub

在宿主机创建 SSH 密钥对

```shell
# 1. 创建存放 Jenkins 数据的目录（如果没有目录，后面生成密钥会失败）
mkdir -p /data/jenkins_home/.ssh
chmod 700 /data/jenkins_home/.ssh

# 2. 生成 SSH 密钥（一路回车，不设密码）
ssh-keygen -t rsa -b 4096 -C "你的邮箱@example.com" -f /data/jenkins_home/.ssh/id_rsa
# ssh-keygen -t rsa -b 4096 -C "1055380813@qq.com" -f /data/jenkins_home/.ssh/id_rsa

# 3. 修改权限（关键！Jenkins 容器内用 1000 用户运行）
chown -R 1000:1000 /data/jenkins_home/.ssh
chmod 755 /data/jenkins_home
chmod 700 /data/jenkins_home/.ssh
chmod 600 /data/jenkins_home/.ssh/id_rsa  # 私钥必须 600 权限
chmod 644 /data/jenkins_home/.ssh/id_rsa.pub # 公钥 644

# 3. 手动创建 known_hosts 文件（容器内需要）
touch /data/jenkins_home/.ssh/known_hosts
chown 1000:1000 /data/jenkins_home/.ssh/known_hosts
chmod 644 /data/jenkins_home/.ssh/known_hosts

# 4. 手动写入 GitHub 主机密钥到 known_hosts（避免容器内操作）
ssh-keyscan github.com > /data/jenkins_home/.ssh/known_hosts
```

## 重启 Jenkins 容器

```shell
# 1. 停止旧的 jenkins 容器
docker stop jenkins

# 2. 删除旧的 jenkins 容器
docker rm jenkins

# 4. 启动 Jenkins 容器
docker run -d \
  --name jenkins \
  --privileged \
  --cpus 1.5 \
  -u 1000:1000 \
  -p 8080:8080 \
  -p 50000:50000 \
  -v /data/jenkins_home:/var/jenkins_home \
  -v /data/project:/data/project \
  -v /data/jenkins-npm-cache:/root/.npm \
  -v /usr/local/nginx/sbin/nginx:/usr/local/nginx/sbin/nginx \
  --restart=always \
  -e TZ="Asia/Shanghai" \
  jenkins/jenkins:2.504.3-lts
```

## 验证 SSH 连通性

```shell
# 1. 验证容器内路径（关键）
docker exec -it jenkins bash

# 2. 容器内执行：查看 .ssh 目录是否存在（容器内路径是 /var/jenkins_home/.ssh）
ls -la /var/jenkins_home/.ssh
# 能看到 id_rsa、id_rsa.pub 即为正确

# 3. 测试 GitHub 连通性（容器内执行）
ssh -i /var/jenkins_home/.ssh/id_rsa -o UserKnownHostsFile=/var/jenkins_home/.ssh/known_hosts -T git@github.com
```

## 配置 GitHub 公钥

```shell
# 查看公钥（复制到 GitHub）
cat /data/jenkins_home/.ssh/id_rsa.pub
```

登录 GitHub → Settings → SSH and GPG keys → New SSH key → 粘贴查看的公钥，保存。

## 安装 Jenkins 插件

- NodeJS（需要在系统管理 → 全局工具配置中配置，版本需要适配 Hexo 项目依赖，我这里使用 20.20.0）
- Git

## 新建任务

1. 输入任务名称，选择 “构建一个自由风格的软件项目”，点击 “确定” 。
2. 源码管理 → 选择 Git → 输入仓库 URL（`git@github.com:你的用户名/你的仓库名.git`），点击 “添加” 。
3. 「凭证」：留空（已将 SSH 密钥挂载到 /var/jenkins_home/.ssh，无需额外配置凭证）。
4. **Environment**：选择“Provide Node & npm bin/ folder to PATH”，选择 NodeJS 版本。
5. 添加构建步骤，选择“执行 Shell”

```shell
#!/bin/bash
set -e  # 报错立即退出，便于排查问题

# ========== 1. 配置 SSH 环境（核心，解决 GitHub 连接问题） ==========
# 1.1 确保 .ssh 目录存在并设置正确权限（SSH 要求目录权限 700，文件 600）
mkdir -p /var/jenkins_home/.ssh
chmod 700 /var/jenkins_home/.ssh  # 关键：SSH 目录必须是 700 权限

# 1.2 确保 known_hosts 文件存在并添加 GitHub 主机密钥（覆盖旧内容，避免重复）
touch /var/jenkins_home/.ssh/known_hosts
chmod 600 /var/jenkins_home/.ssh/known_hosts  # 关键：SSH 文件必须是 600 权限
ssh-keyscan github.com > /var/jenkins_home/.ssh/known_hosts

# 1.3 加载 SSH 密钥（强制指定路径，避免环境问题）
export GIT_SSH_COMMAND="ssh -i /var/jenkins_home/.ssh/id_rsa -o UserKnownHostsFile=/var/jenkins_home/.ssh/known_hosts"

# ========== 2. Hexo 构建与部署 ==========
# 2.1 进入 Jenkins 拉取源码的工作目录
cd ${WORKSPACE}

# 2.2 安装 Hexo 核心依赖（容器内全局安装）
npm install -g hexo-cli@4.3.2

# 2.3 安装项目依赖 + 部署插件（提前安装，避免部署时缺失）
npm install
npm install hexo-deployer-git --save

# 2.4 清理旧静态文件 + 生成新静态文件
hexo clean
hexo generate

# 2.5 配置 Git 提交者身份（关键：进入 .deploy_git 目录配置本地身份）
# 先确保 .deploy_git 目录存在（hexo generate 后会生成）
mkdir -p ${WORKSPACE}/.deploy_git
cd ${WORKSPACE}/.deploy_git
# 配置当前仓库（.deploy_git）的本地 Git 身份（无 --global）
git config user.email "Your Email"
git config user.name "Your Name"

# 2.6 回到 Hexo 根目录执行部署
cd ${WORKSPACE}
hexo deploy
```

## 自动部署

1. 进入任务 -> 配置
2. Triggers -> 选择“定时构建”，日程表 中填入 Cron 表达式。我设置的是每日0点执行一次。填入 `H 0 * * *` 。（用 `H 0 * * *` 替代 `0 0 * * *` 作用是分散负载、避免服务器峰值压力）
3. 保存配置。

## 解决错误

```
Author identity unknown

*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.

fatal: unable to auto-detect email address (got 'jenkins@545faf00ffb9.(none)')
error: src refspec HEAD does not match any
error: failed to push some refs to 'github.com:Azurestone21/azurestone21.github.io.git'
```

需要设置：
git config user.email "Your Email"
git config user.name "Your Name"
