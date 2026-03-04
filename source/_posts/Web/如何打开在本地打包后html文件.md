---
title: 如何打开在本地打包后html文件
date: 2025-12-30 09:15:01
categories: [Web]
tags: [Web]
---

## 使用 Node.js 的 serve 包

1. <font style="color:rgb(0, 0, 0);">先全局安装 serve（仅需安装一次）</font>

```bash
npm install -g serve
```

2. <font style="color:rgb(0, 0, 0);">进入项目 </font>`<font style="color:rgb(0, 0, 0);">dist</font>`<font style="color:rgb(0, 0, 0);"> 目录，或在项目根目录直接指定 dist 路径启动服务</font>

```bash
# 方式1：先进入dist目录
cd dist
serve

# 方式2：直接在项目根目录指定路径
serve ./dist
```

3. <font style="color:rgb(0, 0, 0);">终端会输出访问地址（如 </font>`<font style="color:rgb(0, 0, 0);">http://localhost:3000</font>`<font style="color:rgb(0, 0, 0);">），浏览器打开即可。</font>
