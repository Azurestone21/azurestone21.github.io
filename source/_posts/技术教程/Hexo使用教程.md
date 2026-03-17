---
title: Hexo使用教程
date: 2026-03-02 15:56:21
categories: [技术教程]
tags: [Hexo, Fluid]
---

文档：[https://hexo.io/zh-cn/](https://hexo.io/zh-cn/)

## 初始化项目

1. 安装 hexo

```bash
npm install -g hexo-cli
```

`hexo@8.1.1`需要 nodejs 版本`>=20.19.0`

2. 初始化 hexo 项目（我这里是已经创建了文件夹，直接使用`hexo init`）

```bash
hexo init
```

3. 启动项目

```bash
hexo s
```

4. 部署

```bash
hexo d
```



## 常用命令

1. 新建页面

```shell
# 创建「关于我」页面（路径：source/about/index.md）
hexo new page <页面名称>
```

2. 新建文章

Hexo 中所有博客文章（post）的默认存放目录是：`博客根目录/source/_posts/`

```shell
# 示例1：创建一篇文章（路径：source/_posts/文章标题.md）
hexo new 文章标题

# 示例2：在指定目录创建文章（路径：source/自定义路径/文章标题.md）
hexo new -p 自定义路径/文章标题
```

## Front-matter 配置

```shell
---
title: 自定义标题  # 文章标题
date: 2026-03-03 18:00:00  # 发布日期
categories: 教程  # 分类（可选）
tags: [Hexo, Fluid]  # 标签（可选）
---
```

## 图片

1. 图片放到到`source/images/`文件夹

2. 使用图床上传图片，获取图片链接

## 主题

[https://hexo.io/themes/](https://hexo.io/themes/)

1. 下载主题到项目 theme 文件夹，文件夹名称改为主题名称
2. 修改`_config.yml`文件

```yaml
# _config.yml
# 原主题可能是 landscape（默认），改为下载的主题名
theme: fluid # 对应 themes/fluid 文件夹
```

3. 配置主题

复制`themes/主题名/_config.yml`到项目根目录，改名为`_config.主题名.yml`，避免和原主题的配置文件冲突以及更新主题文件时被覆盖。

### Fluid

1. 代码块里面的代码不能自动换行