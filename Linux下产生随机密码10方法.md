## Linux下产生随机密码10方法

Linux的特点之一，就是给我们提供了多种选择。一种目的，可以多种方法解决。 如何在Linux下产生随机密码呢?我给大家收集了10来种方法，仅供参考。用得着的就mark下。对于下面的任何命令，都可以控制输出结果的长度。

Linux的特点之一，就是给我们提供了多种选择。一种目的，可以多种方法解决。

如何在Linux下产生随机密码呢?我给大家收集了10来种方法，仅供参考。用得着的就mark下。对于下面的任何命令，都可以控制输出结果的长度。

[![Linux下产生随机密码10方法 ](https://s4.51cto.com/wyfs02/M01/A0/E2/wKioL1mfkRbjZTC6AANa7pkjeA0889.png-wh_651x-s_702774202.png)](https://s4.51cto.com/wyfs02/M01/A0/E2/wKioL1mfkRbjZTC6AANa7pkjeA0889.png-wh_651x-s_702774202.png)

**#1**

```
date +%s | sha256sum | base64 | head -c 32 ; echo
```

上述命令使用SHA来哈希日期，输出头32个字节。

**#2**

```
< /dev/urandom tr -dc _A-Z-a-z-0-9 | head -c${1:-32};echo;
```

上述命令使用内嵌的/dev/urandom，只输出字符，结果取头32个。

**#3**

```
openssl rand -base64 32
```

上述命令使用系统自带的openssl的随机特点来产生随机密码

**#4**

```
tr -cd ‘[:alnum:]‘ < /dev/urandom | fold -w30 | head -n1
```

**#5**

```
strings /dev/urandom | grep -o ‘[[:alnum:]]’ | head -n 30 | tr -d ‘\n’; echo
```

通过过滤字符命令，输出随机密码

**#6**

```
< /dev/urandom tr -dc _A-Z-a-z-0-9 | head -c6
```

这个命令比起来比较简单了

**#7**

```
dd if=/dev/urandom bs=1 count=32 2>/dev/null | base64 -w 0 | rev | cut -b 2- | rev
```

上述命令使用命令dd的强大功能

**#8**

```
 </dev/urandom  tr -dc ’12345!@#$%qwertQWERTasdfgASDFGzxcvbZXCVB’ | head -c8; echo “”
```

上述命令输出很简洁

**#9**

```
randpw(){ < /dev/urandom tr -dc _A-Z-a-z-0-9 | head -c${1:-16};echo;}
```

使用randpw随时产生随机密码，可以把它放到~/.bashrc文件里面。

**#10**

```
date | md5sum
```

如果只用这一个，足够了，因为它太简洁了，:-)