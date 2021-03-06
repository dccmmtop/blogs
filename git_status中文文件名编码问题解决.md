## git status 中文文件名编码问题解决

Git发展至今，对中文的支持任然不足，本地化做得并不完善，命令的输出及命令的帮助还只能输出英文。

目前大家最关心的问题是：可以在提交说明中使用中文。

要实现提交说明的中文注释，这就需要需要对Git进行特殊设置。

Linux平台的中文用户一般会使用UTF-8字符集，Git在UTF-8字符集下可以工作得非常好：

在提交时，可以在提交说明中输入中文。

显示提交历史，能够正常显示提交说明中的中文字符。

可以添加名称为中文的文件，并可以在同样使用UTF-8字符集的Linux环境中克隆和检出。

可以创建带有中文字符的里程碑名称。

但是，在默认设置下，中文文件名在工作区状态输出，查看历史更改概要，以及在补丁文件中，文件名的中文不能正确地显示，而是显示为八进制的字符编码，示例如下：
```shell
$ git status -s
?? "\350\257\264\346\230\216.txt\n
$ printf "\350\257\264\346\230\216.txt\n"
说明.txt
```

通过将Git配置变量 core.quotepath 设置为false，就可以解决中文文件名称在这些Git命令输出中的显示问题，
示例：

```shell
$ git config --global core.quotepath false
$ git status -s
```

> 输出: 
>
> 说明.txt