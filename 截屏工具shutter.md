---
tags: [shutter,截屏,linux]
date: 2018-08-20 12:28:57
---

### 安装软件

`sudo apt install shutter`

### 自定义脚本

```ruby
#!/usr/bin/ruby
# 本来想复制文件名到剪切板，与原来的功能冲突了
# shutter 默认复制了图片到剪切板
# require "clipboard"
name = Time.now.to_i.to_s + ".png"
`shutter -s -o ~/图片/#{name}`
# Clipboard.copy("/home/mc/图片/#{name}")
```

将脚本重命名为`custom_shutter`

shutter 默认的命名方式是 '选区 01 选区 02 ...' ，当我清空图片时，又会重头开始循环，由于之前的图片可能上传到了图床，后来与之相同名字的图片在上传时，就会覆盖以前的内容。

此脚本的目的是 让每一次截屏的图片以时间戳命名。

关于 shutter 的更多命令行操作 使用 `shutter -h` 查看

### 绑定快捷键

![](http://ogbkru1bq.bkt.clouddn.com/1534739826.png)
