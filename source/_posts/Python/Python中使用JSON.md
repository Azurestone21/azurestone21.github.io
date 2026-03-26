---
title: Python 中使用 JSON
date: 2026-03-26 10:00:00
categories: [Python]
tags: [JSON]
---

## 核心作用

- **序列化**：将 Python 字典/列表 → JSON 字符串，用于数据存储或网络传输。
- **反序列化**：将 JSON 字符串 → Python 字典/列表，方便程序读取处理。
- JSON 是带格式的字符串，便于不同程序间数据交互。

## 数据结构对应关系

| JSON 类型 | Python 类型 | 示例                                   |
| :-------- | :---------- | :------------------------------------- |
| JSON 对象 | 字典 (dict) | `{"name": "周杰伦", "age": 11}`        |
| JSON 数组 | 列表 (list) | `[{"name": "小明"}, {"name": "小红"}]` |

## 核心 API（内置 `json` 库）

```python
import json

# 1. 序列化：Python 数据 → JSON 字符串
data = {"name": "小明", "age": 16, "hobby": ["篮球", "游戏"]}
json_str = json.dumps(data, ensure_ascii=False)  # ensure_ascii=False 保证中文正常显示

# 2. 反序列化：JSON 字符串 → Python 数据
py_data = json.loads(json_str)
print(py_data["name"])  # 输出: 小明
```
