---
tags: [nginx,rails,http]
date: 2018-08-08 10:56:22
---

**当 post 请求中参数的长度过大时或者 get 请求链接过长时，会引发 `414 Request-URI Too Large`错误**

### 解决办法

在`nginx.conf中`的`http{ }` 区域内添加如下设置

```shell
client_header_buffer_size 512k;
large_client_header_buffers 4 512k;
```

### 重启服务

```shell
service nginx restart
```
