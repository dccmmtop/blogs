---
tags: [rails,whenever]
date: 2018-09-01 16:00:05
---

有一个 gem 包`whenever` 是用来写定时任务的，他的作用主要是将 ruby 语法转换成 crontab 的形式，所以本质上还是用 crontab 来实现定时任务的。

- 检查系统中是否有 crontab # 默认都有
- 检查服务是否启动 `service cron status`
- 启动服务 `service cron start`
- 停止服务 `service cron stop`
- 重启服务 `service cron restart`

#### 安装 whenever

- 在 Gemfile 中 `gem 'whenever', :require => false`
- 生成`config/schedule.rb`文件 执行 `wheneverize`

#### 执行周期任务

- 添加周期任务

  > 在`schedule.rb`文件中添加
  >
  > ```ruby
  > set :environment, :development # 设置执行环境为开发环境
  > every 2.minutes do  # 每隔2分钟执行一次 若是1分钟则是 1.minute （单数）
  >   rake "mv_log"  # 执行
  > end
  > ```

- 执行周期任务 ,在 rails 项目下

  `whenever -i` # 更新 schedule.rb 中的任务到 cronjob 中

  `whenever -w` # 执行周期性任务

  可以合并为 `whenever -iw`

  此时定时任务就在运行了，通过 `crontab -l`查看正在运行的定时任务

  ### [ 如何编写任务 `rake xxx` ](http://dccmm.world/topics/Rails%E4%B8%AD%E7%BC%96%E5%86%99%E8%87%AA%E5%B7%B1%E7%9A%84%E4%BB%BB%E5%8A%A1)

#### 停止定时任务

```shell
ps -ef | grep rake
```

找到你的任务，kill 掉它

#### 查看Ubuntu 定时服务的状态

```shell
service cron status
service cron stop
service cron start
service cron restart
```
