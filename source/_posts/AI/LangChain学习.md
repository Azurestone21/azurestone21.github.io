---
title: LangChain 学习
date: 2026-03-26 10:00:00
categories: [AI]
tags: [LangChain]
---

> 前置知识：Python 基础、大语言模型（LLM）、提示词、RAG 框架基础

## LangChain 框架基础

### 框架简介

- **创建**：由 Harrison Chase 于 2022 年 10 月创建。
- **定位**：围绕大语言模型（LLMs）的 Python 第三方开发框架，**自身不开发模型**。
- **核心理念**：为各类 LLM 提供通用接口，将相关组件“链接”在一起，简化复杂 LLM 应用开发。
- **地位**：后续学习 RAG 开发的主力框架。

### 核心功能模块

| 模块        | 功能描述                                      |
| :---------- | :-------------------------------------------- |
| **Prompts** | 提示词工程，优化用户指令                      |
| **Models**  | 统一接口调用各类大模型（通义千问、OpenAI 等） |
| **History** | 管理会话历史，实现对话记忆                    |
| **Indexes** | 文档管理与分析，为 RAG 做准备                 |
| **Chains**  | 构建功能执行链条，串联多个步骤                |
| **Agent**   | 构建能自主决策的智能体                        |

### 安装命令

```bash
pip install langchain langchain-community langchain-ollama dashscope chromadb
```

- **包说明**：
  - `langchain`：核心包
  - `langchain-community`：社区扩展包（支持阿里云千问等第三方模型）
  - `langchain-ollama`：本地 Ollama 模型支持包
  - `dashscope`：阿里云通义千问 Python SDK
  - `chromadb`：轻量向量数据库（后续 RAG 场景使用）

### 核心价值

- 提供了提示词、模型调用、记忆、文档管理、Agent 等全链路 API。
- 让开发者无需关注底层模型差异，专注于业务逻辑实现。
- 口号：`Full LLM power—you only need LangChain`
- LangChain 封装了复杂的 LLM 交互逻辑，包括上下文管理（History）和提示词优化（Prompts），是之前所学上下文对话与提示词工程思想的工程化实现。

## 向量（Vector）

向量是文本的**数学身份证**，它将一段文字的**语义信息**转换为固定长度的**数字列表**，让计算机能“看懂”文字含义并计算相似度。

### 核心作用

- 让计算机判断两段文本是否表述相似含义
- 为语义检索、RAG等应用提供数学基础

### 向量生成（文本嵌入）

- 借助**文本嵌入模型**（如 `text-embedding-v1`）完成
- 模型通过深度学习提取文本语义特征，映射为数字序列

### 向量匹配（相似度计算）

- 核心算法：**余弦相似度**
- 原理：衡量两个向量在高维空间中的方向夹角，夹角越小越相似
- 公式：
  $$
  \text{cosine similarity}(\vec{A}, \vec{B}) = \frac{\vec{A} \cdot \vec{B}}{\|\vec{A}\| \times \|\vec{B}\|}
  $$
- 计算步骤：
  1. **点积**：两个向量同维度乘积之和
  2. **模长**：单个向量各维度平方和开根号
  3. **相似度**：点积 ÷ 模长乘积

### 向量维度

- 维度数：模型用多少个抽象语义特征描述文本
- 特点：维度越高，语义匹配越精准，但计算性能压力越大

---

### 余弦相似度（扩展）

- 向量的数字序列决定了其在高维空间的**方向**和**长度**
- 余弦相似度**只关注方向**，剔除长度影响：
  - 同向向量（如 `[0.5, 0.5]` 和 `[0.7, 0.7]`）相似度为 1
  - 反向向量（如 `[0.5, 0.5]` 和 `[-0.6, -0.5]`）相似度接近 -1
  - 夹角接近垂直的向量相似度接近 0

---

## LangChain 模型

LangChain模型组件提供了与各种模型的集成，并为所有模型提供一个精简的统一接口。

LangChain目前支持三种类型的模型：LLMs（大语言模型）、Chat Models（聊天模型）、Embeddings Models（嵌入模型）。

- LLMs：是技术范畴的统称，指基于大参数量、海量文本训练的 Transformer 架构模型，核心能力是理解和生成自然语言，主要服务于文本生成场景。
- Chat Models：是应用范畴的细分，是专为对话场景优化的 LLMs，核心能力是模拟人类对话的轮次交互，主要服务于聊天场景。
- Embeddings Models：文本嵌入模型接收文本作为输入，得到文本的向量。

---

## 模型调用实践

### 1. 调用大语言模型（LLMs）

LLMs使用场景最多，常用大模型的下载库：

- https://huggingface.co/models
- https://modelscope.cn/models

1. 使用 云模型（如阿里云通义千问）

```python
from langchain_community.llms.tongyi import Tongyi

# 初始化模型（qwen-max 是大语言模型，qwen3-max 是聊天模型）
model = Tongyi(model="qwen-max")

# 批量调用（一次性返回结果）
res = model.invoke(input="你是谁呀能做什么？")
print(res)

# 流式调用（逐段输出）
res = model.stream(input="你是谁呀能做什么？")
for chunk in res:
    print(chunk, end="", flush=True)
```

2. 使用 本地 Ollama 模型

```python
from langchain_ollama import OllamaLLM
# 实例化本地模型（需提前启动Ollama并下载模型）
model = OllamaLLM(model="qwen3:4b")
# 调用模型
res = model.invoke(input="你是谁呀能做什么？")
print(res)
```

- invoke方法：一次型返回完整结果
- stream方法：逐段返回结果，流式输出

---

### 2. Chat Models（聊天模型）

#### 消息类型

| 消息类型        | 对应角色  | 作用                                             |
| --------------- | --------- | ------------------------------------------------ |
| `SystemMessage` | system    | 设定模型角色、背景或输出格式（如“你是代码专家”） |
| `HumanMessage`  | user      | 用户输入的问题或指令                             |
| `AIMessage`     | assistant | 模型历史回复，用于多轮对话上下文                 |

#### 基础调用示例

```python
from langchain_community.chat_models.tongyi import ChatTongyi
from langchain_core.messages import HumanMessage, SystemMessage, AIMessage

# 初始化聊天模型
chat = ChatTongyi(model="qwen3-max")

# 构造消息列表
messages = [
    SystemMessage(content="你是一名来自边塞的诗人"),
    HumanMessage(content="给我写一首唐诗"),
    AIMessage(content="锄禾日当午，汗滴禾下土..."),  # 历史回复
    HumanMessage(content="给予你上一首的格式，再来一首")
]

# 流式输出
for chunk in chat.stream(input=messages):
    print(chunk.content, end="", flush=True)
```

#### 消息简写形式

- **类对象形式**：静态，直接得到 Message 对象
  ```python
  messages = [
      SystemMessage(content="内容..."),
      HumanMessage(content="内容..."),
      AIMessage(content="内容...")
  ]
  ```
- **简写元组形式**：动态，运行时由 LangChain 转换，支持变量占位
  ```python
  messages = [
      ("system", "今天的天气是{weather}"),
      ("human", "我的名字是: {name}"),
      ("ai", "欢迎{lastname}先生")
  ]
  ```
  > 优势：写法更简洁，支持 `{变量}` 占位，可在运行时填充具体值（提示词模板场景常用）

---

### 3. Embeddings Models（文本嵌入模型）

#### 核心作用

将文本字符串转换为**固定长度的浮点数向量**，用于语义相似度计算、检索等场景。

#### 阿里云千问嵌入模型

```python
from langchain_community.embeddings import DashScopeEmbeddings

# 初始化（默认模型：text-embedding-v1）
embed = DashScopeEmbeddings()

# 单次文本向量化
vec = embed.embed_query("我喜欢你")
print(vec)

# 批量文本向量化
vecs = embed.embed_documents(["我喜欢你", "我稀饭你", "晚上吃啥"])
print(vecs)
```

#### Ollama 本地嵌入模型

```python
from langchain_ollama import OllamaEmbeddings

embed = OllamaEmbeddings(model="qwen3:4b")
vec = embed.embed_query("我喜欢你")
print(vec)
```

---

#### 调用方式对比

| 模型类型           | 调用方法          | 输出特点                   |
| ------------------ | ----------------- | -------------------------- |
| LLMs / Chat Models | `invoke`          | 一次性返回完整结果         |
| LLMs / Chat Models | `stream`          | 逐段流式输出，适合实时交互 |
| Embeddings Models  | `embed_query`     | 单条文本转向量             |
| Embeddings Models  | `embed_documents` | 多条文本批量转向量         |

---

#### 模型使用总结表

| 方式            | LLMs 大语言模型                                      | 聊天模型                                                        | 文本嵌入模型                                                     |
| --------------- | ---------------------------------------------------- | --------------------------------------------------------------- | ---------------------------------------------------------------- |
| 阿里云千问      | `from langchain_community.llms.tongyi import Tongyi` | `from langchain_community.chat_models.tongyi import ChatTongyi` | `from langchain_community.embeddings import DashScopeEmbeddings` |
| Ollama 本地模型 | `from langchain_ollama import OllamaLLM`             | `from langchain_ollama import ChatOllama`                       | `from langchain_ollama import OllamaEmbeddings`                  |
| 核心方法        | `invoke` / `stream`                                  | `invoke` / `stream`                                             | `embed_query` / `embed_documents`                                |

---

### 通用Prompt

#### PromptTemplate（zero-shot）

LangChain提供了PromptTemplate类，用来协助优化提示词。
PromptTemplate表示提示词模板，可以构建一个自定义的基础提示词模板，支持变量的注入，最终生成所需的提示词。

1. 标准写法（format 方式）

```python
from langchain_core.prompts import PromptTemplate
from langchain_community.llms.tongyi import Tongyi

# 定义模板
prompt_template = PromptTemplate.from_template(
    "我的邻居姓{lastname}，刚生了{gender}，帮忙起名字，请简略回答。"
)

# 变量注入，生成提示词文本
prompt_text = prompt_template.format(lastname="张", gender="女儿")

# 调用模型
model = Tongyi(model="qwen-max")
res = model.invoke(input=prompt_text)
print(res)
```

2. 链式写法（`|` 管道符）

```python
from langchain_core.prompts import PromptTemplate
from langchain_community.llms.tongyi import Tongyi

prompt_template = PromptTemplate.from_template(
    "我的邻居姓{lastname}，刚生了{gender}，帮忙起名字，请简略回答。"
)
model = Tongyi(model="qwen-max")

# 构建链：模板 → 模型
chain = prompt_template | model

# 调用链，传入变量
res = chain.invoke(input={"lastname": "曹", "gender": "女儿"})
print(res)
```

---

#### FewShotPromptTemplate（少样本提示词模板）

在提示词中注入**示例数据**，让模型学习特定任务的输出格式（few-shot 思想）。

#### 核心参数

| 参数              | 含义                                     |
| ----------------- | ---------------------------------------- |
| `examples`        | 示例数据，`list` 类型，内部嵌套字典      |
| `example_prompt`  | 示例数据的提示词模板（`PromptTemplate`） |
| `prefix`          | 示例数据前的引导语                       |
| `suffix`          | 示例数据后的提问语，包含变量占位         |
| `input_variables` | 声明需要注入的变量列表                   |

#### 代码示例

```python
from langchain_core.prompts import FewShotPromptTemplate, PromptTemplate

# 1. 定义示例数据
examples_data = [
    {"input": "高兴", "output": "难过"},
    {"input": "快", "output": "慢"}
]

# 2. 定义示例模板
example_template = PromptTemplate.from_template(
    "输入：{input}，反义词：{output}"
)

# 3. 构建 FewShot 模板
few_shot_template = FewShotPromptTemplate(
    example_prompt=example_template,
    examples=examples_data,
    prefix="告知我单词的反义词，我提供如下的示例：",
    suffix="基于前面的示例告知我，{input_word}的反义词是？",
    input_variables=["input_word"]
)

# 4. 生成提示词
prompt_text = few_shot_template.invoke(input={"input_word": "左"}).to_string()
print(prompt_text)
```

---

#### format vs invoke 方法对比

所有模板类（`PromptTemplate`/`FewShotPromptTemplate`/`ChatPromptTemplate`）都支持这两个方法：

| 维度     | `format`                           | `invoke`                                            |
| -------- | ---------------------------------- | --------------------------------------------------- |
| 功能     | 纯字符串替换，解析占位符生成提示词 | Runnable 接口标准方法，解析占位符生成提示词         |
| 返回值   | 字符串                             | `PromptValue` 类对象（需 `.to_string()` 转文本）    |
| 传参方式 | `.format(k=v, ...)`                | `.invoke({"k": v, ...})`                            |
| 解析能力 | 仅支持 `{}` 占位符                 | 支持 `{}` 占位符 + `MessagesPlaceholder` 结构化占位 |

---

#### ChatPromptTemplate（对话提示词模板）

支持注入**多轮历史会话信息**，专门用于对话场景。

- 通过 `from_messages` 方法接收**消息列表**（区别于 `from_template` 仅接收单条消息）
- 支持 `("system"/"human"/"ai", "内容")` 简写格式
- 可注入历史对话，实现上下文感知

#### 代码示例

```python
from langchain_core.prompts import ChatPromptTemplate

chat_prompt_template = ChatPromptTemplate.from_messages([
    ("system", "你是一个边塞诗人，风格苍凉悲壮"),
    ("human", "给我写一首关于战争的唐诗"),
    ("ai", "黄沙百战穿金甲，不破楼兰终不还"),
    ("human", "再来一首，风格保持一致")
])
```

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260328002755597.png)

---

### Chain 链（管道调用）

将多个组件**串联**，上一个组件的输出自动作为下一个组件的输入，实现数据自动化流转。

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260328002755598.png)

#### 核心语法

```python
chain = 组件1 | 组件2 | 组件3
```

- 示例：`chain = prompt_template | model`
- 执行：`chain.invoke(input={"变量": "值"})` 或 `chain.stream(...)`

#### 关键前提

- 只有 **Runnable 接口子类** 才能入链
- 我们学过的模板类（`PromptTemplate` 等）和模型类（`Tongyi`/`ChatTongyi` 等）都实现了 `Runnable` 接口
- 链本身也是 `RunnableSerializable` 对象，可继续入链

#### 执行流程

`invoke/stream` 触发 → 传入变量字典 → 模板生成 `PromptValue` → 模型接收并生成回复 → 返回结果

---

#### 模板类总结

| 模板类                  | 适用场景     | 核心特点                         |
| ----------------------- | ------------ | -------------------------------- |
| `PromptTemplate`        | 通用单轮提示 | 支持变量注入，zero-shot 场景     |
| `FewShotPromptTemplate` | 少样本学习   | 可注入示例数据，引导模型输出格式 |
| `ChatPromptTemplate`    | 多轮对话     | 支持历史会话列表，上下文感知     |

---

#### Chain 链总结

- **串联思想**：`组件1 | 组件2`，自动传递数据
- **触发方式**：`invoke`（批量）/ `stream`（流式）
- **本质**：将复杂流程拆分为可复用的组件，通过链实现协同工作
