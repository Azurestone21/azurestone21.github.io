---
title: react+antd 实现 textarea 获取光标位置并在光标处插入文字
date: 2025-09-05 16:26:16
categories: [React]
tags: [React]
---

> react：18.3.1
>
> antd：5.23.0

1. 使用 ref 保存 TextArea 组件

```javascript
const textareaRef = useRef<any>(null)
const [contentVal, setContentVal] = useState('')

<TextArea
  ref={textareaRef}
  placeholder="请输入"
  showCount
  maxLength={500}
  onInput={(e: any) => {
    setContentVal(e.target?.value)
  }}
  onSelect={getCursorPosition}
  rows={3}
/>
```

2. 获取光标位置

```javascript
const [cursorPosition, setCursorPosition] = useState<number>(0)

const getCursorPosition = ( => {
  if (!textareaRef.current) return
  const textarea = textareaRef.current.resizableTextArea.textArea
  const start = textarea.selectionStart // 光标开始位置
  // const end = textarea.selectionEnd  // 光标结束位置
  setCursorPosition(start)
}
```

3. 插入变量值

```javascript
const setFormContent = (content: string) => {
  setContentVal(content)
  formRef.current?.setFieldValue('content', content)
}

// 插入变量值
const addTemplateVariate = (variate: any) => {
  const content = contentVal.slice(0, cursorPosition) + variate + contentVal.slice(cursorPosition)
  // 设置content的值
  setFormContent(content)

  // 计算新的光标位置
  const newPosition = cursorPosition + variate.length

  // 设置光标位置
  setTimeout(() => {
    if (textareaRef.current) {
      textareaRef.current.focus()
      const textarea = textareaRef.current.resizableTextArea.textArea
      textarea.setSelectionRange(newPosition, newPosition)
      setCursorPosition(newPosition)
    }
  }, 0)
}
```
