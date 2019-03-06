---
tags: arch
date: 2019-02-14 16:57:53
---

本机是双硬盘，系统在固态中装着，每次开机需要手动挂载机械硬盘，很是麻烦。

#### 解决办法


### 安装ntfs-3g
```shell
sudo pacman -S ntfs-3g
```
### 修改fstab
```shell
sudo vim /etc/fstab
```
```config
#
# /etc/fstab: static file system information
#
# <file system>	<dir>	<type>	<options>	<dump>	<pass>
/dev/mmcblk0p1  /boot   vfat    defaults        0       0
/dev/sda1       /mnt/tmp ntfs-3g defaults 0 0
```
说明，把/dev/sda1 挂在到/mnt/tmp 目录下，type为ntfs-3g

示例：

![1550134609.png](https://i.loli.net/2019/02/14/5c652d91f0f7b.png?filename=1550134609.png)

