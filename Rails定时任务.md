## Rails定时任务

有一个gem包`whenever` 是用来写定时任务的，他的作用主要是将ruby语法转换成crontab的形式，所以本质上还是用crontab来实现定时任务的。

* 检查系统中是否有crontab # 默认都有
* 检查服务是否启动 `service cron status` 
* 启动服务 `service cron start`
* 停止服务 `service cron stop`
* 重启服务 `service cron restart`

#### 安装whenever

* 在Gemfile 中 `gem 'whenever', :require => false`
* 生成`config/schedule.rb`文件 执行 `wheneverize`

#### 执行周期任务

* 添加周期任务

  > 在`schedule.rb`文件中添加
  >
  > ```ruby
  > set :environment, :development # 设置执行环境为开发环境
  > every 2.minutes do  # 每隔2分钟执行一次 若是1分钟则是 1.minute （单数）
  >   rake "mv_log"  # 执行
  > end
  > ```

* 执行周期任务  ,在rails项目下

  `whenever -i`  # 更新schedule.rb中的任务到cronjob中

  ` whenever -w`  # 执行周期性任务

  可以合并为 ` whenever -iw`

  此时定时任务就在运行了，通过 `crontab -l`查看正在运行的定时任务

  ###[ 如何编写任务 `rake xxx` ]()

  
