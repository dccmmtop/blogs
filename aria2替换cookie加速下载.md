---
tags: [aria2c,axel]
date: 2019-01-13 09:13:20
---

>  部分整理自: https://www.yinquesiting.net/archives/109

部分网站需要登录才能下载，如果默认的下载速度很慢，想用[使用ipv6突破网络限速](https://www.yinquesiting.net/archives/80)说的aria2工具下载加速，需要替换cookie来骗过服务器。

### 获得文件的下载地址

先登录以后，正常下载文件，则在浏览器或者下载工具那里可以获取到文件的下载地址。

#### 获取cookie

在浏览器按下F12进入开发者工具，刷新页面，可以在network标签页看到访问的元素请求。

随便找个页面的请求记录点击一下，就可以看到右侧出现了详细的请求内容。

找到`Request Headers`部分，即可看到有cookie的数据，其他部分的数据，可以要可以不要，有些服务器要求有些不要求，如果只替换cookie还是无法下载的话，则可以添加其他内容参数。

#### 新的下载命令

```bash
aria2c -c -s10 -k1M -x16 --enable-rpc=false -o '要保存的文件名' --header "User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/61.0.3163.100 Safari/537.36" --header "Cookie: 你的cookie内容" "文件的下载地址"
```

替换如上命令里的

- 要保存的文件名
- 你的cookie内容
- 文件的下载地址

## axel

例子：

```shell
axel -o 保存的文件名 -n 线程的数量 -H Cookie:"你的cookie（加双引号）" "要下载的文件链接"
```
