---
tags: [js]
date: 2018-10-08 16:52:00
---

### 阻止事件冒泡

```js
event.stopPropagation();
```

### 阻止事件的默认行为

比如阻止`<a>`链接跳转行为

```js
event.preventDefault();
```

### 阻止事件冒泡以及阻止默认行为

```js
return false;
```
