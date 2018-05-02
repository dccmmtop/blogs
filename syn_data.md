```rb
#!/usr/bin/ruby
require "net/ssh"
require "net/scp"
io = File.open("/home/mc/rubycode/logs_domestictrade/database.log",'ab+')
io.puts("================#{Time.now}====================")
# 备份数据库
io.puts("正在连接远程服务器...")
puts("正在连接远程服务器...")

Net::SSH.start('**ip**', 'root', :password => "****************",:port => ****) do |ssh|
  io.puts("连接成功")
  puts("连接成功")
  io.puts("备份数据库...")
  puts("备份数据库...")
  ssh.exec!("pg_dump -U root  DomesticTrade_development > /tmp/test.sql")
  io.puts("数据库备份完毕")
  puts("数据库备份完毕")
  io.puts("压缩备份文件...")
  puts("压缩备份文件...")
  ssh.exec!('cd /tmp && zip test.sql.zip test.sql')
  io.puts("压缩备份文件成功")
  puts("压缩备份文件成功")
  io.puts("删除源文件...")
  puts("删除源文件...")
  ssh.exec!("rm /tmp/test.sql")
  io.puts("删除源文件成功")
  puts("删除源文件成功")
end
io.puts("下载压缩后的备份文件...")
puts("下载压缩后的备份文件...")

# # # 将数据下载到本地
Net::SCP.download!("**ip**", "root",
                   "/tmp/test.sql.zip", "/tmp/",
                   :ssh => { :password => "****************" ,:port => ****})
io.puts("下载完毕")
puts("下载完毕")

io.puts("删除远程备份文件")
puts("删除远程备份文件")
# # # 删除备份
Net::SSH.start('**ip**', 'root', :password => "****************",:port => ****) do |ssh|
  ssh.exec!("rm /tmp/test.sql.zip")
end
io.puts("将本地数据库备份文件移到/home/mc/logs_domestictrade/下")
puts("将本地数据库备份文件移到/home/mc/logs_domestictrade/下")
`cd /tmp/ && mv test.sql.zip /home/mc/rubycode/logs_domestictrade/`
io.puts("重命名本分文件...")
`cd /home/mc/rubycode/logs_domestictrade/ && mv test.sql.zip "./#{Time.now}.sql.zip"`
io.puts("完成\n\n\n\n")
puts("完成")
io.close
```
