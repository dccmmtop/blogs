```ruby
#!/home/mc/.rvm/rubies/ruby-2.3.1/bin/ruby
require 'qiniu'
Qiniu.establish_connection! access_key: 'xxxxxxxxxx',
                            secret_key: 'xxxxxxxxxx'
bucket = 'dc141210104'
puts ARGV[0]
key = ARGV[0].split(/\//).last

put_policy = Qiniu::Auth::PutPolicy.new(
    bucket, # 存储空间
    key,    # 指定上传的资源名，如果传入 nil，就表示不指定资源名，将使用默认的资源名
    3600    # token 过期时间，默认为 3600 秒，即 1 小时
)
# 生成上传 Token
uptoken = Qiniu::Auth.generate_uptoken(put_policy)
filePath = ARGV[0]
# 调用 upload_with_token_2 方法上传
code, result, response_headers = Qiniu::Storage.upload_with_token_2(
     uptoken,
     filePath,
     key,
     nil, # 可以接受一个 Hash 作为自定义变量，请参照 http://developer.qiniu.com/article/kodo/kodo-developer/up/vars.html#xvar
     bucket: bucket
)
#打印上传返回的信息
# puts "\n\e[1;40;37mhttp://ogbkru1bq.bkt.clouddn.com/#{key}\e[0m"
puts "http://ogbkru1bq.bkt.clouddn.com/#{key}"
```
