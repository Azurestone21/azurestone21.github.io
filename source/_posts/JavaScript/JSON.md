---
title: JSON
date: 2023-10-19 21:18:03
categories: [JavaScript]
tags: [JSON]
---

**JSON（JavaScript Object Notation）**指的是 JavaScript 对象标记法

JSON 是一种轻量级的数据交换格式

JSON 具有自我描述性且易于理解

JSON 独立于语言

## 语法

JSON 语法衍生于 JavaScript 对象标记法语法：

- 数据在名称/值对中
- 数据由逗号分隔
- 花括号容纳对象
- 方括号容纳数组
- 字符串值使用双引号

## 数据类型

在 JSON 中，值必须是以下数据类型之一：

- 字符串
- 数字
- 对象（JSON 对象）
- 数组
- 布尔
- Null

JSON 的值**不可以**是以下数据类型之一：

- 函数
- 日期
- undefined

## 序列化与反序列化

- 序列化：将 JS 对象转化为 JSON 字符串，JSON.stringify(obj)、obj.toJSONString()
- 反序列化：将 JSON 字符串转化为 JS 对象，JSON.parse(jsonStr)、jsonStr.parseJSON()
- 使用场景：后端响应参数、页面间传参、浏览器本地存储

### JSON.parse()

解析数据，数据会成为 JavaScript 对象.

```javascript
var obj = JSON.parse('{ "name":"Bill Gates", "age":62, "city":"Seattle"}');
```

### JSON.stringify()

把 JavaScript 对象转换为字符串。

```javascript
var obj = { name: "Bill Gates", age: 62, city: "Seattle" };
var myJSON = JSON.stringify(obj);
```

###

## JSONP

JSONP = JSON with Padding。

JSONP 是一种无需考虑跨域问题即可传送 JSON 数据的方法。

JSONP 不使用 XMLHttpRequest 对象。

JSONP 使用 <script> 标签取而代之。

> 从另一个域请求文件会引起问题，由于跨域政策。
>
> 从另一个域请求外部脚本没有这个问题。

## JSON vs XML

- JSON 和 XML 都是“自描述的”（人类可读的）
- JSON 和 XML 都是分级的（值中有值）
- JSON 和 XML 都能被大量编程语言解析和使用
- JSON 和 XML 都能被 XMLHttpRequest 读取

### 差异

- JSON 不使用标签
- JSON 更短
- JSON 的读写速度更快
- JSON 可使用数组

- 最大的不同在于：XML 必须使用 XML 解析器进行解析。而 JSON 可通过标准的 JavaScript 函数进行解析。

### JSON 比 XML 更好

- XML 比 JSON 更难解析。
- JSON 被解析为可供使用的 JavaScript 对象。

- 对于 AJAX 应用程序，JSON 比 XML 更快更易用：

使用 XML

- 读取 XML 文档
- 使用 XML DOM 遍历文档
- 提取变量中存储的值

使用 JSON

- 读取 JSON 字符串
- JSON.Parse JSON 字符串
