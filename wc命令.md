---
tags: [linux,wc]
date: 2018-08-19 16:35:57
---

### Linux 系统中的 wc(Word Count)命令的功能为统计指定文件中的字节数、字数、行数，并将统计结果显示输出。

### 命令格式

wc [选项]文件...

### 命令功能

统计指定文件中的字节数、字数、行数，并将统计结果显示输出。该命令统计指定文件中的字节数、字数、行数。如果没有给出文件名，则从标准输入读取。wc 同时也给出所指定文件的总统计数。

### 命令参数

| 参数      | 操作                                                         |
| --------- | ------------------------------------------------------------ |
| -c        | 统计字节数。                                                 |
| -l        | 统计行数。                                                   |
| -m        | 统计字符数。这个标志不能与 -c 标志一起使用。                 |
| -w        | 统计字数。一个字被定义为由空白、跳格或换行字符分隔的字符串。 |
| -L        | 打印最长行的长度。                                           |
| -help     | 显示帮助信息                                                 |
| --version | 显示版本信息                                                 |

### 使用实例

#### 实例 1：查看文件的字节数、字数、行数

命令：

```shell
wc test.txt
```

输出：

```shell
[root@localhost test]#cat test.txt

hnlinux

peida.cnblogs.com

ubuntu

ubuntu linux

redhat

Redhat

linuxmint

[root@localhost test]#wc test.txt

7 8 70test.txt

[root@localhost test]#wc -l test.txt

7test.txt

[root@localhost test]#wc -c test.txt

70test.txt

[root@localhost test]#wc -w test.txt

8test.txt

[root@localhost test]#wc -m test.txt

70test.txt

[root@localhost test]#wc -L test.txt

17 test.txt
```

说明：

7 8 70 test.txt

行数 单词数 字节数 文件名

#### 实例 2：用 wc 命令怎么做到只打印统计数字不打印文件名

```shell
[root@localhost test]#wc -l test.txt

7test.txt

[root@localhost test]#cat test.txt |wc -l

7[root@localhost test]#
```

说明：

使用管道线，这在编写 shell 脚本时特别有用。

#### 实例 3：用来统计当前目录下的文件数

```shell
ls -l | wc -l

[root@localhost test]#cd test6

[root@localhost test6]#ll

总计 604

---xr--r-- 1 root mail 302108 11-30 08:39linklog.log

---xr--r-- 1 mail users 302108 11-30 08:39log2012.log

-rw-r--r-- 1 mail users 61 11-30 08:39log2013.log

-rw-r--r-- 1 root mail 0 11-30 08:39log2014.log

-rw-r--r-- 1 root mail 0 11-30 08:39log2015.log

-rw-r--r-- 1 root mail 0 11-30 08:39log2016.log

-rw-r--r-- 1 root mail 0 11-30 08:39log2017.log

[root@localhost test6]#ls -l | wc -l

[root@localhost test6]#
```

说明：

数量中包含当前目录
