---
title: Flutter 入门
date: 2026-03-28 10:00:00
categories: [移动端]
tags: [Flutter]
---

## 配置环境

### 安装 Flutter SDK

1. 官网下载：https://docs.flutter.cn/install/archive
2. 使用国内镜像链接

```text
https://storage.flutter-io.cn/flutter_infra_release/releases/stable/windows/flutter_windows_3.35.5-stable.zip

# 清华镜像（备用）
https://mirrors.tuna.tsinghua.edu.cn/flutter_infra_release/releases/stable/windows/flutter_windows_3.35.5-stable.zip


# 其他版本 / 系统（替换链接即可）：
把官方地址里的 storage.googleapis.com
换成：storage.flutter-io.cn
```

下载后，解压缩到自定义目录。

### 配置环境变量

1. 环境变量 -> 系统变量 -> Path -> 新增 Flutter SDK 的bin目录
2. 新增系统变量
   PUB_HOSTED_URL -> https://pub.flutter-io.cn
   FLUTTER_STORAGE_BASE_URL -> https://mirrors.tuna.tsinghua.edu.cn/flutter

### 检查安装是否成功

关闭所有命令行，执行以下命令检查安装是否成功：

```shell
flutter --version
```

### 检查环境

自动检查环境，提示缺什么

```shell
flutter doctor
```

### 安装 Visual Studio（可选）

如果需要开发桌面端应用，需要安装 Visual Studio。

选择适合的版本，安装后执行 flutter clean。

### 开启开发者模式

以win11为例，打开设置 -> 高级 -> 开发者选项 -> 开发者模式 -> 开启。

## 查看插件

https://pub.dev/packages

## 新建项目

```shell
flutter create <project_name>
```

下载项目插件

```shell
flutter pub get
```

### 启动失败

```shell
D:\Project\Flutter\elara_player [0:0] $ flutter build windows --debug
Flutter assets will be downloaded from
https://storage.flutter-io.cn. Make sure you trust this source!

Building Windows application...
CMake Error at flutter/ephemeral/.plugin_symlinks/media_kit_libs_windows_video/windows/CMakeLists.txt:74 (message):
Building Windows application...
 mpv-dev-x86_64-20230807-git-140ec21.7z integrity check failed.

Building Windows application...   8.8s
Unable to generate build files
```

原因：在https://github.com/media-kit/libmpv-win32-audio-build/releases/ 上没有找到对应的版本。

解决：

D:\Project\Flutter\elara_player\windows\flutter\ephemeral\.plugin_symlinks\media_kit_libs_windows_video\windows\CMakeLists.txt
