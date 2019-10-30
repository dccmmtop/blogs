---
tags: [linux]
date: 2018-09-01 15:34:09
---

首先检查你的 console 是否已经使用了代理

```shell
curl ip.gs
```

如果使用的是国内的 ip 那么就说明没有使用代理

这里只针对使用 socket 服务翻墙

在`.bash_profile`文件中添加如下两行

```shell
export http_proxy="socks5://127.0.0.1:1080"
export https_proxy="socks5://127.0.0.1:1080"
```

其中 `1080是soket监听的本地端口`

然后重启终端，（ `source .bash_profile` 可能不起作用）

此时再输入 `curl ip.gs` 查看结果
