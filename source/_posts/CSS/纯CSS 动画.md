---
title: 纯CSS 动画
date: 2026-03-02 17:33:00
categories: [CSS]
tags: [CSS, 动画]
---

## 纯色渐变圆环 1

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260305000657568.png)

```javascript
.circular-ring-loading {
  width: 16px;
  height: 16px;
  border-radius: 50%;
  border: 3px solid transparent;
  background: conic-gradient(from 90deg at 50% 50%, rgba(255, 255, 255, 0.01) 0%, #003dd9 100%);
  background-clip: padding-box;
  animation: spin 1s linear infinite;
  &::after {
    content: '';
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    width: 12px;
    height: 12px;
    background: #ffffff;
    border-radius: 50%;
  }
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
```

利用背景锥形渐变属性`conic-gradient`实现渐变，再使用伪元素把中心遮挡，形成圆环。

注意：中心的颜色需要和外部的背景颜色一致，适合纯色背景的情况。

## 纯色渐变圆环 2

![](https://azurestone21.oss-cn-guangzhou.aliyuncs.com/blogs/20260305000657569.png)

```javascript
.circular-ring-loading {
  width: 16px;
  height: 16px;
  background: conic-gradient(from 90deg at 50% 50%, rgba(255, 255, 255, 0.01) 0%, #ffffff 100%);
  -webkit-mask: radial-gradient(closest-side circle, transparent 60%, #000000 61% 99%, transparent 100%);
  mask: radial-gradient(closest-side circle, transparent 60%, #000000 61% 99%, transparent 100%);
  animation: spin 1s linear infinite;
  backface-visibility: hidden;
}

@keyframes spin {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}
```

利用背景锥形渐变属性`conic-gradient`实现渐变，再使用遮罩`mask`把中心隐藏起来，形成圆环。

这种方法生成的圆环中间是透明的，适合任何背景。
