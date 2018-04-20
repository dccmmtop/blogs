## 连接远程服务器时不能运行ruby程序

```ruby
#!/usr/bin/ruby
require "net/ssh"
require "net/scp"
puts "连接服务器..."
Net::SSH.start("ip", "username",:password => "*****") do |ssh|
    puts ssh.exec!("cd /var/www/script_blog;/usr/local/rvm/gems/ruby-2.3.3/wrappers/bundle exec rake analysis_blog")
end
```

 `puts ssh.exec!("cd /var/www/script_blog;rake nanlysis_blog") `会报找不到 rake 错误
需要`bundle exec rake`
 此时还会报找不到bundle 错误,**需要使用bundle的绝对路径**

> which bundle : /usr/local/rvm/gems/ruby-2.3.3/bin/bundle ,
>
> 但是此时还会报“`/usr/bin/env: ruby_executable_hooks: No such file or directory` 错误,

需要这样改: **usr/local/rvm/gems/ruby-2.3.3/wrappers/bundle** **使用wrappers替换bin** [详情](https://stackoverflow.com/questions/26247926/how-to-solve-usr-bin-env-ruby-executable-hooks-no-such-file-or-directory/26370576#26370576)