---
title: JavaScript的类型转换机制
date: 2023-03-19 21:18:03
categories: [JavaScript]
tags: [JavaScript]
---

`JavaScript`的类型转换机制主要分为两种：**显式转换**和**隐式转换**。

## 一、显式转换

调用内置方法或函数主动改变数据类型。主要方法有`String()`、`Number()`、`parseInt()`、`parseFloat()`、`Boolean()`。

1. **转为字符串（String()）**

- 基本类型：直接转为对应字符串

```javascript
String(123); // "123"
String(true); // "true"
String(null); // "null"
String(undefined); // "undefined"
```

- 引用类型：默认调用 `toString()` 方法

```javascript
String([1, 2, 3]); // "1,2,3"
String({ a: 1 }); // "[object Object]"
```

2. **转为数字（Number()）**

- 字符串：纯数字字符串转为对应数字，否则为 `NaN`
- 布尔值：`true -> 1`，`false -> 0`
- `null -> 0`，`undefined -> NaN`
- 对象：先调用 `valueOf()`，再尝试转为数字

```javascript
Number("123"); // 123
Number("12a"); // NaN
Number(true); // 1
Number(false); // 0
Number(null); // 0
Number(undefined); // NaN
Number({}); // NaN
Number({ a: 1 }); // NaN
```

3. **转为整数 / 浮点数**

```javascript
parseInt("12.34"); // 12（只保留整数部分）
parseFloat("12.34"); // 12.34
```

4. **转为布尔值（Boolean()）**

- 以下值会转为 `false`：`0`、`""`、`null`、`undefined`、`NaN`
- 其他值转为 `true`，包括空对象`{}`、空数组`[]`

## 二、隐式转换

在运算或比较时，JavaScript 会自动转换类型。

1. **算术运算中的转换**

- `+` 号对**多个值**的转换
  - 若有字符串，另一个值类型会转为字符串拼接
  - 若无字符串，转为数字计算
  - 若有数组，数组会先转成字符串（相当于字符串）
- `+` 号对**单个值**的转换（转数字）

```javascript
// 有字符串
1 + "2"     // "12"
1 + true    // 2
[] + '1'    // "1"
[] + 1      // "1"
[1] + 1     // "11"
[1, 2] + 1  // "1,21"

// 无字符串
1 + true       // 2
null + 1       // 1（null -> 0）
undefined + 1  // NaN（undefined -> NaN）

// 单个值
+ "123"         // 123（字符串 -> 数字）
+ true          // 1（布尔 -> 数字）
+ null          // 0
+ undefined     // NaN
+ [5]           // 5（数组 -> 数字）
```

- 其他运算符（`-`、`*`、`/` 等）会转为数字计算

```javascript
"5" - 3; // 2（字符串转数字）
true * 10; // 10（布尔转数字）
```

2. **比较运算中的转换**

- 宽松相等（`==`）会先转换类型再比较

```javascript
// 数字与字符串比较：字符串转数字
1 == "1"         // true
0 == ""          // true（"" -> 0）

// 布尔值与其他类型：布尔值转数字
true == 1           // true（true -> 1）
false == 0          // true（false -> 0）
false == ""         // true（false -> 0，"" -> 0）

// null 与 undefined 的特殊规则
null == undefined   // true（仅这一对特殊相等）
null == 0           // false（null 不转数字0参与比较）

// 对象与原始值比较：对象转原始值
[1] == "1"              // true（数组[1] -> 字符串"1"）
{} == "[object Object]" // true（对象 -> 字符串）

// NaN比较（NaN与任何其他值（包括另一个NaN）都不相等。）
NaN == NaN              // false
NaN == false            // false
```

- 严格相等（`===`）不转换类型，直接比较（推荐使用）

```javascript
1 === "1";              // false（类型不同）
null === undefined;     // false（类型不同）
```

3. **逻辑运算中的转换**

- `if`、`&&`、`||`、`!`、`!!` 等场景会隐式转为布尔值

```javascript
if ("") { ... }     // false（空字符串 -> false）
if ([0]) { ... }    // true（对象始终为true）

!0                  // true（0 -> false）
!""                 // true（"" -> false）
![]                 // false（数组 -> true）
```

4. **特殊规则**

- `null` 与 `undefined` 不转为数字（`null == undefined` 为 `true`，但 `null === undefined` 为 `false`）
- 对象转换为原始值时，优先调用 `valueOf()`，若结果不是原始值则调用 `toString()`
- `+` 号对数组的特殊处理：`[1] + [2] -> "12"`（数组先转为字符串再拼接）
- `NaN`与任何其他值（包括另一个`NaN`）都不相等。

## 总结

隐式转换的情况比较复杂·，在实际开发中应该尽量使用显式转换和严格相等`===`，以避免意外 bug。
