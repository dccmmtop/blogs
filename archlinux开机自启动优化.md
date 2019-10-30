---
date: 2018-12-25
tags: [arch,自启]
---


1.查看开机启动时间：

```shell
systemd-analyze
```

2.查看开机启动项及启动时间：

```shell
systemd-analyze blame
```

3.查看出错启动项：



```shell
systemctl --all | grep not-found
```

4.关闭出错启动项（

以 plymouth-start.service 为例）：

```shell
systemctl mask plymouth-start.service
```
