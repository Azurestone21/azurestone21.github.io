---
title: hexo+github构建静态网站
date: 2026-03-03 23:26:11
categories: [技术教程]
tags: [hexo, github]
---

在之前的[Hexo使用教程](Hexo%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B.md)中，我们已经安装了hexo，并且创建了一个hexo项目。

在这篇教程中，我们将使用hexo项目部署到github pages上，实现静态网站的搭建。

## 创建github仓库

1. 登录github，点击`New repository`创建新仓库
2. 仓库名称为`用户名.github.io`
3. 可见状态选择为`Public`
4. 点击`Create repository`创建仓库

## 配置hexo项目

安装 hexo-deployer-git

```bash
npm install hexo-deployer-git --save
```

修改 `_config.yml` 文件末尾的 Deployment 部分

```
deploy:
  type: git
  repository: git@github.com:用户名/用户名.github.io.git
  branch: master
```

## 部署项目

清除缓存等文件并重新发布网站
- hexo clean 清除缓存文件等
- hexo g 生成页面
- hexo d 部署到github pages

```bash
# 完整一键部署命令（推荐，所有系统通用）
hexo clean
hexo generate
hexo deploy

# 简写版（g=generate，d=deploy，效果完全一样）
hexo clean
hexo g
hexo d
```
