---
tags: linux,arch,hidden
date: 2018-11-30 11:20:00
---

# manjaro 使用 yay 命令--待整理

2018 年 07 月 21 日 15:51:28 [HD2killers](https://me.csdn.net/HD2killers) 阅读数：1196

### 使用 yay 命令

> 安装 yay

```shell
sudo pacman -S yay1
```

> yay 安装软件，安装时不使用 sudo 安装网易云音乐

```shell
yay -S netease-cloud-music1
```

> 安装中文字体（可不选择），安装成功后可在 tweaks 中自行更换

```shell
yay -S --noconfirm wqy-microhei && fc-cache -fv1
```

> 安装 wps，及其字体

```shell
yay -S wps-office        # 安装wps
yay -S ttf-wps-fonts    # 安装wps字体12
```

> 配置 wps，使 wps 可以输入中文

```shell
$ sudo vim /usr/bin/wps        # 编辑wps配置文件
# 在 紧跟#!/bin/bash后添加下列三行
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"12345
```

> 安装 MariaDb 代替 mysql(MyriaDb 与 Mysql 相互兼容)

```
$ yay -S mariadb mariadb-clients
# 安装成功后，根据提示，输入下列指令初始化MariaDb数据库
$ sudo mysql_install_db --user=mysql --basedir=/usr --datadir=/var/lib/mysql
# 一番信息自动输出完成后，执行以下代码
$ sudo systemctl start mysqld # 启动MariaDb
$ mysqladmin -u root password "root" # 为root、用户添加密码
$ sudo systemctl enable mysqld # 设置mariaDb开机自启
$ mysql -uroot -p # 输入设置的的密码，登录数据库12345678
```

> 安装 ssr

```
yay -S shadowsocks-qt51
```

> 安装 VS code

```
yay -S visual-studio-code
```

## ArchLinux 你可能需要知道的操作与软件包推荐「持续更新」

发表于 2017-07-02 | 更新于 2018-09-12 | 分类于 [Linux](https://www.viseator.com/categories/Linux/) ， [Arch](https://www.viseator.com/categories/Linux/Arch/) | 阅读次数： 13126

## 你可能需要知道的操作与软件包推荐

在[第一篇教程](http://www.viseator.com/2017/05/17/arch_install/)中介绍了`ArchLinux`的基本安装，[第二篇教程](http://www.viseator.com/2017/05/19/arch_setup/)中介绍了必须的设置与图形界面的安装，这篇文章并不是教程，只是推荐一些自己日常使用的操作与软件包。写这篇文章时没有重新安装，所以不会有详细的过程，只是简单地列举应该装的软件包或者是基础的配置，更加细节的内容请查阅相关`wiki`。

### 安装 yay

在之前我们管理软件包都是使用官方为我们提供的`pacman`，软件包的来源都是官方。但是`Arch`拥有一个强大的用户库`AUR`即[Arch User Repository](https://wiki.archlinux.org/index.php/Arch_User_Repository)，为我们提供了官方包之外的各种软件包，一些闭源的软件包也可以在上面找到，可以说`AUR`极大地丰富了软件包的种类与数量，并可以配合`yay`这样的工具为用户省下大量安装、更新软件包的时间。

`yay`实际上也是一个软件包，我们可以把它看成是对`pacman`的包装，它兼容`pacman`的所有操作，最大的不同是我们可以用它方便地安装与管理`AUR`中的包，下面的许多软件包都是在`AUR`库中的，也都是使用`AUR`来安装的。

#### 安装

执行如下命令：

```
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

`yay`就被成功  地安装了。

#### `yay`使用

请见(虽然讲的是`yaourt`但是`yaourt`已经被废弃了，`yay`的使用方式与`yaourt`保持一致)：

> <https://www.linuxdashen.com/arch-linux%E4%BD%BF%E7%94%A8yaourt%E5%8C%85%E7%AE%A1%E7%90%86%E5%99%A8%E8%BD%BB%E6%9D%BE%E5%AE%89%E8%A3%85aur%E8%BD%AF%E4%BB%B6%E5%8C%85>

### 滚动更新

`ArchLinux`的更新机制是非常激进的滚动更新，也就是说`ArchLinux`的软件与内核会时刻与稳定版本保持一致，你所用的系统总是时刻保持最新的。

这个机制给很多`Arch`教徒带来了强大的快感，可以第一时间体验到新的软件与新 的内核，但是也存在着日常滚炸这样的问题。虽然滚动更新的包可能因为没有经过完善的测试会导致系统不能工作种种问题，但是绝大部分情况下的更新都不会导致太大的问题。修复滚炸的系统和提交 Bug 信息也是`ArchLinux`用户的技能之一。

滚动更新命令使用`yay`非常简单：

```
yay -Syu
```

### shadowsocks

#### 图形版本

**2017.10.11 更新：目前的 qt5 客户端可能有失效的问题，请使用 shadowsocks 包提供的命令行版本**

安装官方源中的`shadowsocks-qt5`包，自带图形界面，通过软件菜单（桌面环境自带）启动即可。

#### 命令行版本

安装官方源中的`shadowsocks`包，编辑`/etc/example.json`文件，按示例填写：

```
{
    "server":"my_server_ip",
    "server_port":8388,
    "local_address": "127.0.0.1",
    "local_port":1080,
    "password":"mypassword",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open": false
}
```

`server`：服务器地址

`server_port`：服务器端口

下面两行分别是本地地址和本地端口

`password`：密码

`method`：加密方式

然后以系统服务方式启动：

```
sudo systemctl start shadowsocks@example.service
```

如需开机启动：

```
sudo systemctl enable shadowsocks@example.service
```

### Chrome 代理

**需先配置好本地 shadowsocks 代理**

安装官方源中开源的`chromium`或者`AUR`中的`google-chrome`都可以，下面以`google-chrome`为例。

先用命令行代理启动`chrome`：

```
google-chrome-stable --proxy-server="socks5://127.0.0.1:1080"
```

`chromium`换下命令就可以。

然后安装[SwitchyOmega](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif?hl=en)这个插件，配置好[`GFWList`](https://raw.githubusercontent.com/gfwlist/gfwlist/master/gfwlist.txt)和代理规则就可以自动代理了。之后的启动就不需要命令行了。

### 命令行代理

**需先配置好本地 shadowsocks 代理**

推荐使用`proxychains-ng`包进行命令行代理：

安装`proxychains-ng`包后编辑`/etc/proxychains.conf`文件（需`root`权限）

到文件末尾找到`ProxyList`项，按示例添加本地代理：

![img](https://www.viseator.com/images/arch24.png)

图为我的`socks5`配置，保存后退出。

之后需要用代理运行的命令都可以通过在命令前加上`proxychains`来使用代理运行。

### 中文字体与中文输入法

中文字体推荐安装官方源中`noto-fonts-cjk`，中文输入法需要安装`fcitx`包与`fcitx-im`集合包，再加上一个中文支持包，可以到<https://wiki.archlinux.org/index.php/fcitx#Chinese>中挑选一个喜欢的包装上。

装完以后需要修改`/etc/profile`文件，在文件开头加入三行：

```
export XMODIFIERS="@im=fcitx"
export GTK_IM_MODULE="fcitx"
export QT_IM_MODULE="fcitx"
```

可以解决一些软件无法调出`fcitx`的问题。

### zsh

`zsh`是默认`shell` `bash`的替代品之一，它的特点是插件多配置方便，兼容`bash`脚本并且支持更强大的高亮与补全。

安装官方源中`zsh`包。

设置`zsh`为默认`shell`：

```
sudo chsh -s /bin/zsh username
```

推荐安装`AUR`中的`oh-my-zsh-git`这个包，可以帮助配置一些实用的功能。

其他主题插件配置请见[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)的配置介绍，分享一下我安装的插件：

```
plugins=(vim git sudo extract z wd archlinux zsh-autosuggestions zsh-syntax-highlighting)
```

注意后面两个插件需要安装相应的支持包并配置才能使用。

### Synapse

`Synapse`是一个快速的软件启动器，可以方便地查找安装的软件，设置快捷键使用再也不用找软件入口了。

安装官方源中的`synapse`包。

### Yakuake

`Yakuake`是一个终端模拟器，我使用它的原因是它支持下拉，配合快捷键使用非常方便：

![img](https://www.viseator.com/images/arch23.png)

安装官方源中的`yakuake`包。

### 虚拟机

有些时候需要使用`windows`而不想切换系统或干脆没有`windows`的情况下，我们可以使用`windows`虚拟机来代替。当然虚拟机的用处不止于此。

`Arch`下的虚拟机首先开源的`VirtualBox`，安装官方源的`virtualbox` `virtualbox-ext-vnc` ` virtualbox-guest-iso``virtualbox-host-modules-arch `这几个包。

再去[官网](https://www.virtualbox.org/wiki/Downloads)下载**Oracle VM VirtualBox Extension Pack** ，在设置中导入使用。安装`windows`的过程不在这里讲解，记得安装之后在`windows`内安装扩展客户端软件即可。

- **本文作者：** viseator(吴迪)
- **本文链接：** <https://www.viseator.com/2017/07/02/arch_more/>
- **版权声明：** 本博客所有文章除特别声明外，均采用 [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/) 许可协议。转载请注明出处！
