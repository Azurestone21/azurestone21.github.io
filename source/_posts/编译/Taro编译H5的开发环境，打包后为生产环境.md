---
title: Taro编译H5的开发环境，打包后为生产环境
date: 2026-01-05 14:55:58
categories: [编译]
tags: [pnpm, 编译, Taro]
---

## 背景

项目使用 Taro 编译为 H5。有环境配置文件`.env.development`、`.env.test`、`.env.production`，里面定义了各个环境的环境变量。

```json
"build:h5 dev": "taro build --type h5 --mode development",
"build:h5 test": "taro build --type h5 --mode test",
"build:h5 prod": "taro build --type h5 --mode production",
```

## 问题

执行`pnpm run build:h5 dev`后，获取的是`.env.production`里面的环境变量。

## 原因

pnpm 对带有空格的脚本名支持存在问题 ，导致：

- dev 没有被识别为完整脚本名的一部分
- 而是被当作参数传递给了 build:h5 脚本
- 实际执行的命令变成： taro build --type h5 "dev"
- 因此 --mode development 参数没有被正确传递

## 解决

将 package.json 中的脚本名从 `build:h5 dev` 改为 `build:h5:dev` （使用连字符代替空格）

执行`npm run build:h5:dev`
