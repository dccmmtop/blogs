---
tags: [vim]
date: 2018-10-23 14:29:42
---

_本文章为转载内容，点击查看原文章https://zhuanlan.zhihu.com/p/27389503_

使用脚本语言，可以更灵活地定制编辑器以完成复杂的任务。

## **自定义命令**

Vim 编辑器允许定义自己的命令，我们可以像执行内置命令一样来执行我们自己定义的命令。

使用以下:command 命令自定义命令：

```vim
:command Delete_first :1delete
```

注意自定义命令的名称，必须以大写字母开头，而且不能包含下划线；如果我们执行:Delete_first 自定义命令，那么 Vim 就会执行:1delete 命令，从而删除第一行。

可以使用!来强制重新定义同名的自定义命令：

```text
:command! -nargs=+ Say :echo <args>
```

用户定义的命令可以指定一系列的参数，参数的个数由-nargs 选项在命令行中指定。例如定义 Delete_one 命令没有参数：

```vim
:command Delete_one -nargs=0 1delete
```

默认情况下-nargs=0，所以可以省略。其他-nargs 选项值如下：

-nargs=0 没有参数

-nargs=1 1 个参数

-nargs=\* 任何个数的参数

-nargs=? 零个或是一个参数

-nargs=+ 一个或是更多个参数

在命令定义中，参数是由关键字<args>指定的：

```text
:command -nargs=+ Say :echo "<args>"
```

输入以下自定义命令：

```vim
:Say Hello World
```

命令的执行结果显示：

```text
Hello World
```

使用-range 选项，可以指定一个范围作为自定义命令的参数。-range 选项值如下：

-range 允许范围，默认为当前行

-range=%允许范围，默认为当前文件(while file)

-range=count 允许范围，单一的数字

当指定范围之后，就可以用关键字<line1>和<line2>得到这个范围的第一行和最后一行。

例如以下定义了 SaveIt 命令，用于将指定范围的文件写入文件 save_file：

```vim
:command -range=% SaveIt :<line1>,<line2>write! save_file
```

关键字<f-args>含有与关键字<args>相同的信息，所不同的是它用于调用函数。例如以下自定义命令：

```text
:command -nargs=* DoIt :call AFunction(<f-args>)
```

执行自定义命令：

```vim
:DoIt a b c
```

将会传递参数给调用的函数：

```vim
:call AFunction("a","b","c")
```

其他选项和关键字包括：

-count=number 指定数量保存在关键字<count>中

-bang 指定!修饰符存放在关键字<bang>中

-register 指定寄存器，默认为未命名寄存器，寄存器的定义保存在关键字<register>中

-bar 其他命令可以用|跟随在此命令之后

-buffer 命令仅对当前缓冲区有效

使用以下命令，首先分别创建一个用户自定义命令，然后再将两个命令组合起来。

```vim
command! -bar DelTab %s/	//
command! DelLF %s/\n//
command! FmtCode DelTab|DelLF
```

view raw

ScriptCommandMulti.vim

hosted with ❤ by

GitHub

## **列示自定义命令**

使用以下命令，可以列出用户定义的命令：

```vim
:command
```

## **删除自定义命令**

使用以下:delcommand 命令，可以删除用户定义的命令：

```vim
:delcommand Delete_one
```

使用以下命令，清除所有的用户定义的命令：

```vim
:comclear
```
