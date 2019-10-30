---
tags: [linux]
date: 2019-01-14 08:43:30
---

>  摘选自 https://blog.csdn.net/mxgsgtc/article/details/12048919

## 文件分割

#### 模式一：指定分割后文件行数

例如将一个BLM.txt文件分成前缀为 BLM_ 的1000个小文件，后缀为系数形式，且后缀为4位数字形式

先利用

```shell
wc -l BLM.txt #      读出 BLM.txt 文件一共有多少行
```

再利用 split 命令

```shell
split -l 2482 ../BLM/BLM.txt -d -a 4 BLM_
```

将 文件 BLM.txt 分成若干个小文件，每个文件2482行(-l 2482)，文件前缀为BLM_ ，系数不是字母而是数字（-d），后缀系数为四位数（-a 4）

#### 模式二：指定分割后文件大小

split -b 10m server.log waynelog

对二进制文件我们同样也可以按文件大小来分隔。


## 文件的合并

在Linux下用cat进行文件合并：

```shell
cat small_files* > large_file
```

将a.txt的内容输入到b.txt的末尾

cat a.txt >> b.txt
