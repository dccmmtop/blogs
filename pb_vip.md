#!/usr/bin/ruby
require "optparse"

def analysis(file)
  content = File.open(file,"r").read
  content_array = content.split(/\n/)
  tag,body,date= nil
  title = file.split(".").first
  puts content[0]
  if content_array[0] == "---"
    (1..3).each do |i|
      if content_array[i] =~ /tags:/
        tag = content_array[i].split(/:/).last.gsub(/\s/,"")
      elsif content_array[i] =~ /date/
        date = content_array[i].split(/:/).last
      elsif content_array[i] == "---"
        length = content_array.length
        body = content_array[i+1,length-1].join("\n")
        break
      end
    end
    {:body => body,:tag => tag,:title => title,:created_at => date }
  else
    {:body => content,:tag => nil,:title => title,:created_at => nil }
  end
end

def add_files(files)

    puts "\e[32mdone!\e[0m"
    headers = {"Access" => ENV[SCRIPT_BLOG_ACCESS] }
    query = {
      "title" => blog.
    
    }
end

def delete_files(files)
  files = files.map{|file| file.strip.gsub(/\.md/,"")}
  puts "连接服务器..."
  Net::SSH.start("65.49.211.131", "dccmmtop",:password => nil,:port => '29484') do |ssh|
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
