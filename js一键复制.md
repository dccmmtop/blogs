---
tags: [js]
date: 2018-08-20 10:38:16
---

对于非技术人员来说，有个一键复制的按钮，比手动复制要友好。

![](http://ogbkru1bq.bkt.clouddn.com/选区_150.png)

如图，一键复制功能可以把二维码信息复制到剪切板。

### 实现原理

浏览器提供了 copy 命令 ，可以复制选中的内容

`document.execCommand("copy")`

如果是输入框，可以通过 select() 方法，选中输入框的文本，然后调用 copy 命令，将文本复制到剪切板
**但是 `select()` 方法只对 `<input>` 和`<textarea>` 有效**

如果想复制 div 内的文字，可以制作一个隐藏的输入框，把 div 内的文字赋值到 input 的 value 中

**input： 复制出来的内容没有换行**
**textarea: 含有换行**

### 解决方案

隐藏`<input>` 按钮，注意：**不可采用 `hidden` 或者 `visibility:none`的方式隐藏，否则复制不生效**

用如下方式隐藏：

```css
input#link-info {
  opacity: 0;
  position: absolute;
}
```

### 功能实现

```coffee
autoCopyLink = () ->
  $(".copy-link-btn").click ->
    input = $("#link-info")
    input.select()
    document.execCommand("copy")
```
