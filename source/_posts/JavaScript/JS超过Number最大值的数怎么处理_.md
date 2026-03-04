---
title: JS 超过 Number 最大值的数怎么处理
date: 2023-09-29 14:18:08
categories: [JS]
tags: [JS, 最大值]
---

## Number 最大值

`Number`有两种最大值：`Number.MAX_SAFE_INTEGER`和`Number.MAX_VALUE`。

- `Number.MAX_SAFE_INTEGER`
  - **含义**：在 JavaScript 中能**精确表示**的最大**整数。**
  - **值**：介于 -(2<sup>53</sup> – 1) 和 2<sup>53</sup> – 1 之间的整数，最大值是`9007199254740991`。
  - **作用**：判断一个整数是否能被精确处理。
- `Number.MAX_VALUE`
  - **含义**：在 JavaScript 中可表示的最大**数值，超过这个值的数字会被视为 Infinity（无穷大）。**
  - **值**：是 2<sup>1024</sup> - 1，或大约 `1.7976931348623157E+308`。
  - **作用**：判断数值是否溢出到无穷大。

## 处理前端的大数值

1. 使用字符串处理

适用于不需要进行运算的情况，例如 ID。对应的是`Number.MAX_SAFE_INTEGER`。

2. 使用 BigInt 类型

ES2020 引入的 BigInt 类型专门用于表示任意精度的整数，在数字末尾加 n 或使用 BigInt() 构造函数创建。

- `<font style="color:rgb(0, 0, 0);">BigInt</font>`<font style="color:rgb(0, 0, 0);"> 不能与 </font>`<font style="color:rgb(0, 0, 0);">Number</font>`<font style="color:rgb(0, 0, 0);"> 直接运算，需要先转换类型。</font>
- `<font style="color:rgb(0, 0, 0);">JSON</font>`<font style="color:rgb(0, 0, 0);"> 不原生支持 </font>`<font style="color:rgb(0, 0, 0);">BigInt</font>`<font style="color:rgb(0, 0, 0);">，序列化时需转为字符串。</font>
- `<font style="color:rgb(0, 0, 0);">BigInt</font>`<font style="color:rgb(0, 0, 0);"> 不适用于浮点数。</font>

```javascript
// 创建 BigInt
const big1 = 123456789012345678901234567890n;
const big2 = BigInt("123456789012345678901234567890");

// 运算（注意必须与 BigInt 类型一起运算）
const sum = big1 + big2;
console.log(sum.toString()); // "246913578024691357802469135780"

// 与 Number 转换（超出安全范围会丢失精度）
const num = Number(big1); // 可能丢失精度
```

3. <font style="color:rgb(0, 0, 0);">使用第三方库</font>

- **<font style="color:rgb(0, 0, 0) !important;">bignumber.js</font>**<font style="color:rgb(0, 0, 0);">：提供高精度算术运算</font>
- **<font style="color:rgb(0, 0, 0) !important;">decimal.js</font>**<font style="color:rgb(0, 0, 0);">：支持十进制精度控制</font>
- **<font style="color:rgb(0, 0, 0) !important;">big.js</font>**<font style="color:rgb(0, 0, 0);">：轻量级的高精度计算库</font>

## <font style="color:rgb(0, 0, 0);">处理后端的大数值</font>

有时候会从接口获取到大整数的 ID，前端接收时会丢失精度。

1. 后端接口优化
   1. 最好的方法就是后端在返回数据时，将长整型数值转换为字符串格式。
2. 前端处理
   1. 使用 BigInt 处理：在请求拦截器接收`response`时将大于`Number.MAX_SAFE_INTEGER`的数值使用`BigInt`处理。
   2. 使用 JSON 解析器配置：可以使用第三方库`json-bigint`
