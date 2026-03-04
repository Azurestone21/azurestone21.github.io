---
title: css实现毛玻璃效果
date: 2025-08-26 16:02:42
categories: [css]
tags: [css]
---

在很多网页上可以看到页面的导航栏或卡片，元素本身是半透明，但是背后的内容是模糊的，类似磨砂玻璃的质感，这就是毛玻璃效果（Frosted Glass）。

## 方法一：backdrop-filter

- Chrome、Firefox、Safari、Edge 等现代浏览器均支持
- 对于较旧的浏览器（如 IE）不支持此属性

```css
.frosted-glass {
  background-color: rgba(255, 255, 255, 0.15); /* 半透明背景 */
  backdrop-filter: blur(10px); /* 核心：背景模糊效果 */
  -webkit-backdrop-filter: blur(10px); /* 浏览器兼容性前缀 */
  border: 1px solid rgba(255, 255, 255, 0.2);
  box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.15);
}
```

## 方法二：filter: blur() + background-attachment: fixed

```css
.content {
  background-image: url(imgurl);
}

/* 通用毛玻璃容器样式 */
.glass {
  position: relative;
  /* 确保内容在模糊层上方 */
  z-index: 2;
}

/* 毛玻璃效果核心：伪元素 + 模糊 */
.glass::before {
  content: "";
  position: absolute;
  /* 定位覆盖整个容器 */
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  /* 复制背景图 */
  background-image: url(imgurl);
  background-size: cover;
  background-position: center;
  background-attachment: fixed;
  /* 关键：应用模糊效果 */
  filter: blur(8px);
  -webkit-filter: blur(8px);
  /* 扩展模糊区域，避免边缘生硬 */
  transform: scale(1.1);
  /* 降低透明度，增强玻璃感 */
  opacity: 0.3;
  /* 放置在内容下方 */
  z-index: -1;
}
```

深色毛玻璃

```css
.glass-dark::before {
  background-color: #000;
  background-blend-mode: overlay;
  opacity: 0.5;
  filter: blur(10px);
}
```

如果背景不是图片，而是背景颜色，这种方法实现不了毛玻璃效果。
