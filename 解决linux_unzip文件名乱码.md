---
tags: [linux]
date: 2018-08-09 15:14:16
---

### 给 unzip 打补丁

根据 [GitHub - ikohara/dpkg-unzip-iconv: Makefile for Debian unzip package with iconv](https://github.com/ikohara/dpkg-unzip-iconv) 上的安装步骤，给 unzip 打补丁，然后就可以用-O 参数了

### unar 方法

```shell
sudo apt install unar
```

这个最简单省力，默认 debian 已经安装了额 unar，这个工具会自动检测文件的编码，也可以通过-e 来指定：

unar file.zip
