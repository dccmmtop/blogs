### 推送博客
```ruby
#!/usr/bin/ruby
require "net/ssh"
require "net/scp"
puts "连接服务器..."
Net::SSH.start("ip", "username",:password => "*****") do |ssh|
  file_count = ARGV.size
  puts "需要推送#{file_count}个文件"
  (0...file_count).each do |i|
    puts "正在上传#{ARGV[i]}..."
    ssh.scp.upload!("#{ARGV[i]}","/var/www/script_blog/public/blogs/")
  end
  puts "所有文件上传成功\n解析博客..."
  if file_count > 0
    puts ssh.exec!("cd /var/www/script_blog && /usr/local/rvm/gems/ruby-2.3.3/wrappers/bundle exec rake analysis_blog")
  end
  puts "\e[32mdone!\e[0m"
end
```
