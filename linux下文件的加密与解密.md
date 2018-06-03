## linux下文件的加密与解密
使用GnuPG实现对文件的加密与解密[详细参考](https://www.howtoing.com/linux-password-protect-files-with-encryption)
### 安装GnuPG
```shell
sudo apt install gnupg
```
### 加密
```shell
gpg -c filename // -c 使用对称加密
```
输入命令`gpg --version` 查看更多的加密方式

### 解密
```shell
gpg filename
```
