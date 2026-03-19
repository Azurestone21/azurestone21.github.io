---
title: 安装MySql
date: 2024-11-12 21:01:00
category: [服务器]
tags: [Linux, MySql]
---

## 安装

### 1.下载

```sh
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
```

### 2.解压

```sh
tar xzvf mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz
```

### 3.移动到 mysql 目录

一般是 /usr/local/mysql

```sh
mv mysql-5.7.24-linux-glibc2.12-x86_64 /usr/local/mysql
```

### 4.创建 data 目录

```sh
mkdir /usr/local/mysql/data
```

### 5.创建用户组

```sh
groupadd mysql     #创建用户组
# -r 参数表示 mysql 用户是系统用户，不可用于登录系统，创建用户 mysql 并将其添加到用户组 mysql 中
useradd -r -g mysql mysql
```

### 6.分配用户组

```sh
chown -R mysql /usr/local/mysql/ # 将文件的所有属性改为 mysql 用户
chgrp -R mysql /usr/local/mysql/ # 将组属性改为 mysql 组
```

### 7.修改配置文件

```sh
vim /etc/my.cnf

[mysqld]
bind-address=0.0.0.0
port=3306
user=mysql
socket=/tmp/mysql.sock
# 设置mysql的安装目录
basedir=/usr/local/mysql
# 设置mysql数据库的数据的存放目录
datadir=/usr/local/mysql/data
#设置mysql数据库的日志及进程数据的存放目录
log-error=/usr/local/mysql/data/mysql.err
pid-file=/usr/local/mysql/data/mysql.pid
#character config
character_set_server=utf8mb4
symbolic-links=0
explicit_defaults_for_timestamp=true
```

### 8.初始化mysql

```sh
[root@localhost /]# cd /usr/local/mysql/bin
[root@localhost bin]# ./mysqld --defaults-file=/etc/my.cnf --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data/ --user=mysql --initialize
```

### 9.查看初始密码

```sh
cat /usr/local/mysql/data/mysql.err

2024-06-22T16:47:22.528749Z 0 [Warning] InnoDB: New log files created, LSN=45790
2024-06-22T16:47:22.618665Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2024-06-22T16:47:22.683644Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: 18e8d979-30b7-11ef-bee3-00163e037111.
2024-06-22T16:47:22.686107Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2024-06-22T16:47:22.686665Z 1 [Note] A temporary password is generated for root@localhost: N!te%Ok(;4eb
```

### 10.配置环境变量

```sh
vi ~/.bashrc # 好像不生效
vim /etc/profile

# 加以下配置
export PATH=$PATH:/usr/1ocal/mysql/bin

# 重启配置
source ~/.bashrc # 好像不生效
source /etc/profile
```

### 11.mysql 服务

```sh
# 创建
systemctl enable mysql.service

# 启动
[root@iZ7xvgqq72fzxpvqh6itdkZ bin]# /usr/local/mysql/support-files/mysql.server start
Starting MySQL.                                            [  确定  ]
```

### 12.登录mysql，修改密码

```sh
[root@localhost /]#  mysql -u root -p
Enter password:
mysql>set password for root@localhost = password('n123456');
```

## 服务

```sh
# 查看 mysql 启动状态
service mysql status

# 启动 mysql 服务
service mysql start

# 停止 mysql 服务
service mysql stop
```

## 设置开机自启动

1.将mysql.server 文件复制到 /etc/rc.d/init.d/ 目录下mysql 文件

```sh
cp /usr/local/mysql/support-files/mysql.server /etc/rc.d/init.d/mysql
```

2.赋予权限

```sh
chmod +x /etc/init.d/mysql
```

3.添加服务

```sh
chkconfig --add mysql
```

4.查看服务列表

```sh
chkconfig --list

注：该输出结果只显示 SysV 服务，并不包含
原生 systemd 服务。SysV 配置数据
可能被原生 systemd 配置覆盖。

      要列出 systemd 服务，请执行 'systemctl list-unit-files'。
      查看在具体 target 启用的服务请执行
      'systemctl list-dependencies [target]'。

mysqld           0:关 1:关 2:开 3:开 4:开 5:开 6:关
```

如果345为关

```sh
chkconfig --level 345 mysqld on
```

## 开放远程连接

```sh
mysql>use mysql;

msyql>update user set user.Host='%' where user.User='root';
Query OK, 1 row affected (0.00 sec)
Rows matched: 1  Changed: 1  Warnings: 0

msyql> grant all privileges on *.* to 'root'@'%' identified by 'n123456' with grant optionb;
msyql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'n123456' WITH GRANT OPTION; # ??

mysql>flush privileges;
Query OK, 0 rows affected (0.00 sec)

```

## 删除

查看已安装的mysql

```sh
rpm -qa | grep mysql
```

删除

```sh
rpm -e --nodeps mysql-libs-5.1.73-5.el6_6.x86_64
```

查看 mysql 目录，删除

```
[root@localhost /]# whereis mysql
mysql: /usr/bin/mysql /usr/include/mysql
[root@localhost lib]# find / -name mysql
/data/mysql
/data/mysql/mysql


[root@localhost /]#  rm -rf /usr/bin/mysql /usr/include/mysql /data/mysql /data/mysql/mysql
```

## 问题

> mysql -u root -p
> -bash: mysql: 未找到命令

解决办法：

1. 进入mysql的bin目录，使用 `./mysql -u root -p`

2. 软链接

```sh
ln -s /usr/local/mysql/bin/mysql /usr/bin
```

> [root@iZ7xvgqq72fzxpvqh6itdkZ /]# mysql -v
> ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: NO)

原因：

> Unit mysql.service could not be found.
> Failed to start mysqld.service: Unit mysqld.service not found.

原因：mysql 服务没有安装

解决：

```sh
[root@iZ7xvgqq72fzxpvqh6itdkZ /]# find / -name  mysql.server
/usr/local/mysql/support-files/mysql.server
[root@iZ7xvgqq72fzxpvqh6itdkZ /]# cd /etc/init.d
[root@iZ7xvgqq72fzxpvqh6itdkZ init.d]# ls
functions  README
[root@iZ7xvgqq72fzxpvqh6itdkZ init.d]# cd /
[root@iZ7xvgqq72fzxpvqh6itdkZ /]# cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql
[root@iZ7xvgqq72fzxpvqh6itdkZ /]# service mysqld status
MySQL running (21056)                                      [  OK  ]
```

> 在本机用 Navicat 链接服务器数据库报错
> 2003 - Can't connect to MySQL server on ‘8.138.20.29'(10060 "Unknown error)

解决：

1. 查看本地 mysql 服务是否启动
2. 查看服务器 mysql 服务是否启动

```sh
systemctl start mysql
```

3. 检查MySQL配置：编辑MySQL的配置文件（my.cnf或my.ini），确保bind-address参数设置为允许远程连接的值（如0.0.0.0），并检查port参数是否设置为正确的值（默认为3306）。

4. 查看服务器是否开放远程连接
5. 检查防火墙是否开放端口

```sh
firewall-cmd --list-ports #查看已经开放的端口
firewall-cmd --zone=public --add-port=3306/tcp --permanent # 打开防火墙端口3306
firewall-cmd --reload  #重启firewall
```

6. 阿里云会默认关闭一些端口，需要进入控制台手动添加安全组规则
   ![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260312001929387.png)

## 参考

<https://blog.csdn.net/weixin_45371955/article/details/129026276>

<https://www.cnblogs.com/csjoz/p/18178796>
