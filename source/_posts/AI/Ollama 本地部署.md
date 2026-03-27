---
title: Ollama 本地部署
date: 2026-03-26 10:00:00
categories: [AI]
tags: [Ollama]
---

## Ollama是什么

Ollama 是一个本地部署的模型管理工具，用于在本地运行大语言模型（LLMs）。

## Ollama 优点和缺点

### 优点

1. 部署简单，一行命令跑大模型，新手友好
2. 完全本地运行，隐私性强，数据不上云，不发给第三方
3. 轻量、占用低，比传统部署（Transformers + PyTorch）省资源。支持量化模型
4. 自带 API，兼容 OpenAI 格式，可以直接接入自己的项目、后端、前端、AI 应用
5. 模型生态极丰富，Llama3、Qwen2、GLM、DeepSeek、Gemma、Phi 等全都支持。
6. 适合开发 / 二次开发
7. 免费、开源、无限制

### 缺点

1. 性能看电脑配置
2. 不擅长超大规模模型，不适合做高并发、多用户服务，70B 模型需要高配置，普通电脑跑不动
3. 功能不如云端 API 强，本地模型整体弱于顶级云模型
4. 更适合 “个人 / 小团队”

## 安装

官网：https://ollama.com/

Ollama 适用全平台，根据系统选择对应的版本。

Ollama 默认安装在C盘，不能修改安装目录。如果不想在C盘安装，需要手动指定安装目录。

### 自定义安装目录

1. 把安装包放在在需要安装的目录下，例如 `E:\Software\Ollama`
2. 在目录中打开cmd，执行命令：

```shell
OllamaSetup.exe /DIR=E:\Software\Ollama
```

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260326213848759.png)

3. 安装完成后，就会安装在对应目录下。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260326213848760.png)

### 设置模型安装目录

默认的模型安装目录是 `C盘 -> 用户 -> 自己使用的用户名 -> .ollama -> models`。

方法1：在 Ollama 客户端进入 设置，可以修改模型安装目录。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260326213848761.png)

方法2：在安装目录中新一个模型文件夹，例如 `E:\Software\Ollama\models`，配置环境变量->系统变量

```
OLLAMA_MODELS = E:\Software\Ollama\models
```

## 核心命令

```shell
# 查看版本
ollama

# 下载模型
ollama pull <model-name>

# 删除模型
ollama delete <model-name>

# 运行该模型（也可以直接通过这个命令安装运行）
ollama run <model-name>

# 查看所有模型
ollama list

# 查看运行中模型
ollama ps

# 删除模型
ollama rm <model-name>
```

## 下载模型

在 https://ollama.com/search 中查看需要下载的模型的名称。

**方法1：命令行下载**

```shell
# 下载模型
ollama pull <model-name>

# 运行该模型（也可以直接通过这个命令安装运行）
ollama run <model-name>
```

**方法2：客户端下载**

选择模型，如果没有安装模型，发送消息后，会自动下载模型。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260326213848762.png)

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260326213848763.png)

## 访问 Ollama API

Ollama 默认在 http://localhost:11434 上提供服务。

## 关闭/打开思考过程

发送消息后，模型再生成回答之前，会先生成一个思考过程，展示模型的思考过程和推理步骤。但是这个过程非常慢，影响效率。

```shell
# 关闭思考过程
ollama run <model-name> --think=false
# 或 在对话中输入
/set nothink

# 打开思考过程
ollama run <model-name> --think=true
# 或 在对话中输入
/set think
```
