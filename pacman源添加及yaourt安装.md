# pacman源添加及yaourt安装

2018年01月13日 19:37:48 [sticky_rice](https://me.csdn.net/sticky_rice) 阅读数：3844



1.添加源

源，是软件源的简称，是互联网上存放软件包和库的服务器，这些服务器一般都是由官方维护，不少高校、互联网公司等权威机构有自己的镜像源，也有开发者自己的社区软件源，这里的软件比较常用，但暂时还没有被官方收入。

软件包工具，是使用这些源的工具，多是终端里的一种命令，如apt-get 、yum、dpkg、pacman等，在这些工具中，分为高级和低级，低级工具（dpkg、rpm）执行安装删除等任务，高级工具（apt-get、yum、pacman）提供依赖关系解决等功能。每种工具都有相对应的软件包格式、相对应的源。

pacman源的设置在/etc/pacman.conf和/etc/pacman.d/mirrorlist里， 如果有内容可以全部删除，添加内容如下：

 

```perl
Server = http://mirrors.ustc.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.aliyun.com/archlinux/$repo/os/$arch
Server = http://mirrors.163.com/archlinux/$repo/os/$arch
Server = http://mirrors.hust.edu.cn/archlinux/$repo/os/$arch
Server = http://run.hit.edu.cn/archlinux/$repo/os/$arch
Server = http://ftp.kaist.ac.kr/ArchLinux/$repo/os/$arch
Server = http://mirrors.hustunique.com/archlinux/$repo/os/$arch
Server = http://ftp.jaist.ac.jp/pub/Linux/ArchLinux/$repo/os/$arch
Server = http://mirror.premi.st/archlinux/$repo/os/$arch
Server = http://mirror.its.dal.ca/archlinux/$repo/os/$arch
Server = http://mirror.de.leaseweb.net/archlinux/$repo/os/$arch
Server = http://mirror.clibre.uqam.ca/archlinux/$repo/os/$arch
```

在/etc/pacman.conf文件中最后一行添加如下内容：

 

```cs
# USTC
[archlinuxcn]
 
SigLevel = Optional TrustedOnly
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```

 

2.安装yaourt

pacman -S yaourt


终端中执行sudo pacman -Syu 进行更新







## zsh 安装

```
$ sudo pacman -S zsh
```

------

配置默认shell：

```
$ sudo vim /etc/passwd
```

将指定用户的shell路径改为 `/usr/bin/zsh` 即可，也就是将 bash 改为 zsh

------

## on-my-zsh 安装

保证安装了git ，curl (或 wget )

```
$ sudo pacman -S git wget curl #curl 一般都有了，装 git wget 即可
```

- curl 方式：

```
$ sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

- wget 方式

```
$ sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

------

**修改主题**

```
$ sudo vim ~/.zshrc
```

找到 `ZSH_THEME="robbyrussell"`

修改为 `ZSH_THEME="random"` 为随机主题，要换其他主题，修改此处即可

修改完后打开终端即为 `on-my-zsh`

作者：mfiaqv

链接：https://www.jianshu.com/p/e8f976447506

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。







# How to copy Docker Images to Another Host?

[ February 8, 2017](https://www.techietown.info/2017/02/copy-docker-images-another-host/)  [admin	](https://www.techietown.info/author/admin/)[ 0 Comments](https://www.techietown.info/2017/02/copy-docker-images-another-host/#respond) [docker](https://www.techietown.info/tag/docker/)

**How to copy Docker Images to Another Host?**

Sometimes you might need to copy your docker container image to another docker Host. One way is to commit the image to a Private or Public Docker Repository and on the second host, run the “docker pull ” command. This will work most of the times, but if you dont have a private docker registry, you will have to look for other options.

Docker has “save” feature which is used to persist an image. First we need to get the Docker image ID .



**Use the following command to see the image ID**

*docker images*

**To save one image**



*docker save myimage-1 > /home/myimage-1.tar*



This command will save the image to tar archive /home/myimage-1.tar

Now we can copy this tar file to destination using any of the linux tools like “scp” or “rsync”.



**To load this image to Docker on Destination Host** 

Run the following command

*docker load < /home/myimage-1.tar*





This will take few seconds depending on the image size. Once the command finished, we can verify the image is loaded to Docker on new server , by running the following command

*# check the available images*
*docker images*

The Image “myimage-1” should be listed in the output. Please see the image below



![docker-save-copy-image](https://www.techietown.info/wp-content/uploads/2017/02/docker-save-copy-image.png)

You can see the image “myimage-1” listed .

Now we can start container using these images.

This way we copied docker image to another host without using Docker registry. There is one more feature called “export” , this is used to persist the running containers (not images). This feature also allows us to create tar archive of the running container ,  but we will loss the history of the particular image.









以arch的基本系统为基础，我们可以对其进行各种配置操作，让其更符合个人喜好。下面介绍了一些常用的配置。

# 1.用户管理

新安装的arch只有一个root用户，使用root用户来进行日常系统管理是很危险的事情，说不定哪天手抖输了个rm -rf /*然后你就呵呵了。所以我们通常用普通用户来进行日常使用，有需要的时候就用sudo来获取root权限。

首先添加一个用户，并把它加到wheel组

```
useradd -m -G wheel -s /bin/bash  [用户名]
```

然后为这个用户设置密码

```
passwd [用户名]
```

最后设置wheel组的用户能用sudo获取root权限:

```
visudo
    #找到这样的一行,把前面的#去掉:
    #%wheel ALL=(ALL) ALL
按ESC键，输入x!回车就可以保存并退出
```

现在我们就可以使用这个新用户了。执行exit退出root用户登录，然后用新的用户重新登录系统。

------

# 2.软件管理

arch采用pacman来管理软件，常用的命令有:

```
#安装软件/软件组
pacman -S [软件1] [软件2] ...

#卸载软件/软件组
pacman -R [软件1] [软件2] ...

#刷新数据库
pacman -Syy

#升级整个系统
pacman -Syyu
```

记住pacman前面要加sudo。
 archlinux采用滚动更新，也就是说只要配置好系统，以后就只需要隔三差五地Syyu一下就行了。这样你的arch永远都是最新版,一劳永逸，所以用arch的人都比较懒。

作为pacman的应用我们来玩两个简单的例子

```
sudo pacman -S bash-completion
```

安装完成后重新登录，输命令的时候就可以感受到效果咯，Tab补全大法好。如果还觉得不够的话可以试试zsh。

现在我们来安装一个小玩意

```
sudo pacman -S screenfetch
```

至于它是干啥的，试试就知道了

```
screenfetch
```

------

# 3.图形界面

单纯的命令行看着总会很无聊，大多数人都希望安装图形界面吧。这里以Gnome3桌面为例介绍一下，其他如KDE Xfce LXDE的安装大同小异。

首先安装xorg-server，这是图形界面的基础。

```
sudo pacman -S xorg-server
```

然后安装对应的驱动程序，比如安装nvidia的显卡驱动:

```
sudo pacman -S xf86-video-nouveau
```

具体的驱动程序请看[archwiki](https://link.jianshu.com?t=https://wiki.archlinux.org/index.php/Xorg_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))。

当然，如果你觉得麻烦也可以把软件组xorg中的软件一股脑安上。

```
sudo pacman -S xorg
```

另外如果是你的arch是在virtualbox中安装的，那么你可以安装virtualbox-guest-utils这个软件组:

```
sudo pacman -S virtualbox-guest-utils
```

现在可以安装Gnome3桌面了。这一步当然是直接安装gnome这个软件组啦

```
sudo pacman -S gnome
```

为了让我们开机时能够进入图形界面，还需要把显示管理器GDM设置为开机启动。

```
sudo systemctl enable gdm.service
```

现在重启系统，进入GDM,然后是输密码登录，就可以看到Gnome桌面了，就像这样:

作者：Air_WaWei

链接：https://www.jianshu.com/p/6eaf642a94ed

來源：简书

简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。