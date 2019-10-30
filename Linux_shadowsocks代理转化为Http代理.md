---
tags: [proxy]
date: 2019-02-14 17:09:31
---

### 安装 polipo

```shell
yay -s polipo
```

### 配置

打开 `/etc/polipo/config` 添加如下配置

```config
proxyAddress = "0.0.0.0"  # 监听地址
socksParentProxy = "127.0.0.1:1080"  # socks 代理地址
socksProxyType = socks5  # 类型
proxyPort = 8118 # 设置端口
```

### 测试

```ruby
curl --proxy http://127.0.0.1:8118 http://ip.gs
```
输出

```txt
Current IP / 当前 IP: xxxxx
ISP / 运营商: it7.net leaseweb.com
City / 城市: Los Angeles California
Country / 国家: United States
```

示例：

![1550135740.png](https://i.loli.net/2019/02/14/5c65324b1c1d1.png?filename=1550135740.png)
