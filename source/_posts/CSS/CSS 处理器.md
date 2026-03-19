---
title: CSS 处理器
date: 2024-08-12 16:12:32
category: [CSS]
tags: [CSS]
---

CSS 处理器主要分为三大类：**预处理器（Preprocessor）**、**后处理器（Postprocessor）** 和 **CSS-in-JS 工具**。

## 一、预处理器（Preprocessor）

CSS 预处理器（Preprocessor）是一种**扩展 CSS 功能**的工具，引入了编程语言的特性（如变量、函数、嵌套等），让 CSS 编写更高效、可维护性更强。预处理后的代码**最终会被编译为普通 CSS** 供浏览器使用。

**工作阶段**：在代码编写阶段，将扩展语法编译为普通 CSS。

**核心功能**：扩展 CSS 语法，引入编程语言特性（变量、函数、嵌套等）。

**解决问题**：原生 CSS 缺乏模块化、复用性和逻辑处理能力的问题。

**主流工具**：

- **Sass/SCSS**：最流行，支持两种语法（缩进式 .sass 和类 CSS 式 .scss），提供变量、混入（mixin）、继承、函数等功能。
  - **SCSS 语法（.scss）**
    - 类 CSS 风格，保留了 CSS 的大括号 {} 和分号 ;
  - **SASS 语法（.sass）**
    - 缩进式语法，不使用大括号和分号
    - 依靠缩进（空格或制表符）来表示层级关系
    - 换行代替分号，缩进代替大括号
- **Less**：语法接近原生 CSS，学习成本低，支持变量、嵌套、混入，可在浏览器中直接编译。
- **Stylus**：语法极简，可选分号和括号，支持灵活的缩进式写法。

## 二、后处理器（Postprocessor）

CSS 后处理器（Postprocessor）是对已经编写完成的普通 CSS 代码进行**优化、转换和增强**的工具。它工作在 CSS 生成之后，主要解决兼容性、性能和规范问题，让 CSS 更健壮、更易维护。通常与构建工具（Webpack、Vite、Gulp 等）结合使用。

**工作阶段**：在普通 CSS 生成后，对其进行优化和转换，输出优化后的 CSS。

**核心功能**：优化 CSS 代码、处理兼容性、规范化语法，输入输出均为标准 CSS。

1. **兼容性处理**
   - 自动添加浏览器前缀（如 `-webkit-`、`-moz-`）
   - 将现代 CSS 特性（如 CSS Grid、Flexbox）转换为旧浏览器可识别的语法
   - 处理不同浏览器的私有属性差异
2. **代码优化**
   - 压缩 CSS 代码（移除空格、合并重复规则）
   - 合并相同样式，减少代码体积
   - 优化选择器，提高渲染性能
3. **规范与校验**
   - 检查 CSS 语法错误
   - 强制遵循团队 CSS 规范（如命名约定、属性顺序）
   - 检测兼容性问题并给出提示

**解决问题**：CSS 兼容性、性能优化和代码规范问题。

**主流工具**：

- **PostCSS**：最常用，通过插件系统实现功能（如自动前缀、压缩、语法转换）。
  - 核心插件：`Autoprefixer`（自动加浏览器前缀）、`cssnano`（压缩 CSS）、`postcss-preset-env`（转换现代 CSS 语法）、`stylelint`（代码检查）。
- **Stylelint**：专注于 CSS 代码检查，确保代码符合规范。

**示例**（PostCSS 处理前后对比）：

```css
/* 输入（原生 CSS）*/
.container {
  display: flex;
  gap: 20px;
}

/* 输出（自动添加前缀后） */
.container {
  display: -webkit-box;
  display: -ms-flexbox;
  display: flex;
  gap: 20px;
  -webkit-column-gap: 20px;
  -moz-column-gap: 20px;
  column-gap: 20px;
}
```

## 三、CSS-in-JS 工具

**工作阶段**：在 JavaScript 中编写 CSS，运行时或构建时转换为普通 CSS。

**核心功能**：将 CSS 与组件逻辑结合，实现 CSS 的模块化和动态生成。

**解决问题**：CSS 全局作用域冲突、组件样式隔离、动态样式生成困难等问题。

**代表工具**：

- **Styled Components**：允许在 JS 中用模板字符串编写组件样式，自动生成唯一类名，避免样式冲突。
- **Emotion**：轻量高效，支持类似 Styled Components 的语法，也可用于内联样式。
- **JSS**：通过 JavaScript 对象定义样式，更贴近 JS 语法。

**示例**（Styled Components）：

```jsx
import styled from "styled-components";

// 在 JS 中定义样式组件
const Button = styled.button`
  background: #4285f4;
  color: white;
  &:hover {
    background: #3367d6;
  }
`;

// 在组件中使用
function App() {
  return <Button>Click me</Button>;
}
```

虽然 CSS-in-JS 违背了“关注点分离”的原则，但是有利于组件的隔离。每个组件包含了所有需要用到的代码，不依赖外部，组件之间没有耦合，方便复用。

## 总结

| 类型           | 工作阶段               | 核心特点                    | 典型工具                   |
| -------------- | ---------------------- | --------------------------- | -------------------------- |
| 预处理器       | 编写阶段 → 编译为 CSS  | 扩展 CSS 语法，增强开发效率 | Sass/SCSS、Less、Stylus    |
| 后处理器       | 普通 CSS → 优化 CSS    | 处理兼容性，优化最终代码    | PostCSS、Stylelint         |
| CSS-in-JS 工具 | JS 中编写 → 转换为 CSS | 组件化样式，避免全局冲突    | Styled Components、Emotion |
