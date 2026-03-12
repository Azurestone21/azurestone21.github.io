---
title: 安装nvm
date: 2023-05-10 10:00:00
category: [服务器]
tags: [Linux, nvm]
---

## 安装nvm

1. 命令下载（命令模式默认安装在root目录下，可修改install.sh脚本中nvm_default_install_dir()方法更改默认路径）

```sh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
```

或

```sh
wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
```

2. 刷新配置

```sh
source ~/.bashrc
```

3. 验证

```sh
nvm --version
```
