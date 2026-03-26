---
title: LangChain 学习
date: 2026-03-26 10:00:00
categories: [AI]
tags: [LangChain]
---

> 前置知识：Python 基础、大语言模型（LLM）、提示词、RAG 框架基础

## LangChain 框架基础

### 1. 框架简介

- **创建**：由 Harrison Chase 于 2022 年 10 月创建。
- **定位**：围绕大语言模型（LLMs）的 Python 第三方开发框架，**自身不开发模型**。
- **核心理念**：为各类 LLM 提供通用接口，将相关组件“链接”在一起，简化复杂 LLM 应用开发。
- **地位**：后续学习 RAG 开发的主力框架。

### 2. 核心功能模块

| 模块        | 功能描述                                      |
| :---------- | :-------------------------------------------- |
| **Prompts** | 提示词工程，优化用户指令                      |
| **Models**  | 统一接口调用各类大模型（通义千问、OpenAI 等） |
| **History** | 管理会话历史，实现对话记忆                    |
| **Indexes** | 文档管理与分析，为 RAG 做准备                 |
| **Chains**  | 构建功能执行链条，串联多个步骤                |
| **Agent**   | 构建能自主决策的智能体                        |

### 3. 安装命令

```bash
pip install langchain langchain-community langchain-ollama dashscope chromadb
```

- **包说明**：
  - `langchain`：核心包
  - `langchain-community`：社区扩展包（支持阿里云千问等第三方模型）
  - `langchain-ollama`：本地 Ollama 模型支持包
  - `dashscope`：阿里云通义千问 Python SDK
  - `chromadb`：轻量向量数据库（后续 RAG 场景使用）

### 4. 核心价值

- 提供了提示词、模型调用、记忆、文档管理、Agent 等全链路 API。
- 让开发者无需关注底层模型差异，专注于业务逻辑实现。
- 口号：`Full LLM power—you only need LangChain`
- LangChain 封装了复杂的 LLM 交互逻辑，包括上下文管理（History）和提示词优化（Prompts），是之前所学上下文对话与提示词工程思想的工程化实现。
