---
tags: [manjaro,linux]
date: 2019-02-17 12:42:31
---

安装系统注意事项：

对于有独立显卡的笔记本 **驱动选择 no free**， 否则会经常卡死

### 安装后配置

#### 首先更换国内源

```shell
sudo pacman -Syy
sudo pacman-mirrors -i -c China -m rank  #选一个清华源就行
sudo pacman -Syyu
```

## 安装vim
```shell
sudo pacman -S vim
```
### 添加arch源

```shell
sudo vim /etc/pacman.conf
```

把下边这个添加进去
```cogfig
[archlinuxcn]
SigLevel = Optional TrustedOnly
Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch
```
然后

```shell
sudo pacman -Syy && sudo pacman -S archlinuxcn-keyring
```
### 安装zsh

```shell
sudo pacman -S git

sudo pacman -S zsh

sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

chsh -s (where zsh)
```

### 更换输入法

```shell
sudo pacman -S fcitx-sogoupinyin
sudo pacman -S fcitx-im # 全部安装
sudo pacman -S fcitx-configtool # 图形化配置工具
```

设置中文输入法环境变量，编辑~/.xprofile文件，增加下面几行(如果文件不存在，则新建)

```config
export GTK_IM_MODULE=fcitx
export QT_IM_MODULE=fcitx
export XMODIFIERS="@im=fcitx"
```

## 常用软件

1. sudo pacman -S shadowsocks-qt5

2. sudo pacman -S visual-studio-code-bin

3. sudo pacman -S make

4. sudo pacman -S screenfetch # 显示Linux环境工具

5. sudo pacman -S clang

6.  sudo pacman -S gdb

7. sudo pacman -S wps-office ttf-wps-fonts

8. sudo pacman -S shutter perl-goo-canvas # 截图工具

9. sudo pacman -S synapse # 启动器

![1550379292.png](https://i.loli.net/2019/02/17/5c68e93816d99.png?filename=1550379292.png)

10. sudo pacman -S google-chrome

11. sudo pacman -S the_silver_searcher

12. sudo pacman -S yay

13. yay -s polipo # socks5 代理转换为 http 代理 

关于polipo 设置请看 https://dccmm.world/topics/Linux_shadowsocks%E4%BB%A3%E7%90%86%E8%BD%AC%E5%8C%96%E4%B8%BAHttp%E4%BB%A3%E7%90%86
