## whenever 使用方式

根据项目的进展，我们需要实现后台进行定时读取信息的功能，而最关键的实现部分是周期性功能，根据调研，决定使用whenever来实现这一功能。

github：[https://github.com/javan/whenever](https://link.jianshu.com?t=https://github.com/javan/whenever)

#### 开发前需要明确的问题

- whenever是怎样一种周期性机制？
- whenever能为我们提供什么功能？
- whenever为周期性任务提供了哪些控制方式？

问题解决

whenever周期性机制

我们来看一下github上面是怎么说的：

Whenever is a Ruby gem that provides a clear syntax for writing and deploying cron jobs.

意思就是说，whenever是一个ruby gem，但同时它是基于cron jobs的。

那么什么是cron jobs呢？我们来看一下维基百科的定义：

#### Cron

crontab命令常见于[Unix](https://link.jianshu.com?t=https://wikipedia.kfd.me/wiki/Unix)和[类Unix](https://link.jianshu.com?t=https://wikipedia.kfd.me/wiki/%E7%B1%BBUnix)的[操作系统](https://link.jianshu.com?t=https://wikipedia.kfd.me/wiki/%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F)之中，用于设置周期性被执行的指令。该命令从标准输入设备读取指令，并将其存放于“crontab”文件中，以供之后读取和执行。该词来源于[希腊语](https://link.jianshu.com?t=https://wikipedia.kfd.me/wiki/%E5%B8%8C%E8%85%8A%E8%AF%AD)chronos（χρόνος），原意是时间。

通常，crontab储存的指令被[守护进程](https://link.jianshu.com?t=https://wikipedia.kfd.me/wiki/%E5%AE%88%E6%8A%A4%E8%BF%9B%E7%A8%8B)激活，crond常常在后台运行，每一分钟检查是否有预定的作业需要执行。这类作业一般称为cron jobs。

也就是说，crontab是在unix和类unix系统中用来实现周期性功能的指令。在网上搜一下，我们就会看到很多crontab指令相关的语法。

根据上述的分析，我们可以得出这样的结论：

whenever事实上是一个cron翻译器，它将rails中的ruby代码翻译成cron脚本，从而将周期性的任务交给cron来执行。 这样，通过whenever我们可以使用ruby语言来写周期性任务代码，在ruby层控制代码，而不需要与shell脚本进行切换；另一方面，我们会发现，由于cron命令的强大，它的语法也因此变得很复杂，通过whenever，我们可以很方便的实现周期性任务。

#### 一个十分简单的demo

1.添加whenever(Gemfile)

`gem 'whenever', :require =>false`

2.生成config/schedule.rb文件

执行命令：

`wheneverize`

3.添加自己的周期性任务

在`config/schedule.rb`文件中添加：

```ruby
set :environment, :developmentevery
2.minutes do  
  runner "Timetest.mytime"
end
```

其中，`set :environment, :development`是设置执行任务时的环境，默认情况下环境为`production`

上述代码实现的是每两分钟读取当前时间并存入到数据库的功能。其中，runner方法执行的方法如下：

```ruby
classTimetest < ApplicationRecord
def self.mytime
  a = Timetest.new
  a.time_now = Time.now
  a.save
 end
end
```

这样，在rails中实现whenever的代码就算是写完了，真的是简单到不行啊！（实在忍不住感慨一句）

下面就要执行周期性任务了。

4.执行周期性任务

在rails工程文件夹下进行一下操作

- 更新schedule.rb中的任务到cronjob中

`whenever -i`

可以看到这样的打印结果：

`[write]crontabfileupdated`

- 执行周期性任务

`whenever -w`

可以看到：

`[write]crontabfilewritten`

此时我们的周期性任务便在后台运行了，此时查看我们的任务：

`crontab -l`

可以看到以下打印：

\# Begin Whenever generated tasks for: 

```shell
/home/vito/rails/test_of_rails/test_rails/config/schedule.rb0,2,4,6,8,10,12,14,16,18,20,22,24,26,28,30,32,34,36,38,40,42,44,46,48,50,52,54,56,58 * * * * /bin/bash -l -c 'cd /home/vito/rails/test_of_rails/test_rails && bundle exec bin/rails runner -e development '\''Timetest.mytime'\'''# End Whenever generated tasks for: /home/vito/rails/test_of_rails/test_rails/config/schedule.rb
```

这样，我们的周期性任务就算是在顺利执行了。

需要注意的一点是运行时crontab的环境（rails和crontab环境不匹配时whenever无法执行），一般调试时多使用的是development环境，而不设置时默认的是production环境，如果你使用`crontab -l`发现是production环境，可以使用

`crontab -e`

直接修改为development，或者直接将-e production删掉即可。

经过上述流程，我们便可以成功地实现周期性任务了。如果此时你发现自己的周期性任务还是没有执行，那你就得好好看看你自己的任务代码了，很可能是执行的任务代码本身有问题，而与whenever的实现没有太大的关系了。