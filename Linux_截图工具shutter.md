---
tags: [shutter]
date: 2019-02-14 17:05:26
---

### 安装

```shell
yay -s shutter
```

### 插件

```shell
yay -s perl-goo-canvas # 使用编辑图片功能
```

### 快捷键脚本运行

```ruby

#!/usr/bin/ruby
# shutter 默认复制了图片到剪切板
# require "clipboard"
name = Time.now.to_i.to_s + ".png"
`shutter -s -o ~/图片/#{name}`
# Clipboard.copy("/home/mc/图片/#{name}")
```

#### 博客截图专用

```ruby
#!/usr/bin/ruby
# 本来想复制文件名到剪切板，与原来的功能冲突了
# shutter 默认复制了图片到剪切板
picture_path = "#{ENV["HOME"]}/blog/picture/"
`mkdir -p #{picture_path}` unless Dir.exist?(picture_path)
name = "#{picture_path}#{Time.now.to_i.to_s}.png"
`shutter -s -o #{name}`
```

将以上保存脚本，系统添加快捷键运行此脚本


