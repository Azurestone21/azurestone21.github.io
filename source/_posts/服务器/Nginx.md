---
title: 安装Nginx
date: 2024-11-12 21:01:00
category: [服务器]
tags: [Linux, Nginx]
---

Nginx 是由伊戈尔·赛索耶夫为俄罗斯访问量第二的 Rambler.ru 站点（俄文：Рамблер）开发的，公开版本1.19.6发布于2020年12月15日。

Nginx (engine x) 是一个高性能的 HTTP 和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务。可在 BSD-like 协议下发行。其特点是占有内存少，并发能力强。

## 服务器配置 Nginx

### 安装 Nginx 依赖库

1、安装gcc环境

编译时依赖gcc环境

```sh
yum -y install gcc gcc-c++ autoconf automake make
```

2、安装 pcre

提供nginx支持重写功能

```sh
yum -y install pcre pcre-devel
```

3、安装zlib

zlib 库提供了很多压缩和解压缩的方式，nginx 使用 zlib 对 http 包内容进行 gzip 压缩

```sh
yum -y install zlib zlib-devel make libtool
```

4、安装openssl

安全套接字层密码库，用于通信加密

```sh
yum -y install openssl openssl-devel
```

### nginx 安装

手动创建用户和用户组

```sh
groupadd nginx
useradd nginx -g nginx -s /sbin/nologin -M
```

官网下载nginx源码包

```sh
wget https://nginx.org/download/nginx-1.20.2.tar.gz
```

解压

```sh
tar -zxvf nginx-1.20.2.tar.gz
```

编译目录

```sh
cd nginx-1.20.2
```

检查平台安装环境

```sh
./configure --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --user=nginx --group=nginx
```

> 参数说明：
>
> –prefix=/usr/local/nginx #编译安装目录
> –user=nginx #所属用户nginx
> –group=nginx #所属组nginx
> –with-http_stub_status_module #该模块提供nginx的基本状态信息
> –with-http_ssl_module #支持HTTPS

编译源码并安装，默认安装到 `/usr/local/nginx`

```sh
make       # 编译
make install    # 安装

make && make install # 可以一起执行
```

查找 nginx 安装位置

```sh
whereis nginx

# nginx: /usr/local/nginx
```

nginx编译安装完成以后，修改nginx.conf

```sh
vim /usr/local/nginx/conf/nginx.conf
```

```conf
server {
  listen       80;
  server_name  localhost;

  #charset koi8-r;

  #access_log  logs/host.access.log  main;

  location / {
      root   html;
      index  index.html index.htm;
  }

  # 新增
  location /blogs {
      alias  /data/project/admin-vue; # 项目存放目录 /data/project/admin-vue
      index  index.html index.htm;
  }
}
```

`alias` 需要使用绝对路径。使用相对路径会 Nginx 会默认在前面拼接 Nginx 安装目录（/usr/local/nginx/）

打开 `http://服务器IP/blogs` 即可访问项目。

### 配置 nginx 服务

创建并配置服务文件

```sh
vi /lib/systemd/system/nginx.service
```

配置信息

```sh
[Unit]
Description=nginx
After=network.target

[Service]
Type=forking
ExecStart=/usr/local/nginx/sbin/nginx
ExecReload=/usr/local/nginx/sbin/nginx reload
ExecStop=/usr/local/nginx/sbin/nginx quit
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

创建服务

```sh
systemctl enable nginx.service
```

使用服务命令

```sh
systemctl start nginx
```

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260312001610411.png)

## 命令

### nginx版本信息查看

```sh
nginx -V
```

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260312001610409.png)

### 配置文件

```sh
vim /usr/local/nginx/conf/nginx.conf
```

### nginx配置文件正否正确

```sh
[root@iZ7xvgqq72fzxpvqh6itdkZ /]# nginx -t -c /usr/local/nginx/conf/nginx.conf
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
```

### 创建服务

```sh
systemctl enable nginx.service
```

### 启动 nginx 服务

```sh
systemctl start nginx
```

### 查看 nginx 服务状态

```sh
systemctl status nginx.service
```

### 查看进程

```sh
ps -ef | grep nginx
```

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260312001610408.png)

## 问题

### -bash: nginx: 未找到命令

```sh
-bash: nginx: 未找到命令
```

解决方式

1. 打开环境变量所在的文件：

```sh
vim /etc/profile
```

2. 在profile文件末尾，加上一行指向你的nginx的安装位置的sbin 目录

```sh
export PATH=$PATH:/usr/local/nginx/sbin
```

3. 重新加载环境，解决

```sh
source /etc/profile
```

4. 重启nginx

nginx -s reload

### 启动 nginx 失败

```sh
[root@iZ7xvgqq72fzxpvqh6itdkZ /]# systemctl restart nginx
Job for nginx.service failed because the control process exited with error code.
See "systemctl status nginx.service" and "journalctl -xe" for details.
```

查看日志

```sh
[root@iZ7xvgqq72fzxpvqh6itdkZ nginx]# journalctl -xe
6月 21 00:40:50 iZ7xvgqq72fzxpvqh6itdkZ systemd[1]: Starting system activity accounting tool...
-- Subject: sysstat-collect.service 单元已开始启动
-- Defined-By: systemd
-- Support: https://access.redhat.com/support
--
-- sysstat-collect.service 单元已开始启动。
6月 21 00:40:50 iZ7xvgqq72fzxpvqh6itdkZ systemd[1]: sysstat-collect.service: Succeeded.
-- Subject: Unit succeeded
-- Defined-By: systemd
-- Support: https://access.redhat.com/support
--
-- The unit sysstat-collect.service has successfully entered the 'dead' state.
6月 21 00:40:50 iZ7xvgqq72fzxpvqh6itdkZ systemd[1]: Started system activity accounting tool.
-- Subject: sysstat-collect.service 单元已结束启动
-- Defined-By: systemd
-- Support: https://access.redhat.com/support
--
-- sysstat-collect.service 单元已结束启动。
--
-- 启动结果为“done”。
6月 21 00:42:49 iZ7xvgqq72fzxpvqh6itdkZ systemd[1]: Starting nginx...
-- Subject: nginx.service 单元已开始启动
-- Defined-By: systemd
-- Support: https://access.redhat.com/support
--
-- nginx.service 单元已开始启动。
6月 21 00:42:49 iZ7xvgqq72fzxpvqh6itdkZ nginx[14206]: nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
6月 21 00:42:50 iZ7xvgqq72fzxpvqh6itdkZ nginx[14206]: nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
6月 21 00:42:50 iZ7xvgqq72fzxpvqh6itdkZ nginx[14206]: nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
6月 21 00:42:51 iZ7xvgqq72fzxpvqh6itdkZ nginx[14206]: nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
6月 21 00:42:51 iZ7xvgqq72fzxpvqh6itdkZ nginx[14206]: nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)
6月 21 00:42:52 iZ7xvgqq72fzxpvqh6itdkZ nginx[14206]: nginx: [emerg] still could not bind()
6月 21 00:42:52 iZ7xvgqq72fzxpvqh6itdkZ systemd[1]: nginx.service: Control process exited, code=exited status=1
6月 21 00:42:52 iZ7xvgqq72fzxpvqh6itdkZ systemd[1]: nginx.service: Failed with result 'exit-code'.
-- Subject: Unit failed
-- Defined-By: systemd
-- Support: https://access.redhat.com/support
--
-- The unit nginx.service has entered the 'failed' state with result 'exit-code'.
6月 21 00:42:52 iZ7xvgqq72fzxpvqh6itdkZ systemd[1]: Failed to start nginx.
-- Subject: nginx.service 单元已失败
-- Defined-By: systemd
-- Support: https://access.redhat.com/support
--
-- nginx.service 单元已失败。
--
-- 结果为“failed”。
```

原因：80端口被占用。

解决办法：找到占用 80 端口的进程（lsof），并将其结束（kill）。

如果是 `AliYunDun`，需要用 `fuser -k 80/tcp` 命令

如果还不行，检查防火墙是否开放80端口

## 参考

<https://blog.csdn.net/AlbenXie/article/details/131841546?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-131841546-blog-114821637.235^v43^pc_blog_bottom_relevance_base6&spm=1001.2101.3001.4242.1&utm_relevant_index=3>
