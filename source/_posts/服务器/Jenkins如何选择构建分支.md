---
title: Jenkins如何选择构建分支
date: 2026-03-17 14:23:22
categories: [服务器]
tags: [Jenkins]
---

1. 安装 Git Parameter 插件

Git Parameter 插件允许用户在构建过程中动态地从 Git 存储库中选择分支、标签、拉取请求或修订号作为参数。

2. 配置构建参数

进入任务 -> 配置/Configuration -> General -> 勾选“参数化构建过程/This project is parameterized” -> 添加参数 -> 选择“Git参数/Git Parameter”。

3. Build with Parameters

设置完参数，任务中会出现“Build with Parameters”，点击进去就可以选择构建分支了。