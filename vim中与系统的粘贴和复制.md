# vim中与系统的粘贴和复制

以前就遇到过的问题。有如下情景：

> 1.假设现在我要将文件a的部分内容复制到文件b中，一般情况，我会用`vs`或者`sp`命令打开这两个文件然后用`y`和`p`进行复制粘贴。但是如果分别用vim打开这两个文件就不能完成上述动作。 
> 2.假设我先在要把vim打开的源代码中的部分内容复制到博客中，一般我会用vim编辑好以后，退出用gedit打开，或者cat一下，再复制到系统剪切板，再粘贴。

今天，对于vim这个没办法跟“外界”交流的特性忍够了，决定解决一下。

### **1.首先，查看vim版本是否支持clipboard**

```
vim --version | grep "clipboard"1
```

结果如下： 

![这里写图片描述](https://img-blog.csdn.net/20161215215734971?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvemhhbmd4aWFvOTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

clipboard 前面有一个小小的减号，说明不支持。

### **2.如果不支持的话，需要安装图形化界面的vim，或者重新编译vim**

```
sudo apt-get install vim-gnome1
```

安装完成后再次执行：

```
vim --version | grep "clipboard"1
```

发现已经支持clipboard

### **3.vim的寄存器**

打开vim输入`:reg`查看vim的寄存器，当支持clipboard之后，会多出`"+`寄存器，表示系统剪切板，在vim中进入visual视图后使用`"Ny`(N表示特定寄存器编好)，将内容复制到特定的剪切板，那么我们的目的是要复制到系统剪切板则需要选中内容后输入命令：

```
"+y1
```

粘贴到特定的寄存器也是同理。例如`"+p`将系统剪切板的内容拷贝到vim中（非编辑模式下）。



**4. 映射**

```vim
" 从系统剪切板粘贴
nnoremap P "+p
" 复制到系统剪切板
vmap Y "+y
```



