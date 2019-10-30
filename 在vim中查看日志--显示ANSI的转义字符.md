---
tags: [vim]
date: 2018-09-26 17:48:06
---

用 vim 查看日志时，有的查询语句以及其他信息是经过特殊的 ANSI 字符修饰的，如果正常显示可以看出颜色，以及加粗样式。但是，默认没有任何样式，很难分辨,如图：

![](http://ogbkru1bq.bkt.clouddn.com/1537955317.png)

### powerman/vim-plugin-AnsiEsc

借助此插件可以让转义的字符正常显示

vim 中执行`:AnsiEsc`

![](http://ogbkru1bq.bkt.clouddn.com/1537955874.png)

### 绑定快捷键

```vim
nnoremap <leader>log :AnsiEsc<cr>
```
