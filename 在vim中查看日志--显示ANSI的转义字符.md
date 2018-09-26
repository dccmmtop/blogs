---
tags: vim
date: 2018-09-26 17:48:06
---

用 vim 查看日志时，有的查询语句以及其他特殊字符是特殊的 ANSI 字符，如果正常显示可以看出颜色，以及加粗样式。但是，默认是这样的

![](http://ogbkru1bq.bkt.clouddn.com/1537955317.png)

### powerman/vim-plugin-AnsiEsc

借助此插件可以让转义的字符正常显示

vim 中执行`:AnsiEsc`

![](http://ogbkru1bq.bkt.clouddn.com/1537955874.png)

### 绑定快捷键

```vim
nnoremap <leader>log :AnsiEsc<cr>
```
