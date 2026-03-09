---
title: 【微前端】MicroApp 入门实践
date: 2025-11-10 21:09:00
category: [Web]
tags: [微前端, MicroApp]
---

> `micro-app`是由京东前端团队推出的一款微前端框架，它借鉴了WebComponent的思想，通过`js沙箱`、`样式隔离`、`元素隔离`、`路由隔离`模拟实现了ShadowDom的隔离特性，并结合CustomElement将微前端封装成一个类WebComponent组件，从而实现微前端的组件化渲染，旨在降低上手难度、提升工作效率。
>
> `micro-app`和技术栈无关，也不和业务绑定，可以用于任何前端框架。

文档：[https://micro-zoe.github.io/doc/zh/](https://micro-zoe.github.io/doc/zh/)

下面以 react 主应用和 react+webpack 的 H5 子应用为例。

## 开始

### 安装

```shell
npm i @micro-zoe/micro-app --save
```

### 初始化

```javascript
// index.js / main.js
import microApp from "@micro-zoe/micro-app";

microApp.start();
```

### 主应用加载子应用

```javascript
export function MyPage() {
  return (
    <div>
      <micro-app name="my-app" url="http://localhost:3000/"></micro-app>
    </div>
  );
}
```

- name：必传，应用名称
- url：必传参数，必须指向子应用的index.html，不带页面路由

### 子应用配置跨域

```javascript
devServer: {
  headers: {
    'Access-Control-Allow-Origin': '*'
  }
},
```

## 加载子应用指定页面

子应用默认渲染首页，但可以通过设置`default-page`渲染指定的默认页面。

```javascript
<micro-app name='my-app' url='http://localhost:3000/' default-page='/page1'></micro-app>

// hash路由
<micro-app name='my-app' url='http://localhost:3000/' default-page='#/page1'></micro-app>
```

## 通信

### 主应用

接收子应用数据

```javascript
import microApp from "@micro-zoe/micro-app";

useEffect(() => {
  // 这里的'my-app'是在<micro-app>绑定的name
  const listener = () => {
    console.log("收到子应用数据", data);
  };
  microApp.addDataListener("my-app", listener);
  return () => microApp.removeDataListener("my-app", listener);
}, []);
```

给子应用传数据

```javascript
import microApp from "@micro-zoe/micro-app";

microApp.setData("my-app", data);
```

### 子应用

接收主应用数据

```javascript
useEffect(() => {
  const microApp = (window as any).microApp as MicroApp | undefined;
  if (!microApp) return;

  const listener = (data: Data) => {
    console.log('收到主应用数据', data)
  };
  microApp.addDataListener(listener);

  return () => microApp.removeDataListener(listener);
}, []);
```

给主应用传数据

```javascript
(window as any).microApp as MicroApp)?.dispatch(data)
```

## 遇到的问题

1. 在弹窗中使用`micro-app`，重复打开弹窗，子应用的样式错乱。

- 原因：`micro-app`给子应用元素加了`display: inline`样式。
- 解决办法：在子应用给元素加上`display: block`样式。每次打开弹窗给<micro-app>绑定一个新 key。
