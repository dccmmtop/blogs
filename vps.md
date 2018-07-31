---
tags: vps
---

### shadowsocks 服务器安装

- 更新软件源

```shell
sudo apt update
```

- 安装 pip 环境

```shell
sudo apt-get install python-pip
```

- 直接安装 shadowsocks

```shell
sudo pip install shadowsocks
```

- 运行 shadowsocks 服务器
  启动命令如下：如果要停止运行，将命令中的 start 改成 stop。

```shell
sudo ssserver -p 8388 -k password -m rc4-md5 -d start
```

也可以使用配置文件进行配置，方法创建/etc/shadowsocks.json 文件，填入如下内容：

```json
{
  "server": "my_server_ip",
  "server_port": 8388,
  "local_address": "127.0.0.1",
  "local_port": 1080,
  "password": "mypassword",
  "timeout": 300,
  "method": "rc4-md5"
}
```

各字段的含义：
| 字段| 含义|
|-----|-----|
|server| 服务器 IP (IPv4/IPv6)，注意这也将是服务端监听的 IP 地址|
| server_port| 服务器端口|
|local_port| 本地端端口|
| password| 用来加密的密码|
|timeout| 超时时间（秒）|
|method| 加密方法，可选择 “bf-cfb”, “aes-256-cfb”, “des-cfb”, “rc4″, 等等|

Tips: 加密方式推荐使用 rc4-md5，因为 RC4 比 AES 速度快好几倍，如果用在路由器上会带来显著性能提升。旧的 RC4 加密之所以不安全是因为 Shadowsocks 在每个连接上重复使用 key，没有使用 IV。现在已经重新正确实现，可以放心使用。更多可以看 issue。
Tips: 如果需要配置多个用户,可以这样来设置:

```json
{
  "server": "my_server_ip",
  "port_password": {
    "端口1": "密码1",
    "端口2": "密码2"
  },
  "timeout": 300,
  "method": "rc4-md5",
  "fast_open": false
}
```

创建完毕后，赋予文件权限：

```shell
sudo chmod 755 /etc/shadowsocks.json
```

为了支持这些加密方式，你要需要安装

```shell
sudo apt–get install python–m2crypto
```

然后使用配置文件在后台运行：

```shell
sudo ssserver -c /etc/shadowsocks.json -d start
```

配置开机自启动
编辑 /etc/rc.local 文件
sudo vi /etc/rc.local
在 exit 0 这一行的上边加入如下

```shellj
/usr/local/bin/ssserver –c /etc/shadowsocks.json
```

或者 不用配置文件 直接加入命令启动如下：

```shell
/usr/local/bin/ssserver -p 8388 -k password -m aes-256-cfb -d start
```

到此重启服务器后，会自动启动。
安装和配置 shadowsocks 客户端
最新版本的 shadowsocks 客户端可以从其 Github 上下载.
客户端配置及使用方法可以参考这里的教程。
iPhone 及安卓手机上的配置,可以参考这个教程。
需要特别注意的是,在 Chrome 上需要设置代理为 SOCKS v5 模式,127.0.0.1:1080,建议安装 SwitchySharp 扩展。

### ubuntu 客户端

在 ubuntu 上可以这样，通过 PPA 源安装，仅支持 Ubuntu 14.04 或更高版本。

```shell
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```

由于是图形界面，配置和 windows 基本没啥差别就不赘述了。经过上面的配置，你只是启动了 sslocal 但是要上网你还需要配置下浏览器到指定到代理端口比如 1080 才可以正式上网。
