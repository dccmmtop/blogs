---
tags: rake,task
date: 2018-05-29 16:03:39
---

我们都用过 `rake db:migrate` `rake db:create`等，我们可以编写自己的任务。

在`lib/tasks`新建一个文件，后缀为`.rake` 加入我要编写的任务是 mv_log ，文件名为`mv_log.rake`

`mv_log.rake`内容如下：

```ruby
desc 'move log'
task :mv_log  => :environment  do
  `mv date.log /tmp/date.log`
end
```

运行`rake -T`可以看到自己写的任务了，运行`rake mv_log` 试试！
