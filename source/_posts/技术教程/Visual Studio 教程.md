---
title: Visual Studio 教程
date: 2026-03-28 10:00:00
categories: [技术教程]
tags: [Visual Studio, C++]
---

## 安装 Visual Studio

1. https://visualstudio.microsoft.com/zh-hans/vs/older-downloads/，下载需要的版本。
2. 选择“使用C++的桌面开发”，其他不需要改，点击安装。
3. 如果安装后发现缺少一些组件，不需要重装整个IDE。打开Visual Studio Installer，找到已安装版本点击"修改"，在"单个组件"标签页补充安装。

## 卸载 Visual Studio

1. 进入 https://github.com/Microsoft/VisualStudioUninstaller/releases ，下载 TotalUninstaller.zip。
2. 解压后运行 Setup.ForcedUninstallexe，输入 Y 确认卸载。
3. 删除安装目录（下面是默认安装目录）。

```
C:\Program Files\Microsoft Visual Studio
C:\Program Files(x86)\Microsoft Visual Studio
```

4. 删除注册表项
   1. win+R，输入 regedit，打开注册表编辑器。
   2. 找到计算机\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\VisualStudio，右键删除。
