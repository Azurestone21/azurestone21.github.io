---
title: Electron打包
date: 2026-03-10 23:49:30
categories: [桌面端]
tags: [Electron]
---

## 配置package.json

```json
{
  "name": "your-electron-app", // 你的应用名称（不能有中文）
  "version": "1.0.0", // 版本号
  "main": "./out/main/index.js", // 主进程入口文件（根据实际调整）
  "scripts": {
    "dev": "electron-vite dev", // 运行项目
    "build:win": "npm run build && electron-builder --win" // 打包命令
  },
  "build": {
    "appId": "com.yourcompany.yourapp", // 应用唯一标识（反向域名格式）
    "productName": "你的应用名称", // 打包后exe显示的名称（可含中文）
    "directories": {
      "output": "dist" // 打包产物输出目录
    },
    "win": {
      "target": [
        {
          "target": "nsis", // 打包成exe安装包（最常用）
          "arch": [
            "x64", // 64位系统
            "ia32" // 32位系统（可选）
          ]
        }
      ],
      "icon": "build/icon.ico" // 应用图标（必须是ico格式，建议256x256）
    },
    "nsis": {
      "oneClick": false, // 取消一键安装（可选，改为自定义安装）
      "allowToChangeInstallationDirectory": true, // 允许用户修改安装目录
      "installerIcon": "build/icon.ico", // 安装包图标
      "uninstallerIcon": "build/icon.ico", // 卸载程序图标
      "installerHeaderIcon": "build/icon.ico", // 安装头部图标
      "createDesktopShortcut": true, // 创建桌面快捷方式
      "createStartMenuShortcut": true // 创建开始菜单快捷方式
    }
  }
}
```

## 执行打包命令

```bash
npm run build:win
```

打包完成后，dist目录下会生成：
1. [your-app] Setup [版本号].exe：可执行安装包
2. win-unpacked目录：免安装的绿色版（直接运行里面的 exe 即可）

如果打包时遇到：
```
errorOut=Reserved header is not 0 or image type is not icon for '项目地址\build\icon.ico'
Fatal error: Unable to set icon
```
原因可能是icon.ico文件格式不合法，不是标准的 Windows ICO 格式，或者文件本身存在损坏。
