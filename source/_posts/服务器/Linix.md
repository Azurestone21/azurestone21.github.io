---
title: Linux实践
date: 2024-11-12 21:01:00
category: [服务器]
tags: [Linux]
---

## 命令

### lsof 查看端口占用进程

```sh
yum install lsof # 如果命令不存在，需要安装

lsof -i # 查看所有端口

lsof -i:80 # 查看某个端口
```

### 查询所有开启的端口

```sh
netstat -tlnp
```

### 查询所有80端口进程

```sh
netstat -apn|grep :80
```

### 查询80端口是否启动

```sh
firewall-cmd --query-port=80/tcp
```

### 添加80端口

之后重启防火墙

```sh
firewall-cmd --zone=public --add-port=80/tcp --permanent
```

### kill 结束进程

```sh
kill -9 [进程号]
```

### 防火墙

```sh
systemctl start firewalld # 开启

systemctl status firewalld # 关闭

firewall-cmd --reload # 重启

systemctl enable firewalld.service # 开启开机启动
systemctl disable firewalld.service # 关闭开机启动‘

# 查看防火墙状态
systemctl status firewalld
# 或
firewall-cmd --state


firewall-cmd --list-port # 查询已经开放的端口列表
firewall-cmd --query-port=80/tcp # 查询某个端口是否开放
```

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260312001610410.png)

### 启动 nginx 服务

```sh
systemctl start nginx
```

## nvm 安装

### 一、安装

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260312001846526.png)

- 方式一

```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash
// 或
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.2/install.sh | bash
```

- 方式二

手动下载nvm 包

```sh
wget https://github.com/nvm-sh/nvm/archive/refs/tags/v0.39.2.tar.gz
```

创建安装地址

```sh
mkdir -p /root/.nvm
```

解压到安装目录

```sh
tar -zxvf v0.38.0.tar.gz -C /root/.nvm
```

### 二、配置环境变量

编辑配置文件

```sh
vim ~/.bashrc
```

尾部添加该内容（如果已经有则不需要配置，直接:q退出）

```sh
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

// 或

export NVM_DIR="$HOME/.nvm/nvm-0.39.2"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
```

使用配置（已经有配置也需要执行）

```sh
source ~/.bashrc
```

### 三、检查

```sh
nvm -v
```

安装node

```sh
nvm install 14.17.0
```
