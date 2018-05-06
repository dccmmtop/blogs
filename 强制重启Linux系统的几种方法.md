# 强制重启Linux系统的几种方法

 [2014年01月26日 星期日](https://zohead.com/archives/linux-force-reboot/)  [Uranus Zhou](https://zohead.com/archives/author/admin/) [Linux](https://zohead.com/archives/category/technology/linux/), [技术](https://zohead.com/archives/category/technology/)

实际生产环境中某些情况下 Linux 服务器系统在出现致命错误需要远程进行重启，通过常规的 `reboot`、`init 6` 等方法无法正常重启（例如重启时卡在驱动程序里等情况），这时就需要通过下面介绍的几种特殊的方法进行强制重启。

> **注意**
>
> 下面这些强制重启 Linux 的方法都是直接跳过 umount 文件系统及 sync 等操作，可能导致数据损坏，不在特殊情况下请勿使用。另外当然这些都是需要 root 超级用户权限的哦。

## reboot 命令

直接通过运行 `reboot -nf` 命令，这样重启时可以指定跳过 init 的处理和 sync 操作，这样可以避免大多数情况下的问题。

## magic SysRq key 方法

magic SysRq key 通过 proc 接口提供用户直接发底层命令给 kernel 的功能，可以实现关机、重启、宕机等操作，Linux kernel 需要开启 `CONFIG_MAGIC_SYSRQ` 才可以支持 magic SysRq key。

运行下面两条命令就可以直接强制重启系统：

相应的直接强制关机的命令：

## watchdog 方法

如果 Linux kernel 未开启 magic SysRq key 或者不起作用，可以尝试使用 watchdog 重启方法。watchdog 通过监控数据输入是否正常可以实现在系统出现异常时自动重启系统，这里我们刚好可以借用的。

首先需要加载 watchdog 支持，这个和主板硬件设备有关，如果只需要软件模拟的，可以运行：

命令加载软件 watchdog 支持，接着再运行：

命令，该命令会马上退出并报错，同时系统日志中就会提示：

这就表示 watchdog 设备是被意外关闭的而不是正常停止的，大约等待 60 秒之后你就会发现 Linux 系统自动重启了。Linux watchdog 的异常等待时间是通过 `/proc/sys/kernel/watchdog_thresh` 设置的，一般默认为 60 秒。

## IPMI 方法

上面几种方法都不能用？如果你的主板刚好支持 IPMI 管理接口的话，那可以直接通过 IPMI 实现硬件上的强制关机或重启。

首先加载 IPMI 支持：

确认 IPMI 设备是否已找到：

如果输出正常的话表示 IPMI 被正确加载了，接着安装 `ipmitool` 管理工具。`ipmitool` 可以通过 IPMI 接口完成对本机或远程主机的一系列管理操作。

这里我们就用直接电源管理的，重启系统：

运行完成后主机就会马上重启，相应的关闭主机可以运行命令：

`ipmitool` 还可以实现在系统未启动时远程查看监控主板硬件状态等功能，在 IPMI 可用的情况下 `ipmitool` 还是比较方便好用的。

无相关文章.

 [IPMI](https://zohead.com/archives/tag/ipmi/), [ipmitool](https://zohead.com/archives/tag/ipmitool/), [kernel](https://zohead.com/archives/tag/kernel/), [Linux](https://zohead.com/archives/tag/linux/), [SysRq](https://zohead.com/archives/tag/sysrq/), [watchdog](https://zohead.com/archives/tag/watchdog/), [重启](https://zohead.com/archives/tag/reboot/)