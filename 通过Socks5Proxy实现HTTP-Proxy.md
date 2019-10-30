---
tags: [proxy,linux]
date: 2018-12-11 13:46:48
---

最近在用 Eclipse 来写 Android 程序时发现 Android SDK Manager 下载 SDK 时非常慢（Google~）。 查看了下发现 Android SDK Manager 可以使用 HTTP Proxy 但是 我只有 Socks5 Proxy 于是查了下如何通过 Socks5 Proxy 实现 HTTP Proxy，最终找到了如下方法。

### 安装 Polipo

[Polipo](http://www.pps.univ-paris-diderot.fr/~jch/software/polipo/) 是一个很小以及快速的有缓存的 HTTP Proxy，关键是它可以和 Socks5 协议通讯。

在 Ubuntu 下安装 Polipo 很简单：

```
$ sudo apt-get install -y polipo
```

### 修改配置文件

假设 Sockes Proxy 是本机的 12345 端口，那么配置文件修改为：

```
proxyAddress = "0.0.0.0"
proxyPort = 8118
socksParentProxy = "127.0.0.1:12345"
socksProxyType = socks5
allowedClients = 127.0.0.1
```

其中:

- proxyPort 是 HTTP Proxy 端口地址
- socksParentProxy 表示通过这个地址转发请求
- socksProxyType 表示通过 socks5 来转发请求
- allowedClients 表示允许访问这个 HTTP Proxy 的地址

### 重启 Polipo

修改好配置文件后，只需要重启 Polipo 即可：

```
$ sudo /etc/init.d/polipo restart
```

现在，在需要使用 HTTP Proxy 的地方填上 127.0.0.1:8118 即可。
