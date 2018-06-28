### 推送博客
```ruby
#!/home/dccmmtop/.rbenv/shims/ruby
require "net/ssh"
require "net/scp"
require "optparse"

def add_files(files)
  puts "连接服务器..."
  Net::SSH.start("ip", "username",:password => nil,:port => 'port') do |ssh|
    file_count = files.size
    puts "需要推送#{file_count}个文件"
    (0...file_count).each do |i|
      puts "正在上传#{files[i]}..."
      ssh.scp.upload!("#{files[i]}","/home/dccmmtop/rails_app/script_blog/public/blogs/")
      puts "\n\ngit add #{files[i]}"
      puts ` git add #{files[i]}`
      puts "git commit -m ' #{files[i]}'"
      puts ` git commit -m '#{files[i]}'`
    end
    if file_count > 0
      puts "所有文件上传成功\n解析博客中..."
      puts ssh.exec!("cd /home/dccmmtop/rails_app/script_blog && RAILS_ENV=production /home/dccmmtop/.rbenv/shims/bundle exec rake analysis_blog")

      puts "\n\n\n备份到github\n"
      puts "git push origin master"   
      puts `git push origin master`
    end
    puts "\e[32mdone!\e[0m"
  end
end

def delete_files(files)
  files = files.map{|file| file.strip.gsub(/\.md/,"")}
  puts "连接服务器..."
  Net::SSH.start("ip", "username",:password => nil,:port => 'port') do |ssh|
      puts ssh.exec!("cd /home/dccmmtop/rails_app/script_blog && RAILS_ENV=production /home/dccmmtop/.rbenv/shims/bundle exec rake delete_blog[#{files.join(',')}]")
  end
end
options = {}

option_parse = OptionParser.new do |opts|
  opts.banner = 'manage blog of conmmand line tool'
  opts.on('-a file_name','--add files','add new file or update file') do |value|
    options[:add_files] = value.split(/\s|,/).map{|name| name if name.size > 0}.compact
  end

  opts.on('-d file_name','--delete files','delete file') do |value|
    options[:delete_files] = value.split(/\s|,/).map{|name| name if name.size > 0}.compact
  end
end.parse!
if options.has_key?(:add_files)
  add_files(options[:add_files])
elsif options.has_key?(:delete_files)
  delete_files(options[:delete_files])
end
```
