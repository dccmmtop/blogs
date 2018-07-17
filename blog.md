---
tags: diy_script
---

```ruby
#!/usr/bin/ruby
# linux系统安装 ag ,可以利用本脚本在固定的文件下搜索文章,
# 本地博客检索使用
re = `ag #{ARGV[0]} ~/blog/`.split("\n")
title = ""
i = 1
content = []
re.each do |r|
   info =  r.split(/:[0-9]{1,100}:/)
   if title != info[0]
     title = info[0]
     puts "\n\e[1;40;37m#{info[0]}\e[0m  \e[1;43m#{i}\e[0m"
     content << "#{info[0]}"
     i += 1
   end
   r =~ /:[0-9]{1,100}:/
   num = "\t\e[36m#{$&.gsub(/:/,"")}\e[0m "
   info[1] =~ /#{ARGV[0]}/i
   info[1].gsub!(/#{ARGV[0]}/i,"\e[41m#{$&}\e[0m")
   puts "#{num}#{info[1]}\n "
 end
File.open("/tmp/blogsearch",'w+') do |io|
  content.each do |co|
    io.puts(co)
  end
end
```
