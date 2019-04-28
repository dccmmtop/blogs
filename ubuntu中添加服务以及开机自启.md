---
tags: linux
date: 2019-04-28 17:59:37
---

### 自定义脚本

编写自己的需要开启自启的脚本，如下：

```ruby
#!/usr/bin/env ruby
`cd /home/mc/code/rails_app/master_crawler_trm &&  nohup rake real_time_extract_word  > /dev/null 2&>1`
```

### 启动脚本

```ruby
#!/usr/bin/env ruby
start = " sudo -u mc  /home/mc/bin/real_time_extract_words "
stop = "ps -ef | grep real_time_extract_word | grep -v grep | cut -c 9-15 | xargs kill -9"
action = ARGV[0]
if action == 'start'
  system(start)
  puts "启动成功"
elsif action == 'stop'
  system(stop)
  puts "已停止"
elsif action == 'restart'
  system(stop)
  system(start)
  puts "已重启"
end
```

这个文件可以被 ubuntu 下的服务读取并执行，保存该文件为 `custom_sh` 然后 `sudo chmod +x custom_sh`赋予可执行权限

要注意自己的脚本需要在哪个用户下执行

### 操作该服务

```shell
sudo systemctl daemon-reload # 添加新的 或者改动 服务之后要刷新
sudo service custom_sh start # 启动
sudo service custom_sh stop # 停止
sudo service custom_sh status # 查看状态
```

### 开启自启

```shell
sudo update-rc.d custom_sh defaults
```
