### 推送博客
```ruby
#!/usr/bin/ruby
require "net/ssh"
require "net/scp"
puts "连接服务器..."
Net::SSH.start("xxxxx", "xxxx",:password => "xxxxx") do |ssh|
  file_count = ARGV.size
  puts "需要推送#{file_count}个文件"
  (0...file_count).each do |i|
    puts "正在上传#{ARGV[i]}..."
    ssh.scp.upload!("#{ARGV[i]}","/home/deploy/rails_app/script_blog/public/blogs/")
    puts "\n\ngit add #{ARGV[i]}"
    puts ` git add #{ARGV[i]}`
    puts "git commit -m ' #{ARGV[i]}'"
    puts ` git commit -m '#{ARGV[i]}'`
  end
  if file_count > 0
    puts "所有文件上传成功\n解析博客中..."
    puts ssh.exec!("cd /home/deploy/rails_app/script_blog && RAILS_ENV=production /home/deploy/.rbenv/shims/bundle exec rake analysis_blog")

    puts "\n\n\n备份到github\ng"
    puts "git push origin master"   
    puts `git push origin master`
  end
  puts "\e[32mdone!\e[0m"
end
```
