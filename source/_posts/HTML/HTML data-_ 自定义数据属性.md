---
title: "HTML data-_: 自定义数据属性"
date: 2024-08-24 21:12:32
category: [HTML]
tags: [HTML]
---

## 一、是什么

在 HTML 中，以`data-`为前缀的属性被称为自定义数据属性（Custom Data Attributes）。这些属性允许开发者在 HTML 元素上存储额外的自定义数据，以便在 JavaScript 中访问和使用，但又不影响元素的表现。

## 二、命名规则

1. 必须以`data-`开头
2. 后续部分可以包含字母、数字、连字符（-）、下划线（\_）
3. 建议使用小写字母，多个单词用连字符分隔（如 data-user-id）

## 三、访问方式

### JavaScript

1. **通过**`**element.dataset**`**对象访问**
   1. 如果`data-`绑定的字段是全小写并且用连接符`-`连接，例如`data-user-id`，`dataset`访问时的字段写成驼峰格式：`userId`。
   2. 如果`data-`绑定的字段是驼峰形式，例如`data-userName`，`dataset`访问时的字段写成小写格式：`username`。
2. **通过**`**getAttribute()**`**、**`**setAttribute()**`**访问和设置自定义属性的值**
   1. 访问时原样写入参数中，例如`data-user-id`、`data-userName`，访问时：`getAttribute("data-user-id")`、`getAttribute("data-userName")`

#### 普通 html

```html
<div id="user" data-user-id="123" data-userName="小明" data-is-active="true">
  王小明
</div>

<script>
  const userElement = document.getElementById("user");

  // 通过dataset访问
  console.log(userElement.dataset.userId); // 输出 "123"
  console.log(userElement.dataset.userid); // 输出 undefined
  console.log(userElement.dataset.userName); // 输出 "小明"
  console.log(userElement.dataset.username); // 输出 undefined
  console.log(userElement.dataset.isActive); // 输出 "true"
  // 通过getAttribute访问
  console.log(userElement.getAttribute("data-user-id")); // 输出 "123"

  // 通过dataset修改
  userElement.dataset.isActive = "false";
  // 通过setAttribute修改
  userElement.setAttribute("data-userName", "小赵");

  // 添加新的自定义数据属性
  userElement.dataset.userAge = "30";

  // 删除属性
  delete userElement.dataset.userAge;
</script>
```

#### React 和 Vue

使用`e.currentTarget`访问`dataset`和`getAttribute()`。

传递非字符串类型（如布尔值、数字）时，React 会自动转为字符串，但读取时需手动转回原类型。

下面用 React 举例：

```jsx
const handleClick = (e) => {
  console.log(e.currentTarget?.dataset?.userid); // 输出 undefined
  console.log(e.currentTarget?.dataset?.userId); // 输出 "123"
  console.log(e.currentTarget?.dataset?.username); // 输出 undefined
  console.log(e.currentTarget?.dataset?.userName); // 输出 "小明"
  console.log(e.currentTarget?.dataset?.isActive); // 输出 "true" (字符串)
  console.log(e.currentTarget.getAttribute("data-user-id")); // 输出 "123"
  console.log(e.currentTarget.getAttribute("data-userName")); // 输出 "小明"
}; // "小明"

return (
  <div
    data-user-id="123"
    data-userName="小明"
    data-is-active={true}
    onClick={handleClick}
  >
    王小明
  </div>
);
```

### CSS

CSS 可以通过属性选择器获取并使用 HTML 中的 data-\* 自定义属性。

1. **根据属性是否存在设置样式**

匹配包含指定的 `data-*` 属性的元素

```css
/* 所有包含 data-status 属性的元素 */
[data-status] {
  padding: 8px;
  border: 1px solid #ccc;
}
```

2. **根据属性的具体值设置样式**

精确匹配 `data-*` 属性的值

```css
/* data-status 的值为 "active" 的元素 */
[data-status="active"] {
  ...
}
```

3. **使用部分匹配（模糊匹配）**

可以通过 `^=`（开头匹配）、`$=`（结尾匹配）、`*=`（包含匹配）等进行模糊匹配

```css
/* data-type 的值以 "user-" 开头的元素 */
[data-type^="user-"] {
  ...
}

/* data-tags 包含 "feat" 的元素 */
[data-tags*="feat"] {
  ...
}
```

4. **在伪元素中使用 **`**attr()**`** 函数获取属性值**

可以通过 `attr()` 函数将 `data-*` 属性的值作为内容显示（例如在 `::before` 或 `::after` 中）

```css
/* 将 data-label 的值显示为伪元素内容 */
[data-label]::before {
  content: attr(data-label);
}
```
