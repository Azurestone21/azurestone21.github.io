---
title: 【坑】微信小程序canvas使用filter和globalAlpha属性，真机里不生效
date: 2026-01-30 16:40:00
categories: [小程序]
tags: [微信小程序, canvas]
---

## 背景

canvas 绘制毛玻璃效果

## 问题

在开发工具里面可以显示毛玻璃效果，但是真机中不生效

## 代码

```javascript
function drawGlass(canvas: OffscreenCanvas, ctx: CanvasRenderingContext2D, x, y, w, h) {
  // 步骤1：保存当前画布状态（避免filter污染后续绘制）
  ctx.save()

  // 步骤2：截取目标区域的画布内容 → 绘制到临时位置（0,0）
  // getImageData：获取指定区域的像素数据（x,y,宽,高）
  const imageData = ctx.getImageData(x, y, w, h)
  // 先清空临时绘制区域（避免残留），再绘制截取的内容
  ctx.clearRect(x, y, w, h)
  ctx.putImageData(imageData, x, y)

  // 步骤3：设置高斯模糊滤镜
  ctx.filter = 'blur(8px)' // blur(像素值)，值越大模糊越强

  // 步骤4：将模糊后的内容绘制回原毛玻璃区域，添加半透明遮罩
  ctx.globalAlpha = 1 // 透明度
  ctx.drawImage(canvas, x, y, w, h, x, y, w, h)

  // 步骤5：恢复画布原始状态（清除filter/globalAlpha等全局属性）
  ctx.restore()
}
```
