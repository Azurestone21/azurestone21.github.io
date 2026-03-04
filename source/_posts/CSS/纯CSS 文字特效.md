---
title: 纯CSS 文字特效
date: 2026-03-02 17:33:00
categories: [CSS]
tags: [CSS, 特效]
---

## 渐变颜色文字

实现图片中的文字颜色渐变效果

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260305000657571.png)

```css
.gradient-color-text {
  /* 定义线性渐变背景 */
  background: linear-gradient(90deg, #2f74ed 0%, #782fed 100%);
  /* webkit内核浏览器兼容：背景裁剪为文字区域 */
  -webkit-background-clip: text;
  /* 标准属性：背景裁剪为文字区域（核心） */
  background-clip: text;
  /* webkit内核浏览器兼容：文字填充色设为透明 */
  -webkit-text-fill-color: transparent;
  /* 标准属性：文字填充色设为透明 */
  color: transparent;
}
```
