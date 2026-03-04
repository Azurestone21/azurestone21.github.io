---
title: 【TinyMCE】解决TinyMCE不显示HTTP图片问题
date: 2025-09-03 14:18:23
categories: [Web]
tags: [TinyMCE]
---

## 问题描述

从第三方网页复制网络HTTP图片到富文本编辑器时，图片不显示。

## 原因

页面是 https 协议，浏览器默认会屏蔽不安全内容，不允许安全的 https 页面混合请求不安全的 http 请求。

> 控制台会显示警告 “Mixed Content”。

## 解决办法

### 方法一：修改浏览器配置

Edge浏览器可以在**隐私、搜索和服务/站点权限/所有权限**对**不安全的内容**进行设置。

Google浏览器可以在**隐私与安全/网站设置/更多内容设置**对**不安全内容**进行设置。

<!-- 这是一张图片，ocr 内容为： -->

![浏览器不安全内容设置](/images/tinymce-http-img1.png)

<!-- 这是一张图片，ocr 内容为： -->

![浏览器不安全内容设置](/images/tinymce-http-img2.png)

### 方法二：设置CSP

将自动将 http 的不安全请求升级为 https，但是如果服务器不支持 https，链接将访问不了。

```html
<meta
  http-equiv="Content-Security-Policy"
  content="upgrade-insecure-requests"
/>
```

### 方法二：将图片上传到自己的服务器，使用自己服务器的链接

- paste_data_images：允许粘贴图片数据
- paste_postprocess：在将粘贴的内容**解析为 DOM 结构之后，插入编辑器之前**修改粘贴的内容

```html
<Editor
  ...
  init={{
    paste_data_images: true,
    paste_postprocess: handlePastedContent,
    setup: (editor: any) => {
      editor.on('change', () => {
        const currentContent = editor.getContent()
        props.onChange?.(currentContent)
      })
    }
  }}
/>
```

```javascript
// 处理粘贴的图片，上传服务器并替换url
const handlePastedContent = async (editor: any, args: { node: any }) => {
  if (!editor) return

  // 1. 创建临时容器处理内容
  const tempDiv = editor.dom.create('div')
  tempDiv.innerHTML = args.node.innerHTML

  // 2. 处理所有图片
  const images = tempDiv.querySelectorAll('img[src^="http"]')
  const uploadPromises = Array.from(images).map(async (img: any) => {
    // 获取图片链接
    const originalSrc = img.getAttribute('src')
    if (!originalSrc || ['过滤自己服务器url域名'].includes(new URL(originalSrc)?.hostname))
      return

    try {
      // 调用接口换取图片url
      const { data: newUrl } = await copyImageFromUrl(originalSrc)

      if (code !== 200) {
        console.error('图片上传失败:', msg)
        img.parentNode?.removeChild(img)
        return
      }

      // 3. 在临时DOM中更新图片
      img.setAttribute('src', newUrl)
      img.setAttribute('data-mce-src', newUrl)
      img.removeAttribute('data-mce-src')
    } catch (error) {
      console.error('图片上传失败:', error)
      img.parentNode?.removeChild(img)
    }
  })

  // 4. 等待所有上传完成
  await Promise.all(uploadPromises)

  // 5. 完全替换原始节点内容
  // args.node.innerHTML = tempDiv.innerHTML

  // 6. 更新内容
  props.onChange?.(tempDiv.innerHTML)
}
```
