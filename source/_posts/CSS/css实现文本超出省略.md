---
title: css实现文本超出省略
date: 2023-03-04 10:46:56
categories: [css]
tags: [css]
---

## css

```css
// 超出一行
.one-row-ellipsis {
  overflow: hidden;
  white-space: nowrap;
  text-overflow: ellipsis;
}

// 超出两行
.two-row-ellipsis {
  display: -webkit-box;
  -webkit-box-orient: vertical;
  -webkit-line-clamp: 2;
  line-clamp: 2;
  overflow: hidden;
  word-break: break-all;
}
```

## Sass

使用 Sass 可以动态设置超出几行省略

```css
@mixin text-overflow($row: 1) {
  @if $row == 1 {
    white-space: nowrap;
    text-overflow: ellipsis;
    overflow: hidden;
  } @else  {
    display: -webkit-box;
    -webkit-box-orient: vertical;
    -webkit-line-clamp: $row;
    overflow: hidden;
    word-break: break-all;
    white-space: normal;
  }
}
```

使用

```css
.text {
  @include text-overflow(3);
}
```

## 补充

如果元素设置了弹性布局，超出省略会不生效。
