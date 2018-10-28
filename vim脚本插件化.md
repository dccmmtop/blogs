---
tags: vim
date: 2018-10-28 15:48:33
---

日积月累，自己写的 vim 脚本越来越多，大大的方便了日常编写任务，但是这些脚本没有做成插件的形式，导致换一台新机器时，不方便下载使用，下面就介绍一下如何把
自己写的脚本做成一个插件，可以在`vimrc`中使用`Plug xxx`安装。

### begin

1.  新建文件夹，命名为`vim_script`
2.  进入文件价，执行 `git init`初始化一个仓库
3.  去 github 新建一个仓库，`vim_scipt`
4.  设置本地仓库的 remote 信息
5.  在 vim_script 下新建 autoload 文件夹，把自己写的 vim 脚本放到 autoload 下
6.  在 vim_script 下新建 plugin 文件夹，新建`script.vim`(名字随意)，在该文件内设置脚本执行的命令，或者设置执行脚本的快捷键

如图：

![](http://ogbkru1bq.bkt.clouddn.com/1540713719.png)
