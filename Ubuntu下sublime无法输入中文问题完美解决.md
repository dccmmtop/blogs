## Ubuntu下sublime无法输入中文问题完美解决

1. 从官网[下载](https://download.sublimetext.com/sublime_text_3_build_3143_x64.tar.bz2)压缩包
2. 解压到`/opt/`目录下
3. 将解压后的文件夹重命名为`sublime_text`
4. 在终端执行`git clone https://github.com/lyfeyaj/sublime-text-imfix.git`
5. 进入`sublime-text-imfix/src/` 执行 `sudo cp subl /opt/bin/`
6. 进入`sublime-text/`下 执行 `./sublime-imfix`
7. 大功告成，，仔细看一下`sublime-imfix`的代码，就知道为什么这么做了