---
title: Electron入门
date: 2025-12-28 10:44:11
categories: [技术教程]
tags: [electron]
---

https://www.electronjs.org/zh/docs/latest/

npm下载electron失败，可以使用yarn来下载electron。

yarn config set ELECTRON_MIRROR https://npmmirror.com/mirrors/electron/指定electron的镜像，

再yarn add electron。

## 什么是Electron

Electron是一个使用 JavaScript、HTML 和 CSS 构建的跨平台桌面应用开发框架。

Electron 的本质是结合了 Chromium 与 Node.js。

## 环境

安装 Node.js，建议最新

## 创建应用

```bash
// 初始化npm包
npm init
```

`package.json`

```json
{
  "name": "my-electron-app",
  "version": "1.0.0",
  "description": "Hello World!", // 可以任意值，打包必填
  "main": "main.js", // 入口文件
  "author": "Jane Doe", // 可以任意值，打包必填
  "license": "MIT"
}
```

**安装依赖**

```bash
npm install --save-dev electron
```

## **启动 electron 应用**

`package.json` 的 scripts 里面配置

```json
"start": "electron .",
```

自动重启，使用 nodemon

- 安装

```bash
npm i nodemon -D
```

- 配置

```json
"start": "nodemon --exec electron ."
```

- 配置 nodemon.json

```json
{
  "ignore": [
    // 忽略文件
    "node_modules",
    "dist"
  ],
  "restartable": "r", // 刷新快捷键
  "watch": ["·"], // 监听所有文件
  "ext": "html,js,css" // 监听文件后缀
}
```

### Content-Security-Policy 警告

- 配置CSP

```html
<meta
  http-equiv="Content-Security-Policy"
  content="default-src 'self'; script-src 'self'; img-src 'self' data:; style-src 'self' 'unsafe-inline'"
/>
```

- 暂时关闭安全警告（在createWindow配置）

```js
process.env['ELECTRON_DISABLE_SECURITY_WARNINGS] = true
```

### 控制台

快捷键：`ctrl+shift+i`

## 进程

执行顺序：主进程 -》 预加载脚本 -》 渲染进程

### 主进程

每个 Electron 应用都有一个单一的主进程，作为应用程序的入口点（ `package.json`的`main`字段配置的文件）。

主进程在 Node.js 环境中运行，它具有 require 横块和使用所有Node.jsAPI 的能力，主进程的核心就是：使用BrowserWindow来创建和管理窗口。

```javascript
// main.js

// app模块，控制应用程序的事件生命周期。
// BrowserWindow模块，用于创建和管理应用程序窗口。
const { app, BrowserWindow } = require("electron/main");
const path = require("path");

// 创建窗口
function createWindow() {
  const win = new BrowserWindow({
    width: 800, // 窗口宽度
    height: 600, // 窗口高度
    autoHideMenuBar: true, // 隐藏默认工具栏
    webPreferences: {
      preload: path.join(__dirname, "preload.js"), // 预加载脚本
    },
  });
  // 加载本地文件
  win.loadFile("index.html");
}

// whenReady 当Electron 初始化完成。返回 Promise
// app.whenReady().then(() => {
app.on("ready", () => {
  // 创建窗口
  createWindow();

  // 如果没有窗口打开则打开一个窗口 (macOS)
  app.on("activate", () => {
    if (BrowserWindow.getAllWindows().length === 0) {
      createWindow();
    }
  });
});

// 关闭所有窗口时退出应用 (Windows & Linux)
app.on("window-all-closed", () => {
  if (process.platform !== "darwin") {
    app.quit();
  }
});
```

#### 完善窗口行为

Windows & Linux平台：关闭所有窗口时退出应用

```js
app.on("window-all-closed", () => {
  if (process.platform !== "darwin") {
    app.quit();
  }
});
```

macOS平台：如果没有窗口打开则打开一个窗口

```js
app.on("activate", () => {
  if (BrowserWindow.getAllWindows().length === 0) {
    createWindow();
  }
});
```

### 预加载脚本 Preload

预加载文件是主进程和渲染进程的通信桥梁。

预加载文件可以使用部分的nodejs

预加载（Preload）脚本是运行在渲染进程中的，但它是在网页内容加载之前执行的，这意味着它具有比普通渲染器代码更高的权限，可以访问Nodejs 的API，同时又可以与网页内容进行安全的交互。

简单说：它是 Node.js 和 WebAPI 的桥梁，Preload 脚本可以安全地将部分Nodejs 功能暴需给网页，从而减少安全风险。

```javascript
const { contextBridge } = require("electron");

contextBridge.exposeInMainWorld("myAPI", {
  version: process.version,
});
```

### 渲染进程

每个 BrowserWindow 实例都对应一个单独的渲染器进程，运行在渲染器进程中的代码，必须遵守网页标准，这也就意味着：渲染器进程无权直接访问 require 或使用任何 Node.js 的API。

## 进程通信（IPC）

### 渲染进程主进程（单向）

在演染器进程中 `ipcRenderer.send` 发送消息，在主进程中使用`ipcMain.on` 接收消息。

常用于：在Web中调用主进程的API。

### 渲染进程主进程（双向）

演染进程通过 `ipcRenderer.invoke` 发送消息，主进程使用`ipcMain.handle` 接收并处理消息，

> `ipcRender.invoke` 的返回值是 `Promise` 实例。

常用于：从渲染器进程调用主进程方法并等待结果。

### 主进程到渲染进程

主进程使用 `win.webContents.send` 发送消息，演染进程通过`ipcRenderer.on` 处理消息，

常用于：从主进程主动发送消息给渲染进程。：

## 打包应用

使用electron-builder 打包应用

1. 安装 electron-builder

```bash
npm install electron-builder -D
```

2. 在 `package.json` 中进行相关配置，具体配置如下：

```json
{
  "name": "video-tools", 						// 应用程序的名称
  "version": "1.0.0", 							// 应用程序的版本
  "main": "main.js", 								// 应用程序的入口文件
  "scripts": {
    "start": "electron .", 					// 使用electron.命令启动应用程序
    "build": "electron-builder", 		// 使用electron-builder打包座用程序，生成安装包
  },
  build:{
  	"appId": "com.atguigu.video",		// 应用程序的唯一标识符
  	// 打包windows平合安装包的具体配置
  	"win": {
      "icon": "./1ogo.ico",					//应用图标
      "target": [
				{
          "target": "nsis", 				// 指定使用 NSIS 作为安装程序格式
          "arch": ["x64"] 				 	// 生成64位安装包
				}
			]
		},
    "nsis": {
      "oneclick": fslse, 						// 设置为 false 使安装程序是示安装向导界面，需不是一健安装
      "perMachine": true, 					// 允许每台机暴安装一次，而不是每个用户都安装
      "allowToChangeInstallationDirectory”：true 	// 允许用户在安装过程中选择安装目录
    }
	},
  "devDependencies": {
  	"electron": "^30.0.0",				 	// 开发依赖中的Electron 版本
  	"electron-builder": "24.13.3"		// 开发依赖中的electron-builder版本
  },
  "author": "azure", 								// 作者信息
  "1icense": "ISC",							 		// 许可证信息
  "description": "A program based on Electron" // 应用程序的扬述
}
```

## 脚手架创建应用

@quick-start/electron

https://www.npmjs.com/package/@quick-start/create-electron

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260305000554834.png)

## 请求接口

### blocked csp 问题

加上服务器host：http://8.138.20.29:3000

1. 在主进程中设置 CSP

```javascript
function createWindow(): void {
  // Create the browser window.
  const mainWindow = new BrowserWindow({
    ...
  })

  // 修改 CSP 以允许从特定源加载资源（请求数据）
 mainWindow.webContents.session.webRequest.onHeadersReceived((details, callback) => {
    callback({
      responseHeaders: {
        ...details.responseHeaders,
        'Content-Security-Policy': ["default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data:; connect-src 'self' http://8.138.20.29:3000"],
      },
    });
  })
}
```

### 修改 CSP 元标签

```javascript
<meta
  http-equiv="Content-Security-Policy"
  content="default-src 'self'; script-src 'self'; style-src 'self' 'unsafe-inline'; img-src 'self' data:; connect-src 'self' http://8.138.20.29:3000"
/>
```

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260305000312314.png)
