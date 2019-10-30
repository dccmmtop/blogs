---
tags: [ctags,vim]
date: 2018-08-14 11:51:23
---

### 功能

函数跳转、查看源码

### 简介

项目开发过程中会调用一些库函数和宏定义，这些头文件一般不在工程目录下，
所以工程目录下生成的 tags 文件，无法实现在 vim 中跳转到一些库头文件定义的结构体或宏定义当中
这就需要 vim 实现跳转到库头文件，方便浏览代码

### 安装

```shell
apt install ctags
```

### 生成 tags

假如我需要经常查看 rails 的源码，从[github](https://github.com/rails/rails)下载 rails 的源码，在本地新建一个专门存放所有源码的文件夹`ctags-source`,然后把 rails 的源码移到这个文件夹内，
在`ctags-_source`目录下执行

```shell
ctags -R .
```

便会生成一个 tags 文件。

### vim 中使用生成的 tags 文件

在我们的 rails 项目下 执行`ctags -R .`会生成关于本项目的 tags 文件，可以帮助我们实现函数跳转，
在 `.vimrc`中 添加

```vim
" 加载rails的tags文件
set tags=~/ctag_source/tags
" 加载本项目下的tags文件
set tags+=tags;
```

在光标所在单词下，使用 `ctrl + ]`便可以跳转函数的源码处，使用`ctrl + o` 或者`ctrl + t`返回
