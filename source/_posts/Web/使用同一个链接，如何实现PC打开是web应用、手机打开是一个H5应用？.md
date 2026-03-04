---
title: 使用同一个链接，如何实现PC打开是web应用、手机打开是一个H5应用？
date: 2023-09-30 09:20:12
categories: [Web]
tags: [Web, 设备判断]
---

核心是根据`**User-Agent**`判断设备类型，然后根据对应的设备值做出处理。

1. 前端可以通过`JavaScript`获取`User-Agent`
   - 普通 Web 页面可以跳转不同的页面
   - React 或 Vue 可以动态加载不同的组件。

```jsx
const userAgent = navigator.userAgent.toLowerCase();
```

2. 后端读取 HTTP 请求头中的 `User-Agent`，判断用户设备，返回不同的页面数据。
3. Nginx 可以通过其内置的`$http_user_agent`变量判断设备类型，并实现页面跳转或代理。
