---
title: 安装JDK
date: 2024-07-23 00:57:20
categories: [技术教程]
tags: [JDK]
---

[https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html](https://www.oracle.com/java/technologies/javase/javase-jdk8-downloads.html)

> 这里安装的是17.0.10

- 安装JDK安装包（一直"下一步"）
- 配置JDK环境

如果有多个JDK，可以为每个JDK创建系统变量，再创建一个JAVA_HOME对应正在使用的变量

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260305000657572.png)

- 配置Path环境变量

```plain
%JAVA_HOME%\bin  // 放在前面
%JAVA_HOME%\jre\bin
```

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260305000657573.png)

- 重启命令行中，输入 java -version，验证安装是否成功

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260305000657574.png)
